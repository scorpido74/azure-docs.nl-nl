---
title: Azure Disk Encryption voor Linux
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
ms.openlocfilehash: 4fa7f7d1419a8cd1006a632ba67587ab3434bf5a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073812"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption voor Linux (micro soft. Azure. Security. AzureDiskEncryptionForLinux)

## <a name="overview"></a>Overzicht

Azure Disk Encryption maakt gebruik van het subsysteem DM-cryptografie in Linux om versleuteling van de volledige schijf te bieden voor het [selecteren van Azure Linux-distributies](https://aka.ms/adelinux).  Deze oplossing is geïntegreerd met Azure Key Vault voor het beheren van schijf versleutelings sleutels en geheimen.

## <a name="prerequisites"></a>Vereisten

Zie voor een volledige lijst met vereisten [Azure Disk Encryption voor Linux-vm's](../linux/disk-encryption-overview.md), met name de volgende secties:

- [Azure Disk Encryption voor Linux-Vm's](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Aanvullende VM-vereisten](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Netwerk vereisten](../linux/disk-encryption-overview.md#networking-requirements)

## <a name="extension-schemata"></a>Extensie schema's

Er zijn twee schema's voor Azure Disk Encryption: v 1.1, een nieuwer, aanbevolen schema dat geen gebruik maakt van Azure Active Directory (AAD)-eigenschappen en v 0.1, een ouder schema dat AAD-eigenschappen vereist. U moet de schema versie gebruiken die overeenkomt met de extensie die u gebruikt: schema v 1.1 voor de AzureDiskEncryptionForLinux-extensie versie 1,1, schema v 0.1 voor de AzureDiskEncryptionForLinux-extensie versie 0,1.
### <a name="schema-v11-no-aad-recommended"></a>Schema v 1.1: geen AAD (aanbevolen)

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

Voor het 0,1-schema zijn `aadClientID` en `aadClientSecret` of `AADClientCertificate`vereist.

`aadClientSecret`gebruiken:

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

`AADClientCertificate`gebruiken:

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

| Naam | Waarde / voorbeeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | tekenreeks |
| type | AzureDiskEncryptionForLinux | tekenreeks |
| typeHandlerVersion | 0,1, 1,1 | int |
| (0.1-schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0.1-schema) AADClientSecret | wachtwoord | tekenreeks |
| (0.1-schema) AADClientCertificate | thumbprint | tekenreeks |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-woorden lijst |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | tekenreeks | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | tekenreeks |
| KeyEncryptionKeyURL | url | tekenreeks |
| Beschrijving KeyVaultURL | url | tekenreeks |
| Passphrase | wachtwoord | tekenreeks | 
| SequenceVersion | uniqueidentifier | tekenreeks |
| VolumeType | Besturings systeem, gegevens, alle | tekenreeks |

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