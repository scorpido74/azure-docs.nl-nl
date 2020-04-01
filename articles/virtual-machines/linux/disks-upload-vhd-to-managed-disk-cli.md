---
title: Een VHD uploaden naar Azure of een schijf kopiëren tussen regio's - Azure CLI
description: Meer informatie over het uploaden van een VHD naar een door Azure beheerde schijf en het kopiëren van een beheerde schijf in verschillende regio's, met behulp van de Azure CLI, via directe upload.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 6813206aebe67e4e6d2afd0d9c78d03f0c20c952
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420945"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Een VHD uploaden naar Azure of een beheerde schijf kopiëren naar een andere regio - Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Vereisten

- Download de nieuwste [versie van AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installeer de Azure CLI](/cli/azure/install-azure-cli).
- Als u van plan bent om een VHD te uploaden van on-premises: een vhd met vaste grootte die [is voorbereid voor Azure,](../windows/prepare-for-upload-vhd-image.md)lokaal opgeslagen.
- Of een beheerde schijf in Azure als u van plan bent een kopieeractie uit te voeren.

## <a name="getting-started"></a>Aan de slag

Als u liever schijven uploadt via een GUI, u dit doen met Azure Storage Explorer. Voor meer informatie verwijzen naar: [Azure Storage Explorer gebruiken om beheerde Azure-schijven te beheren](disks-use-storage-explorer-managed-disks.md)

Als u uw VHD wilt uploaden naar Azure, moet u een lege beheerde schijf maken die is geconfigureerd voor dit uploadproces. Voordat u er een maakt, is er nog meer informatie die u moet weten over deze schijven.

Dit soort beheerde schijf heeft twee unieke toestanden:

- ReadToUpload, wat betekent dat de schijf klaar is om een upload te ontvangen, maar er is geen [secure access signature](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) gegenereerd.
- ActiveUpload, wat betekent dat de schijf klaar is om een upload te ontvangen en de SAS is gegenereerd.

> [!NOTE]
> Terwijl in een van deze staten, de beheerde schijf zal worden gefactureerd op [standaard HDD prijzen,](https://azure.microsoft.com/pricing/details/managed-disks/)ongeacht het werkelijke type schijf. Een P10 wordt bijvoorbeeld gefactureerd als een S10. Dit geldt totdat `revoke-access` wordt aangeroepen op de beheerde schijf, die nodig is om de schijf te koppelen aan een VM.

## <a name="create-an-empty-managed-disk"></a>Een lege beheerde schijf maken

Voordat u een lege standaardHDD maken voor het uploaden, hebt u de bestandsgrootte nodig van de VHD die u wilt uploaden, in bytes. Om dat te krijgen, `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd`kunt u een van beide gebruiken of . Deze waarde wordt gebruikt bij het opgeven van de parameter **--upload-size-bytes.**

Maak een lege standaard HDD voor uploaden door zowel de parameter **-for-upload** als de parameter **--upload-size-bytes** op te geven in een [schijf maken](/cli/azure/disk#az-disk-create) cmdlet:

Vervang `<yourdiskname>` `<yourresourcegroupname>`, `<yourregion>` , met waarden van uw keuze. De `--upload-size-bytes` parameter bevat een `34359738880`voorbeeldwaarde van , vervang deze door een waarde die bij u past.

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Als u een premium SSD of een standaard SSD wilt uploaden, vervang **dan standard_lrs** door **premium_LRS** of **standardssd_lrs.** Ultra schijven worden niet ondersteund voor nu.

Nu u een lege beheerde schijf hebt gemaakt die is geconfigureerd voor het uploadproces, u er een VHD naar uploaden. Als u een VHD naar de schijf wilt uploaden, hebt u een schrijfbare SAS nodig, zodat u deze verwijzen als de bestemming voor uw upload.

Als u een schrijfbare SAS van uw `<yourdiskname>` `<yourresourcegroupname>`lege beheerde schijf wilt genereren, vervangt en gebruikt u vervolgens de volgende opdracht:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Voorbeeld geretourneerde waarde:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Een VHD uploaden

Nu u een SAS voor uw lege beheerde schijf hebt, u deze gebruiken om uw beheerde schijf in te stellen als bestemming voor uw uploadopdracht.

Gebruik AzCopy v10 om uw lokale VHD-bestand te uploaden naar een beheerde schijf door de SAS URI op te geven die u hebt gegenereerd.

Deze upload heeft dezelfde doorvoer als de gelijkwaardige [standaard HDD.](disks-types.md#standard-hdd) Als u bijvoorbeeld een grootte hebt die overeenkomt met S4, hebt u een doorvoervan maximaal 60 MiB/s. Maar als je een grootte hebt die gelijk staat aan S70, heb je een doorvoer van maximaal 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Nadat de upload is voltooid en u geen gegevens meer naar de schijf hoeft te schrijven, trekt u de SAS in. Als u de SAS intrekt, verandert u de status van de beheerde schijf en u de schijf aan een vm koppelen.

Vervang `<yourdiskname>` `<yourresourcegroupname>`en gebruik vervolgens de volgende opdracht om de schijf bruikbaar te maken:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
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

Nu u een VHD met succes hebt geüpload naar een beheerde schijf, u de schijf als [gegevensschijf](add-disk.md) aan een bestaande vm koppelen of [de schijf als osschijf aan een VM koppelen](upload-vhd.md#create-the-vm)om een nieuwe vm te maken. 

