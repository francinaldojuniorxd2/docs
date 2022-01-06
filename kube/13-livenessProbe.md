# Liveness Probes

- Verifica se aplicação está saudável, podemos ter um container funcionando 
normalmente dentro de um pod, mas isso não garante que aplicação que está
sendo executada pelo container está funcionando normalmente.

- Através do liveness Probe será lançado requisições em um determinado intervalo de tempo, 
será verificado o estado da aplição através do status de resposta, se o número de status
for igual 200 e menor que 400 indica sucesso. Caso ao contrario falha.

```
    linevessProbe:
        httpGet:
            path: /
            port: 80
        periodSeconds: 10           # A cada 10 segundo será envido um requisição para o Pod na porta 80 
        failureThreshold: 3         # Se não conseguir realizar a operção três vezes irá realizar restart do Pod.
        initialDelaySeconds: 30     # Ao iniciar o Pod após 30 segundos será iniciado a verficação da aplicação.

```


- Está configuração deve ser aplicada ao container.

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
        volumes:
            - name: nginx-volume
              persistentVolumeClaim:
                claimName: nginx-pvc
    selector:
        matchLabels:
            app: nginx-app-system
    serviceName: nginx-svc
```
