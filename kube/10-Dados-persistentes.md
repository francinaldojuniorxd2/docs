# Persistindo dados com volumes

- Todo arquivo criado dentro dos containers são perdidos 
assim que pod é encerrado, os pods são efêmeros, eles devem
estar prontos para serem trocados a qualquer momento.

- Para compartilhar um arquivo entre os containers do Pods
devemos criar um volume, o ciclo de vida de um volume é independente do container,
é vinculado diretamente ao Pod, se caso um dos containers no 
pod for interropindo o volume vai persistir.

```
apiVersion: v1
kind: Pod
metadata:
    name: pod-volume
spec: 
    containers:
        - name: nginx-firts
          image: nginx:latest
          volumeMounts:
            - mouthPath: /first-volume
              name: first-volume
        - name: nginx-second
          image: nginx:latest
          volumeMounts:
            - mounthPath: /second-volume
              name: first-volume
    volumes:
        - name: first-volume
          hostPath:
            path: /primeiro-volume
            type: Directory 
```

- No type do volume podemos atribuir dois tipos,
  - Directory : O diretorio que foi especificado no path já deve existir.
  - DirectoryOrCreate: Se não encontrar o directory especificado path será criado um novo.