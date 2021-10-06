# LoadBalancer 

- O serviço de LoadBalancer é uma maneira no qual cada aplicação vai obter seu proprío 
endereço IP.

### Pod

svc-pod-2.yaml

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
      ports: 
        - containerPort: 80
```
### SVC

- As unicas alterações a se realizar comparando com NodePort é o 

**type**:**LoadBalancer** 

```
apiVersion: v1
kind: Service 
metadata:
  name: svc-pod-lb-2
spec:
  type: LoadBalancer
  selector:
    app: second-pod
  ports:
    - port: 80
      nodePort: 30001
```

### MiniKube 
 

 - Para quem estiver utilizando [minikube](https://minikube.sigs.k8s.io/docs/handbook/accessing/) para realizar os teste é necessário 
 habilitar o serviço de tunnel.

 ```
minikube tunnel

 ```

 - Para acessar a aplicação atráves do navegador ;

 http://Exeternal-IP