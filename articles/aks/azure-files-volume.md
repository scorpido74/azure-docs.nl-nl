---
title: Een statisch volume maken voor meerdere pods in Azure Kubernetes Service (AKS)
description: Meer informatie over het handmatig maken van een volume met Azure Files voor gebruik met meerdere gelijktijdige pods in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 084ab5cd6736c9148bcab1faf048d3d9081855d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596399"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Handmatig een volume maken en gebruiken met Azure Files share in Azure Kubernetes Service (AKS)

Containergebaseerde toepassingen moeten vaak toegang krijgen tot en blijven gegevens in een extern gegevensvolume. Als meerdere pods gelijktijdige toegang tot hetzelfde opslagvolume nodig hebben, u Azure Files gebruiken om verbinding te maken met het [SMB-protocol (Server Message Block).][smb-overview] In dit artikel ziet u hoe u handmatig een Azure-bestandenshare maakt en deze aan een pod in AKS koppelt.

Zie [Opslagopties voor toepassingen in AKS voor][concepts-storage]meer informatie over Kubernetes-volumes.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

U hebt ook de Azure CLI-versie 2.0.59 of hoger geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken

Voordat u Azure Files als Kubernetes-volume gebruiken, moet u een Azure Storage-account en het bestandsshare maken. Met de volgende opdrachten wordt een resourcegroep gemaakt met de naam *myAKSShare,* een opslagaccount en een delen met de naam *aksshare:*

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Noteer de naam en sleutel van het opslagaccount die aan het einde van de scriptuitvoer worden weergegeven. Deze waarden zijn nodig wanneer u het Kubernetes-volume maakt in een van de volgende stappen.

## <a name="create-a-kubernetes-secret"></a>Maak een Kubernetes-geheim

Kubernetes heeft referenties nodig om toegang te krijgen tot het bestandsaandeel dat in de vorige stap is gemaakt. Deze referenties worden opgeslagen in een [Kubernetes-geheim,][kubernetes-secret]waarnaar wordt verwezen wanneer u een Kubernetes-pod maakt.

Gebruik `kubectl create secret` de opdracht om het geheim te maken. In het volgende voorbeeld wordt een gedeelde *azure-secret* gemaakt en wordt de *azurestorageaccountnaam* en *azurestorageaccountsleutel* van de vorige stap ingevuld. Als u een bestaand Azure-opslagaccount wilt gebruiken, geeft u de naam en de sleutel van het account op.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Het bestandsaandeel als volume monteren

Als u het aandeel Azure-bestanden in uw pod wilt monteren, `azure-files-pod.yaml` configureert u het volume in de containerspec. Maak een nieuw bestand met de volgende inhoud. Als u de naam van de delen van bestanden of de geheime naam hebt gewijzigd, werkt u de *shareName* en *secretName*bij . Werk desgewenst `mountPath`het pad bij waar de delen van bestanden in de pod is gemonteerd. Voor Windows Server-containers (momenteel in preview in AKS) geeft u een *mountPath* op met behulp van de Windows-padconventie, zoals *'D:'.*

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
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Gebruik `kubectl` de opdracht om de pod te maken.

```console
kubectl apply -f azure-files-pod.yaml
```

U hebt nu een lopende pod met een Azure Files-share die is gemonteerd op */mnt/azure.* U kunt `kubectl describe pod mypod` ervoor controleren of het aandeel is gemonteerd. De volgende verkorte voorbeelduitvoer toont het volume dat in de container is gemonteerd:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-options"></a>Koppelingsopties

De standaardwaarde voor *fileMode* en *dirMode* is *0755* voor Kubernetes versie 1.9.1 en hoger. Als u een cluster met Kuberetes versie 1.8.5 of hoger gebruikt en statisch het permanente volumeobject maakt, moeten de opties voor het *persistentvolume* worden opgegeven. In het volgende voorbeeld wordt *0777 ingesteld:*

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Als u een cluster van versie 1.8.0 - 1.8.4 gebruikt, kan een beveiligingscontext worden opgegeven met de *waarde runAsUser* ingesteld op *0*. Zie [Een beveiligingscontext configureren][kubernetes-security-context]voor meer informatie over de beveiligingscontext van de pod .

Als u uw bevestigingsopties wilt bijwerken, maakt u een *azurefile-mount-options-pv.yaml-bestand* met een *PersistentVolume.* Bijvoorbeeld:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Maak een *azurefile-mount-options-pvc.yaml-bestand* met een *PersistentVolumeClaim* dat het *PersistentVolume*gebruikt. Bijvoorbeeld:

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

Gebruik `kubectl` de opdrachten om de *persistentevolume-* en *persistentevolumeclaim*te maken.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Controleer of uw *PersistentVolumeClaim* is gemaakt en gebonden is aan het *persistente volume*.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Werk de containerspecificaties bij om naar uw *PersistentVolumeClaim* te verwijzen en uw pod bij te werken. Bijvoorbeeld:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor opslag en back-ups in AKS voor][operator-best-practices-storage]bijbehorende aanbevolen procedures.

Zie de [Kubernetes-plug-in voor Azure Files voor][kubernetes-files]meer informatie over AKS-clusters.

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
