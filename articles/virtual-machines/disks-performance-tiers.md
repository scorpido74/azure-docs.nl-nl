---
title: Prestaties van Azure Managed disk verhogen
description: Meer informatie over prestatie lagen voor Managed disks en het upgraden van prestatie lagen voor uw Managed disks.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/22/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: aa188babf56d4a825059fe6103e2e07745eb134f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90974138"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Prestatie lagen voor beheerde schijven (preview-versie)

Azure Disk Storage biedt momenteel ingebouwde burst-mogelijkheden voor betere prestaties bij het verwerken van onverwacht verkeer op korte termijn. Premium Ssd's hebben de flexibiliteit om de schijf prestaties te verbeteren zonder de daad werkelijke schijf grootte te verg Roten, zodat u kunt voldoen aan de prestatie behoeften van uw werk belasting en de kosten kunt verlagen. deze functie is momenteel beschikbaar als preview-versie. Dit is ideaal voor gebeurtenissen die tijdelijk een consistent hoger prestatie niveau vereisen, zoals het kopen van vakantie, het testen van prestaties of het uitvoeren van een trainings omgeving. Als u deze gebeurtenissen wilt verwerken, kunt u zo lang als nodig een hogere prestatie laag selecteren en terugkeren naar de oorspronkelijke laag wanneer de extra prestaties niet meer nodig zijn.

## <a name="how-it-works"></a>Uitleg

Wanneer u een schijf voor het eerst implementeert of inricht, wordt de basislijn prestatie laag voor die schijf ingesteld op basis van de ingerichte schijf grootte. U kunt een hogere prestatie-laag selecteren om te voldoen aan de hogere vraag en, wanneer deze niet langer nodig is, kunt u teruggaan naar de eerste prestatie laag van de basis lijn. Als u bijvoorbeeld een P10-schijf inricht (128 GiB), wordt de laag voor basislijn prestaties ingesteld op P10 (500 IOPS en 100 MB/s). U kunt de laag bijwerken zodat deze overeenkomt met de prestaties van P50 (7500 IOPS en 250 MB/s) zonder de schijf grootte te verhogen en terug te keren naar P10 wanneer de hogere prestaties niet meer nodig zijn.

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

## <a name="restrictions"></a>Beperkingen

- Momenteel alleen ondersteund voor Premium-Ssd's.
- Schijven moeten worden losgekoppeld van een actieve virtuele machine voordat u de lagen wijzigt.
- Het gebruik van de P60-, P70-en P80-prestatie lagen is beperkt tot schijven van 4096 GiB of hoger.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Het aanpassen van de prestatie tier van een beheerde schijf is momenteel alleen beschikbaar voor Premium-Ssd's in de volgende regio's:

- VS - west-centraal 
- VS Oost 2 
- Europa - west
- Oost-Australië 
- Zuid-Oost-Australië 
- India - zuid

## <a name="createupdate-a-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Een gegevens schijf maken/bijwerken met een laag hoger dan de basislijn laag

1. Maak een lege gegevens schijf met een hogere laag dan de basislijn laag of werk de laag van een schijf die hoger is dan de basislijn laag, bij met behulp van de voorbeeld sjabloon [CreateUpdateDataDiskWithTier.jsop](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json)

     ```cli
     subscriptionId=<yourSubscriptionIDHere>
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     diskSize=<yourDiskSizeHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az login
    
     az account set --subscription $subscriptionId
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
     ```

1. De laag van de schijf bevestigen

    ```cli
    az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="createupdate-an-os-disk-with-a-tier-higher-than-the-baseline-tier"></a>Een besturingssysteem schijf maken/bijwerken met een hogere laag dan de basislijn laag

1. Een besturingssysteem schijf maken op basis van een Marketplace-installatie kopie of de laag van een besturingssysteem schijf die hoger is dan de basislijn laag bijwerken met behulp van de voorbeeld sjabloon [CreateUpdateOSDiskWithTier.jsop](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateOSDiskWithTier.json)

     ```cli
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateOSDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier"
     ```
 
 1. De laag van de schijf bevestigen
 
     ```cli
     az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="next-steps"></a>Volgende stappen

Als u de grootte van een schijf moet wijzigen om te kunnen profiteren van de grotere prestatie lagen, raadpleegt u onze artikelen over het onderwerp:

- [Virtuele harde schijven op een Linux VM uitbreiden met de Azure CLI](linux/expand-disks.md)
- [Een beheerde schijf uitbreiden die is gekoppeld aan een virtuele Windows-machine](windows/expand-os-disk.md)