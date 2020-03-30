---
title: Azure NetApp-bestanden integreren met Azure Kubernetes-service
description: Meer informatie over het integreren van Azure NetApp-bestanden met Azure Kubernetes-service
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 1c4996df66d475c63110e3d2797f55598fd85b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273757"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Azure NetApp-bestanden integreren met Azure Kubernetes-service

[Azure NetApp Files][anf] is een bedrijfsservice met krachtige, gemeten bestandsopslagservice die op Azure wordt uitgevoerd. In dit artikel ziet u hoe u Azure NetApp-bestanden integreren met DE Azure Kubernetes Service (AKS).

## <a name="before-you-begin"></a>Voordat u begint
In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

> [!IMPORTANT]
> Uw AKS-cluster moet zich ook [in een regio bevinden die Azure NetApp Files ondersteunt.][anf-regions]

U hebt ook de Azure CLI-versie 2.0.59 of hoger geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

### <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u Azure NetApp-bestanden gebruikt:

* Azure NetApp-bestanden zijn alleen beschikbaar [in geselecteerde Azure-regio's.][anf-regions]
* Voordat u Azure NetApp Files gebruiken, moet u toegang krijgen tot de Azure NetApp Files-service. Als u toegang wilt aanvragen, u het formulier voor het indienen van de [wachtlijst van Azure NetApp Files gebruiken.][anf-waitlist] U hebt geen toegang tot de Azure NetApp Files-service totdat u de officiële bevestigingse-mail van het Azure NetApp Files-team hebt ontvangen.
* Uw Azure NetApp Files-service moet worden gemaakt in hetzelfde virtuele netwerk als uw AKS-cluster.
* Na de eerste implementatie van een AKS-cluster wordt alleen statische inrichting voor Azure NetApp-bestanden ondersteund.
* Als u dynamische inrichting wilt gebruiken met Azure NetApp-bestanden, installeert en configureert u [NetApp Trident-versie](https://netapp-trident.readthedocs.io/) 19.07 of hoger.

## <a name="configure-azure-netapp-files"></a>Azure NetApp-bestanden configureren

> [!IMPORTANT]
> Voordat u de *Microsoft.NetApp-bronprovider* registreren, moet u het aanvraagformulier voor het indienen van de [wachtlijst van Azure NetApp Files][anf-waitlist] voor uw abonnement invullen. U de bronbron niet registreren totdat u de officiële bevestigingse-mail van het Azure NetApp Files-team hebt ontvangen.

Registreer de *Microsoft.NetApp-bronprovider:*

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Dit kan enige tijd duren om te voltooien.

Wanneer u een Azure NetApp-account maakt voor gebruik met AKS, moet u het account maken in de brongroep **knooppunt.** Download eerst de naam van de resourcegroep met `--query nodeResourceGroup` de opdracht [az aks show][az-aks-show] en voeg de queryparameter toe. In het volgende voorbeeld wordt de knooppuntbrongroep voor het AKS-cluster met de naam *myAKSCluster* in de naam *myResourceGroup*van de resourcegroep:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Maak een Azure NetApp Files-account in **de** knooppuntbrongroep en dezelfde regio als uw AKS-cluster met behulp van [az netappfiles-account maken.][az-netappfiles-account-create] In het volgende voorbeeld wordt een account met de naam *myaccount1* gemaakt in de *MC_myResourceGroup_myAKSCluster_eastus* resourcegroep en *de regio Eastus:*

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Maak een nieuwe capaciteitspool door [az netappfiles pool te maken.][az-netappfiles-pool-create] In het volgende voorbeeld wordt een nieuwe capaciteitspool met de naam *mypool1* gemaakt met 4 TB groot en Premium-serviceniveau: *Premium*

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Maak een subnet om [te delegeren aan Azure NetApp Files][anf-delegate-subnet] met behulp van [het az-netwerk vnet subnet maken][az-network-vnet-subnet-create]. *Dit subnet moet zich in hetzelfde virtuele netwerk bevinden als uw AKS-cluster.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Maak een volume met behulp van [az netappfiles volume maken][az-netappfiles-volume-create].

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --creation-token $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Het persistente volume maken

Vermeld de details van uw volume met behulp van [az netappfiles volume show][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Een `pv-nfs.yaml` definiërend persistentvolume maken. Vervang `path` door de `server` *creationToken* en met *ipAddress* van de vorige opdracht. Bijvoorbeeld:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Werk de *server* en *het pad* bij naar de waarden van het nfs-volume (Network File System) dat u in de vorige stap hebt gemaakt. Maak de opdracht PersistentVolume met de opdracht [kubectl apply:][kubectl-apply]

```console
kubectl apply -f pv-nfs.yaml
```

Controleer of de *status* van persistentvolume *beschikbaar* is met de opdracht [kubectl describe:][kubectl-describe]

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>De PersistentVolumeClaim maken

Een `pvc-nfs.yaml` definiërend persistentvolume maken. Bijvoorbeeld:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Maak de persistentvolumeclaim met de opdracht [kubectl apply:][kubectl-apply]

```console
kubectl apply -f pvc-nfs.yaml
```

Controleer of de *status* van persistentvolumeclaim *is gebonden* met de opdracht [kubectl describe:][kubectl-describe]

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Monteren met een pod

Maak `nginx-nfs.yaml` een definiërende pod die de PersistentVolumeClaim gebruikt. Bijvoorbeeld:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Maak de pod met de opdracht [kubectl apply:][kubectl-apply]

```console
kubectl apply -f nginx-nfs.yaml
```

Controleer of de pod *wordt uitgevoerd* met de opdracht [kubectl describe:][kubectl-describe]

```console
kubectl describe pod nginx-nfs
```

Controleer of uw volume in de pod is gemonteerd met behulp van `df -h` [kubectl exec][kubectl-exec] om verbinding te maken met de pod om vervolgens te controleren of het volume is gemonteerd.

```console
$ kubectl exec -it nginx-nfs -- bash
```

```output
root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure NetApp-bestanden][anf]voor meer informatie over Azure NetApp-bestanden . Zie [Handmatig een Linux-servervolume (NFS) maken en gebruiken met Azure Kubernetes Service (AKS) voor][aks-nfs]meer informatie over het gebruik van NFS met AKS.


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
