---
title: De latentie aan serverzijde controleren op bewerkingen in Azure Cosmos DB
description: Meer informatie over het bewaken van serverlatentie voor bewerkingen in Azure Cosmos DB-account of een container. Eigenaren van een Azure Cosmos DB-account kunnen inzicht krijgen in de problemen met de latentie aan de serverzijde met uw Azure Cosmos-accounts.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 62c10a2ada9ff7d3bf7090028dd9684192517d02
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991390"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>De latentie aan serverzijde controleren op bewerkingen in een Azure Cosmos DB-container of -account

Azure Monitor voor Azure Cosmos DB biedt een weergave met statistieken om uw account te controleren en dashboards te maken. De Azure Cosmos DB-statistieken worden standaard verzameld, deze functie vereist niet dat u iets expliciet in- of configureert. De latentiestatistiek aan de serverzijde wordt gebruikt om de latentie aan de serverzijde van een bewerking weer te geven. Azure Cosmos DB biedt SLA van minder dan 10 ms voor puntlees-/schrijfbewerkingen met directe connectiviteit. Voor puntenlees- en schrijfbewerkingen worden de SLA's berekend zoals beschreven in het [SLA-document](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Als u ongewoon grote latentie ziet voor puntbewerkingen, zoals:

* Een get- of setbewerking met partitiesleutel en ID in de directe modus
* Een lees- of schrijfbewerking of
* Een query

U het diagnostisch logboek opzoeken om de grootte van de geretourneerde gegevens te zien. Als u een aanhoudende hoge latentie ziet voor querybewerkingen, u het diagnostisch logboek opzoeken op de grootte van geretourneerde gegevens, [doorvoer of RU/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) die worden gebruikt, of het aantal van dergelijke bewerkingen in een bepaalde periode. Op deze manier u de problemen met de latentie aan de serverzijde debuggen.

## <a name="view-server-side-latency-metric"></a>Latentiemetriek aan serverzijde weergeven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer **Monitor op** de navigatiebalk aan de linkerkant en selecteer **Statistieken**.

   ![Deelvenster Metrische gegevens in Azure Monitor](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. Selecteer in het deelvenster **Statistieken** > **Selecteer een resource** > kies het vereiste **abonnement**en **de resourcegroep**. Selecteer **voor**het type Resource **Azure Cosmos DB-accounts**een van uw bestaande Azure Cosmos-accounts en selecteer **Toepassen**.
   
   ![Kies een Cosmos DB-account om statistieken weer te geven](./media/monitor-server-side-latency/select-cosmos-db-account.png)

1. Selecteer vervolgens de **metriek serverzijde latentie** in de lijst met beschikbare statistieken. Zie het artikel [Statistieken per rubriek](monitor-cosmos-db-reference.md) voor meer informatie over alle beschikbare statistieken in deze lijst. In dit voorbeeld selecteren we **Serverzijdelatentie** en **Avg** als aggregatiewaarde. Naast deze details u ook de **granulariteit** **tijdenen en** tijd van de statistieken selecteren. Bij max u statistieken van de afgelopen 30 dagen bekijken.  Nadat u het filter hebt toegepast, wordt een grafiek weergegeven op basis van uw filter. U het gemiddelde aantal verbruikte aanvraageenheden per minuut voor de geselecteerde periode bekijken.  

   ![Kies de latentiestatistiek serverzijde in de Azure-portal](./media/monitor-server-side-latency/server-side-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>Filters voor latentie aan serverzijde

U ook statistieken en de grafiek filteren die wordt weergegeven door een specifieke **CollectionName**, **ConnectionMode**, **DatabaseName,** **OperationType**, **Region**en **PublicAPIType**. 

Als u de statistieken wilt filteren, selecteert u **Filter toevoegen** en kiest u de vereiste eigenschap zoals **PublicAPIType** en selecteert u de waarde **sql**. Voeg een ander filter toe voor **OperationType**. In de grafiek wordt vervolgens de latentie aan de serverzijde weergegeven voor verschillende bewerkingen tijdens de geselecteerde periode. De bewerkingen die via de procedure Opgeslagen worden uitgevoerd, worden niet geregistreerd, zodat ze niet beschikbaar zijn onder de metriek OperationType.

De **latentiestatistieken aan serverzijde** voor elke bewerking worden weergegeven zoals weergegeven in de volgende afbeelding:

![Filters voor latentiestatistieken aan serverzijde](./media/monitor-server-side-latency/server-side-latency-filters.png)

U de statistieken ook groeperen met de optie **Splitsen toepassen.**  

## <a name="next-steps"></a>Volgende stappen

* Azure Cosmos DB-gegevens bewaken met [diagnostische instellingen](cosmosdb-monitor-resource-logs.md) in Azure
* [Azure Cosmos DB-besturingsvlakbewerkingen controleren](audit-control-plane-logs.md)