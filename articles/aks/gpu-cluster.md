---
title: Gpu's gebruiken op Azure Kubernetes Service (AKS)
description: Meer informatie over het gebruik van GPU's voor high performance compute of grafisch intensieve workloads op Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/27/2020
ms.openlocfilehash: 242fefb3b153d11e23d66f26049d0b68c0a4bf4a
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383987"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>GPU's gebruiken voor rekenintensieve workloads op Azure Kubernetes Service (AKS)

Grafische verwerkingseenheden (GPU's) worden vaak gebruikt voor computerintensieve workloads, zoals grafische en visualisatieworkloads. AKS ondersteunt het maken van gpu-enabled node pools om deze compute-intensieve workloads in Kubernetes uit te voeren. Zie [GPU-geoptimaliseerde VM-formaten in Azure][gpu-skus]voor meer informatie over beschikbare VM's met GPU.For more information on available GPU-enabled VMMs, see GPU optimized VM sizes in Azure . Voor AKS-knooppunten raden we een minimale grootte van *Standard_NC6*aan.

> [!NOTE]
> VM's met GPU-functionaliteit bevatten gespecialiseerde hardware die onderhevig is aan hogere prijzen en regiobeschikbaarheid. Zie voor meer informatie de [prijstool][azure-pricing] en [de beschikbaarheid van de regio.][azure-availability]

Momenteel is het gebruik van gpu-enabled knooppuntpools alleen beschikbaar voor Linux-knooppuntpools.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt met knooppunten die GPU's ondersteunen. Uw AKS-cluster moet Kubernetes 1.10 of hoger uitvoeren. Als u een AKS-cluster nodig hebt dat aan deze vereisten voldoet, raadpleegt u het eerste gedeelte van dit artikel om [een AKS-cluster](#create-an-aks-cluster)te maken.

U hebt ook de Azure CLI-versie 2.0.64 of hoger geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Als u een AKS-cluster nodig hebt dat voldoet aan de minimumvereisten (GPU-node en Kubernetes-versie 1.10 of hoger), voert u de volgende stappen uit. Als u al een AKS-cluster hebt dat aan deze vereisten voldoet, [gaat u naar de volgende sectie](#confirm-that-gpus-are-schedulable).

Maak eerst een resourcegroep voor het cluster met de opdracht [Az-groep maken.][az-group-create] In het volgende voorbeeld wordt een resourcegroepnaam *myResourceGroup* in het *eastus-gebied* gemaakt:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak nu een AKS-cluster met de opdracht [az aks create.][az-aks-create] In het volgende voorbeeld wordt een cluster `Standard_NC6`met één knooppunt van grootte gemaakt:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Haal de referenties voor uw AKS-cluster op met de opdracht [az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-drivers"></a>NVIDIA-stuurprogramma's installeren

Voordat de GPU's in de knooppunten kunnen worden gebruikt, moet u een DaemonSet implementeren voor de NVIDIA-apparaatplug-in. Deze DaemonSet draait een pod op elk knooppunt om de vereiste stuurprogramma's voor de GPU's te leveren.

Maak eerst een naamruimte met de [opdracht kubectl create namespace,][kubectl-create] zoals *gpu-resources:*

```console
kubectl create namespace gpu-resources
```

Maak een bestand met de naam *nvidia-device-plugin-ds.yaml* en plak het volgende YAML-manifest. Dit manifest wordt geleverd als onderdeel van de [NVIDIA-apparaatplugint voor Kubernetes-project][nvidia-github].

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

Gebruik nu de [opdracht kubectl apply om][kubectl-apply] de DaemonSet te maken en te bevestigen dat de NVIDIA-apparaatplug-in met succes is gemaakt, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="confirm-that-gpus-are-schedulable"></a>Controleren of GPU's kunnen worden gescheduld

Als uw AKS-cluster is gemaakt, moet u controleren of GPU's in Kubernetes kunnen worden gescheduld. Vermeld eerst de knooppunten in uw cluster met de opdracht [kubectl get nodes:][kubectl-get]

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Gebruik nu de [opdracht kubectl describe node][kubectl-describe] om te bevestigen dat de GPU's kunnen worden gescheduld. Onder de sectie *Capaciteit* moet `nvidia.com/gpu:  1`de GPU worden vermeld als .

Het volgende verkorte voorbeeld laat zien dat een GPU beschikbaar is op het knooppunt met de naam *aks-nodepool1-18821093-0:*

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Een GPU-workload uitvoeren

Als u de GPU in actie wilt zien, plant u een GPU-workload met de juiste resourceaanvraag. Laten we in dit voorbeeld een [Tensorflow-taak](https://www.tensorflow.org/) uitvoeren ten opzichte van de [MNIST-gegevensset](http://yann.lecun.com/exdb/mnist/).

Maak een bestand met de naam *samples-tf-mnist-demo.yaml* en plak het volgende YAML-manifest. Het volgende taakmanifest bevat `nvidia.com/gpu: 1`een resourcelimiet van :

> [!NOTE]
> Als u een fout in de verkeerde versie ontvangt wanneer u stuurprogramma's aanroept, zoals CUDA-stuurprogrammaversie is onvoldoende voor CUDA runtime-versie, bekijkt u de compatibiliteitsgrafiek van de NVIDIA-drivermatrix -[https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: microsoft/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Gebruik de [opdracht kubectl toepassen][kubectl-apply] om de taak uit te voeren. Met deze opdracht wordt het manifestbestand ontreeerd en worden de gedefinieerde Kubernetes-objecten gemaakt:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>De status en uitvoer van de GPU-workload weergeven

Controleer de voortgang van de taak met behulp van de [opdracht kubectl get jobs][kubectl-get] met het `--watch` argument. Het kan enkele minuten duren om eerst de afbeelding te trekken en de gegevensset te verwerken. Wanneer de kolom *VOLTOOIINGen* *1/1*wordt weergegeven, is de taak voltooid. Sluit `kubetctl --watch` de opdracht af met *Ctrl-C:*

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Als u wilt kijken naar de uitvoer van de gpu-belasting, krijgt u eerst de naam van de pod met de opdracht [kubectl get pods:][kubectl-get]

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Gebruik nu de opdracht [kubectl-logboeken][kubectl-logs] om de podlogs weer te geven. In het volgende voorbeeld pod logs bevestigen dat `Tesla K80`de juiste GPU-apparaat is ontdekt, . Geef de naam op voor uw eigen pod:

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de bijbehorende Kubernetes-objecten wilt verwijderen die in dit artikel zijn gemaakt, gebruikt u de [taakopdracht kubectl verwijderen][kubectl delete] als volgt:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Volgende stappen

Zie [Apache Spark-taken uitvoeren op AKS][aks-spark]als u Apache Spark-taken wilt uitvoeren.

Zie [Kubeflow Labs][kubeflow-labs]voor meer informatie over het uitvoeren van machine learning -workloads (ML) op Kubernetes.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
