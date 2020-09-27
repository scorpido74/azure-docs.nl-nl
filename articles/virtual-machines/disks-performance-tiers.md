---
title: De prestaties van Azure Managed disks wijzigen
description: Meer informatie over prestatie lagen voor Managed disks en het wijzigen van de prestatie lagen voor bestaande beheerde schijven.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7da500c3f18b7bf7057b0c5875bc9b39136a6483
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396583"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Prestatie lagen voor beheerde schijven (preview-versie)

Azure Disk Storage biedt momenteel ingebouwde burst-mogelijkheden voor betere prestaties bij het verwerken van onverwacht verkeer op korte termijn. Premium Ssd's hebben de flexibiliteit om de schijf prestaties te verbeteren zonder de daad werkelijke schijf grootte te verg Roten, zodat u kunt voldoen aan de prestatie behoeften van uw werk belasting en de kosten kunt verlagen. deze functie is momenteel beschikbaar als preview-versie. Dit is ideaal voor gebeurtenissen die tijdelijk een consistent hoger prestatie niveau vereisen, zoals het kopen van vakantie, het testen van prestaties of het uitvoeren van een trainings omgeving. Als u deze gebeurtenissen wilt verwerken, kunt u zo lang als nodig een hogere prestatie laag selecteren en terugkeren naar de oorspronkelijke laag wanneer de extra prestaties niet meer nodig zijn.

## <a name="how-it-works"></a>Uitleg

Wanneer u een schijf voor het eerst implementeert of inricht, wordt de basislijn prestatie laag voor die schijf ingesteld op basis van de ingerichte schijf grootte. U kunt een hogere prestatie-laag selecteren om te voldoen aan de hogere vraag en, wanneer deze niet langer nodig is, kunt u teruggaan naar de eerste prestatie laag van de basis lijn.

Uw facturering wordt gewijzigd wanneer de laag wordt gewijzigd. Als u bijvoorbeeld een P10-schijf inricht (128 GiB), wordt de laag voor basislijn prestaties ingesteld op P10 (500 IOPS en 100 MB/s) en wordt u gefactureerd op basis van de P10 snelheid. U kunt de laag bijwerken zodat deze overeenkomt met de prestaties van P50 (7500 IOPS en 250 MB/s) zonder de schijf grootte te verg Roten, gedurende welke tijd u wordt gefactureerd tegen het P50ings bedrag. Wanneer de hogere prestaties niet meer nodig zijn, kunt u teruggaan naar de P10-laag en de schijf wordt opnieuw gefactureerd op basis van het P10-tarieven.

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

- Momenteel alleen ondersteund voor Premium-Ssd's.
- Schijven moeten worden losgekoppeld van een actieve virtuele machine voordat u de lagen wijzigt.
- Het gebruik van de P60-, P70-en P80-prestatie lagen is beperkt tot schijven van 4096 GiB of hoger.
- Een prestatie niveau voor schijven kan eenmaal elke 24 uur worden gewijzigd.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Het aanpassen van de prestatie tier van een beheerde schijf is momenteel alleen beschikbaar voor Premium-Ssd's in de volgende regio's:

- VS - west-centraal 

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

Als u de grootte van een schijf moet wijzigen om te kunnen profiteren van de grotere prestatie lagen, raadpleegt u onze artikelen over het onderwerp:

- [Virtuele harde schijven op een Linux VM uitbreiden met de Azure CLI](linux/expand-disks.md)
- [Een beheerde schijf uitbreiden die is gekoppeld aan een virtuele Windows-machine](windows/expand-os-disk.md)
