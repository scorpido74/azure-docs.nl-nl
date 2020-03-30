---
title: Een door de klant beheerde sleutel gebruiken om Azure-schijven te versleutelen in Azure Kubernetes Service (AKS)
description: Neem uw eigen sleutels (BYOK) mee om AKS-besturingssysteem- en gegevensschijven te versleutelen.
services: container-service
ms.topic: article
ms.date: 01/12/2020
ms.openlocfilehash: bb6ba5e6dd4ace9e33043079c0f435c10baf5cb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596501"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Breng uw eigen sleutels (BYOK) met Azure-schijven in Azure Kubernetes Service (AKS)

Azure Storage versleutelt alle gegevens in een opslagaccount in rust. Standaard worden gegevens versleuteld met door Microsoft beheerde sleutels. Voor extra controle over versleutelingssleutels u [door de klant beheerde sleutels][customer-managed-keys] leveren die u gebruiken voor versleuteling in rust voor zowel het besturingssysteem als gegevensschijven voor uw AKS-clusters.

> [!NOTE]
> BYOK Linux- en Windows-gebaseerde AKS-clusters zijn beschikbaar in [Azure-regio's][supported-regions] die serverzijdeversleuteling van door Azure beheerde schijven ondersteunen.

## <a name="before-you-begin"></a>Voordat u begint

* In dit artikel wordt ervan uitgegaan dat u een nieuw *AKS-cluster maakt.*

* U moet soft delete and purge protection inschakelen voor *Azure Key Vault* wanneer u Key Vault gebruikt om beheerde schijven te versleutelen.

* U hebt de Azure CLI-versie 2.0.79 of hoger en de aks-preview 0.4.26-extensie nodig

> [!IMPORTANT]
> AKS preview-functies zijn self-service opt-in. Previews worden geleverd "as-is" en "as available" en zijn uitgesloten van de service level agreements en beperkte garantie. AKS Previews worden gedeeltelijk gedekt door de klantenservice op basis van de beste inspanning. Als zodanig zijn deze functies niet bedoeld voor productiegebruik. Zie voor aanvullende infromation de volgende ondersteuningsartikelen:
>
> * [AKS-ondersteuningsbeleid](support-policies.md)
> * [Veelgestelde vragen over Azure Support](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>De nieuwste AKS CLI-preview-extensie installeren

Als u door de klant beheerde toetsen wilt gebruiken, hebt u de *AKS-Preview CLI-extensieversie* 0.4.26 of hoger nodig. Installeer de *aks-preview Azure CLI-extensie* met de opdracht [Toevoegen van AZ-extensie][az-extension-add] en controleer vervolgens op beschikbare updates met de opdracht [update van AZ-extensie:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Een Azure Key Vault-exemplaar maken

Gebruik een Azure Key Vault-exemplaar om uw sleutels op te slaan.  U de [Azure-portal][byok-azure-portal] optioneel gebruiken om door de klant beheerde sleutels te configureren met Azure Key Vault

Maak een nieuwe *brongroep,* maak vervolgens een nieuwe *Key Vault-instantie* en schakel zachte verwijderings- en verwijderingsbeveiliging in.  Zorg ervoor dat u voor elke opdracht dezelfde namen van regio- en resourcegroepen gebruikt.

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

Vervang *myKeyVaultName* door de naam van uw sleutelkluis.  U hebt ook een *sleutel* nodig die is opgeslagen in Azure Key Vault om de volgende stappen uit te voeren.  Sla uw bestaande sleutel op in de Key Vault die u in de vorige stappen hebt gemaakt, of [genereer een nieuwe sleutel][key-vault-generate] en vervang *myKeyName* hieronder met de naam van uw sleutel.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>De DiskEncryptionSet toegang verlenen tot sleutelkluis

Gebruik de DiskEncryptionSet en resourcegroepen die u in de vorige stappen hebt gemaakt en geef de DiskEncryptionSet-bron toegang tot de Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Een nieuw AKS-cluster maken en de osschijf versleutelen

Maak een **nieuwe brongroep** en AKS-cluster en gebruik vervolgens uw sleutel om de OS-schijf te versleutelen. Door de klant beheerde sleutels worden alleen ondersteund in Kubernetes-versies groter dan 1,17. 

> [!IMPORTANT]
> Zorg ervoor dat u een nieuwe resoruce-groep maakt voor uw AKS-cluster

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

Wanneer nieuwe knooppuntgroepen worden toegevoegd aan het cluster dat hierboven is gemaakt, wordt de door de klant beheerde sleutel die tijdens het maken wordt geleverd, gebruikt om de osschijf te versleutelen.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Uw AKS-clustergegevensschijf versleutelen

U de AKS-gegevensschijven ook versleutelen met uw eigen sleutels.

> [!IMPORTANT]
> Zorg ervoor dat u over de juiste AKS-referenties beschikt. De serviceprincipal moet toegang hebben tot de brongroep waar de diskencryptionset wordt geïmplementeerd. Anders krijgt u een foutmelding dat de serviceprincipal geen machtigingen heeft.

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

Maak een bestand genaamd **byok-azure-disk.yaml** dat de volgende informatie bevat.  Vervang myAzureSubscriptionId, myResourceGroup en myDiskEncrptionSetName door uw waarden en pas de yaml toe.  Zorg ervoor dat u de brongroep gebruikt waarin uw DiskEncryptionSet is geïmplementeerd.  Als u de Azure Cloud Shell gebruikt, kan dit bestand worden gemaakt met vi of nano alsof het werkt op een virtueel of fysiek systeem:

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
Voer deze implementatie vervolgens uit in uw AKS-cluster:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Beperkingen

* BYOK is momenteel alleen beschikbaar in GA en Preview in bepaalde [Azure-regio's][supported-regions]
* OS Disk Encryption ondersteund met Kubernetes versie 1.17 en hoger   
* Alleen beschikbaar in regio's waar BYOK wordt ondersteund
* Versleuteling met door de klant beheerde sleutels is momenteel alleen voor nieuwe AKS-clusters, bestaande clusters kunnen niet worden bijgewerkt
* AKS-cluster met virtuele machineschaalsets is vereist, er is geen ondersteuning voor virtuele machinebeschikbaarheidssets


## <a name="next-steps"></a>Volgende stappen

Best [practices voor AKS-clusterbeveiliging bekijken][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
