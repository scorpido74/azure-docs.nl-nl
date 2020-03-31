---
title: Dynamisch een blijvend volume maken en gebruiken met Azure-schijven in Azure Kubernetes Service (AKS)
description: Meer informatie over het dynamisch maken van een blijvend volume met Azure-schijven in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 37fea36567866af69e832a1f7e3caff2a68477a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596960"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Dynamisch een blijvend volume maken en gebruiken met Azure-schijven in Azure Kubernetes Service (AKS)

Een blijvend volume vertegenwoordigt een stuk opslag dat is ingericht voor gebruik met Kubernetes-pods. Een persistent volume kan door een of meer pods worden gebruikt en kan dynamisch of statisch worden ingericht. In dit artikel ziet u hoe u dynamisch permanente volumes maken met Azure-schijven voor gebruik door één pod in een AKS-cluster (Azure Kubernetes Service).

> [!NOTE]
> Een Azure-schijf kan alleen worden gemonteerd met het type *ReadWriteOnce*van *de Toegangsmodus* , waardoor deze alleen beschikbaar is voor slechts één pod in AKS. Als u een blijvend volume moet delen in meerdere pods, gebruikt u [Azure Files][azure-files-pvc].

Zie [Opslagopties voor toepassingen in AKS voor][concepts-storage]meer informatie over Kubernetes-volumes.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

U hebt ook de Azure CLI-versie 2.0.59 of hoger geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

## <a name="built-in-storage-classes"></a>Ingebouwde opslagklassen

Een opslagklasse wordt gebruikt om te bepalen hoe een opslageenheid dynamisch wordt gemaakt met een aanhoudend volume. Zie [Kubernetes Storage Classes][kubernetes-storage-classes]voor meer informatie over Kubernetes-opslagklassen.

Elk AKS-cluster bevat twee vooraf gemaakte opslagklassen, beide geconfigureerd om met Azure-schijven te werken:

* In *de standaardopslagklasse* wordt een standaard Azure-schijf voorzien.
    * Standaardopslag wordt ondersteund door HDD's en levert kosteneffectieve opslag terwijl deze nog steeds performant is. Standard-schijven zijn ideaal voor een kostenefficiënte werkbelasting voor ontwikkelen en testen.
* De *beheerde premium* opslagklasse voorziet in een premium Azure-schijf.
    * Premium-schijven worden ondersteund door hoogwaardige schijven met een lage latentie op basis van SSD. Ideaal voor virtuele machines met een productiewerkbelasting. Als de AKS-knooppunten in uw cluster premiumopslag gebruiken, selecteert u de *klasse managed-premium.*
    
Met deze standaardopslagklassen u de volumegrootte die is gemaakt niet bijwerken. Als u deze mogelijkheid wilt inschakelen, voegt u de *regel allowVolumeExpansion: true* toe aan een van de standaardopslagklassen of maakt u een eigen aangepaste opslagklasse. U een bestaande opslagklasse `kubectl edit sc` bewerken met de opdracht. Zie [Opslagopties voor toepassingen in AKS voor][storage-class-concepts]meer informatie over opslagklassen en het maken van uw eigen beheer.

Gebruik de [kubectl get sc-opdracht][kubectl-get] om de vooraf gemaakte opslagklassen te bekijken. In het volgende voorbeeld worden de vooraf gemaakte opslagklassen weergegeven die beschikbaar zijn in een AKS-cluster:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Persistente volumeclaims worden opgegeven in GiB, maar door Azure beheerde schijven worden door SKU gefactureerd voor een specifieke grootte. Deze SKU's variëren van 32GiB voor S4- of P4-schijven tot 32TiB voor S80- of P80-schijven (in preview). De doorvoer- en IOPS-prestaties van een door Premium beheerde schijf zijn afhankelijk van zowel de SKU als de instantiegrootte van de knooppunten in het AKS-cluster. Zie [Prijzen en prestaties van beheerde schijven][managed-disk-pricing-performance]voor meer informatie .

## <a name="create-a-persistent-volume-claim"></a>Een permanente volumeclaim maken

Een persistent volume claim (PVC) wordt gebruikt om automatisch opslag te verstrekken op basis van een opslagklasse. In dit geval kan een PVC een van de vooraf gemaakte opslagklassen gebruiken om een standaard of premium Azure-beheerde schijf te maken.

