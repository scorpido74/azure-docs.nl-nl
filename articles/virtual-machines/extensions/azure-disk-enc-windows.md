---
title: Azure Disk Encryption voor Windows | Microsoft Docs
description: Hiermee worden Azure Disk Encryption geïmplementeerd op een virtuele Windows-machine met behulp van een extensie van een virtuele machine.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 11394f692765cc1df5db0eb5c0dd06425026505d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092651"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption voor Windows (micro soft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Overzicht

Azure Disk Encryption maakt gebruik van BitLocker om versleuteling van de volledige schijf te bieden op virtuele machines van Azure waarop Windows wordt uitgevoerd.  Deze oplossing is geïntegreerd met Azure Key Vault voor het beheren van schijf versleutelings sleutels en geheimen in uw sleutel kluis abonnement. 

## <a name="prerequisites"></a>Vereisten

Zie [AzureDiskEncryption](
../../security/azure-security-disk-encryption-prerequisites.md)vereisten voor een volledige lijst met vereisten.

### <a name="operating-system"></a>Besturingssysteem

Zie [Azure Disk Encryption vereisten](../../security/azure-security-disk-encryption-prerequisites.md)voor een lijst met de huidige versies van Windows.

### <a name="internet-connectivity"></a>Internetconnectiviteit

Azure Disk Encryption Internet connectiviteit vereist voor toegang tot Active Directory-, Key Vault-, opslag-en Package Management-eind punten.  Zie [AzureDiskEncryption](
../../security/azure-security-disk-encryption-prerequisites.md)vereisten voor meer informatie over de beveiligings instellingen van het netwerk.

## <a name="extension-schemata"></a>Extensie schema's

Er zijn twee schema's voor Azure Disk Encryption: v 1.1, een nieuwer, aanbevolen schema dat geen gebruik maakt van Azure Active Directory (AAD)-eigenschappen en v 0.1, een ouder schema dat AAD-eigenschappen vereist. U moet de schema versie gebruiken die overeenkomt met de extensie die u gebruikt: schema v 1.1 voor de AzureDiskEncryption-extensie versie 1,1, schema v 0.1 voor de AzureDiskEncryption-extensie versie 0,1.

### <a name="schema-v11-no-aad-recommended"></a>Schema v 1.1: Geen AAD (aanbevolen)

Het v 1.1-schema wordt aanbevolen en vereist geen Azure Active Directory eigenschappen.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>Schema v 0,1: met AAD 

Het 0,1-schema `aadClientID` vereist en `aadClientSecret` ofwel `AADClientCertificate`of.

Gebruiken `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```

Gebruiken `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Waarden van eigenschappen

| Name | Waarde / voorbeeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0,1, 1,1 | int |
| (0,1-schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0,1-schema) AADClientSecret | password | string |
| (0,1-schema) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-woorden lijst |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyEncryptionKeyURL | url | string |
| KeyVaultURL | url | string |
| Beschrijving Passphrase | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | Besturings systeem, gegevens, alle | string |

## <a name="template-deployment"></a>Sjabloonimplementatie
Zie [een nieuwe versleutelde Windows-VM maken op basis van een galerie afbeelding](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)voor een voor beeld van een sjabloon implementatie.

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie

Instructies vindt u in de meest recente [documentatie van Azure cli](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Raadpleeg de [hand leiding](../../security/azure-security-disk-encryption-tsg.md)voor het oplossen van problemen met Azure Disk Encryption.

### <a name="support"></a>Ondersteuning

Als u hulp nodig hebt op elk gewenst moment in dit artikel, u kunt contact opnemen met de Azure-experts op het [forums voor Azure MSDN en Stack Overflow](https://azure.microsoft.com/support/community/). U kunt ook een Azure-ondersteuning-incident indienen. Ga naar de [ondersteuning van Azure site](https://azure.microsoft.com/support/options/) en selecteer Get-ondersteuning. Voor meer informatie over het gebruik van ondersteuning voor Azure, de [Veelgestelde vragen over Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Volgende stappen
Zie [virtuele machines en functies voor Windows](features-windows.md)voor meer informatie over uitbrei dingen.
