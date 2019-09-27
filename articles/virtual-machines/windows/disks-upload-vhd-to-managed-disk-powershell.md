---
title: Een VHD uploaden naar Azure met behulp van Azure PowerShell
description: Meer informatie over het uploaden van een VHD naar een Azure Managed disk met behulp van Azure PowerShell.
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: cd8c5b174d92edcf69801edaeabd0c0730985654
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326926"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Een VHD uploaden naar Azure met behulp van Azure PowerShell

In dit artikel wordt uitgelegd hoe u een VHD van uw lokale machine uploadt naar een door Azure beheerde schijf. Voorheen moest u een recenter proces volgen dat uw gegevens in een opslag account heeft opgenomen en die opslag account beheren. U hoeft nu geen opslag account meer te beheren of gegevens in het werk gebied te laden om een VHD te uploaden. In plaats daarvan maakt u een lege beheerde schijf en uploadt u een VHD direct hiernaar. Dit vereenvoudigt het uploaden van on-premises Vm's naar Azure en biedt u de mogelijkheid om een VHD te uploaden naar 32 TiB rechtstreeks naar een grote beheerde schijf.

Als u een back-upoplossing voor IaaS-Vm's in azure levert, raden we u aan om direct uploaden te gebruiken voor het herstellen van back-ups van klanten naar beheerde schijven. Als u een VHD uploadt van een externe computer naar Azure, zijn de snelheden afhankelijk van uw lokale band breedte. Als u een virtuele machine van Azure gebruikt, is uw band breedte hetzelfde als standaard Hdd's.

Op dit moment wordt direct uploaden ondersteund voor standaard schijven, standaard SSD en Premium SSD Managed disks. Het wordt nog niet ondersteund voor Ultra Ssd's.

## <a name="prerequisites"></a>Vereisten

- Down load de nieuwste [versie van AzCopy V10 toevoegen](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installeer de Azure PowerShell-module](/powershell/azure/install-Az-ps).
- Een VHD-bestand lokaal opgeslagen.

## <a name="create-an-empty-managed-disk"></a>Een lege beheerde schijf maken

Als u uw VHD naar Azure wilt uploaden, moet u een lege beheerde schijf maken die specifiek is geconfigureerd voor dit upload proces. Voordat u er een maakt, moet u over deze schijven enige aanvullende informatie weten.

Dit soort beheerde schijven heeft twee unieke statussen:

- ReadToUpload, wat betekent dat de schijf gereed is om een upload te ontvangen, maar geen [beveiligde toegangs handtekening](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) is gegenereerd.
- ActiveUpload, wat betekent dat de schijf gereed is om een upload te ontvangen en dat de SAS is gegenereerd.

In een van deze statussen wordt de beheerde schijf gefactureerd tegen [standaard prijzen voor harde schijven](https://azure.microsoft.com/pricing/details/managed-disks/), ongeacht het werkelijke type schijf. Een P10 wordt bijvoorbeeld gefactureerd als een S10. Dit is waar totdat `revoke-access` wordt aangeroepen op de beheerde schijf, wat vereist is om de schijf aan een virtuele machine te koppelen.

Voordat u een lege standaard HDD maakt voor het uploaden, hebt u de bestands grootte in bytes van de VHD die u wilt uploaden. Met de voorbeeld code krijgt u maar zelf, kunt u het volgende gebruiken: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`. Deze waarde wordt gebruikt bij het opgeven van de para meter **-UploadSizeInBytes** .

Maak nu op uw lokale shell een lege standaard HDD voor het uploaden door de **Upload** -instelling op te geven in de para meter **-CreateOption** en de para meter **-UploadSizeInBytes** in de cmdlet [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) . Roep vervolgens [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) aan om de schijf te maken:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Als u een Premium SSD of een Standard SSD wilt uploaden, vervangt u **Standard_LRS** door ofwel **Premium_LRS** of **StandardSSD_LRS**. Ultra-SSD wordt nog niet ondersteund.

U hebt nu een lege beheerde schijf gemaakt die voor het upload proces is geconfigureerd. Als u een VHD naar de schijf wilt uploaden, moet u een Beschrijf bare SAS hebben, zodat u deze kunt raadplegen als bestemming voor uw Upload.

Gebruik de volgende opdracht om een Beschrijf bare SAS van uw lege beheerde schijf te genereren:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>VHD uploaden

Nu u een SAS hebt voor uw lege beheerde schijf, kunt u deze gebruiken om de beheerde schijf als bestemming in te stellen voor uw Upload opdracht.

Gebruik AzCopy V10 toevoegen om uw lokale VHD-bestand te uploaden naar een beheerde schijf door de SAS-URI op te geven die u hebt gegenereerd.

Deze upload heeft dezelfde door Voer als de equivalente [standaard HDD](disks-types.md#standard-hdd). Als u bijvoorbeeld een grootte hebt die gelijk is aan S4, hebt u een door Voer van Maxi maal 60 MiB/s. Maar als u een grootte hebt die gelijk is aan S70, hebt u een door Voer van Maxi maal 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas --blob-type PageBlob
```

Als uw SAS verloopt tijdens het uploaden en u nog geen `revoke-access` sa's hebt aangeroepen, kunt u een nieuwe SAS krijgen om het uploaden `grant-access`met opnieuw te laten verlopen.

Nadat het uploaden is voltooid en u geen gegevens meer naar de schijf hoeft te schrijven, trekt u de SAS in. Als u de SA'S intrekt, wordt de status van de beheerde schijf gewijzigd en kunt u de schijf koppelen aan een virtuele machine.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="next-steps"></a>Volgende stappen

Nu u een VHD hebt geüpload naar een beheerde schijf, kunt u uw schijf koppelen aan een virtuele machine en het gebruik ervan starten.

Zie ons artikel over het onderwerp voor meer informatie over het koppelen van een schijf aan een virtuele machine: [Een gegevens schijf koppelen aan een virtuele Windows-machine met Power shell](attach-disk-ps.md).
