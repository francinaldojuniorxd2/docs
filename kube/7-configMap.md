# ConfigMap

- Um ConfigMap é um objeto da API usado para armazenar dados não-confidenciais 
em pares chave-valor. Pods podem consumir ConfigMaps como variáveis de ambiente, 
argumentos de linha de comando ou como arquivos de configuração em um volume.

- Um ConfigMap ajuda a desacoplar configurações vinculadas ao ambiente das 
imagens de contêiner, de modo a tornar aplicações mais facilmente portáveis.

- Um bom exemplo é a definição de variáveis de ambiente que foi definida no Pod,
vamos desacomplar essa configuração para um ConfigMap, que poderar ser utilizado
por um ou mais Pods.

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

### Definido ConfigMap

- Os valores serão passados no **data** no formato de chaves e valores.

db-config.yaml

```
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: db-config
    data:
        MYSQL_ROOT_PASSWORD: 123554849
        MYSQL_DATABASE: teste
        MYSQL_USER: bob
        MYSQL_PASSWORD: 123554849
```

- Aplicando a configuração:

```
    kubectl apply -f db-config.yaml

```

### Importando variváveis específicas

- Nesse primeiro momento vamos aplicar a configuração de variveis especifícas:

- Através do **valueFrom** vamos especificar de onde queremos obter o valor da variável,
então queremos pegar esse valor de um configMap então específicamos um **configMapKeyRef**, 
passamos para o configMapKeyRef o name da configMap desejada e a Key que desejamos obter o valor.

- Essa configuração torna o codigo verboso.

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
            - name: MYSQL_ROOT_PASSWORD
              valueFrom: 
                configMapKeyRef:
                    name: db-configmap
                    key: MYSQL_ROOT_PASSWORD
```

### Importando todas as variveis:

- Todas as configurações aplicadas ao configMap serão importadas para 
o Pod.

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
          envFrom: 
            - configMapRef: 
                name: db-config
```