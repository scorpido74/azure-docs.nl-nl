---
title: Een statisch volume maken voor pods in Azure Kubernetes Service (AKS)
description: Meer informatie over het handmatig maken van een volume met Azure-schijven voor gebruik met een pod in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 17795ae696c0d710f099a5c21aa754fc925953ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047942"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Handmatig een volume maken en gebruiken met Azure-schijven in Azure Kubernetes Service (AKS)

Containergebaseerde toepassingen moeten vaak toegang krijgen tot en blijven gegevens in een extern gegevensvolume. Als één pod toegang tot opslag nodig heeft, u Azure-schijven gebruiken om een native volume voor toepassingsgebruik weer te geven. In dit artikel ziet u hoe u handmatig een Azure-schijf maakt en deze aan een pod in AKS koppelt.

> [!NOTE]
> Een Azure-schijf kan slechts op één pod tegelijk worden gemonteerd. Als u een blijvend volume moet delen in meerdere pods, gebruikt u [Azure Files][azure-files-volume].

Zie [Opslagopties voor toepassingen in AKS voor][concepts-storage]meer informatie over Kubernetes-volumes.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

U hebt ook de Azure CLI-versie 2.0.59 of hoger geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

## <a name="create-an-azure-disk"></a>Een Azure-schijf maken

Wanneer u een Azure-schijf maakt voor gebruik met AKS, u de schijfbron maken in de brongroep **knooppunt.** Met deze aanpak kan het AKS-cluster toegang krijgen tot de schijfbron en deze beheren. Als u de schijf in plaats daarvan in een afzonderlijke brongroep maakt, moet u `Contributor` de AKS-serviceprincipal (Azure Kubernetes Service) voor uw cluster de rol aan de brongroep van de schijf toekennen. U ook de beheerde identiteit met het systeem gebruiken voor machtigingen in plaats van de serviceprincipal. Zie [Beheerde identiteiten gebruiken voor](use-managed-identity.md)meer informatie .

Maak voor dit artikel de schijf in de brongroep knooppunt. Download eerst de naam van de resourcegroep met `--query nodeResourceGroup` de opdracht [az aks show][az-aks-show] en voeg de queryparameter toe. In het volgende voorbeeld wordt de knooppuntbrongroep voor de AKS-clusternaam *myAKSCluster* in de naam *myResourceGroup*van de resourcegroep:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Maak nu een schijf met de opdracht [AZ-schijf maken.][az-disk-create] Geef de naam van de knooppuntbrongroep op die is verkregen in de vorige opdracht en vervolgens een naam voor de schijfbron, zoals *myAKSDisk.* In het volgende voorbeeld wordt een *20*GiB-schijf gemaakt en wordt de id van de schijf uitgevoerd nadat deze is gemaakt. Als u een schijf wilt maken voor gebruik met Windows Server-containers `--os-type windows` (momenteel in preview in AKS), voegt u de parameter toe om de schijf correct op te maken.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Azure-schijven worden gefactureerd door SKU voor een specifieke grootte. Deze SKU's variëren van 32GiB voor S4- of P4-schijven tot 32TiB voor S80- of P80-schijven (in preview). De doorvoer- en IOPS-prestaties van een door Premium beheerde schijf zijn afhankelijk van zowel de SKU als de instantiegrootte van de knooppunten in het AKS-cluster. Zie [Prijzen en prestaties van beheerde schijven][managed-disk-pricing-performance].

De schijfbron-id wordt weergegeven zodra de opdracht is voltooid, zoals wordt weergegeven in de volgende voorbeelduitvoer. Deze schijf-ID wordt gebruikt om de schijf in de volgende stap te monteren.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Schijf als volume monteren

Als u de Azure-schijf in uw pod wilt monteren, configureert u het volume in de containerspec. Maak een nieuw bestand met de `azure-disk-pod.yaml` volgende inhoud. Update `diskName` met de naam van de schijf `diskURI` die in de vorige stap is gemaakt en met de schijf-id die wordt weergegeven in de uitvoer van de opdracht schijf maken. Werk desgewenst `mountPath`het pad bij waar de Azure-schijf in de pod is gemonteerd. Voor Windows Server-containers (momenteel in preview in AKS) geeft u een *mountPath* op met behulp van de Windows-padconventie, zoals *'D:'.*

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Gebruik `kubectl` de opdracht om de pod te maken.

```console
kubectl apply -f azure-disk-pod.yaml
```

U hebt nu een lopende pod met `/mnt/azure`een Azure-schijf gemonteerd op . U kunt `kubectl describe pod mypod` ervoor controleren of de schijf is gemonteerd. De volgende verkorte voorbeelduitvoer toont het volume dat in de container is gemonteerd:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor opslag en back-ups in AKS voor][operator-best-practices-storage]bijbehorende aanbevolen procedures.

Zie de [Kubernetes-plug-in voor Azure Disks voor][kubernetes-disks]meer informatie over AKS-clusters.

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
