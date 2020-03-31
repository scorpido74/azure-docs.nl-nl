---
title: Azure-schijfversleuteling voor Windows
description: Implementeert Azure Disk Encryption naar een Virtuele Windows-machine met behulp van een virtuele machine-extensie.
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
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: e975e1757b77b4aab52a59d1f0709ef9cadae94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066870"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure-schijfversleuteling voor Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Overzicht

Azure Disk Encryption maakt gebruik van BitLocker om volledige schijfversleuteling te bieden op virtuele Azure-machines met Windows.  Deze oplossing is geïntegreerd met Azure Key Vault om schijfversleutelingssleutels en -geheimen te beheren in uw key vault-abonnement. 

## <a name="prerequisites"></a>Vereisten

Zie Azure Disk Encryption for Windows VM's voor een volledige lijst met vereisten, zie [Azure Disk Encryption for Windows VM's,](../windows/disk-encryption-overview.md)met name de volgende secties:

- [Ondersteunde VM's en besturingssystemen](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netwerkvereisten](../windows/disk-encryption-overview.md#networking-requirements)
- [Vereisten voor groepsbeleid](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Uitbreidingsschema

Er zijn twee versies van extensieschema's voor Azure Disk Encryption (ADE):
- v2.2 - Een nieuwer aanbevolen schema dat geen AAD-eigenschappen (Azure Active Directory) gebruikt.
- v1.1 - Een ouder schema waarvoor AAD-eigenschappen (Azure Active Directory) vereist zijn. 

Als u een doelschema wilt selecteren, moet de eigenschap worden ingesteld die gelijk is aan de `typeHandlerVersion` versie van het schema dat u wilt gebruiken.

### <a name="schema-v22-no-aad-recommended"></a>Schema v2.2: Geen AAD (aanbevolen)

Het v2.2-schema wordt aanbevolen voor alle nieuwe VM's en vereist geen Azure Active Directory-eigenschappen.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Schema v1.1: met AAD 

Het 1.1-schema vereist `aadClientID` en vereist `aadClientSecret` of `AADClientCertificate` wordt niet aanbevolen voor nieuwe VM's.

Met `aadClientSecret`behulp van :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Met `AADClientCertificate`behulp van :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde / Voorbeeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| uitgever | Microsoft.Azure.Security | tekenreeks |
| type | AzureDiskEncryption | tekenreeks |
| typeHandlerVersie | 2.2, 1.1 | tekenreeks |
| (1.1 schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Guid | 
| (1.1 schema) AADClientSecret | wachtwoord | tekenreeks |
| (1.1 schema) AADClientCertificaat | Vingerafdruk | tekenreeks |
| EncryptieOperatie | Encryptie inschakelen, EncryptieOpmaakall inschakelen | tekenreeks | 
| (optioneel - standaard RSA-OAEP) Sleutelversleutelingsalgoritme | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | tekenreeks |
| KeyVaultURL | url | tekenreeks |
| KeyVaultResourceId | url | tekenreeks |
| (facultatief) KeyEncryptionKeyURL | url | tekenreeks |
| (facultatief) KekVaultResourceId | url | tekenreeks |
| (facultatief) SequenceVersie | uniqueidentifier | tekenreeks |
| VolumeType | OS, Gegevens, Alles | tekenreeks |

## <a name="template-deployment"></a>Sjabloonimplementatie

Zie Azure QuickStart Template [201-encrypt-running-windows-vm-zonder-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)voor een voorbeeld van sjabloonimplementatie op basis van schema v2.2.

Zie Azure QuickStart Template [201-encrypt-running-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)voor een voorbeeld van sjabloonimplementatie op basis van schema v1.1.

>[!NOTE]
> Ook `VolumeType` als de parameter is ingesteld op Alles, worden gegevensschijven alleen versleuteld als ze correct zijn opgemaakt. 

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Raadpleeg voor het oplossen van problemen de [handleiding voor het oplossen van problemen met azure disk encryptie](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/community/) 

U ook een Azure-ondersteuningsincident indienen. Ga naar [Azure-ondersteuning](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de [veelgestelde vragen](https://azure.microsoft.com/support/faq/)over Microsoft Azure Support voor informatie over het gebruik van Azure Support.

## <a name="next-steps"></a>Volgende stappen

* Zie [Virtuele machine-extensies en -functies voor Windows voor](features-windows.md)meer informatie over extensies.
* Zie [Virtuele Windows-apparaten](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines)voor meer informatie over Azure Disk Encryption voor Windows.
