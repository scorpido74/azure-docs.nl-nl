---
title: Azure-schijfversleuteling voor Linux
description: Implementeert Azure Disk Encryption voor Linux naar een virtuele machine met behulp van een virtuele machineextensie.
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
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 22568c7c23771f143f6cd583114949c380d15e3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066921"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure-schijfversleuteling voor Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Overzicht

Azure Disk Encryption maakt gebruik van het dm-crypt subsysteem in Linux om volledige schijfversleuteling te bieden op [bepaalde Azure Linux-distributies.](https://aka.ms/adelinux)  Deze oplossing is geïntegreerd met Azure Key Vault om schijfversleutelingssleutels en -geheimen te beheren.

## <a name="prerequisites"></a>Vereisten

Zie [Azure Disk Encryption for Linux VM's voor](../linux/disk-encryption-overview.md)een volledige lijst met vereisten, in het bijzonder de volgende secties:

- [Ondersteunde VM's en besturingssystemen](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Aanvullende VM-vereisten](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Netwerkvereisten](../linux/disk-encryption-overview.md#networking-requirements)
- [Vereisten voor opslag van versleutelingssleutels](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Uitbreidingsschema

Er zijn twee versies van extensieschema's voor Azure Disk Encryption (ADE):
- v1.1 - Een nieuwer aanbevolen schema dat geen AAD-eigenschappen (Azure Active Directory) gebruikt.
- v0.1 - Een ouder schema waarvoor AAD-eigenschappen (Azure Active Directory) vereist zijn. 

Als u een doelschema wilt selecteren, moet de eigenschap worden ingesteld die gelijk is aan de `typeHandlerVersion` versie van het schema dat u wilt gebruiken.

### <a name="schema-v11-no-aad-recommended"></a>Schema v1.1: Geen AAD (aanbevolen)

Het v1.1-schema wordt aanbevolen en vereist geen AZURE Active Directory -eigenschappen (AAD).

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Schema v0.1: met AAD 

Het 0.1-schema vereist `AADClientID` en een van beide `AADClientSecret` of `AADClientCertificate`.

Met `AADClientSecret`behulp van :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
| type | AzureDiskEncryptionForLinux | tekenreeks |
| typeHandlerVersie | 1.1, 0.1 | int |
| (0.1-schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Guid | 
| (0.1-schema) AADClientSecret | wachtwoord | tekenreeks |
| (0.1-schema) AADClientCertificaat | Vingerafdruk | tekenreeks |
| (facultatief) (0.1-schema) Passphrase | wachtwoord | tekenreeks |
| DiskFormatQuery | {"dev_path":"","""naam":",""file_system":"""} | JSON-woordenboek |
| EncryptieOperatie | Encryptie inschakelen, EncryptieOpmaakall inschakelen | tekenreeks | 
| (optioneel - standaard RSA-OAEP) Sleutelversleutelingsalgoritme | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | tekenreeks |
| KeyVaultURL | url | tekenreeks |
| KeyVaultResourceId | url | tekenreeks |
| (facultatief) KeyEncryptionKeyURL | url | tekenreeks |
| (facultatief) KekVaultResourceId | url | tekenreeks |
| (facultatief) SequenceVersie | uniqueidentifier | tekenreeks |
| VolumeType | OS, Gegevens, Alles | tekenreeks |

## <a name="template-deployment"></a>Sjabloonimplementatie

Zie de Azure Quickstart Template [201-encrypt-running-linux-vm-zonder-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)voor een voorbeeld van sjabloonimplementatie op basis van schema v1.1.

Zie de Azure Quickstart Template [201-encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)voor een voorbeeld van sjabloonimplementatie op basis van schema v0.1.

>[!WARNING]
> - Als u azure-schijfversleuteling eerder met Azure AD hebt gebruikt om een vm te versleutelen, moet u deze optie blijven gebruiken om uw vm te versleutelen.
> - Bij het versleutelen van Linux-besturingssysteemvolumes moet de VM als niet beschikbaar worden beschouwd. We raden ten zeerste aan om SSH-aanmeldingen te voorkomen terwijl de versleuteling aan de gang is om te voorkomen dat er problemen worden geblokkeerd bij het blokkeren van geopende bestanden die tijdens het versleutelingsproces moeten worden geopend. Als u de voortgang wilt controleren, gebruikt u de powerdlet [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell of de [vm-versleuteling:](/cli/azure/vm/encryption#az-vm-encryption-show) CLI. Dit proces kan naar verwachting een paar uur duren voor een 30GB OS volume, plus extra tijd voor het versleutelen van gegevensvolumes. De versleutelingstijd van het gegevensvolume is evenredig aan de grootte en hoeveelheid van de gegevensvolumes, tenzij de versleutelingsindeling alle opties wordt gebruikt. 
> - Het uitschakelen van versleuteling op Linux VM's wordt alleen ondersteund voor gegevensvolumes. Het wordt niet ondersteund op gegevens of BE-volumes als het os-volume is versleuteld. 

>[!NOTE]
> Ook `VolumeType` als de parameter is ingesteld op Alles, worden gegevensschijven alleen versleuteld als ze goed zijn gemonteerd.

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Raadpleeg voor het oplossen van problemen de [handleiding voor het oplossen van problemen met azure disk encryptie](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/community/) 

U ook een Azure-ondersteuningsincident indienen. Ga naar [Azure-ondersteuning](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de [veelgestelde vragen](https://azure.microsoft.com/support/faq/)over Microsoft Azure Support voor informatie over het gebruik van Azure Support.

## <a name="next-steps"></a>Volgende stappen

* Zie [Virtuele machine-extensies en -functies voor Linux voor](features-linux.md)meer informatie over VM-extensies.
* Zie [virtuele Linux-machines](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)voor meer informatie over Azure Disk Encryption voor Linux.
