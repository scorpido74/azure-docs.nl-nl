---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2c1f61dbb060f9df0b88f301008e3fd45a91fe8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609374"
---
1. Zorg ervoor dat u de nieuwste [Azure cli](/cli/azure/install-az-cli2) hebt geïnstalleerd en dat u bent aangemeld bij een Azure-account in met [AZ login](/cli/azure/reference-index).

1. Maak een instantie van Azure Key Vault en versleutelings sleutel.

    Wanneer u het Key Vault-exemplaar maakt, moet u de beveiliging voor voorlopig verwijderen en leegmaken inschakelen. Met zacht verwijderen zorgt u ervoor dat de Key Vault een verwijderde sleutel bevat voor een bepaalde Bewaar periode (standaard 90 dagen). Bij het opschonen van de beveiliging wordt ervoor gezorgd dat een verwijderde sleutel pas definitief kan worden verwijderd als de retentie periode is verstreken. Met deze instellingen wordt voor komen dat gegevens verloren gaan als gevolg van onbedoeld verwijderen. Deze instellingen zijn verplicht wanneer u een Key Vault gebruikt voor het versleutelen van Managed disks.

    > [!IMPORTANT]
    > Zorg dat u niet Camel de regio, als u dit doet, er problemen kunnen optreden bij het toewijzen van extra schijven aan de resource in de Azure Portal.

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

1.    Maak een instantie van een DiskEncryptionSet. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Verleen de DiskEncryptionSet-resource toegang tot de sleutel kluis. 

        > [!NOTE]
        > Het kan enkele minuten duren voordat Azure de identiteit van uw DiskEncryptionSet in uw Azure Active Directory heeft gemaakt. Als er een fout optreedt, zoals ' kan het Active Directory-object niet vinden ' tijdens het uitvoeren van de volgende opdracht, wacht u enkele minuten en probeert u het opnieuw.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```
