# Horizontal Pode auto scaler HPA

- Escala automaticamente o número de Pods, em um deploymente, replicaset e statefulSet baseado no consumo de CPU.

- Os recursos são finitos em qualquer equipamento definir um número de replicas não
garante que aplicação vai funcionar normalmente se houver um quantidade maior de requisições 
do que o previsto.

- Pense no seguinte ambiente, uma ativida urgente foi acionanda e todas as filiais tem que acessar sistema ao mesmo tempo, dobrando a quantidade de requisições e processamento.

    - Nesse exemplo se tivermos configurado HPA para o nosso deployment, automaticamente será implmentado novas replicas aumentado nossa capacidade de processamento.

    - Se não então devemos aumentar manualmente a quantidade de replicas.

- Para atender necessidades urgentes é recomendável ter configurado o HPA em nossas aplicações garantindo um ambiente mais dinámico possível.


- Vamos definir que o nosso container requisita 10m de CPU :
    resources:
        request:
            cpu: 10m


### nginx-deploment.yaml


```
apiVersion: apps/v1
kind: Deployment
metadata:
    name: nginx-deployment
spec:
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
                  resources:
                    request: 
                        cpu: 10m
        volumes:
            - name: nginx-volume
              persistentVolumeClaim:
                claimName: nginx-pvc
    selector:
        matchLabels:
            app: nginx-app-system
    serviceName: nginx-svc
```

### Definido HPA.


 - *scaleTargetRef* :                        Recebe as informações do deployment a ser escalado.
        apiVersion : apps/v1                 É a versão que o deployment está utilizando.
        kind: Deployment                     É o tipo de serviço que deseja escalar.
        name: nginx-deploymente              O nome do serviço que quero escalar.
    minReplicas: 1                           Número mínimo de replicas
    maxReplicas: 10                          Número maximo de replicas, nunca vai passar desse número.
    metrics:
        - type: Resource                    Baseado no recurso 
          name: cpu                         de CPU
          target: 
            type: Utilization 
            averageUtilization: 50          Se a utilização for maior igual 50 será criado novas replicas.

- Nessa definição definimos que metrica a ser utilizada é CPU, o hpa irá monitorar a utilização do recurso e se a média de utilização for maior igual 50 será criado novas replicas respeitando a regras de minímo e maxímo de replicas.

```
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
    name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 1
  maxReplicas: 10
  metrics: 
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization 
          averageUtilization: 50
```

## Congigurando servidor de Metricas

[metrics-server](https://github.com/kubernetes-sigs/metrics-server)


minikube addons enable metrics-server