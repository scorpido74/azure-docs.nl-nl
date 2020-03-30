---
title: De juiste VM SKU selecteren voor uw Azure Data Explorer-cluster
description: In dit artikel wordt beschreven hoe u de optimale SKU-grootte voor azure data explorer-cluster selecteren.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2d078f9715a0cfa171f0c88776a4ab78c15215a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561847"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>De juiste VM SKU selecteren voor uw Azure Data Explorer-cluster 

Wanneer u een nieuw cluster maakt of een cluster optimaliseert voor een veranderende werkbelasting, biedt Azure Data Explorer meerdere VM-sku's (virtual machine) om uit te kiezen. De VM's zijn zorgvuldig gekozen om u de meest optimale kosten te geven voor elke werkbelasting. 

De grootte en VM SKU van het cluster gegevensbeheer worden volledig beheerd door de Azure Data Explorer-service. Ze worden bepaald door factoren zoals de VM-grootte van de motor en de innamewerkbelasting. 

U de VM SKU voor het enginecluster op elk gewenst moment wijzigen door [het cluster op te schalen.](manage-cluster-vertical-scaling.md) Het is het beste om te beginnen met de kleinste SKU-grootte die past bij het oorspronkelijke scenario. Houd er rekening mee dat het opschalen van het cluster resulteert in een uitvaltijd van maximaal 30 minuten, terwijl het cluster opnieuw wordt gemaakt met de nieuwe VM SKU.

> [!TIP]
> Compute [Reserved Instances (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) is van toepassing op het Azure Data Explorer-cluster.  

In dit artikel worden verschillende VM SKU-opties beschreven en worden de technische details beschreven die u kunnen helpen de beste keuze te maken.

## <a name="select-a-cluster-type"></a>Een clustertype selecteren

Azure Data Explorer biedt twee typen clusters:

* **Productie:** Productieclusters bevatten twee knooppunten voor clusters voor engine- en gegevensbeheer en worden beheerd onder de SLA [van](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)Azure Data Explorer .

* **Dev/Test (geen SLA)**: Dev/Test clusters hebben één D11 v2-knooppunt voor het enginecluster en één D1-knooppunt voor het cluster gegevensbeheer. Dit clustertype is de configuratie met de laagste kosten vanwege het lage aantal gevallen en geen kosten voor het opmaken van de motor. Er is geen SLA voor deze clusterconfiguratie, omdat er geen redundantie is.

## <a name="sku-types"></a>SKU-typen

Wanneer u een Azure Data Explorer-cluster maakt, selecteert u de *optimale* VM SKU voor de geplande werkbelasting. U kiezen uit de volgende twee Azure Data Explorer SKU-families:

* **D v2**: De D SKU is compute-geoptimaliseerd en wordt geleverd in twee smaken:
    * De VM zelf
    * De VM gebundeld met premium opslagschijven

* **LS**: De L SKU is geoptimaliseerd voor opslag. Het heeft een veel grotere SSD grootte dan de vergelijkbaar geprijsde D SKU.

De belangrijkste verschillen tussen de beschikbare SKU-typen worden beschreven in de volgende tabel:
 
| Kenmerk | D SKU | L SKU |
|---|---|---
|**Kleine SKU's**|Minimale grootte is D11 met twee cores|Minimale grootte is L4 met vier cores |
|**Beschikbaarheid**|Beschikbaar in alle regio's (de DS+PS-versie heeft een beperktere beschikbaarheid)|Beschikbaar in een paar regio's |
|**Kosten&nbsp;per GB cache per core**|Hoog met de D SKU, laag met de DS + PS versie|Laagste met de optie Betalen per gebruik |
|**Ri-prijzen (Reserved Instances)**|Hoge korting (meer&nbsp;dan 55 procent voor een driejarige verbintenis)|Lagere korting (20&nbsp;procent voor een driejarige verbintenis) |  

## <a name="select-your-cluster-vm"></a>Selecteer uw cluster-VM 

Als u uw cluster-vm wilt selecteren, [configureert u verticale schaling](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

Met verschillende VM SKU-opties om uit te kiezen, u de kosten optimaliseren voor de vereisten voor prestaties en hot-cache voor uw scenario. 
* Als u de meest optimale prestaties nodig hebt voor een hoog queryvolume, moet de ideale SKU worden geoptimaliseerd voor de berekening. 
* Als u grote hoeveelheden gegevens moet opvragen met een relatief lagere querybelasting, kan de voor opslag geoptimaliseerde SKU helpen de kosten te verlagen en toch uitstekende prestaties te leveren.

Omdat het aantal exemplaren per cluster voor de kleine SKU's beperkt is, verdient het de voorkeur om grotere VM's te gebruiken die meer RAM hebben. Er is meer RAM nodig voor sommige querytypen die meer vraag `joins`stellen aan de RAM-bron, zoals query's die gebruik maken. Wanneer u daarom schaalopties overweegt, raden we u aan op te schalen naar een grotere SKU in plaats van uit te schalen door meer exemplaren toe te voegen.

## <a name="vm-options"></a>VM-opties

De technische specificaties voor de VM's van het Azure Data Explorer-cluster worden beschreven in de volgende tabel:

|**Naam**| **Categorie** | **SSD-grootte** | **Kernen** | **Ram** | **Premium opslagschijven&nbsp;(1 TB)**| **Minimaal aantal instance per cluster** | **Maximaal aantal instance's per cluster**
|---|---|---|---|---|---|---|---
|D11 v2| compute-geoptimaliseerd | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (met uitzondering van de v/test SKU, dat is 1)
|D12 v2| compute-geoptimaliseerd | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| compute-geoptimaliseerd | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1000
|D14 v2| compute-geoptimaliseerd | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| opslag geoptimaliseerd | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| opslag geoptimaliseerd | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| opslag geoptimaliseerd | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| opslag geoptimaliseerd | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1000
|L4s v1| opslag geoptimaliseerd | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| opslag geoptimaliseerd | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1000
|L16s_1| opslag geoptimaliseerd | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1000

* U de bijgewerkte VM SKU-lijst per regio bekijken met behulp van de Azure Data Explorer [ListSkus API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet). 
* Meer informatie over de [verschillende SKU's](/azure/virtual-machines/windows/sizes). 

## <a name="next-steps"></a>Volgende stappen

* U het enginecluster op elk gewenst moment [opschalen of schalen](manage-cluster-vertical-scaling.md) door de VM SKU te wijzigen, afhankelijk van de verschillende behoeften. 

* U de grootte van het motorcluster [inschalen of schalen](manage-cluster-horizontal-scaling.md) om de capaciteit te wijzigen, afhankelijk van veranderende eisen.

