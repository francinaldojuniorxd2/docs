# NodePort

- Ao configurar o NodePort estamos disponibilzando a nossa aplicação 
para rede externa "fora do cluster", atrvés do ecaminhamento de portas,
o NodePort disponibiliza portas para que clientes fora do cluster possa comunicar
com serviços interno.

- Para cada serviço aplicado para um determinado recurso é aplicado um porta no intervalo 
de 30000 - 32767, quando essa porta não é fixada na declaração do serviço o Svc aplica
um valor de porta nesse intervalo.

- Quando o master recebe um determinada requisição nesse intervalo de porta ele encaminha a 
requisição para o serviço, o serviço verifica qual a porta interna está vinculada para aquele 
NodePort e encamiha a requisição para o recurso declarado.


### Alterar arquivo pod-1.yaml e svc-pod-1


pod-1.yaml

```
apiVersion: v1
kind: Pod
metadata: 
    name: pod-1
    labels:
        app: first-pod
spec:
    containers:
        - name: container-pod-1
          image: nginx:latest
          containerPort: 80    
```
svc-pod-1.yaml

- O service NodePort segue o mesmo padrão do cluster IP
a unica alteração relevante e type, selector, ports e **nodePort**;

- **nodePort** : É a porta que será utilizada para os clientes externos acessar 
o serviços interno, essa porta varia de 30000 - 32767, então devemos setar uma porta 
fixa para não ocorrer um problema caso o serviço seja reiniciado a porta ser trocada.

- Nesse exemplo vou configurar o serviço para receber a solicitações 
na porta **8081** e encaminhar para para porta **80** do meu **first-pod**

svc-pod-2.yaml

```
apiVersion: v1
kind: Service
metadata: 
    name: svc-pod-1
spec:
    type: NodePort
    selector:
        app: first-pod
    ports:
        - port: 8081
          targetPort: 80
          nodePort: 30000
```
### Aplicar

```
kubectl apply -f pod-1.yaml
kubectl apply -f svc-pod-1.yaml

```

## Vizualizar o IP do MASTER 

- Vamos obter o ip do master que será responsável por encaminhar às requisições,
quando configuramos o NodePort falamos para o master que a porta específicada dísponível para receber requisições.

- Definimos a porta 30000 para receber as requisições, todas as requisições que chegar nessa 
determinada porta será encaminhada para porta 80 do nosso Pod.

```
    kubectl get node -o wide 

```

- Agora vá até o de navegador de sua preferência para testar se o serviço está funcionando corretamente  :

**INTERNAL-IP:30000**