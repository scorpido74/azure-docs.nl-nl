---
title: Beheerde privé-eind punten
description: Een artikel waarin beheerde persoonlijke eind punten in azure Synapse Analytics worden uitgelegd
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 08c6610541d987cddd7cf2aeb71c526cb2359598
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81423682"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Door Synapse beheerde privé-eind punten (preview-versie)

In dit artikel worden beheerde privé-eind punten in azure Synapse Analytics uitgelegd.

## <a name="managed-private-endpoints"></a>Beheerde privé-eind punten

Beheerde privé-eind punten zijn particuliere eind punten die zijn gemaakt in de beheerde werk ruimte VNet en een persoonlijke koppeling naar Azure-resources tot stand brengen. Deze privé-eind punten worden namens u door Azure Synapse beheerd.

Azure Synapse ondersteunt persoonlijke koppelingen. Met persoonlijke koppeling kunt u Azure-Services (zoals Azure Storage, Azure Cosmos DB en Azure SQL Data Warehouse) en Azure gehoste klanten-en partner services van uw Azure VNet veilig benaderen.

Wanneer u een persoonlijke koppeling gebruikt, loopt het verkeer tussen uw VNet en de werk ruimte volledig over het micro soft-backbone-netwerk. Persoonlijke koppeling beschermt tegen gegevens exfiltration Risico's. U kunt een persoonlijke koppeling naar een resource tot stand brengen door een persoonlijk eind punt te maken.

Persoonlijk eind punt gebruikt een privé-IP-adres van uw VNet om de service effectief in uw VNet te brengen. Persoonlijke eind punten worden toegewezen aan een specifieke resource in Azure en niet op de volledige service. Klanten kunnen de connectiviteit beperken tot een specifieke resource die is goedgekeurd door hun organisatie. Meer informatie over [persoonlijke koppelingen en privé-eind punten](https://docs.microsoft.com/azure/private-link/).

>[!IMPORTANT]
>Beheerde persoonlijke eind punten worden alleen ondersteund in azure Synapse-werk ruimten met een Managed Workspace VNet.
>[!NOTE]
>Het wordt aanbevolen dat u beheerde persoonlijke eind punten maakt om verbinding te maken met al uw Azure-gegevens bronnen. Al het uitgaande verkeer van het >beheerde werk ruimte VNet wordt in de toekomst geblokkeerd.

Een VPN-verbinding wordt gemaakt met de status ' in behandeling ' wanneer u een beheerd persoonlijk eind punt maakt in azure Synapse. Er wordt een goedkeurings werk stroom geïnitieerd. De resource-eigenaar van de persoonlijke koppeling is verantwoordelijk voor het goed keuren of afwijzen van de verbinding.

Als de eigenaar de verbinding goedkeurt, wordt de persoonlijke koppeling tot stand gebracht. Anders wordt de persoonlijke koppeling niet tot stand gebracht. In beide gevallen wordt het beheerde persoonlijke eind punt bijgewerkt met de status van de verbinding.

Alleen een beheerd persoonlijk eind punt met een goedgekeurde status kan verkeer verzenden naar een bepaalde persoonlijke koppelings bron.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Beheerde persoonlijke eind punten voor SQL-groep en SQL op aanvraag

SQL-groep en SQL on-demand zijn analyse mogelijkheden in uw Azure Synapse-werk ruimte. Deze mogelijkheden gebruiken multi-tenant infrastructuur die niet is geïmplementeerd in het [Managed Workspace VNet](./synapse-workspace-managed-vnet.md).

Wanneer een werk ruimte wordt gemaakt, worden in azure Synapse twee beheerde persoonlijke eind punten gemaakt voor een SQL-groep en SQL op aanvraag in die werk ruimte. 

Deze twee beheerde persoonlijke eind punten worden weer gegeven in azure Synapse Studio. Selecteer **beheren** in de linkernavigatiebalk en selecteer vervolgens **beheerde virtuele netwerken** om weer te geven in de Studio.

Het beheerde persoonlijke eind punt dat de SQL-groep aanroept, heet *Synapse-WS\<-SQL\> --workspacenaam* , en de werk ruimte die gericht is op SQL on-demand, wordt *Synapse-WS-sqlOnDemand--\<workspacenaam\>* genoemd.
![Beheerde persoonlijke eind punten voor SQL-groep en SQL op aanvraag](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Deze twee beheerde persoonlijke eind punten worden automatisch voor u gemaakt wanneer u de Azure Synapse-werk ruimte maakt. Er worden geen kosten in rekening gebracht voor deze twee beheerde privé-eind punten.

## <a name="next-steps"></a>Volgende stappen

[Beheerde persoonlijke eind punten maken voor uw gegevens bronnen](./how-to-create-managed-private-endpoints.md)
