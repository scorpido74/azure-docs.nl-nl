---
title: Een VHD uploaden met de Azure CLI
description: Meer informatie over het uploaden van een vhd naar een door Azure beheerde schijf en het kopiëren van een beheerde schijf in verschillende regio's, met behulp van de Azure CLI, via directe upload.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: d89a4279d425e4b12e92aae81edfd6c1514c3eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062670"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Een vhd uploaden naar Azure met Azure CLI

In dit artikel wordt uitgelegd hoe u een vhd van uw lokale machine uploadt naar een door Azure beheerde schijf. Voorheen moest u een meer betrokken proces volgen, waarbij uw gegevens in een opslagaccount werden georganiseerd en dat opslagaccount werd beheerd. Nu hoeft u geen opslagaccount meer te beheren of gegevens te fasen om een vhd te uploaden. In plaats daarvan maakt u een lege beheerde schijf en uploadt u er rechtstreeks een vhd naar. Dit vereenvoudigt het uploaden van on-premises VM's naar Azure en stelt u in staat om een vhd tot 32 TiB rechtstreeks te uploaden naar een grote beheerde schijf.

Als u een back-upoplossing voor IaaS-vm's in Azure aanbiedt, raden we u aan directe uploadte te gebruiken om back-ups van klanten te herstellen naar beheerde schijven. Als u een VHD uploadt van een machine buiten Azure, zijn de snelheden afhankelijk van uw lokale bandbreedte. Als u een Azure VM gebruikt, is uw bandbreedte gelijk aan standaard HDD's.

Momenteel wordt direct uploadondersteund voor standaard HDD, standaard SSD en premium SSD managed disks. Het wordt nog niet ondersteund voor ultra SSDs.

## <a name="prerequisites"></a>Vereisten

- Download de nieuwste [versie van AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installeer de Azure CLI](/cli/azure/install-azure-cli).
- Een vhd-bestand, lokaal opgeslagen
- Als u van plan bent om een vhd te uploaden van on-premises: een vhd met vaste grootte die [is voorbereid voor Azure,](../windows/prepare-for-upload-vhd-image.md)lokaal opgeslagen.
- Of een beheerde schijf in Azure als u van plan bent een kopieeractie uit te voeren.

## <a name="create-an-empty-managed-disk"></a>Een lege beheerde schijf maken

Als u uw vhd wilt uploaden naar Azure, moet u een lege beheerde schijf maken die is geconfigureerd voor dit uploadproces. Voordat u er een maakt, is er nog meer informatie die u moet weten over deze schijven.

Dit soort beheerde schijf heeft twee unieke toestanden:

- ReadToUpload, wat betekent dat de schijf klaar is om een upload te ontvangen, maar er is geen [secure access signature](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) gegenereerd.
- ActiveUpload, wat betekent dat de schijf klaar is om een upload te ontvangen en de SAS is gegenereerd.

Terwijl in een van deze staten, de beheerde schijf zal worden gefactureerd op [standaard HDD prijzen,](https://azure.microsoft.com/pricing/details/managed-disks/)ongeacht het werkelijke type schijf. Een P10 wordt bijvoorbeeld gefactureerd als een S10. Dit geldt totdat `revoke-access` wordt aangeroepen op de beheerde schijf, die nodig is om de schijf te koppelen aan een VM.

Voordat u een lege standaard HDD maken voor het uploaden, moet u de bestandsgrootte hebben van de vhd die u wilt uploaden, in bytes. Om dat te krijgen, `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd`kunt u een van beide gebruiken of . Deze waarde wordt gebruikt bij het opgeven van de parameter **--upload-size-bytes.**

Maak een lege standaard HDD voor uploaden door zowel de parameter **-for-upload** als de parameter **--upload-size-bytes** op te geven in een [schijf maken](/cli/azure/disk#az-disk-create) cmdlet:

```azurecli
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Als u een premium SSD of een standaard SSD wilt uploaden, vervang **dan standard_lrs** door **premium_LRS** of **standardssd_lrs.** Ultra SSD wordt nog niet ondersteund.

U hebt nu een lege beheerde schijf gemaakt die is geconfigureerd voor het uploadproces. Als u een vhd naar de schijf wilt uploaden, hebt u een schrijfbare SAS nodig, zodat u deze verwijzen als de bestemming voor uw upload.

Als u een beschrijfbare SAS van uw lege beheerde schijf wilt genereren, gebruikt u de volgende opdracht:

```azurecli
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Voorbeeld geretourneerde waarde:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Vhd uploaden

Nu u een SAS voor uw lege beheerde schijf hebt, u deze gebruiken om uw beheerde schijf in te stellen als bestemming voor uw uploadopdracht.

Gebruik AzCopy v10 om uw lokale VHD-bestand te uploaden naar een beheerde schijf door de SAS URI op te geven die u hebt gegenereerd.

Deze upload heeft dezelfde doorvoer als de gelijkwaardige [standaard HDD.](disks-types.md#standard-hdd) Als u bijvoorbeeld een grootte hebt die overeenkomt met S4, hebt u een doorvoervan maximaal 60 MiB/s. Maar als je een grootte hebt die gelijk staat aan S70, heb je een doorvoer van maximaal 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Nadat de upload is voltooid en u geen gegevens meer naar de schijf hoeft te schrijven, trekt u de SAS in. Als u de SAS intrekt, verandert u de status van de beheerde schijf en u de schijf aan een vm koppelen.

```azurecli
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>Een beheerde schijf kopiëren

Directe upload vereenvoudigt ook het proces van het kopiëren van een beheerde schijf. U kopiëren binnen dezelfde regio of cross-regio (naar een andere regio).

Het volgscript doet dit voor u, het proces is vergelijkbaar met de eerder beschreven stappen, met enkele verschillen omdat u met een bestaande schijf werkt.

> [!IMPORTANT]
> U moet een verschuiving van 512 toevoegen wanneer u de schijfgrootte in bytes van een beheerde schijf vanuit Azure opgeeft. Dit komt omdat Azure de voettekst weglaat bij het retourneren van de schijfgrootte. De kopie zal mislukken als u dit niet doet. Het volgende script doet dit al voor u.

Vervang `<sourceResourceGroupHere>`het `<sourceDiskNameHere>` `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourTargetLocationHere>` , en (een voorbeeld van een locatiewaarde zou uswest2 zijn) door uw waarden en voer vervolgens het volgende script uit om een beheerde schijf te kopiëren.

```azurecli
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Volgende stappen

Nu u een vhd met succes hebt geüpload naar een beheerde schijf, u de schijf als [gegevensschijf](add-disk.md) aan een bestaande vm koppelen of [de schijf als osschijf aan een VM koppelen](upload-vhd.md#create-the-vm)om een nieuwe vm te maken. 

