# StatefulSets

StatefulSet é a Objeto da API de carga de trabalho utilizado para gerenciar 
estado das aplicações.

Gerenciar a implantação e escalar um conjunto de PODs, fornece garantias sobre
a ordem e singularidade estes Pods.

Como uma implantação, um StatefulSet gerencia Pods que são baseados em um container indentico spec, uma implantação diferente, um StatefulSet preserva uma indentificação fixa para cada um dos Pods. Estes Pods são creadiados para o mesma spec, mas não são intercambíaveis: cada um tem uma indentificação independente que preserva através vários agendamentos.

Se você quer utilizar volumes de armazenamento para forcenecer persistencia para sua carga de trabalho, você pode utilizar Stateful com parte da solução. Embora 
Pods idividuais em um StatefulSet são sucetivel para falhas, o indentificador de Pod persistente combina facilmente volumes existente para para novos Pods que substitua qualquer Pod que teve uma falha.

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
    name: nginx-stefulset
spec:
    replicas: 1
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
        volumes:
            - name: nginx-volume
              persistentVolumeClaim:
                claimName: nginx-pvc
    selector:
        matchLabels:
            app: nginx-app-system
    serviceName: nginx-svc
```
