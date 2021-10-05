# Service SVC

Fazer a comunicação de diferentes pod dentro do cluster:
Quando um pod para de funcionar e o mesmo é recriando não temos qualquer
garantia que o pod vai ter o mesmo IP:

### Criando o primeiro pod:

nginx.yaml

```
apiVersion: v1
kind: Pod
metadata: 
  name: nginx-1
spec:
  containers:
    - name: container-pod-1
      image: nginx:latest
```


### Aplicando configuração 

```
    kubectl apply -f pod-1.yaml

```

### Vizualizar os pods :

Verificar a configuração dos IP's :

```
kubectl get pods -o wide

```

## Deletar o pod:

```
kubectl delete pod nginx-1

```

### Aplicando configuração 

```
    kubectl apply -f pod-1.yaml

```

### Vizualizar os pods mostrando mais atributos como ip

```
kubectl get pods -o wide

```

# SVC

- Abstrações para expor aplicações executando em um ou mais Pods.
- Proveem IP's fixos para comunicação.   
- Proveem um DNS para um ou mais Pods.
- São capazes de fazer balanceamento de carga.

### Os três tipos de serviço fornecido svc :

- ClusterIP 
- NodePort
- LoadBalancer 

## ClusterIP

Os ClusterIP nós permiti atribuir IP's fixos ao serviço, através do 
serviço podemos realizar o encaminhamento do trafégo para um determiado pod
selecionado. 

# Para realizar os testes vamos criar dois pods:

pod-1.yaml

```
apiVersion: v1
kind: Pod
metadata: 
    name: pod-1
spec:
    containers:
        - name: container-pod-1
          image: nginx:latest    
```

pod-2.yaml

```
apiVersion: v1
kind: Pod
metadata: 
    name: pod-2
spec:
    containers:
        - name: container-pod-2
          image: nginx:latest    
```

#### Aplicar as configurações 


```
    kubectl apply -f pod-1.yaml
    kubectl apply -f pod-2.yaml

```

#### Criando SVC


svc-pod-2.yaml

```
apiVersion: v1
kind: Service
metadata: 
    name: pod-2
spec:
    **type: ClusterIP**   
```

**Para especificar que o serviço vai ser atribuido um determinado recurso
deve se especificar a label, como não temos aplicada uma label
ao nosso pod vamos alterar o arquivo**


#### Altere o arquivo **pod-2.yaml**

- Definir que label a ser utilizad :

    **app**:**second-pod**

- Outro ponto importe a definir qual a porta que serviço está disponível:

    **containerPort**:**80**

```
apiVersion: v1
kind: Pod
metadata: 
    name: pod-2
    labels:
        app: second-pod
spec:
    containers:
        - name: container-pod-2
          image: nginx:latest
          containerPort: 80    
```

#### Aplicar as configurações 


```
    kubectl apply -f pod-2.yaml

```

### Selecionar o pod que será aplicado **svc-pod-2.yaml**

Agora que já aplicamos e reconfiguramos o nosso pod vamos continuar
as configuração do nosso **svc**:

- Definir que o trafégo recebido no svc será encaminhado para o pod-2
    - Utilizar o **selector** e especificar o label vinculada ao recurso.

    **selector**:
        **app**:**second-pod**

- Devemos também especificar em qual porta o Pod vai estar ouvindo para receber os dados
    - No caso se você desejar  especificar que pod vai está ouvindo na porta que serviço
    de encaminhamento:
    **ports**:
        **- port**: **80** 
    - A porta de entrada é igual a porta de saída, todo o trafégo que 
    chegar na porta **80** do nosso serviço será encaminha para porta **80** do second-pod.

- Outra opção é que o serviço ouvira em uma porta mas encaminhará em outra porta:
    **ports**:
        **- port**: **9651**
        **targetPort**: **80** 
    - Nesse exemplo todo tráfego direcionado a porta **9651** do nosso serviço será ecaminhado 
    para porta **80** do **second-pod**

svc-pod-2.yaml
```
apiVersion: v1
kind: Service
metadata: 
    name: svc-pod-2
spec:
    type: ClusterIP
    selector:
        app: second-pod
    ports:
        - port: 80
```
**OU**

svc-pod-2.yaml
```
apiVersion: v1
kind: Service
metadata: 
    name: svc-pod-2
spec:
    type: ClusterIP
    selector:
        app: second-pod
    ports:
        - port: 9651
        targetPort: 80
```

### Aplicando a configuração do svc :

``` 
 kubectl apply -f svc-pod-2.yaml
 
```

