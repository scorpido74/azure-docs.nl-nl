---
title: Beheerde privéeindpunten
description: Een artikel waarin beheerde privéeindpunten in Azure Synapse Analytics worden uitgelegd
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 08c6610541d987cddd7cf2aeb71c526cb2359598
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423682"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Privéeindpunten van Synapse Beheerd (voorbeeld)

In dit artikel worden beheerde privéeindpunten in Azure Synapse Analytics uitgelegd.

## <a name="managed-private-endpoints"></a>Beheerde privéeindpunten

Beheerde privéeindpunten zijn privéeindpunten die zijn gemaakt in de VNet voor beheerde werkruimte en een privékoppeling naar Azure-resources tot stand brengen. Azure Synapse beheert deze privéeindpunten namens u.

Azure Synapse ondersteunt privékoppelingen. Met privékoppeling u veilig toegang krijgen tot Azure-services (zoals Azure Storage, Azure Cosmos DB en Azure SQL Data Warehouse) en Azure-services voor klant-/partnerservices.

Wanneer u een privékoppeling gebruikt, loopt het verkeer tussen uw VNet en werkruimte volledig over het Microsoft-backbonenetwerk. Private Link beschermt tegen risico's voor gegevensexfiltratie. U maakt een privékoppeling naar een resource door een privéeindpunt te maken.

Privéeindpunt maakt gebruik van een privé-IP-adres van uw VNet om de service effectief in uw VNet te brengen. Privéeindpunten worden toegewezen aan een specifieke resource in Azure en niet aan de hele service. Klanten kunnen de connectiviteit beperken tot een specifieke resource die is goedgekeurd door hun organisatie. Meer informatie over [privélinks en privé-eindpunten](https://docs.microsoft.com/azure/private-link/).

>[!IMPORTANT]
>Beheerde privéeindpunten worden alleen ondersteund in Azure Synapse-werkruimten met een VNet voor beheerde werkruimte.
>[!NOTE]
>Het wordt aanbevolen om beheerde privéeindpunten te maken om verbinding te maken met al uw Azure-gegevensbronnen. Al het uitgaande verkeer van de >Beheerde werkruimte VNet wordt in de toekomst geblokkeerd.

Er wordt een privé-eindpuntverbinding gemaakt in de status 'In behandeling' wanneer u een beheerd privéeindpunt maakt in Azure Synapse. Er wordt een goedkeuringswerkstroom gestart. De eigenaar van de private link resource is verantwoordelijk voor het goedkeuren of weigeren van de verbinding.

Als de eigenaar de verbinding goedkeurt, wordt de privékoppeling tot stand gebracht. Anders wordt de privéverbinding niet tot stand gebracht. In beide gevallen wordt het beheerde privéeindpunt bijgewerkt met de status van de verbinding.

Alleen een beheerd privéeindpunt in een goedgekeurde status kan verkeer naar een bepaalde bron voor privékoppelingen verzenden.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Beheerde privéeindpunten voor SQL-pool en SQL on-demand

SQL-pool en SQL on-demand zijn analytische mogelijkheden in uw Azure Synapse-werkruimte. Deze mogelijkheden maken gebruik van multi-tenant infrastructuur die niet is geïmplementeerd in de [Beheerde werkruimte VNet](./synapse-workspace-managed-vnet.md).

Wanneer een werkruimte wordt gemaakt, maakt Azure Synapse twee beheerde privéeindpunten voor SQL-groep en SQL on-demand in die werkruimte. 

Deze twee beheerde privéeindpunten worden vermeld in Azure Synapse Studio. Selecteer **Beheren** in de linkernavigatie en selecteer **Vervolgens Beheerde virtuele netwerken** om vervolgens in de studio te zien.

Het beheerde privéeindpunt dat zich richt op SQL-pool wordt *synaps-ws-sql genoemd,\<workspacename\> * en het doel dat zich richt op SQL on-demand wordt *synapse-ws-sqlOnDemand genoemd,\<de naam van workspaces.\>*
![Beheerde privéeindpunten voor SQL-pool en SQL on-demand](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Deze twee beheerde privéeindpunten worden automatisch voor u gemaakt wanneer u uw Azure Synapse-werkruimte maakt. Er worden geen kosten in rekening gebracht voor deze twee beheerde privéeindpunten.

## <a name="next-steps"></a>Volgende stappen

[Beheerde privéeindpunten maken voor uw gegevensbronnen](./how-to-create-managed-private-endpoints.md)
