# Deployment

- Através do deployment teremos recursos de controle de versões:
- Pode se aplicar as replicas.
- Com aplicação das replicas será criado um replicaSet do deployment, 
para gerenciar o estados dos pods declarados.
- Segue a mesma estrutura de declaração do ReplicaSet.

pod-1-deployment.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
    name: nginx-deployment
spec:
    replicas: 3
    template:
        metadata:
            name: ngix-pod
            labels:
              app: ngix-pod
        spec:
            containers:
                - name: ngix-container   
                  image: nginx:stable
                  ports:
                    - containerPort: 80
    selector:
        matchLabels:
            app: ngix-pod
```

- Aplicando a configuração:

```
    kubectl apply -f pod-1-deployment.yaml

```

- Verficando as revisões realizadas

```
kubectl rollout history deployment nginx-deployment

```

- No exemplo anterior da definição do deployment altere a revisão do arquivo
de **stable** para **latest**.


- Aplicando alterações 

```
kubectl apply -f pod-1-deployment.yaml --record

```

- Verificar as revisões :
    - Foi criado uma nova versão.

```
kubectl rollout history deployment nginx-deployment

```

- Definindo CHANGE-CAUSE

```
kubectl annotate deployment nginx-deployment kubernetes.io/change-cause="Definindo a imagem com versão latest"

```

- Verificando versões de lançamento:

```
kubectl rollout history deployment nginx-deployment

```