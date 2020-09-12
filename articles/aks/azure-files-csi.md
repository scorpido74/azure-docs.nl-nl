---
title: Gebruik de Stuur Programma's van de container Storage interface (CSI) voor Azure Files op de Azure Kubernetes-service (AKS)
description: Meer informatie over het gebruik van de Stuur Programma's voor de container Storage interface (CSI) voor Azure Files in een AKS-cluster (Azure Kubernetes service).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 018275b6db4c2d2d1059f35077f74a6f45ec3ba9
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422048"
---
# <a name="use-the-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Gebruik de Azure Filesing container Storage interface (CSI)-Stuur Programma's in azure Kubernetes service (AKS) (preview)
Het Azure Files CSI-stuur programma is een stuur programma voor [CSI-specificatie](https://github.com/container-storage-interface/spec/blob/master/spec.md) dat door aks wordt gebruikt voor het beheren van de levens cyclus van Azure files-shares. 

De container Storage-interface (CSI) is een standaard voor het beschikbaar maken van wille keurige blok-en opslag systemen in containers op Kubernetes. Door CSI te maken en te gebruiken, kan Azure Kubernetes service (AKS) nu invoeg toepassingen schrijven, implementeren en laten lopen, waardoor er nieuwe of verbeterde bestaande opslag systemen in Kubernetes worden weer gegeven zonder dat u de kern Kubernetes hoeft aan te hoeven en te wachten op de release cycli.

Als u een AKS-cluster wilt maken met de ondersteuning van CSI-Stuur Programma's, raadpleegt u de [Stuur Programma's voor de Azure-schijven inschakelen en Azure files op AKS](csi-storage-drivers.md).

>[!NOTE]
> *"In-structuur Stuur Programma's"* verwijzen naar de huidige opslag Stuur Programma's die deel uitmaken van de kern kubernetes-code vs. de nieuwe CSI-Stuur Programma's die zijn ingebouwd als invoeg toepassingen.

## <a name="use-a-persistent-volume-pv-with-azure-files"></a>Een permanent volume (HW) gebruiken met Azure Files

