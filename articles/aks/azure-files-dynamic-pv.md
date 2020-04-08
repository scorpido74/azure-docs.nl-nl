---
title: Azure-bestanden delen dynamisch maken
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het dynamisch maken van een blijvend volume met Azure Files voor gebruik met meerdere gelijktijdige pods in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/12/2019
ms.openlocfilehash: 59b773cd4608187fedb24358eac57715e1c271ea
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803531"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Dynamisch een blijvend volume maken en gebruiken met Azure Files in Azure Kubernetes Service (AKS)

Een blijvend volume vertegenwoordigt een stuk opslag dat is ingericht voor gebruik met Kubernetes-pods. Een persistent volume kan door een of meer pods worden gebruikt en kan dynamisch of statisch worden ingericht. Als meerdere pods gelijktijdige toegang tot hetzelfde opslagvolume nodig hebben, u Azure Files gebruiken om verbinding te maken met het [SMB-protocol (Server Message Block).][smb-overview] In dit artikel ziet u hoe u dynamisch een Azure Files-share maakt voor gebruik door meerdere pods in een AKS-cluster (Azure Kubernetes Service).

Zie [Opslagopties voor toepassingen in AKS voor][concepts-storage]meer informatie over Kubernetes-volumes.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

U hebt ook de Azure CLI-versie 2.0.59 of hoger geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

## <a name="create-a-storage-class"></a>Een opslagklasse maken

Een opslagklasse wordt gebruikt om te bepalen hoe een Azure-bestandsshare wordt gemaakt. Er wordt automatisch een opslagaccount gemaakt in de [brongroep knooppunt][node-resource-group] voor gebruik met de opslagklasse om de Azure-bestandsshares vast te houden. Kies de volgende [Azure-opslagredundantie][storage-skus] voor *skuName:*

* *Standard_LRS* - standaard lokaal redundante opslag (LRS)
* *Standard_GRS* - standaard georedundante opslag (GRS)
* *Standard_ZRS* - standaardzone redundante opslag (ZRS)
* *Standard_RAGRS* - standaard georedundante opslag met leestoegang (RA-GRS)
* *Premium_LRS* - premium locally redundant storage (LRS)
* *Premium_ZRS* - redundante opslag voor premiumzones (GRS)

> [!NOTE]
> Azure Files ondersteunen premium opslag in AKS-clusters met Kubernetes 1.13 of hoger, minimaal premium bestandsaandeel is 100 GB

Zie [Kubernetes Storage Classes][kubernetes-storage-classes]voor meer informatie over Kubernetes-opslagklassen voor Azure-bestanden.

Maak een `azure-file-sc.yaml` bestand met de naam en kopie in het volgende voorbeeldmanifest. Zie voor meer informatie over *mountOptions*het gedeelte [Opties van de berg.][mount-options]

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
parameters:
  skuName: Standard_LRS
```

Maak de opslagklasse met de opdracht [kubectl apply:][kubectl-apply]

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Een permanente volumeclaim maken

Een persistent volume claim (PVC) gebruikt het object van de opslagklasse om een Azure-bestandsshare dynamisch in te richten. De volgende YAML kan worden gebruikt om een permanente volumeclaim *van 5 GB* te maken met *ReadWriteMany-toegang.* Zie de [permanente volumedocumentatie van Kubernetes][access-modes] voor meer informatie over toegangsmodi.

Maak nu een `azure-file-pvc.yaml` bestand met de naam en kopie in de volgende YAML. Controleer of de *storageClassName* overeenkomt met de opslagklasse die in de laatste stap is gemaakt:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> Als u de *Premium_LRS* sku voor uw opslagklasse gebruikt, moet de minimumwaarde voor *opslag* *100Gi*zijn.

Maak de claim voor permanente volume met de opdracht [kubectl apply:][kubectl-apply]

```console
kubectl apply -f azure-file-pvc.yaml
```

Na voltooiing wordt de bestandsshare gemaakt. Er wordt ook een Kubernetes-geheim gemaakt dat verbindingsgegevens en referenties bevat. U de [kubectl-opdracht gebruiken][kubectl-get] om de status van het PVC weer te geven:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Gebruik het aanhoudende volume

Met de volgende YAML wordt een pod gemaakt die de *azurefile* voor permanente volumeclaim gebruikt om de Azure-bestandsshare te monteren op het */mnt/azure-pad.* Voor Windows Server-containers (momenteel in preview in AKS) geeft u een *mountPath* op met behulp van de Windows-padconventie, zoals *'D:'.*

Maak een `azure-pvc-files.yaml`bestand met de naam en kopieer in de volgende YAML. Zorg ervoor dat de *claimName* overeenkomt met het PVC dat in de laatste stap is gemaakt.

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
        claimName: azurefile
```

Maak de pod met de [opdracht kubectl apply.][kubectl-apply]

```console
kubectl apply -f azure-pvc-files.yaml
```

U hebt nu een lopende pod met uw Azure Files-share dat is gemonteerd in de *map /mnt/azure.* Deze configuratie kan worden gezien bij `kubectl describe pod mypod`het inspecteren van uw pod via . De volgende verkorte voorbeelduitvoer toont het volume dat in de container is gemonteerd:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Koppelingsopties

De standaardwaarde voor *fileMode* en *dirMode* is *0777* voor Kubernetes versie 1.13.0 en hoger. Als het permanente volume dynamisch wordt gemaakt met een opslagklasse, kunnen de opties voor de montage worden opgegeven op het object opslagklasse. In het volgende voorbeeld wordt *0777 ingesteld:*

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor opslag en back-ups in AKS voor][operator-best-practices-storage]bijbehorende aanbevolen procedures.

Meer informatie over kubernetes-permanente volumes met Azure Files.

> [!div class="nextstepaction"]
> [Kubernetes-plug-in voor Azure-bestanden][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
