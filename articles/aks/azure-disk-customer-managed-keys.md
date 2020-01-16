---
title: Een door de klant beheerde sleutel gebruiken voor het versleutelen van Azure-schijven in azure Kubernetes service (AKS)
description: Gebruik uw eigen sleutels (BYOK) om AKS-besturings systeem en gegevens schijven te versleutelen.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/12/2020
ms.author: mlearned
ms.openlocfilehash: 1359f645c634f401f139fe1cd559f23aa4126c22
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029982"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Breng uw eigen sleutels (BYOK) met Azure-schijven in azure Kubernetes service (AKS)

Azure Storage versleutelt alle gegevens in een opslag account in rust. Standaard worden gegevens versleuteld met door micro soft beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u door de [klant beheerde sleutels][customer-managed-keys] leveren voor het versleutelen van zowel het besturings systeem als de gegevens schijven voor uw AKS-clusters.

> [!NOTE]
> BYOK Linux-en Windows-AKS-clusters zijn beschikbaar in [Azure-regio's][supported-regions] die ondersteuning bieden voor versleuteling aan de server zijde van Azure Managed disks.

## <a name="before-you-begin"></a>Voordat u begint

* In dit artikel wordt ervan uitgegaan dat u een *Nieuw AKS-cluster*maakt.

* U moet de beveiliging voor voorlopig verwijderen en leegmaken inschakelen voor *Azure Key Vault* wanneer u Key Vault gebruikt om beheerde schijven te versleutelen.

* U hebt de Azure CLI-versie 2.0.79 of hoger nodig en de AKS-Preview 0.4.26-extensie

> [!IMPORTANT]
> De preview-functies van AKS zijn self-service opt-in. Previews worden ' as-is ' en ' as available ' gegeven en zijn uitgesloten van de service level agreements en beperkte garantie. AKS-previews worden gedeeltelijk gedekt door klant ondersteuning, op basis van de beste inspanningen. Daarom zijn deze functies niet bedoeld voor productie gebruik. Raadpleeg de volgende artikelen met technische ondersteuning voor meer informatie.
>
> * [AKS-ondersteunings beleid](support-policies.md)
> * [Veelgestelde vragen over ondersteuning voor Azure](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>De meest recente AKS CLI-preview-extensie installeren

Als u door de klant beheerde sleutels wilt gebruiken, hebt u de *AKS-preview cli-* extensie versie 0.4.26 of hoger nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] en controleer vervolgens of er beschik bare updates zijn met behulp van de opdracht [AZ extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Een Azure Key Vault-exemplaar maken

Gebruik een Azure Key Vault-exemplaar om uw sleutels op te slaan.  U kunt eventueel de Azure Portal gebruiken om [door de klant beheerde sleutels te configureren met Azure Key Vault][byok-azure-portal]

Maak een nieuwe *resource groep*, maak een nieuw *Key Vault* -exemplaar en schakel de beveiliging voorlopig verwijderen en leegmaken in.  Zorg ervoor dat u dezelfde regio en de namen van de resource groepen voor elke opdracht gebruikt.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Een instantie van een DiskEncryptionSet maken

Vervang *myKeyVaultName* door de naam van uw sleutel kluis.  U hebt ook een *sleutel* nodig die is opgeslagen in azure Key Vault om de volgende stappen uit te voeren.  Sla uw bestaande sleutel op in het Key Vault dat u in de vorige stappen hebt gemaakt of [Genereer een nieuwe sleutel][key-vault-generate] en vervang *myKeyName* hieronder door de naam van uw sleutel.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>De toegang tot de DiskEncryptionSet verlenen aan de sleutel kluis

Gebruik de DiskEncryptionSet-en resource groepen die u in de voor gaande stappen hebt gemaakt en verleen de DiskEncryptionSet-resource toegang tot de Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Een nieuw AKS-cluster maken en de besturingssysteem schijf versleutelen

Maak een **nieuwe resource groep** en AKS-cluster en gebruik vervolgens uw sleutel om de besturingssysteem schijf te versleutelen. Door de klant beheerde sleutels worden alleen ondersteund in versies van kubernetes die groter zijn dan 1,17. 

> [!IMPORTANT]
> Zorg ervoor dat u een nieuwe resource-groep voor uw AKS-cluster maakt

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n diskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

Wanneer er nieuwe knooppunt groepen worden toegevoegd aan het hierboven gemaakte cluster, wordt de door de klant beheerde sleutel die tijdens het maken is gegeven, gebruikt voor het versleutelen van de besturingssysteem schijf.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Uw AKS-cluster gegevens schijf versleutelen

U kunt de AKS-gegevens schijven ook versleutelen met uw eigen sleutels.

> [!IMPORTANT]
> Zorg ervoor dat u beschikt over de juiste AKS-referenties. De Service-Principal moet Inzender toegang hebben tot de resource groep waar de diskencryptionset wordt geïmplementeerd. Anders wordt er een fout bericht weer geven dat de service-principal geen machtigingen heeft.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Maak een bestand met de naam **byok-Azure-disk. yaml** dat de volgende informatie bevat.  Vervang myAzureSubscriptionId, myResourceGroup en myDiskEncrptionSetName door uw waarden en pas de yaml toe.  Zorg ervoor dat u de resource groep gebruikt waarin uw DiskEncryptionSet wordt geïmplementeerd.  Als u de Azure Cloud Shell gebruikt, kan dit bestand worden gemaakt met behulp van VI of nano alsof het werkt op een virtueel of fysiek systeem:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Voer vervolgens deze implementatie uit in uw AKS-cluster:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Beperkingen

* BYOK is momenteel alleen beschikbaar in GA en preview in bepaalde [Azure-regio's][supported-regions]
* Schijf versleuteling van het besturings systeem wordt ondersteund met Kubernetes-versie 1,17 en hoger   
* Alleen beschikbaar in regio's waar BYOK wordt ondersteund
* Versleuteling met door de klant beheerde sleutels is momenteel alleen beschikbaar voor nieuwe AKS-clusters, maar bestaande clusters kunnen niet worden bijgewerkt
* AKS-cluster met Virtual Machine Scale Sets is vereist, geen ondersteuning voor beschikbaarheids sets voor virtuele machines


## <a name="next-steps"></a>Volgende stappen

[Aanbevolen procedures voor AKS-cluster beveiliging][best-practices-security] controleren

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-scenarios-and-restrictions
