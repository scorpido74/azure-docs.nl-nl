---
title: De prestaties van Azure Managed disks wijzigen
description: Meer informatie over prestatie lagen voor Managed disks en informatie over het wijzigen van prestatie lagen voor bestaande beheerde schijven.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: efbe8bc24b430716da46601ed073300e4c79cca7
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743723"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Prestatie lagen voor beheerde schijven (preview-versie)

Azure Disk Storage biedt momenteel ingebouwde burst-mogelijkheden om betere prestaties te bieden voor het verwerken van onverwacht verkeer op korte termijn. Premium-Ssd's hebben de flexibiliteit om de schijf prestaties te verbeteren zonder de daad werkelijke schijf grootte te verg Roten. Met deze mogelijkheid kunt u voldoen aan de prestatie behoeften van uw werk belasting en de kosten verlagen. 

> [!NOTE]
> Deze functie is momenteel in preview. 

Deze functie is ideaal voor gebeurtenissen waarvoor tijdelijk een consistent hoger prestatie niveau is vereist, zoals het kopen van vakantie dagen, het testen van prestaties of het uitvoeren van een trainings omgeving. Als u deze gebeurtenissen wilt verwerken, kunt u een hogere prestatie laag gebruiken, zolang u deze nodig hebt. U kunt vervolgens terugkeren naar de oorspronkelijke laag wanneer u de extra prestaties niet meer nodig hebt.

## <a name="how-it-works"></a>Uitleg

Wanneer u een schijf voor het eerst implementeert of inricht, wordt de basislijn prestatie laag voor die schijf ingesteld op basis van de ingerichte schijf grootte. U kunt een hogere prestatie niveau gebruiken om te voldoen aan de hogere vraag. Wanneer u dat prestatie niveau niet meer nodig hebt, kunt u teruggaan naar de initiële prestatie laag van de basis lijn.

Uw facturering wordt gewijzigd wanneer de laag wordt gewijzigd. Als u bijvoorbeeld een P10-schijf inricht (128 GiB), wordt de laag voor basislijn prestaties ingesteld op P10 (500 IOPS en 100 MBps). U wordt gefactureerd tegen het P10ings bedrag. U kunt de laag bijwerken zodat deze overeenkomt met de prestaties van P50 (7.500 IOPS en 250 MBps) zonder de schijf grootte te verg Roten. Tijdens de upgrade wordt u gefactureerd tegen het P50ings bedrag. Wanneer u de hogere prestaties niet meer nodig hebt, kunt u teruggaan naar de P10-laag. De schijf wordt opnieuw gefactureerd tegen het P10ings bedrag.

| Schijfgrootte | Prestatie niveau basis lijn | Kan worden bijgewerkt naar |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Geen |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | Geen |

Zie [prijzen voor beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/)voor informatie over facturering.

## <a name="restrictions"></a>Beperkingen

- Deze functie wordt momenteel alleen ondersteund voor Premium-Ssd's.
- U moet de schijf loskoppelen van een actieve virtuele machine voordat u de laag van de schijf kunt wijzigen.
- Het gebruik van de P60-, P70-en P80-prestatie lagen is beperkt tot schijven van 4.096 GiB of hoger.
- De prestatie-laag van een schijf kan slechts eenmaal per 24 uur worden gewijzigd.

## <a name="regional-availability"></a>Regionale beschikbaarheid

De mogelijkheid om de prestatie laag van een beheerde schijf aan te passen, is momenteel alleen beschikbaar in Premium Ssd's in de regio West-Centraal vs. 

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Een lege gegevens schijf maken met een hogere laag dan de basislijn laag

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Een besturingssysteem schijf maken met een hogere laag dan de basislijn laag van een Azure Marketplace-installatie kopie

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```
     
## <a name="update-the-tier-of-a-disk"></a>De laag van een schijf bijwerken

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```
## <a name="show-the-tier-of-a-disk"></a>De laag van een schijf weer geven

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>Volgende stappen

Als u de grootte van een schijf moet wijzigen om te profiteren van de hogere prestatie lagen, raadpleegt u de volgende artikelen:

- [Virtuele harde schijven op een Linux VM uitbreiden met de Azure CLI](linux/expand-disks.md)
- [Een beheerde schijf uitbreiden die is gekoppeld aan een virtuele Windows-machine](windows/expand-os-disk.md)
