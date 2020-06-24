---
title: De latentie aan de server zijde controleren voor bewerkingen in Azure Cosmos DB
description: Meer informatie over het bewaken van de server latentie voor bewerkingen in Azure Cosmos DB account of een container. Eigen aars van een Azure Cosmos DB-account kunnen inzicht krijgen in de latentie problemen aan de server zijde met uw Azure Cosmos-accounts.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 5be2365fb5850c3f45b320d66c114fb791b22c3e
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262698"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>De latentie aan de server zijde controleren voor bewerkingen in een Azure Cosmos DB container of-account

Azure Monitor voor Azure Cosmos DB biedt een weer gave van metrische gegevens voor het bewaken van uw account en het maken van Dash boards. De Azure Cosmos DB metrische gegevens worden standaard verzameld. voor deze functie hoeft u niets expliciet in te scha kelen of te configureren. De latentie aan de server zijde wordt gebruikt om de latentie aan de server zijde van een bewerking weer te geven. Azure Cosmos DB biedt een SLA van minder dan 10 MS voor Point-Lees-en schrijf bewerkingen met directe connectiviteit. Voor lees-en schrijf bewerkingen naar een punt worden de Sla's berekend zoals beschreven in het [Sla-document](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Als u ongebruikelijk grote latentie ziet voor punt bewerkingen, zoals:

* Een Get-of set-bewerking met partitie sleutel en-ID in directe modus
* Een lees-of schrijf bewerking of
* Een query

U kunt het diagnostische logboek opzoeken om de grootte van de geretourneerde gegevens te zien. Als er een hoge latentie voor query bewerkingen wordt weer gegeven, kunt u het diagnostische logboek opzoeken voor de grootte van de gegevens die worden geretourneerd, de [door Voer of ru/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) die worden gebruikt, of het aantal dergelijke bewerkingen in een bepaalde periode. Op deze manier kunt u fouten opsporen in de latentie van de server.

## <a name="view-the-server-side-latency-metric"></a>De latentie gegevens aan de server zijde weer geven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer **monitor** in de navigatie balk aan de linkerkant en selecteer **metrische gegevens**.

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Deel venster metrische gegevens in Azure Monitor":::

1. Selecteer in het deel venster **metrieken** > **een resource selecteren** > Kies het vereiste **abonnement**en de **resource groep**. Voor het **bron type**selecteert u **Azure Cosmos DB accounts**, kiest u een van uw bestaande Azure Cosmos-accounts en selecteert u **Toep assen**.
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="Kies het Azure Cosmos DB-account om de metrische gegevens weer te geven":::

1. Selecteer vervolgens de metrische latentie aan de **server zijde** in de lijst met beschik bare metrische gegevens. Zie het artikel [metrische gegevens per categorie](monitor-cosmos-db-reference.md) voor meer informatie over alle beschik bare metrische gegevens in deze lijst. In dit voor beeld selecteren we de **latentie aan de server zijde** en **Gem** als de aggregatie waarde. Naast deze details kunt u ook het **tijds bereik** en de **tijd granulatie** van de metrische gegevens selecteren. U kunt Maxi maal de metrische gegevens weer geven voor de afgelopen 30 dagen.  Nadat u het filter hebt toegepast, wordt een grafiek weer gegeven op basis van het filter. U kunt de latentie aan de server zijde per minuut voor de geselecteerde periode bekijken.  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Kies de latentie gegevens aan de server zijde van de Azure Portal":::

## <a name="filters-for-server-side-latency"></a>Filters voor latentie aan server zijde

U kunt de metrische gegevens ook filteren en de grafieken ophalen die worden weer gegeven door een specifieke naam van de **verzameling**, **ConnectionMode**, **DATABASENAME**, **OperationType**, **Region**en **PublicAPIType**. 

Als u de metrische gegevens wilt filteren, selecteert u **filter toevoegen** en kiest u de gewenste eigenschap, zoals **PublicAPIType** , en selecteert u de waarde **SQL**. Voeg nog een filter toe voor **OperationType**. In de grafiek wordt vervolgens de latentie aan de server zijde voor verschillende bewerkingen in de geselecteerde periode weer gegeven. De bewerkingen die zijn uitgevoerd via een opgeslagen procedure, worden niet geregistreerd, zodat ze niet beschikbaar zijn onder de metrische waarde voor OperationType.

De metrische latentie gegevens aan de **server zijde** voor elke bewerking worden weer gegeven, zoals wordt weer gegeven in de volgende afbeelding:

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="Filters voor metrische gegevens over latentie aan de server zijde":::

U kunt de metrische gegevens ook groeperen met behulp van de optie **splitsing Toep assen** .  

## <a name="next-steps"></a>Volgende stappen

* Bewaak Azure Cosmos DB gegevens met behulp van [Diagnostische instellingen](cosmosdb-monitor-resource-logs.md) in Azure.
* [Bewerkingen voor Azure Cosmos DB Control-vlak controleren](audit-control-plane-logs.md)