Maak een `azure-premium.yaml`bestand met de naam en kopieer in het volgende manifest. De claim vraagt `azure-managed-disk` een schijf met de naam *van 5 GB* groot met *ReadWriteOnce* toegang. De *beheerde premium* opslagklasse is opgegeven als de opslagklasse.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Als u een schijf wilt `storageClassName: default` maken die standaardopslag gebruikt, gebruikt u in plaats van *managed-premium*.

Maak de permanente volumeclaim met de [opdracht kubectl apply][kubectl-apply] en geef uw *azure-premium.yaml-bestand* op:

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Gebruik het aanhoudende volume

Zodra de permanente volumeclaim is gemaakt en de schijf is ingericht, kan een pod worden gemaakt met toegang tot de schijf. In het volgende manifest wordt een basis-NGINX-pod gemaakt die de permanente volumeclaim `/mnt/azure`met de naam *azure-managed-disk* gebruikt om de Azure-schijf op het pad te monteren. Voor Windows Server-containers (momenteel in preview in AKS) geeft u een *mountPath* op met behulp van de Windows-padconventie, zoals *'D:'.*

Maak een `azure-pvc-disk.yaml`bestand met de naam en kopieer in het volgende manifest.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Maak de pod met de opdracht [kubectl apply,][kubectl-apply] zoals in het volgende voorbeeld wordt weergegeven:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

U hebt nu een lopende pod met `/mnt/azure` uw Azure-schijf in de map. Deze configuratie kan worden gezien bij `kubectl describe pod mypod`het inspecteren van uw pod via , zoals weergegeven in het volgende verkorte voorbeeld:

```console
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>Een back-up maken van een blijvend volume

Als u een back-up wilt maken van de gegevens in uw permanente volume, maakt u een momentopname van de beheerde schijf voor het volume. U deze momentopname vervolgens gebruiken om een herstelde schijf te maken en aan pods te koppelen als een middel om de gegevens te herstellen.

Download eerst de volumenaam `kubectl get pvc` bij de opdracht, bijvoorbeeld voor de *PVC-naam azure-managed-disk:*

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Deze volumenaam vormt de onderliggende Azure-schijfnaam. Vraag naar de schijf-id met [de AZ-schijflijst][az-disk-list] en geef de naam van het PVC-volume op, zoals in het volgende voorbeeld wordt weergegeven:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Gebruik de schijf-id om een momentopnameschijf te maken met [az-momentopname maken][az-snapshot-create]. In het volgende voorbeeld wordt een momentopname gemaakt met de naam *pvcSnapshot* in dezelfde brongroep als het AKS-cluster *(MC_myResourceGroup_myAKSCluster_eastus*). U problemen met machtigingen ondervinden als u momentopnamen maakt en schijven herstelt in brongroepen waartoe het AKS-cluster geen toegang heeft.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Afhankelijk van de hoeveelheid gegevens op uw schijf kan het enkele minuten duren voordat de momentopname is gemaakt.

## <a name="restore-and-use-a-snapshot"></a>Een momentopname herstellen en gebruiken

Als u de schijf wilt herstellen en deze wilt gebruiken met een Kubernetes-pod, gebruikt u de momentopname als bron wanneer u een schijf maakt met [az-schijf maken.][az-disk-create] Deze bewerking behoudt de oorspronkelijke bron als u vervolgens toegang moet krijgen tot de oorspronkelijke momentopname van gegevens. In het volgende voorbeeld wordt een schijf gemaakt met de naam *pvcRestored* van de momentopname *pvcSnapshot:*

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Als u de herstelde schijf met een pod wilt gebruiken, geeft u de id van de schijf op in het manifest. Haal de schijf-ID op met de opdracht [AZ-schijfshow.][az-disk-show] In het volgende voorbeeld wordt de schijf-id voor *pvcRestored* gemaakt in de vorige stap:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Maak een podmanifest met de naam `azure-restored.yaml` en geef de schijf-URI op die in de vorige stap is verkregen. In het volgende voorbeeld wordt een eenvoudige NGINX-webserver gemaakt, waarbij de herstelde schijf is gemonteerd als een volume op */mnt/azure:*

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Maak de pod met de opdracht [kubectl apply,][kubectl-apply] zoals in het volgende voorbeeld wordt weergegeven:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

U kunt `kubectl describe pod mypodrestored` de details van de pod weergeven, zoals het volgende verkorte voorbeeld dat de volumegegevens weergeeft:

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor opslag en back-ups in AKS voor][operator-best-practices-storage]bijbehorende aanbevolen procedures.

Meer informatie over kubernetes-permanente volumes met Azure-schijven.

> [!div class="nextstepaction"]
> [Kubernetes-plug-in voor Azure-schijven][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
