---
title: Gebruik de container Storage interface (CSI)-Stuur Programma's voor Azure-schijven op de Azure Kubernetes-service (AKS)
description: Meer informatie over het gebruik van de container Storage interface (CSI)-Stuur Programma's voor Azure-schijven in een Azure Kubernetes service-cluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 4b5ccd2712a95f5f020daa0161f1b5908a38a62e
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422055"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Gebruik de Azure Disk container Storage interface (CSI)-Stuur Programma's in azure Kubernetes service (AKS) (preview)
Het Azure Disk CSI-stuur programma is een stuur programma voor [CSI-specificatie](https://github.com/container-storage-interface/spec/blob/master/spec.md) dat door aks wordt gebruikt voor het beheren van de levens cyclus van Azure-schijven. 

De container Storage-interface (CSI) is een standaard voor het beschikbaar maken van wille keurige blok-en opslag systemen in containers op Kubernetes. Door het gebruik van CSI, Azure Kubernetes service (AKS) kunnen invoeg toepassingen die nieuwe of verbeterde bestaande opslag systemen in Kubernetes maken, implementeren en herhalen, zonder dat u de kern Kubernetes code hoeft aan te passen en de release cycli moet wachten.

Als u een AKS-cluster wilt maken met de ondersteuning van CSI-Stuur Programma's, raadpleegt u de [Stuur Programma's voor de Azure-schijven inschakelen en Azure files op AKS](csi-storage-drivers.md).

>[!NOTE]
> *"In-structuur Stuur Programma's"* verwijzen naar de huidige opslag Stuur Programma's die deel uitmaken van de kern kubernetes-code vs. de nieuwe CSI-Stuur Programma's die zijn ingebouwd als invoeg toepassingen.

## <a name="use-csi-persistent-volumes-pv-with-azure-disks"></a>Vaste volumes (PV) gebruiken met Azure-schijven 

