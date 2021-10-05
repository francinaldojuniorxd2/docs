Criando um pod através da linha de comando:

```
kubectl run nginx-1 --image:ngnix:latest

```

Vizualizando todos os pods :

```
kubectl get pods

``` 

Vizualizar os pods mostrando mais atributos como ip

```
kubectl get pods -o wide
```

Realizando a verificação desse pod:

```
kubectl describe pod nginx-1
```

Editando o pod

```
kubectl edit pod nginx-1

```

Um dos grandes problemas quando se utiliza a linha de comando para 
realizar a criação de recursos no kube é a manutenção e garantia
de disponibilidade, devido que assim que houver qualquer reinicialização
todas as configurações seram perdidas.

#Criando um pod no modo declarativo:

Para um pod no modo declarativo o arquivo deve conter uma extensão 
yaml.

```
apiVersion : É a versão do kube
kind : type de recurso a ser criado
metadata:
	name: nome do recurso.
spec:
	O que esperamos que sejá aplicado.
```

Criando o primeiro pod:

```
apiVersion: v1
kind: Pod
metadata:
	name: pod-1
spec: 
	containers:
		- name: container1
		  image: nginx:latest 
```