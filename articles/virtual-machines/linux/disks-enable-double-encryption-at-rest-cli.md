---
title: Dubbele versleuteling inschakelen bij rest-Azure CLI-beheerde schijven
description: Schakel dubbele versleuteling in op rest voor uw beheerde schijf gegevens met behulp van de Azure CLI.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 52a52f6f7bca99d662a68ae48276062969b194f0
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86236001"
---
# <a name="azure-cli---enable-double-encryption-at-rest-on-your-managed-disks"></a>Azure CLI: dubbele versleuteling inschakelen op de rest van uw beheerde schijven

Azure Disk Storage ondersteunt dubbele versleuteling in rust voor beheerde schijven. Zie de sectie [dubbele versleuteling bij rest](disk-encryption.md#double-encryption-at-rest) van ons artikel over schijf versleuteling voor conceptuele informatie over dubbele versleuteling in rust, evenals andere beheerde schijf versleutelings typen.

## <a name="supported-regions"></a>Ondersteunde regio's

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="prerequisites"></a>Vereisten

Installeer de nieuwste [Azure cli](/cli/azure/install-az-cli2) en meld u aan bij een Azure-account met [AZ login](/cli/azure/reference-index).

## <a name="getting-started"></a>Aan de slag

1. Maak een instantie van Azure Key Vault en versleutelings sleutel.

    Wanneer u het Key Vault-exemplaar maakt, moet u de beveiliging voor voorlopig verwijderen en leegmaken inschakelen. Met zacht verwijderen zorgt u ervoor dat de Key Vault een verwijderde sleutel bevat voor een bepaalde Bewaar periode (standaard 90 dagen). Bij het opschonen van de beveiliging wordt ervoor gezorgd dat een verwijderde sleutel pas definitief kan worden verwijderd als de retentie periode is verstreken. Met deze instellingen wordt voor komen dat gegevens verloren gaan als gevolg van onbedoeld verwijderen. Deze instellingen zijn verplicht wanneer u een Key Vault gebruikt voor het versleutelen van Managed disks.

    
    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Maak een DiskEncryptionSet met encryptionType ingesteld als EncryptionAtRestWithPlatformAndCustomerKeys. Gebruik API-versie **2020-05-01** in de sjabloon Azure Resource Manager (arm). 
    
        ```azurecli
        az group deployment create -g $rgName \
       --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" \
        --parameters "diskEncryptionSetName=$diskEncryptionSetName" "encryptionType=EncryptionAtRestWithPlatformAndCustomerKeys" "keyVaultId=$keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
        ```

1.    Verleen de DiskEncryptionSet-resource toegang tot de sleutel kluis. 

        > [!NOTE]
        > Het kan enkele minuten duren voordat Azure de identiteit van uw DiskEncryptionSet in uw Azure Active Directory heeft gemaakt. Als er een fout optreedt, zoals ' kan het Active Directory-object niet vinden ' tijdens het uitvoeren van de volgende opdracht, wacht u enkele minuten en probeert u het opnieuw.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```

## <a name="next-steps"></a>Volgende stappen

Nu u deze resources hebt gemaakt en geconfigureerd, kunt u deze gebruiken om uw beheerde schijven te beveiligen. De volgende koppelingen bevatten voorbeeld scripts, elk met een eigen scenario, dat u kunt gebruiken om uw beheerde schijven te beveiligen.

Voor [beelden](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption) 
 van Azure Resource Manager sjablonen [Door de klant beheerde sleutels met versleuteling aan server zijde inschakelen-voor beelden](disks-enable-customer-managed-keys-cli.md#examples)