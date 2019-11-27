---
title: Dynamisch een bestanden volume maken voor meerdere peulen in azure Kubernetes service (AKS)
description: Meer informatie over het dynamisch maken van een permanent volume met Azure Files voor gebruik met meerdere gelijktijdige peulingen in azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 09/12/2019
ms.author: mlearned
ms.openlocfilehash: 999e106240a8a1d95c35d098062d474a0b57228d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231760"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Dynamisch een permanent volume maken en gebruiken met Azure Files in azure Kubernetes service (AKS)

Een permanent volume vertegenwoordigt een opslag ruimte die is ingericht voor gebruik met Kubernetes peul. Een permanent volume kan worden gebruikt door een of meer peulen en kan dynamisch of statisch worden ingericht. Als meerdere peulen gelijktijdig gelijktijdige toegang tot hetzelfde opslag volume nodig hebben, kunt u Azure Files gebruiken om verbinding te maken met het [SMB-protocol (Server Message Block)][smb-overview]. In dit artikel wordt beschreven hoe u een Azure Files share dynamisch kunt maken voor gebruik door meerdere peulingen in een Azure Kubernetes service-cluster (AKS).

Zie [opslag opties voor toepassingen in AKS][concepts-storage]voor meer informatie over Kubernetes-volumes.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

Ook moet de Azure CLI-versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u wilt installeren of upgraden, raadpleegt u [Azure cli installeren][install-azure-cli].

## <a name="create-a-storage-class"></a>Een opslag klasse maken

Een opslag klasse wordt gebruikt om te bepalen hoe een Azure-bestands share wordt gemaakt. Er wordt automatisch een opslag account gemaakt in de [knooppunt resource groep][node-resource-group] voor gebruik met de opslag klasse om de Azure-bestands shares op te slaan. Kies een van de volgende [Azure Storage-redundantie][storage-skus] voor *skuName*:

* *Standard_LRS* -standaard lokaal redundante opslag (LRS)
* *Standard_GRS* -standaard geo-redundante opslag (GRS)
* *Standard_RAGRS* -standaard geografisch redundante opslag met lees toegang (RA-GRS)
* *Premium_LRS* -Premium lokaal redundante opslag (LRS)

> [!NOTE]
> Azure Files Premium-opslag wordt ondersteund in AKS-clusters met Kubernetes 1,13 of hoger.

Zie [Kubernetes-opslag klassen][kubernetes-storage-classes]voor meer informatie over Kubernetes-opslag klassen voor Azure files.

Maak een bestand met de naam `azure-file-sc.yaml` en kopieer het in het volgende voor beeld-manifest. Zie de sectie [koppelings opties][mount-options] voor meer informatie over *mountOptions*.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Maak de opslag klasse met de opdracht [kubectl apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Een permanente volume claim maken

Een permanente volume claim (PVC) maakt gebruik van het opslag klassen object om een Azure-bestands share dynamisch in te richten. De volgende YAML kunnen worden gebruikt voor het maken van een permanente volume claim *5 GB* in grootte met *ReadWriteMany* -toegang. Zie de documentatie over het [permanente volume van Kubernetes][access-modes] voor meer informatie over de toegangs modi.

Maak nu een bestand met de naam `azure-file-pvc.yaml` en kopieer het in de volgende YAML. Zorg ervoor dat de *storageClassName* overeenkomt met de opslag klasse die in de laatste stap is gemaakt:

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
> Als u de *Premium_LRS* SKU voor uw opslag klasse gebruikt, moet de minimale waarde voor opslag *100Gi*zijn.

Maak de permanente volume claim met de opdracht [kubectl apply][kubectl-apply] :

```console
kubectl apply -f azure-file-pvc.yaml
```

Zodra het is voltooid, wordt de bestands share gemaakt. Er wordt ook een Kubernetes-geheim gemaakt dat verbindings gegevens en referenties bevat. U kunt de opdracht [kubectl Get][kubectl-get] gebruiken om de status van het PVC weer te geven:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Het permanente volume gebruiken

De volgende YAML maakt een pod die gebruikmaakt van de permanente volume claim *azurefile* om de Azure-bestands share te koppelen aan het */mnt/Azure* -pad. Voor Windows Server-containers (momenteel in de preview-versie van AKS) geeft u een *mountPath* op met behulp van de Windows Path-Conventie, zoals *: '* .

Maak een bestand met de naam `azure-pvc-files.yaml`en kopieer de volgende YAML. Zorg ervoor dat de *claim* naam overeenkomt met het PVC dat in de laatste stap is gemaakt.

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

Maak de Pod met de opdracht [kubectl apply][kubectl-apply] .

```console
kubectl apply -f azure-pvc-files.yaml
```

U hebt nu een actieve pod met uw Azure Files-share die is gekoppeld in de */mnt/Azure* -map. Deze configuratie kan worden weer gegeven bij het controleren van uw Pod via `kubectl describe pod mypod`. De volgende gecomprimeerde voorbeeld uitvoer toont het volume dat in de container is gekoppeld:

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

## <a name="mount-options"></a>Koppelings opties

De standaard waarde voor *file mode* en *dirMode* is *0755* voor Kubernetes-versie 1.9.1 en hoger. Als u een cluster met Kuberetes-versie 1.8.5 of hoger gebruikt en het permanente volume dynamisch maakt met een opslag klasse, kunnen er koppelings opties worden opgegeven voor het opslag klassen object. In het volgende voor beeld wordt *0777*ingesteld:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Als u een cluster van versie 1.8.0-1.8.4 gebruikt, kan een beveiligings context worden opgegeven met de *runAsUser* -waarde ingesteld op *0*. Zie [Configure a security context][kubernetes-security-context](Engelstalig) voor meer informatie over pod-beveiligings context.

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor opslag en back-ups in AKS][operator-best-practices-storage]voor gekoppelde aanbevolen procedures.

Meer informatie over Kubernetes permanente volumes met behulp van Azure Files.

> [!div class="nextstepaction"]
> [Kubernetes-invoeg toepassing voor Azure Files][kubernetes-files]

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