Een [permanent volume](concepts-storage.md#persistent-volumes) vertegenwoordigt een opslag ruimte die is ingericht voor gebruik met Kubernetes peul. Een permanent volume kan worden gebruikt door een of meer peulen en kan dynamisch of statisch worden ingericht. In dit artikel wordt beschreven hoe u dynamisch permanente volumes maakt met Azure-schijven voor gebruik door één pod in een Azure Kubernetes service-cluster (AKS). Zie voor statische inrichting [hand matig een volume maken en gebruiken met Azure-schijven](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Zie [opslag opties voor toepassingen in AKS][concepts-storage]voor meer informatie over Kubernetes-volumes.

## <a name="dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes"></a>Dynamisch een Azure-schijf PVs maken met behulp van de ingebouwde opslag klassen

Een opslag klasse wordt gebruikt om te definiëren hoe een opslag eenheid dynamisch wordt gemaakt met een permanent volume. Zie [Kubernetes Storage klassen][kubernetes-storage-classes](Engelstalig) voor meer informatie over Kubernetes-opslag klassen. Wanneer u Storage CSI-Stuur Programma's gebruikt op AKS, zijn er twee extra ingebouwde voor het gebruik van `StorageClasses` de **Azure Disk CSI-opslag Stuur Programma's**. De extra CSI-opslag klassen worden samen met het cluster gemaakt naast de standaard opslag klassen in de structuur.

>[!NOTE]
> *"In-structuur Stuur Programma's"* verwijzen naar de huidige opslag Stuur Programma's die deel uitmaken van de kern kubernetes-code vs. CSI-Stuur Programma's die invoeg toepassingen zijn.

- `managed-csi` -Gebruikt Azure StandardSSD lokaal redundante opslag (LRS) voor het maken van een beheerde schijf.
- `managed-csi-premium` -Maakt gebruik van Azure Premium lokaal redundante opslag (LRS) voor het maken van een beheerde schijf. 

Het beleid voor het opnieuw claimen in beide opslag klassen zorgt ervoor dat de onderliggende Azure-schijf wordt verwijderd wanneer het respectieve permanente volume wordt verwijderd. De opslag klassen configureren ook de permanente volumes die kunnen worden uitgebreid. u hoeft alleen de permanente volume claim te bewerken met de nieuwe grootte.

Als u deze opslag klassen wilt gebruiken, hoeft u alleen een [permanente volume claim (PVC)](concepts-storage.md#persistent-volume-claims) en de bijbehorende pod te maken waarin deze worden verwezen en gebruikt. Een permanente volume claim (PVC) wordt gebruikt voor het automatisch inrichten van opslag op basis van een opslag klasse. Een PVC kan een van de vooraf gemaakte opslag klassen of een door de gebruiker gedefinieerde opslag klasse gebruiken om een door Azure beheerde schijf te maken voor de gewenste SKU en grootte. Wanneer u een pod-definitie maakt, wordt de permanente volume claim opgegeven om de gewenste opslag aan te vragen.

Maak een voor beeld van een pod en een respectievelijke permanente volume claim met de opdracht [kubectl apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Nadat de Pod is uitgevoerd, maakt u een nieuw bestand met de naam `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

U kunt nu valideren of de schijf correct is gekoppeld door de onderstaande opdracht uit te voeren en te controleren of het `test.txt` bestand in de uitvoer wordt weer gegeven: 

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Een aangepaste opslag klasse maken

De standaard-opslag klassen zijn de meest voorkomende scenario's, maar niet alle. In sommige gevallen wilt u misschien uw eigen opslag klasse aanpassen met uw eigen para meters. Voor exemplify hebben we een scenario waarin u de kunt wijzigen `volumeBindingMode` . 

De standaard-opslag klassen gebruiken een van de voor waarden die `volumeBindingMode: Immediate` worden gegarandeerd zodra de PersistentVolumeClaim is gemaakt. In gevallen waarin uw knooppunt groepen een topologie hebben, zoals het gebruik van Beschikbaarheidszones, zijn permanente volumes gebonden of ingericht zonder kennis van de plannings vereisten van de Pod (in dit geval een specifieke zone te zijn).

Als u dit scenario wilt aanpakken, kunt u gebruiken `volumeBindingMode: WaitForFirstConsumer` , waarmee het binden en inrichten van een PersistentVolume wordt vertraagd totdat een pod met de PersistentVolumeClaim wordt gemaakt. Op deze manier wordt de HW conform en ingericht in de beschikbaarheids zone (of een andere topologie) die is opgegeven door de plannings beperkingen van de pod. 

Maak een bestand `sc-azuredisk-csi-waitforfirstconsumer.yaml` met de naam en plak het onderstaande manifest.
De opslag klasse is hetzelfde als onze `managed-csi` opslag klasse, maar met een andere `volumeBindingMode` . 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Maak de opslag klasse met de opdracht [kubectl apply][kubectl-apply] en geef uw `sc-azuredisk-csi-waitforfirstconsumer.yaml` bestand op:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Volume momentopnamen

Het Azure Disk CSI-stuur programma ondersteunt het maken [van moment opnamen van permanente volumes](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html). Als onderdeel van deze mogelijkheid kan het stuur programma *volledige* of [ *incrementele* moment opnamen](../virtual-machines/windows/disks-incremental-snapshots.md) uitvoeren, afhankelijk van de waarde die in de `incremental` para meter is ingesteld (standaard True). 

Zie [para meters voor volume snap shot-klasse](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass)voor meer informatie over alle para meters.

### <a name="create-a-volume-snapshot"></a>Een moment opname van een volume maken

Als u deze mogelijkheid wilt exemplify, maakt u een [volume snap shot-klasse](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) met de opdracht [kubectl apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Nu gaan we een [moment opname](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) maken van een volume van de PVC [die dynamisch aan het begin van deze zelf studie is gemaakt](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes) `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Controleer of de moment opname correct is gemaakt:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Een nieuw PVC maken op basis van een moment opname van een volume

U kunt een nieuw PVC maken op basis van een moment opname van een volume. Gebruik de moment opname die u hebt gemaakt in de vorige stap en maak een [nieuwe PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) en [nieuwe pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) om deze te gebruiken.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Ten slotte kunt u ervoor zorgen dat het hetzelfde PVC is gemaakt voor door de inhoud te controleren.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Zoals verwacht, kunnen we nog steeds het eerder gemaakte `test.txt` bestand zien.

## <a name="clone-volumes"></a>Volumes klonen

Een gekloond volume wordt gedefinieerd als een duplicaat van een bestaand Kubernetes-volume. Voor meer informatie over het klonen van volumes in Kubernetes raadpleegt u de conceptuele documentatie voor het [klonen](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)van volumes.

Het CSI-stuur programma voor Azure-schijven ondersteunt het klonen van volumes. Maak een [gekloond volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) van het [eerder gemaakte](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes) `azuredisk-pvc` en [een nieuwe pod om dit te verbruiken](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)om te demonstreren.



```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

We kunnen nu de inhoud van het gekloonde volume controleren door onderstaande handelingen uit te voeren en te bevestigen dat we het gemaakte bestand nog steeds zien `test.txt` .

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume-pv"></a>Het formaat van een permanent volume wijzigen (HW)

U kunt in plaats daarvan een groter volume aanvragen voor een PVC. Bewerk het PVC-object en geef een grotere grootte op. Met deze wijziging wordt de uitbrei ding geactiveerd van het onderliggende volume dat een back-up van de PersistentVolume maakt. 

> [!NOTE] 
> Er wordt nooit een nieuwe PersistentVolume gemaakt om te voldoen aan de claim. In plaats daarvan wordt het formaat van een bestaand volume gewijzigd.

In AKS is de ingebouwde `managed-csi` opslag klasse al in staat om uit te breiden, dus gebruik het [PVC dat eerder is gemaakt met deze opslag klasse](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes). Het PVC heeft een 10Gi permanente volume aangevraagd, we kunnen controleren dat door de volgende handelingen uit te voeren:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```
> [!IMPORTANT]
> Het Azure Disk CSI-stuur programma ondersteunt momenteel alleen het wijzigen van de grootte van Pvc's zonder dat er een Peul is gekoppeld (en het volume niet is gekoppeld aan een specifiek knoop punt).

Zo kunt u de pod die u eerder hebt gemaakt, verwijderen:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

We gaan het PVC uitbreiden door het veld te verg Roten `spec.resources.requests.storage` :

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

We bevestigen dat het volume nu groter is:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE] 
> Het PVC weerspiegelt niet de nieuwe grootte totdat hieraan een Pod is gekoppeld.

We gaan een nieuwe pod maken:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

En ten slotte de grootte van het PVC en binnen de pod bevestigen: 
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

<!--- ## Shared disk

[Azure shared disks](../virtual-machines/windows/disks-shared.md) is an Azure managed disks feature that enables attaching an Azure disk to agent nodes simultaneously. Attaching a managed disk to multiple agent nodes allows you, for example, to deploy new or migrate existing clustered applications to Azure.

> [!IMPORTANT] Currently, only raw block device (`volumeMode: Block`) is supported by the Azure disk CSI driver. Applications should manage the coordination and control of writes, reads, locks, caches, mounts and fencing on the shared disk which is exposed as raw block device.

Let's create file called `shared-disk.yaml` by copying the below that contains the shared disk storage class and PVC:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Create the storage class with the [kubectl apply][kubectl-apply] command and specify your `shared-disk.yaml` file:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Now let's create a file called `deployment-shared.yml` by copying the below:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: nginx
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Create the deployment with the [kubectl apply][kubectl-apply] command and specify your `deployment-shared.yml` file:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Finally, let's check the block device inside the pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp bash
root@deployment-sharedisk-7454978bc6-xh7jp:/# dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out
104857600 bytes (105 MB, 100 MiB) copied, 0.0502999 s, 2.1 GB/s
```
-->

## <a name="windows-containers"></a>Windows-containers

Het Azure Disk CSI-stuur programma biedt ook ondersteuning voor Windows-knoop punten en-containers. Als u Windows-containers wilt gebruiken, volgt u de [zelf studie over Windows-containers](windows-container-cli.md) om een Windows-knooppunt groep toe te voegen.

Zodra u een Windows-knooppunt groep hebt, kunt u nu gewoon gebruikmaken van de ingebouwde opslag klassen, zoals `managed-csi` . U kunt een voor beeld [van een op Windows gebaseerde stateful set](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) implementeren waarmee tijds tempels worden opgeslagen in een bestand `data.txt` door de onderstaande implementatie uit te voeren met de opdracht [kubectl apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

U kunt nu de inhoud van het volume valideren door het volgende uit te voeren:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het gebruik van het stuur programma CSI voor Azure files [gebruik van Azure files met CSI-Stuur Programma's](azure-files-csi.md).
- Zie [Aanbevolen procedures voor opslag en back-ups in azure Kubernetes service (AKS)][operator-best-practices-storage] voor meer informatie over aanbevolen procedures voor opslag.


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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register