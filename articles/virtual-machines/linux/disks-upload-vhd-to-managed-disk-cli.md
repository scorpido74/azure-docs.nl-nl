---
title: Een VHD uploaden naar Azure met behulp van Azure CLI
description: Meer informatie over het uploaden van een VHD naar een Azure Managed disk met behulp van de Azure CLI.
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: bc8932a9904a3e4e671edc3e624ff15e7253e1ed
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326826"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Een VHD uploaden naar Azure met behulp van Azure CLI

In dit artikel wordt uitgelegd hoe u een VHD van uw lokale machine uploadt naar een door Azure beheerde schijf. Voorheen moest u een recenter proces volgen dat uw gegevens in een opslag account heeft opgenomen en die opslag account beheren. U hoeft nu geen opslag account meer te beheren of gegevens in het werk gebied te laden om een VHD te uploaden. In plaats daarvan maakt u een lege beheerde schijf en uploadt u een VHD direct hiernaar. Dit vereenvoudigt het uploaden van on-premises Vm's naar Azure en biedt u de mogelijkheid om een VHD te uploaden naar 32 TiB rechtstreeks naar een grote beheerde schijf.

Als u een back-upoplossing voor IaaS-Vm's in azure levert, raden we u aan om direct uploaden te gebruiken voor het herstellen van back-ups van klanten naar beheerde schijven. Als u een VHD uploadt van een externe computer naar Azure, zijn de snelheden afhankelijk van uw lokale band breedte. Als u een virtuele machine van Azure gebruikt, is uw band breedte hetzelfde als standaard Hdd's.

Op dit moment wordt direct uploaden ondersteund voor standaard schijven, standaard SSD en Premium SSD Managed disks. Het wordt nog niet ondersteund voor Ultra Ssd's.

## <a name="prerequisites"></a>Vereisten

- Down load de nieuwste [versie van AzCopy V10 toevoegen](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installeer de Azure cli](/cli/azure/install-azure-cli).
- Een VHD-bestand lokaal opgeslagen

## <a name="create-an-empty-managed-disk"></a>Een lege beheerde schijf maken

Als u uw VHD naar Azure wilt uploaden, moet u een lege beheerde schijf maken die specifiek is geconfigureerd voor dit upload proces. Voordat u er een maakt, moet u over deze schijven enige aanvullende informatie weten.

Dit soort beheerde schijven heeft twee unieke statussen:

- ReadToUpload, wat betekent dat de schijf gereed is om een upload te ontvangen, maar geen [beveiligde toegangs handtekening](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) is gegenereerd.
- ActiveUpload, wat betekent dat de schijf gereed is om een upload te ontvangen en dat de SAS is gegenereerd.

In een van deze statussen wordt de beheerde schijf gefactureerd tegen [standaard prijzen voor harde schijven](https://azure.microsoft.com/pricing/details/managed-disks/), ongeacht het werkelijke type schijf. Een P10 wordt bijvoorbeeld gefactureerd als een S10. Dit is waar totdat `revoke-access` wordt aangeroepen op de beheerde schijf, wat vereist is om de schijf aan een virtuele machine te koppelen.

Voordat u een lege standaard HDD voor het uploaden kunt maken, moet u de bestands grootte van de VHD die u wilt uploaden, in bytes hebben. Om dat te bereiken, kunt u een `wc -c <yourFileName>.vhd` of `ls -al <yourFileName>.vhd` gebruiken. Deze waarde wordt gebruikt bij het opgeven van de para meter **--Upload-size-bytes** .

Maak een lege standaard HDD voor het uploaden door de para meter **--for-upload** en de para meter **--Upload-size-bytes** op te geven in een cmdlet voor het maken van een [schijf](/cli/azure/disk#az-disk-create) :

```azurecli-interactive
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Als u een Premium SSD of een Standard SSD wilt uploaden, vervangt u **standard_lrs** door ofwel **premium_LRS** of **standardssd_lrs**. Ultra-SSD wordt nog niet ondersteund.

U hebt nu een lege beheerde schijf gemaakt die voor het upload proces is geconfigureerd. Als u een VHD naar de schijf wilt uploaden, moet u een Beschrijf bare SAS hebben, zodat u deze kunt raadplegen als bestemming voor uw Upload.

Gebruik de volgende opdracht om een Beschrijf bare SAS van uw lege beheerde schijf te genereren:

```azurecli-interactive
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Voor beeld van geretourneerde waarde:

```
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>VHD uploaden

Nu u een SAS hebt voor uw lege beheerde schijf, kunt u deze gebruiken om de beheerde schijf als bestemming in te stellen voor uw Upload opdracht.

Gebruik AzCopy V10 toevoegen om uw lokale VHD-bestand te uploaden naar een beheerde schijf door de SAS-URI op te geven die u hebt gegenereerd.

Deze upload heeft dezelfde door Voer als de equivalente [standaard HDD](disks-types.md#standard-hdd). Als u bijvoorbeeld een grootte hebt die gelijk is aan S4, hebt u een door Voer van Maxi maal 60 MiB/s. Maar als u een grootte hebt die gelijk is aan S70, hebt u een door Voer van Maxi maal 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Als uw SAS verloopt tijdens het uploaden en u nog `revoke-access` geen sa's hebt aangeroepen, kunt u een nieuwe SAS ontvangen om de `grant-access`upload opnieuw te kunnen gebruiken.

Nadat het uploaden is voltooid en u geen gegevens meer naar de schijf hoeft te schrijven, trekt u de SAS in. Als u de SA'S intrekt, wordt de status van de beheerde schijf gewijzigd en kunt u de schijf koppelen aan een virtuele machine.

```azurecli-interactive
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="next-steps"></a>Volgende stappen

Nu u een VHD hebt geüpload naar een beheerde schijf, kunt u uw schijf koppelen aan een virtuele machine en het gebruik ervan starten.

Zie ons artikel over het onderwerp voor meer informatie over het koppelen van een schijf aan een virtuele machine: [Een schijf toevoegen aan een virtuele Linux-machine](add-disk.md).
