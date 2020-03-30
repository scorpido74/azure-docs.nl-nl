---
title: Een vhd uploaden naar Azure met Azure PowerShell
description: Meer informatie over het uploaden van een vhd naar een door Azure beheerde schijf en het kopiëren van een beheerde schijf in verschillende regio's, met Azure PowerShell, via directe upload.
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 883fea1e25ded26c35e96d11edd8f417e96db30e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369553"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Een vhd uploaden naar Azure met Azure PowerShell

In dit artikel wordt uitgelegd hoe u een vhd van uw lokale machine uploadt naar een door Azure beheerde schijf. Voorheen moest u een meer betrokken proces volgen, waarbij uw gegevens in een opslagaccount werden georganiseerd en dat opslagaccount werd beheerd. Nu hoeft u geen opslagaccount meer te beheren of gegevens te fasen om een vhd te uploaden. In plaats daarvan maakt u een lege beheerde schijf en uploadt u er rechtstreeks een vhd naar. Dit vereenvoudigt het uploaden van on-premises VM's naar Azure en stelt u in staat om een vhd tot 32 TiB rechtstreeks te uploaden naar een grote beheerde schijf.

Als u een back-upoplossing voor IaaS-vm's in Azure aanbiedt, raden we u aan directe uploadte te gebruiken om back-ups van klanten te herstellen naar beheerde schijven. Als u een VHD uploadt van een machine buiten Azure, zijn de snelheden afhankelijk van uw lokale bandbreedte. Als u een Azure VM gebruikt, is uw bandbreedte gelijk aan standaard HDD's.

Momenteel wordt direct uploadondersteund voor standaard HDD, standaard SSD en premium SSD managed disks. Het wordt nog niet ondersteund voor ultra SSDs.

## <a name="prerequisites"></a>Vereisten

- Download de nieuwste [versie van AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installeer Azure PowerShell-module](/powershell/azure/install-Az-ps).
- Als u van plan bent om een VHD te uploaden van on-premises: een vhd met vaste grootte die [is voorbereid voor Azure,](prepare-for-upload-vhd-image.md)lokaal opgeslagen.
- Of een beheerde schijf in Azure als u van plan bent een kopieeractie uit te voeren.

## <a name="create-an-empty-managed-disk"></a>Een lege beheerde schijf maken

Als u uw vhd wilt uploaden naar Azure, moet u een lege beheerde schijf maken die is geconfigureerd voor dit uploadproces. Voordat u er een maakt, is er nog meer informatie die u moet weten over deze schijven.

Dit soort beheerde schijf heeft twee unieke toestanden:

- ReadToUpload, wat betekent dat de schijf klaar is om een upload te ontvangen, maar er is geen [secure access signature](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) gegenereerd.
- ActiveUpload, wat betekent dat de schijf klaar is om een upload te ontvangen en de SAS is gegenereerd.

Terwijl in een van deze staten, de beheerde schijf zal worden gefactureerd op [standaard HDD prijzen,](https://azure.microsoft.com/pricing/details/managed-disks/)ongeacht het werkelijke type schijf. Een P10 wordt bijvoorbeeld gefactureerd als een S10. Dit geldt totdat `revoke-access` wordt aangeroepen op de beheerde schijf, die nodig is om de schijf te koppelen aan een VM.

Voordat u een lege standaardHDD maakt voor het uploaden, hebt u de bestandsgrootte nodig in bytes van de vhd die u wilt uploaden. De voorbeeldcode krijgt dat voor u, maar om `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`het zelf te doen u gebruiken:. Deze waarde wordt gebruikt bij het opgeven van de parameter **-UploadSizeInBytes.**

Maak nu op uw lokale shell een lege standaardHDD voor uploaden door de instelling **Upload** op te geven in de parameter **-CreateOption** en de parameter **-UploadSizeInBytes** in de cmdlet [Nieuw-AzDiskConfig.](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) Bel vervolgens [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) om de schijf te maken:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Als u een premium SSD of een standaard SSD wilt uploaden, vervang **dan Standard_LRS** door **Premium_LRS** of **StandardSSD_LRS.** Ultra SSD wordt nog niet ondersteund.

U hebt nu een lege beheerde schijf gemaakt die is geconfigureerd voor het uploadproces. Als u een vhd naar de schijf wilt uploaden, hebt u een schrijfbare SAS nodig, zodat u deze verwijzen als de bestemming voor uw upload.

Als u een beschrijfbare SAS van uw lege beheerde schijf wilt genereren, gebruikt u de volgende opdracht:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>Vhd uploaden

Nu u een SAS voor uw lege beheerde schijf hebt, u deze gebruiken om uw beheerde schijf in te stellen als bestemming voor uw uploadopdracht.

Gebruik AzCopy v10 om uw lokale VHD-bestand te uploaden naar een beheerde schijf door de SAS URI op te geven die u hebt gegenereerd.

Deze upload heeft dezelfde doorvoer als de gelijkwaardige [standaard HDD.](disks-types.md#standard-hdd) Als u bijvoorbeeld een grootte hebt die overeenkomt met S4, hebt u een doorvoervan maximaal 60 MiB/s. Maar als je een grootte hebt die gelijk staat aan S70, heb je een doorvoer van maximaal 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Nadat de upload is voltooid en u geen gegevens meer naar de schijf hoeft te schrijven, trekt u de SAS in. Als u de SAS intrekt, verandert u de status van de beheerde schijf en u de schijf aan een vm koppelen.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Een beheerde schijf kopiëren

Directe upload vereenvoudigt ook het proces van het kopiëren van een beheerde schijf. U kopiëren binnen dezelfde regio of cross-regio (naar een andere regio).

Het volgscript doet dit voor u, het proces is vergelijkbaar met de eerder beschreven stappen, met enkele verschillen omdat u met een bestaande schijf werkt.

> [!IMPORTANT]
> U moet een verschuiving van 512 toevoegen wanneer u de schijfgrootte in bytes van een beheerde schijf vanuit Azure opgeeft. Dit komt omdat Azure de voettekst weglaat bij het retourneren van de schijfgrootte. De kopie zal mislukken als u dit niet doet. Het volgende script doet dit al voor u.

Vervang `<sourceResourceGroupHere>`het `<sourceDiskNameHere>` `<targetDiskNameHere>`, `<targetResourceGroupHere>` `<yourOSTypeHere>` , `<yourTargetLocationHere>` , en (een voorbeeld van een locatiewaarde zou uswest2 zijn) door uw waarden en voer vervolgens het volgende script uit om een beheerde schijf te kopiëren.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Volgende stappen

Nu u een vhd met succes hebt geüpload naar een beheerde schijf, u uw schijf aan een vm koppelen en deze gaan gebruiken.

Zie ons artikel over het onderwerp: [Een gegevensschijf koppelen aan een Windows-vm met PowerShell](attach-disk-ps.md)voor meer informatie over het koppelen van een gegevensschijf aan een virtuele machine. Zie [Een Windows-vm maken vanaf een gespecialiseerde schijf](create-vm-specialized.md#create-the-new-vm)als de schijf voor het besturingssysteem.
