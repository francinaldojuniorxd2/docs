# ReplicaSets

- O objetivo de um ReplicaSet é manter um conjunto estável de pods de réplica 
em execução a qualquer momento. Como tal, costuma ser usado para garantir a 
disponibilidade de um número especificado de pods idênticos.

- Quando criamos o Pod estamos criando a recurso para ser executado, em caso de falhas
ficara indisponível.

- O replicaSet encapsula os Pods e gerencia os estados, se caso  algum dos Pods
falhar o replicaSet realiza a correção subindo uma nova replica.

pod-1-replicaset.yaml

```
apiVersion: apps/v1
kind: ReplicaSet
metadata: 
    name: pod-1-replicaset
spec: 
    template:
        metadata: 
            name: pod-1
            labels:
                app: first-pod
        spec:
            containers:
                - name: container-pod-1
                  image: nginx:latest
                  ports:
                   - containerPort: 80  
    replicas: 3
    selector:
        matchLabels:
            app: first-pod
```

- Aplicando as cofigurações:

```
kubectl apply -f pod-1-replicaset.yaml

```

- Listar Pods: 

```
kubectl get pods

```

- Remova um pod de Replica set 

```
kubectl delete pods pod-1-replicaset-<idRs> 

```


- Listar Pods: 
- Ao deletar o Pod o replica set indentifica que a quantidade 
de pods em execução é menor do que a desejada e automanticamente
ele executa um novo pod.

```
kubectl get pods

```

- Verificando estados do replicaset
- Nessa vizualização temos três colunas
    **DESIRED** : Quantidade de pods em execução desejada.
    **CURRENT** : Quantidade de pods em execuação no momento.
    **Read** : Quantidade de pods inicializando.

```
kubectl get rs

```