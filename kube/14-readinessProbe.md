# Readiness Probe 

- Verifica se a aplicação está pronta para receber requisições, os contaneirs 
ou pod muitas vezes podem falhar o service tem o papel fundamental de encaminhar 
as requisições para os containers dos Pods em execução, mas nem todo Pod em execução
indica que aplicação já está totalmente operante.

- O readiness Probe irá realizar a verificação inicial da aplicação, após ele conseguir
realizar as requisição especificadas, envia a informação ao kubernetes informando que aquele
pod já pode receber requisições.

- Através do readiness Probe será lançado requisições em um determinado intervalo de tempo, 
será verificado o estado da aplição através do status de resposta, se o número de status
for igual 200 e menor que 400 indica sucesso. Caso ao contrario falha.

- A cada 10 segundo será enviado um requisição para o container na porta 80 
    periodSeconds: 10

- Assim que conseguir realizar a tarefá especificada por 3 vezes, irá enviar um sinal de pronto para          
    failureThreshold: 3

- Ao iniciar o container após 3 segundos será iniciado a verficação da aplicação.
    initialDelaySeconds: 3

```
    readinessProbe:
        httpGet:
            path: /
            port: 80
        periodSeconds: 10           
        failureThreshold: 3   
        initialDelaySeconds: 3
```


```
apiVersion: apps/v1
kind: Deployment
metadata:
    name: nginx-deployment
spec:
    replicas: 3
    template:
        metadata: 
            labels:
                app: nginx-app-system
            name: nginx-pod
        spec:
            containers:
                - name: nginx-container
                  image: nginx/latest
                  ports: 
                    - containerPort: 80
                      name: "http-server"
                  envFrom:
                    - configMapRef:
                        - name: nginx-configmap
                  volumeMounts:
                    - mouthPath: "/usr/share/nginx/html"
                      name: nginx-volume
                  livenessProbe:
                    httpGet:
                        path: /
                        port: 80
                    periodSeconds: 10
                    failureThreshold: 3
                    initialDelaySeconds: 30
                  readinessProbe:
                    httpGet:
                        path: /
                        port: 80
                    periodSeconds: 10           
                    failureThreshold: 3   
                    initialDelaySeconds: 3
        volumes:
            - name: nginx-volume
              persistentVolumeClaim:
                claimName: nginx-pvc
    selector:
        matchLabels:
            app: nginx-app-system
    serviceName: nginx-svc
```
