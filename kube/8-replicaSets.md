# ReplicaSets

- O objetivo de um ReplicaSet é manter um conjunto estável de pods de réplica 
em execução a qualquer momento. Como tal, costuma ser usado para garantir a 
disponibilidade de um número especificado de pods idênticos.

- Quando criamos o Pod estamos criando a recurso para ser executado, em caso de falhas
ficara indisponível.

- O replicaSet encapsula os Pods e gerencia os estados, se caso  algum dos Pods
falhar o replicaSet realiza a correção subindo uma nova replica.


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




