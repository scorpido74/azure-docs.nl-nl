---
title: Selecteer de juiste VM-SKU voor uw Azure Data Explorer-cluster
description: In dit artikel wordt beschreven hoe u de optimale SKU-grootte voor Azure Data Explorer cluster selecteert.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 8293fd2d84189cc1f1df3564abbfdcbf86e3543e
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186744"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Selecteer de juiste VM-SKU voor uw Azure Data Explorer-cluster 

Wanneer u een nieuw cluster maakt of een cluster optimaliseert voor een wijzigings werk belasting, biedt Azure Data Explorer meerdere Sku's voor virtuele machines (VM) waaruit u kunt kiezen. De virtuele machines zijn zorgvuldig gekozen om u de meest optimale kosten te bieden voor elke werk belasting. 

De grootte en VM-SKU van het gegevens beheer cluster worden volledig beheerd door de Azure Data Explorer-service. Ze worden bepaald door dergelijke factoren als de VM-grootte van de engine en de opname werk belasting. 

U kunt de VM-SKU voor het engine-cluster op elk gewenst moment wijzigen door [het cluster omhoog te schalen](manage-cluster-vertical-scaling.md). U kunt het beste beginnen met de kleinste SKU-grootte die past bij het eerste scenario. Wanneer het cluster wordt geschaald, resulteert dit in een uitval tijd van Maxi maal 30 minuten terwijl de nieuwe VM-SKU opnieuw wordt gemaakt.

> [!TIP]
> Gereserveerde COMPUTE- [instanties (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) zijn van toepassing op het Azure Data Explorer-cluster.  

In dit artikel worden verschillende opties voor de VM-SKU beschreven en vindt u de technische details waarmee u de beste keuze kunt maken.

## <a name="select-a-cluster-type"></a>Een clustertype selecteren

Azure Data Explorer biedt twee typen clusters:

* **Productie**: Productie clusters bevatten twee knoop punten voor Engine-en gegevens beheer clusters en worden beheerd onder de [Sla](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)van Azure Data Explorer.

* **Dev/test (geen sla)** : Dev/test-clusters hebben één D11 v2-knoop punt voor het engine-cluster en één D1-knoop punt voor het gegevens beheer cluster. Dit cluster type is de laagste kosten configuratie vanwege een laag aantal exemplaren en geen kosten voor Engine-aantekeningen. Er is geen SLA voor deze cluster configuratie, omdat deze geen redundantie heeft.

## <a name="sku-types"></a>SKU-typen

Wanneer u een Azure Data Explorer-cluster maakt, selecteert u de *optimale* VM-SKU voor de geplande werk belasting. U kunt kiezen uit de volgende twee Azure Data Explorer SKU-families:

* **D v2**: De D-SKU is geoptimaliseerd voor berekening en komt in twee soorten voor:
    * De VM zelf
    * De VM gebundeld met Premium-opslag schijven

* **LS**: De L-SKU is geoptimaliseerd voor opslag. Het heeft een veel groter SSD-grootte dan de D-SKU met dezelfde prijs.

De belangrijkste verschillen tussen de beschik bare SKU-typen worden beschreven in de volgende tabel:
 
| Kenmerk | D SKU | L-SKU |
|---|---|---
|**Kleine Sku's**|De minimale grootte is D11 met twee kernen|Minimale grootte is N4 met vier kernen |
|**Beschikbaarheid**|Beschikbaar in alle regio's (de DS + PS-versie heeft een meer beperkte Beschik baarheid)|Beschikbaar in een paar regio's |
|**Kosten per&nbsp;GB cache per kern**|Hoog met de D-SKU, laag met de DS + PS-versie|Laagste met de optie voor betalen naar gebruik |
|**Prijzen voor gereserveerde instanties (RI)**|Hoge korting (meer dan&nbsp;55 procent voor een toezeg ging van drie jaar)|Lagere korting (20&nbsp;procent voor een toezeg ging van drie jaar) |  

## <a name="select-your-cluster-vm"></a>De virtuele machine van het cluster selecteren 

Als u uw cluster-VM wilt selecteren, [configureert u verticaal schalen](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

Met de verschillende opties voor de VM-SKU kunt u de kosten voor de prestaties en de Hot-cache vereisten voor uw scenario optimaliseren. 
* Als u de meest optimale prestaties voor een hoog query volume nodig hebt, moet de ideale SKU worden geoptimaliseerd voor berekening. 
* Als u grote hoeveel heden gegevens met een relatief lagere query belasting moet opvragen, kan de door opslag geoptimaliseerde SKU bijdragen aan lagere kosten en toch uitstekende prestaties bieden.

Omdat het aantal exemplaren per cluster voor de kleine Sku's beperkt is, is het beter om grotere Vm's te gebruiken met meer RAM-geheugen. Er is meer RAM-geheugen nodig voor sommige query typen waarmee meer eisen worden gesteld aan de RAM-bron, `joins`zoals query's die gebruikmaken van. Daarom raden we u aan om met schaal opties te schalen naar een grotere SKU in plaats van uitschalen door meer instanties toe te voegen.

## <a name="vm-options"></a>VM-opties

De technische specificaties voor de Azure Data Explorer cluster-Vm's worden beschreven in de volgende tabel:

|**Name**| **Categorie** | **SSD-grootte** | **Kernen** | **NODIG** | **Premium-opslag schijven (&nbsp;1 TB)**| **Minimum aantal exemplaren per cluster** | **Maximum aantal exemplaren per cluster**
|---|---|---|---|---|---|---|---
|D11 v2| compute-Optimized | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (behalve voor de SKU dev/test, die 1 is)
|D12 v2| compute-Optimized | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| compute-Optimized | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1000
|D14 v2| compute-Optimized | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| opslag geoptimaliseerd | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| opslag geoptimaliseerd | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| opslag geoptimaliseerd | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| opslag geoptimaliseerd | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1000
|L4s v1| opslag geoptimaliseerd | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| opslag geoptimaliseerd | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1000
|L16s_1| opslag geoptimaliseerd | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1000

* U kunt de bijgewerkte SKU-lijst van de VM per regio weer geven met behulp van de Azure Data Explorer [LISTSKUS API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet). 
* Meer informatie over de [verschillende sku's](/azure/virtual-machines/windows/sizes). 

## <a name="next-steps"></a>Volgende stappen

* U kunt het engine-cluster op elk gewenst moment [omhoog of omlaag](manage-cluster-vertical-scaling.md) schalen door de VM-SKU te wijzigen, afhankelijk van verschillende behoeften. 

* U kunt de grootte van het engine-cluster [schalen of uitschalen](manage-cluster-horizontal-scaling.md) om de capaciteit te wijzigen, afhankelijk van de veranderende vereisten.

