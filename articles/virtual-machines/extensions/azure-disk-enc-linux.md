---
title: Azure Disk Encryption voor Linux | Microsoft Docs
description: Implementeert Azure Disk Encryption voor Linux naar een virtuele machine met behulp van een extensie van een virtuele machine.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: 6a81f105f9632a7ca7e2bf7188e358274020c78f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084771"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption voor Linux (micro soft. Azure. Security. AzureDiskEncryptionForLinux)

## <a name="overview"></a>Overzicht

Azure Disk Encryption maakt gebruik van het subsysteem DM-cryptografie in Linux om versleuteling van de volledige schijf te bieden voor het [selecteren van Azure Linux](https://aka.ms/adelinux)-distributies.  Deze oplossing is geïntegreerd met Azure Key Vault voor het beheren van schijf versleutelings sleutels en geheimen.

## <a name="prerequisites"></a>Vereisten

Zie [AzureDiskEncryption](
../../security/azure-security-disk-encryption-prerequisites.md)vereisten voor een volledige lijst met vereisten.

### <a name="operating-system"></a>Besturingssysteem

Azure Disk Encryption wordt momenteel ondersteund voor geselecteerde distributies en versies.  Zie de [Azure Disk Encryption ondersteunde besturings systemen: Linux](../../security/azure-security-disk-encryption-prerequisites.md#linux) voor de lijst met Linux-distributies die worden ondersteund.

### <a name="internet-connectivity"></a>Internetconnectiviteit

Voor Azure Disk Encryption voor Linux is Internet connectiviteit vereist voor toegang tot Active Directory-, Key Vault-, opslag-en Package Management-eind punten.  Zie [Azure Disk Encryption vereisten](../../security/azure-security-disk-encryption-prerequisites.md)voor meer informatie.

## <a name="extension-schemata"></a>Extensie schema's

Er zijn twee schema's voor Azure Disk Encryption: v 1.1, een nieuwer, aanbevolen schema dat geen gebruik maakt van Azure Active Directory (AAD)-eigenschappen en v 0.1, een ouder schema dat AAD-eigenschappen vereist. U moet de schema versie gebruiken die overeenkomt met de extensie die u gebruikt: schema v 1.1 voor de AzureDiskEncryptionForLinux-extensie versie 1,1, schema v 0.1 voor de AzureDiskEncryptionForLinux-extensie versie 0,1.
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
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
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
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
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
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
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
| (0.1-schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0.1-schema) AADClientSecret | password | string |
| (0.1-schema) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-woorden lijst |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyEncryptionKeyURL | url | string |
| Beschrijving KeyVaultURL | url | string |
| Passphrase | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | Besturings systeem, gegevens, alle | string |

## <a name="template-deployment"></a>Sjabloonimplementatie

Zie voor een voor beeld van een sjabloon implementatie [versleuteling inschakelen op een actieve Linux-machine](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie

Instructies vindt u in de meest recente [documentatie van Azure cli](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Raadpleeg de [Azure Disk Encryption Troubleshooting Guide (Engelstalig](../../security/azure-security-disk-encryption-tsg.md)) voor het oplossen van problemen.

### <a name="support"></a>Ondersteuning

Als u hulp nodig hebt op elk gewenst moment in dit artikel, u kunt contact opnemen met de Azure-experts op het [forums voor Azure MSDN en Stack Overflow](https://azure.microsoft.com/support/community/). U kunt ook een Azure-ondersteuning-incident indienen. Ga naar de [ondersteuning van Azure site](https://azure.microsoft.com/support/options/) en selecteer Get-ondersteuning. Voor meer informatie over het gebruik van ondersteuning voor Azure, de [Veelgestelde vragen over Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Volgende stappen

Zie [extensies en functies van virtuele machines voor Linux](features-linux.md)voor meer informatie over VM-extensies.