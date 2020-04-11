---
title: Het doorvoergebruik van een bewerking in Azure Cosmos DB bewaken
description: Meer informatie over het bewaken van de doorvoer of het aanvragen van eenheidsgebruik van een bewerking in Azure Cosmos DB. Eigenaren van een Azure Cosmos DB-account kunnen begrijpen welke bewerkingen meer aanvraageenheden gebruiken.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115428"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Overslag controle of het gebruik van een bewerking in Azure Cosmos DB aanvragen of het gebruik van een bewerking aanvragen

Azure Monitor voor Azure Cosmos DB biedt een weergave met statistieken om uw account te controleren en dashboards te maken. De Azure Cosmos DB-statistieken worden standaard verzameld, deze functie vereist niet dat u iets expliciet in- of configureert. De statistiek **Total Request Units** wordt gebruikt om het gebruik van aanvraageenheden voor verschillende typen bewerkingen te krijgen. Later u analyseren welke bewerkingen het grootste deel van de doorvoer hebben gebruikt. Standaard worden de doorvoergegevens samengevoegd met een interval van één minuut. U de aggregatie-eenheid echter wijzigen door de optie tijdgedetailleerdheid te wijzigen.

Er zijn twee manieren om de gebruiksgegevens van de aanvraageenheid te analyseren:

* Binnen het bepaalde tijdsinterval welke bewerkingen meer aanvraageenheden uitvoeren.
* Welke bewerkingen in het algemeen uw werklast domineren door meer aanvraageenheden te verbruiken.
Met deze analyse u zich richten op bewerkingen zoals insert, upsert en kijken naar hun indexering. U nagaan of u over/onder het indexeren van specifieke velden bent en het [indexeringsbeleid](index-policy.md#include-exclude-paths) wijzigen om de paden op te nemen of uit te sluiten.

Als u merkt dat bepaalde query's meer aanvraageenheden uitvoeren, u acties ondernemen zoals:

* Heroverweeg of u de juiste hoeveelheid gegevens opvraagt.
* Wijzig de query om index te gebruiken met filterclausule.
* Voer minder dure UDF-functieaanroepen uit.
* Definieer partitiesleutels om de ventilator uit query's in verschillende partities te minimaliseren.
* U ook de querystatistieken gebruiken die zijn geretourneerd in het gespreksantwoord, de diagnostische logboekdetails en verwijzen naar het artikel voor [het afstemmen van queryprestaties](sql-api-query-metrics.md) voor meer informatie over de queryuitvoering.
* U vanaf de som beginnen en vervolgens kijken naar avg-gebruik met behulp van de juiste dimensie.

## <a name="view-the-total-request-unit-usage-metric"></a>De statistiek voor het gebruik van de totale aanvraageenheid weergeven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer **Monitor op** de navigatiebalk aan de linkerkant en selecteer **Statistieken**.

   ![Deelvenster Metrische gegevens in Azure Monitor](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. Selecteer in het deelvenster **Statistieken** > **Selecteer een resource** > kies het vereiste **abonnement**en **de resourcegroep**. Selecteer **voor**het type Resource **Azure Cosmos DB-accounts**een van uw bestaande Azure Cosmos-accounts en selecteer **Toepassen**.

   ![Kies het Azure Cosmos DB-account om statistieken weer te geven](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. Selecteer vervolgens de statistiek **Total Request Units** in de lijst met beschikbare statistieken. Zie het artikel [Statistieken per rubriek](monitor-cosmos-db-reference.md) voor meer informatie over alle beschikbare statistieken in deze lijst. In dit voorbeeld selecteren we **Total Request Units** en **Avg** als aggregatiewaarde. Naast deze details u ook de **granulariteit** **tijdenen en** tijd van de statistieken selecteren. Bij max u statistieken van de afgelopen 30 dagen bekijken.  Nadat u het filter hebt toegepast, wordt een grafiek weergegeven op basis van uw filter. U het gemiddelde aantal verbruikte aanvraageenheden per minuut voor de geselecteerde periode bekijken.  

   ![Een statistiek kiezen uit de Azure-portal](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>Filters voor het gebruik van aanvraageenheden

U ook statistieken filteren en de grafieken weergeven door een specifieke **CollectionName**, **DatabaseName,** **OperationType**, **Regio**, **Status**en **StatusCode**. Met de **opties** Filter **toevoegen** en Splitsen toepassen u het gebruik van de aanvraageenheid filteren en de statistieken groeperen.

Als u het gebruik van elke bewerking van elke bewerking op totaal(som) of gemiddeld wilt krijgen, selecteert u **Splitsen toepassen** en kiest **u Operationeel type** en de filterwaarde zoals weergegeven in de volgende afbeelding:

   ![Cosmos DB Request-eenheden voor bewerkingen in Azure-monitor](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

Als u het gebruik van aanvraageenheden per verzameling wilt zien, selecteert u **Splitsen toepassen** en kiest u de verzamelnaam als filter. U ziet een chat als volgt met een keuze van collecties in het dashboard. U vervolgens een specifieke verzamelingsnaam selecteren om meer details weer te geven:

   ![Cosmos DB Request-eenheden voor alle bewerkingen door de verzameling in Azure-monitor](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>Volgende stappen

* Controleer Azure Cosmos DB-gegevens met [diagnostische instellingen](cosmosdb-monitor-resource-logs.md) in Azure.
* [Azure Cosmos DB-besturingsvlakbewerkingen controleren](audit-control-plane-logs.md)