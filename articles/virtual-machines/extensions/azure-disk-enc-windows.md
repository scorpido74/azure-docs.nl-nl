---
title: Azure Disk Encryption voor Windows
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
ms.openlocfilehash: 8435663dcf92e2617ea2fe9218649e94243272d2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073212"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption voor Windows (micro soft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Overzicht

Azure Disk Encryption maakt gebruik van BitLocker om versleuteling van de volledige schijf te bieden op virtuele machines van Azure waarop Windows wordt uitgevoerd.  Deze oplossing is geïntegreerd met Azure Key Vault voor het beheren van schijf versleutelings sleutels en geheimen in uw sleutel kluis abonnement. 

## <a name="prerequisites"></a>Vereisten

Zie voor een volledige lijst met vereisten [Azure Disk Encryption voor Linux-vm's](../linux/disk-encryption-overview.md), met name de volgende secties:

- [Azure Disk Encryption voor Linux-Vm's](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netwerk vereisten](../windows/disk-encryption-overview.md#networking-requirements)
- [groepsbeleid vereisten](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schemata"></a>Extensie schema's

Er zijn twee schema's voor de Windows AzureDiskEncryption-uitbrei ding: v 2.2, een nieuwer, aanbevolen schema dat geen gebruik maakt van Azure Active Directory (AAD)-eigenschappen en v 1.1, een ouder schema dat AAD-eigenschappen vereist. U moet de schema versie gebruiken die overeenkomt met de extensie die u gebruikt: schema v 2.2 voor de AzureDiskEncryption-extensie versie 2,2, schema v 1.1 voor de AzureDiskEncryption-extensie versie 1,1.

### <a name="schema-v22-no-aad-recommended"></a>Schema v 2.2: geen AAD (aanbevolen)

Het v 2.2-schema wordt aanbevolen voor alle nieuwe virtuele machines en vereist geen Azure Active Directory eigenschappen.

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


### <a name="schema-v11-with-aad"></a>Schema v 1.1: met AAD 

Het 1,1-schema vereist `aadClientID` en `aadClientSecret` of `AADClientCertificate` en wordt niet aanbevolen voor nieuwe Vm's.

`aadClientSecret`gebruiken:

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

`AADClientCertificate`gebruiken:

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

| Naam | Waarde / voorbeeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | tekenreeks |
| type | AzureDiskEncryptionForLinux | tekenreeks |
| typeHandlerVersion | 1,1, 2,2 | tekenreeks |
| (1,1-schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (1,1-schema) AADClientSecret | wachtwoord | tekenreeks |
| (1,1-schema) AADClientCertificate | thumbprint | tekenreeks |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-woorden lijst |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | tekenreeks | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | tekenreeks |
| KeyEncryptionKeyURL | url | tekenreeks |
| KeyVaultURL | url | tekenreeks |
| Beschrijving Passphrase | wachtwoord | tekenreeks | 
| SequenceVersion | uniqueidentifier | tekenreeks |
| VolumeType | Besturings systeem, gegevens, alle | tekenreeks |

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
