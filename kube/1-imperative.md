# Imperative

### Criando Pod :

```
kubectl run nginx-1 --image:ngnix:latest

```

### Vizualizando todos os pods :

```
kubectl get pods

``` 

### Vizualizar os pods mostrando mais atributos: 

```
kubectl get pods -o wide
```

### Descrevendo  um Pod:

- Essa opção nós permiti verificar se algum erro durante a execução do pod.

```
kubectl describe pod nginx-1

```

### Editando o pod

```
kubectl edit pod nginx-1

```

- Um dos grandes problemas quando se utiliza a linha de comando para 
realizar a criação de recursos no kube é a manutenção e garantia
de disponibilidade, devido que assim que houver qualquer reinicialização
todas as configurações seram perdidas.