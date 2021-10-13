# Variveis de ambiente 

Nesse exemplo vamos criar um Pod contendo um container de banco de 
dados mysql. Objetivo é implantação de um Pod com variveis de ambiente :

- Em **env** vamos definir nossas variveis de ambiente, no parametro **name**
definimos o nome dado para varivel e em **value** definimos o valor que será 
atribuido a mesma, para verificar quais variaveis de ambiente:

db-mysql.yaml

```
apiVersion: v1
kind: Pod
metadata: 
    name: db-mysql
    labels:
        app: db-mysql
spec:
      containers:
        - name: db-mysql
          image: mysql:latest
          ports: 
            - containerPort: 3306
          env: 
            - name: 'MYSQL_ROOT_PASSWORD'
              value: '123554849'
            - name: 'MYSQL_DATABASE'
              value: 'teste'
            - name: 'MYSQL_USER'
              value: 'bob'
            - name: 'MYSQL_PASSWORD'
              value: '123554849'
```
- Aplicando as configurações:

```
kubectl apply -f db-mysql.yaml 

```

- Acessando o container do Pod para verificar se as variaveis foram
atribuidas corretamente.

```
 kubectl exec -it  db-mysql -- bash 
```
- vizualizar o conteudo da varivel :

```
echo $MYSQL_ROOT_PASSWORD

```

### Definindo um serviço para o Pod

- Para que possamos acessar o mysql de fora do cluster através da nossa máquina
será criado o um serviço do tipo NodePort. Lembrando que esse tipo de serviço 
cria portas de encaminhamentos para que possamos alcançar o recurso é necessário 
utilizar o ip do master ao receber as requisições ele vai verificar a Nodeport e encaminhar 
a requisição para o serviço especifico, o serviço recebera a requisição e ecaminhara para 
o Pod selecionado e porta especificada.

```
apiVersion: v1 
kind: Service
metadata:
    name: svc-db-mysql
spec:
    type: Nodeport 
    selector:
        app: db-mysql
    ports: 
        - port: 3306
          Nodeport: 30002 
```

- Lembrando que o Nodeport será a porta que será utilizada para alcançar o nosso recurso
de ambiente fora do cluter.

```
    kubectl apply -f svc-db-mysql.yaml
```

- Verificando se o serviço foi configurado corretamente:

```
    kubectl get svc 

```

- Verificar o IP do node master:

```
kubectl get node -o wide

```

- No meu caso que estou utilizando **minikube** no docker o Internal-IP
é 192.168.49.2.

- Acessar o mysql do host :

mysql --host=**Internal-Ip** --port=**NodePort** -u bob --password teste

```
mysql --host=192.168.49.2 --port=30002 -u bob --password teste
```