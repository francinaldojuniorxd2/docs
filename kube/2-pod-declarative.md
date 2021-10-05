#Os campos obrigatórios 

O .yaml arquivo do objeto Kubernetes que você deseja criar, você precisará definir valores para os seguintes campos:

apiVersion - Qual versão da Kubernetes API você está usando para criar este objeto
kind - Que tipo de objeto você deseja criar
metadata- Dados que ajudam a identificar exclusivamente o objeto, incluindo uma namestring UID, e opcionalnamespace
spec - Qual estado você deseja para o objeto


###Criando um pod no modo declarativo:

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

### Criando o primeiro pod:

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
kubectl describe pod pod-1

```