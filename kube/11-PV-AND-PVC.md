# Pv and PVC

- PVs ( PersistentVolume ) são volumes plugins como volumes,
mas tem um ciclo de vida independe dos Pods que utilza PV.
Este API object captura os detalhes da implementação do armazenamento, seja NFS, iSCSI, ou provedor de cloud.

- PVC ( PersistentVolumeClaim ) é um requesição de armazenamento
por um usuário. Pods consome recurso de Node e os PVCs consome
recursos de um PV. Pods pode requisistar níveis específicos de recursos (CPU e Memoria). Claims pode especificar requisitos size e acess modes ( e.g, eles podem ser mountados ReadWriterOnce, ReadOnlyMany ou ReadWriteMany)

- Enquanto PersistentVolumeClaims permiti a usuário consumir os recursos de armazenamento da abstração. é comum que o usuário precise PersistentVolumes com varias propriedades. tal como performace, para diferentes problemas. Administrador de cluster precisa ser capaz de oferecer uma varidade de PersistentVolumes que diferem de varias maneiras com size e acess modes, sem expor os detalhes como os volumes são implementados.

# Lifecycle of a volume and claim

Pvs são recursos de um cluster. PVcs são requisições para esses recursos e também atua como verificador de recursos. A intereção 
entre Pvs and PVCs seguem o ciclo de vida.

### Provisioning 

Exitem duas manineira de provisisonar os PVs: Statically or dynamically.

### Static 

Um administrador de cluster cria um numero de Pvs. Eles carregam detalhes do real storage. Que está disponivel para usuário do cluster. Eles exitem no Kubernetes API e está disponível para consumo.

### Dynamic

Quando nenhum dos static PVs é criado pelo adminitrador corresponde ao PersistenVolumeClaim do usuário o cluster pode tentar provivisionar dinamicamente um volume especialmente para o PVC. Este provisionamento é baseado no StorageClasses: o PVC deve requisistar um storage class e o administrador deve ter criado e configurado que aquela classe para dinâmica. Claims que requisita uma class "" é efectivamente desabilitado o dinamica provisionamento deles.

Para habilitar o provisionamento dinâmico de armazenamento baseado no storage class, o administrador do cluster precisa habilitar o DefaultStorageClass admission controler na API server. 

### Using 
 
 - Pods utiliza claims como volumes. O cluster inpeciona o Claim para encontrar o bound volume e montar para um Pod. Para volumes que suporta multiplos modos de acess, o usuário specifica qual modo é desejado quando utilizar seu claim como um volume em um Pod.

 Uma vez um usuário tem um claim que claim é bound, o bound PV pertece ao usuário por quanto tempo ele precisar.

 ### Storage Object in Use Protection

O proposito do Storage Object em utilizar recursos de proteção  é garantir que PersistentVolumeClaims(PVCs) em uso ativo por um Pod e PersistentVolume(PV) que são bound(vinculados) para PVCs não ser removidos do sistema, com isso pode resultar perda de dados.

Se um usuário deletar um PVC em modo ativo por um Pod, o PVC não é removido imediatamente. Remoçao PVC é postergada até o PVC não é ativamente utilizado por qualquer Pods. 


- No cluster em produção, você não utilizaria hostPath. Em vez disso um administrador de cluster deve provisionar um recurso de rede como um Google Compute Egine persistente disk, um NFS compartilhado, ou um Amazon Elastik Block volume store. 


### Capacity 

Geralmente, um PV vai ter uma capacidade especifica de storage. Isto é definido utilizando PV's capacity atributo.

### Volumes Mode

Kubernetes suporta dois volumeModes de PersistentVolumes : Filesystem e Block.

volumeMode é um parametro opicional da API. Filesystem é o padrão quando o parametro é omitido.

Um volume com volumeMode: Filesystem é mountado em Pod  em um diretorio. se o volume é apoaido por um block e o device é vazio. kubernetes cria um filesystem no dispositivo apos montar se for a primeira vez.

### Acess Modes.

Um PersistentVolume pode ser montado em um host de qualquer forma pelo provedor de recurso.

Os acess modes são:

ReadWriteOnce

    Permite multiples pods acessar o volume quando os pods são executados no mesmo node.

ReadOnlyMany

    O volume pode ser montado como read-only para muitos node.

ReadWriteMany

    O volume pode ser montado como read-write por muitos nodes.

ReadWriteOncePod

    O volume pode ser montado com read-write por um simples Pod. Utilizar modo de acesso ReadWriteOncePod se você quer garantir que somente um pode através todo cluster pode ler que PVC ou escrever. Este somente é suportado para CSI volumes Kubernetes version 1.22+

RWO - RedWriteOnce
ROX - ReadOnlyMany
RWX - ReadWriteMany
RWOP - ReadWriteOncePod 

```

apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```
A configuração especifica do arquivo que o volume está /mnt/data no cluster Node. A configuração também especifica o espaço de 10 Gigabytes e o accessMode ReadWriteOnce,que significa que o volume pode ser montado como read-write por um simples Node.

- Criando o diretorio no nosso cluster para ser o nosso disco local.

```
minikube ssh
sudo mkdir /mnt/data
sudo sh -c "echo 'Hello from Kubernetes storage' > /mnt/data/index.html"
```

- Para vizualizar as informações do PersistentVolume

```
kubectl apply -f pv-task.yaml
```

- A saída mostra que o PersistentVolume tem status de Avaible. Isso significa que ainda não foi vículado a um Claim.

# Create um PersistentVolumeClaim

A proxima etapa é para criar um PersistentVolumeClaim. Pods utiliza PersistentVolumeClaims para requisistar physical storage. Neste exercicio, você cria um PersistentVolumeClaim que requisista um volume de um gigabyte que pode prover acesso read-write para pela menos um Node.


```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nginx-pvc
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

# Aplicando PVC em um Stateful

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