Een [permanent volume](concepts-storage.md#persistent-volumes) vertegenwoordigt een opslag ruimte die is ingericht voor gebruik met Kubernetes peul. Een permanent volume kan worden gebruikt door een of meer peulen en kan dynamisch of statisch worden ingericht. Als meerdere peulen gelijktijdig gelijktijdige toegang tot hetzelfde opslag volume nodig hebben, kunt u Azure Files gebruiken om verbinding te maken met het [SMB-protocol (Server Message Block)][smb-overview]. In dit artikel wordt beschreven hoe u een Azure Files share dynamisch kunt maken voor gebruik door meerdere peulingen in een Azure Kubernetes service-cluster (AKS). Zie voor statische inrichting [hand matig een volume maken en gebruiken met Azure Files share](azure-files-volume.md).

Zie [opslag opties voor toepassingen in AKS][concepts-storage]voor meer informatie over Kubernetes-volumes.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-using-the-built-in-storage-classes"></a>Azure Files PVs dynamisch maken met behulp van de ingebouwde opslag klassen
Een opslag klasse wordt gebruikt om te bepalen hoe een Azure-bestands share wordt gemaakt. Er wordt automatisch een opslag account gemaakt in de [knooppunt resource groep][node-resource-group] voor gebruik met de opslag klasse om de Azure-bestands shares op te slaan. Kies een van de volgende [Azure Storage-redundantie][storage-skus] voor *skuName*:

* *Standard_LRS* -standaard lokaal redundante opslag
* Geografisch redundante opslag met *Standard_GRS* standaard
* Redundante opslag met *Standard_ZRS* -standaard zone
* *Standard_RAGRS* -standaard geografisch redundante opslag met lees toegang
* Lokaal redundante opslag *Premium_LRS* -Premium

> [!NOTE]
> Azure Files Premium-opslag biedt een minimale Premium-bestands share van 100 GB.

Wanneer u Storage CSI-Stuur Programma's gebruikt op AKS, zijn er twee extra ingebouwde `StorageClasses` die gebruikmaken van de **Azure files CSI-opslag Stuur Programma's**. De extra CSI-opslag klassen worden samen met het cluster gemaakt naast de standaard opslag klassen in de structuur.

- `azurefile-csi` -Maakt gebruik van Azure Standard-opslag om een Azure-bestands share te maken. 
- `azurefile-csi-premium` -Maakt gebruik van Azure Premium Storage voor het maken van een Azure-bestands share. 

Het beleid voor het opnieuw claimen van beide opslag klassen zorgt ervoor dat de onderliggende Azure-bestands share wordt verwijderd wanneer het respectieve permanente volume wordt verwijderd. De opslag klassen configureren ook de bestands shares die kunnen worden uitgebreid, u hoeft alleen de permanente volume claim te bewerken met de nieuwe grootte.

Als u gebruik wilt maken van deze opslag klassen, maakt u een [permanente volume claim (PVC)](concepts-storage.md#persistent-volume-claims) en de bijbehorende pod die ernaar verwijzen en hiervan gebruikmaken. Een permanente volume claim (PVC) wordt gebruikt voor het automatisch inrichten van opslag op basis van een opslag klasse. Een PVC kan een van de vooraf gemaakte opslag klassen of een door de gebruiker gedefinieerde opslag klasse gebruiken om een Azure Files share te maken voor de gewenste SKU en grootte. Wanneer u een pod-definitie maakt, wordt de permanente volume claim opgegeven om de gewenste opslag aan te vragen.

Maak een [voor beeld van een permanente volume claim en pod die de huidige datum `outfile` afdrukt in een](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) met de opdracht [kubectl apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Nadat de pod de status actief heeft, kunt u valideren of de bestands share correct is gekoppeld door de onderstaande opdracht uit te voeren en de uitvoer te controleren `outfile` : 

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Een aangepaste opslag klasse maken

De standaard-opslag klassen zijn de meest voorkomende scenario's, maar niet alle. In sommige gevallen wilt u misschien uw eigen opslag klasse aanpassen met uw eigen para meters. Gebruik bijvoorbeeld het volgende manifest om de `mountOptions` Bestands share te configureren.

De standaard waarde voor *file mode* en *dirMode* is *0777* voor gekoppelde bestands shares van Kubernetes. U kunt de verschillende koppelings opties voor het opslag klassen object opgeven.

Maak een bestand `azure-file-sc.yaml` met de naam en plak het volgende manifest: 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

Maak de opslag klasse met de opdracht [kubectl apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Het stuur programma voor Azure files CSI ondersteunt het maken [van moment opnamen van permanente volumes](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) en de onderliggende bestands shares. 

Maak een [volume snap shot-klasse](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) met de opdracht [kubectl apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Maak een [moment opname](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) van een volume van de PVC [die dynamisch aan het begin van deze zelf studie is gemaakt](#dynamically-create-azure-files-pvs-using-the-built-in-storage-classes) `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Controleer of de moment opname correct is gemaakt:

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume-pv"></a>Het formaat van een permanent volume wijzigen (HW)

U kunt een groter volume aanvragen voor een PVC. Bewerk het PVC-object en geef een grotere grootte op. Met deze wijziging wordt de uitbrei ding geactiveerd van het onderliggende volume dat een back-up van de PersistentVolume maakt. 

> [!NOTE] 
> Er wordt nooit een nieuwe PersistentVolume gemaakt om te voldoen aan de claim. In plaats daarvan wordt het formaat van een bestaand volume gewijzigd.

In AKS biedt de ingebouwde `azurefile-csi` opslag klasse al ondersteuning voor uitbrei ding, dus maakt gebruik van het [PVC dat eerder is gemaakt met deze opslag klasse](#dynamically-create-azure-files-pvs-using-the-built-in-storage-classes). Het PVC heeft een 100Gi-bestands share aangevraagd, we kunnen controleren dat door het volgende uit te voeren:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Vouw het PVC uit door het veld te verg Roten `spec.resources.requests.storage` :

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Controleer of het PVC en het bestands systeem binnen de pod de nieuwe grootte tonen:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```

## <a name="windows-containers"></a>Windows-containers

Het stuur programma voor Azure files CSI ondersteunt ook Windows-knoop punten en-containers. Als u Windows-containers wilt gebruiken, volgt u de [zelf studie over Windows-containers](windows-container-cli.md) om een Windows-knooppunt groep toe te voegen.

Zodra u een Windows-knooppunt groep hebt, kunt u gebruikmaken van de ingebouwde opslag klassen, `azurefile-csi` of aangepaste maken. U kunt een voor beeld [van een op Windows gebaseerde stateful set](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) implementeren waarmee tijds tempels worden opgeslagen in een bestand `data.txt` door de onderstaande implementatie uit te voeren met de opdracht [kubectl apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

De inhoud van het volume valideren door het volgende uit te voeren:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Volgende stappen

- Zie [use Azure disks with CSI drivers](azure-disk-csi.md)(Engelstalig) voor meer informatie over het gebruik van het stuur programma CSI voor Azure-schijven.
- Zie [Aanbevolen procedures voor opslag en back-ups in azure Kubernetes service (AKS)][operator-best-practices-storage] voor meer informatie over aanbevolen procedures voor opslag.


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md