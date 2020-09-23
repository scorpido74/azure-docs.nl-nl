---
title: SQL Server-Power BI en Synapse voor het analyseren van Azure Cosmos DB gegevens met Synapse-koppeling
description: Meer informatie over het bouwen van een Synapse SQL serverloze data base en weer gaven via de Synapse-koppeling voor Azure Cosmos DB, het uitvoeren van query's op de Azure Cosmos-containers en het bouwen van een model met Power BI over die weer gaven.
author: ArnoMicrosoft
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/22/2020
ms.author: acomet
ms.openlocfilehash: 03ea1b0cdfef30935b38078d0811d1408a78c41e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935396"
---
# <a name="use-power-bi-and-synapse-sql-serverless-to-analyze-azure-cosmos-db-data-with-synapse-link-preview"></a>Power BI en Synapse SQL Server gebruiken om Azure Cosmos DB gegevens te analyseren met Synapse-koppeling (preview-versie)

In dit artikel leert u hoe u een Synapse SQL Server (dat, voorheen een **SQL on-demand**-data base genoemd) kunt bouwen voor Azure Cosmos db. U voert een query uit op de Azure Cosmos-containers en bouwt vervolgens een model met Power BI over die weer gaven om die query weer te geven.

> [!NOTE]
> Het gebruik van het Azure Cosmos DB analytic-archief met Synapse SQL Server is momenteel onder gated preview. Neem contact op met het [Azure Cosmos DB team](mailto:cosmosdbsynapselink@microsoft.com)om toegang aan te vragen.

In dit scenario gebruikt u dummy gegevens over de verkoop van Surface-producten in een Retail Store van partners. U gaat de omzet per winkel analyseren op basis van de nabijheid van grote gezinnen en de impact van de reclame voor een specifieke week. In dit artikel maakt u twee weer gaven met de naam **RetailSales** en **StoreDemographics** en een query ertussen. U kunt de voorbeeld product gegevens ophalen uit deze [github](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) opslag plaats.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de volgende resources maakt voordat u begint:

* [Maak een Azure Cosmos DB account van het type SQL (kern geheugen) of MongoDB.](create-cosmosdb-resources-portal.md)

* Azure Synapse-koppeling voor uw [Azure Cosmos-account](configure-synapse-link.md#enable-synapse-link) inschakelen

* Maak een Data Base binnen het Azure Cosmos-account en twee containers waarvoor het [analytische archief is ingeschakeld.](configure-synapse-link.md#create-analytical-ttl)

* Laad gegevens van producten in de Azure Cosmos-containers, zoals wordt beschreven in deze [batch gegevens opname](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) -notebook.

* [Maak een Synapse-werk ruimte met de](../synapse-analytics/quickstart-create-workspace.md) naam **SynapseLinkBI**.

* [Verbind de Azure Cosmos-data base met de Synapse-werk ruimte](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json).

## <a name="create-a-database-and-views"></a>Een Data Base en weer gaven maken

Ga in de werk ruimte Synapse naar het tabblad **ontwikkelen** en selecteer in het **+** pictogram **SQL-script**.

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="Een SQL-script toevoegen aan de Synapse Analytics-werk ruimte":::

Elke werk ruimte wordt geleverd met een Synapse SQL Server zonder eind punt. Nadat u een SQL-script hebt gemaakt, gaat u vanuit de werk balk aan de bovenkant verbinding maken met **SQL op aanvraag**.

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="Het SQL-script inschakelen voor het gebruik van het Synapse SQL serverloze eind punt in de werk ruimte":::

Maak een nieuwe Data Base met de naam **RetailCosmosDB**en een SQL-weer gave over de containers die zijn ingeschakeld voor de Synapse-koppeling. De volgende opdracht laat zien hoe u een Data Base maakt:

```sql
-- Create database
Create database RetailCosmosDB
```

Maak vervolgens meerdere weer gaven in verschillende Synapse-koppelingen die zijn ingeschakeld voor Azure Cosmos-containers. Zo kunt u T-SQL gebruiken om lid te worden van en query's uitvoeren op Azure Cosmos DB gegevens in verschillende containers.  Zorg ervoor dat u de **RetailCosmosDB** -data base selecteert bij het maken van de weer gaven.

De volgende scripts laten zien hoe u weer gaven maakt voor elke container. Voor eenvoud gebruiken we de functie [automatische schema](analytical-store-introduction.md#analytical-schema) dematen van Synapse SQL serverloos via Synapse-koppeling ingeschakelde containers:


### <a name="retailsales-view"></a>Weer gave RetailSales:

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

Zorg ervoor dat u uw Azure Cosmos DB-regio en de primaire sleutel invoegt in het vorige SQL-script. Alle tekens in de regio naam moeten in kleine letters zonder spaties worden gereduceerd. In tegens telling tot de andere para meters van de `OPENROWSET` opdracht, moet u de para meter container name opgeven zonder aanhalings tekens.

### <a name="storedemographics-view"></a>Weer gave StoreDemographics:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

Voer nu het SQL-script uit door de opdracht **uitvoeren** te selecteren.

## <a name="query-the-views"></a>Query's uitvoeren op de weer gaven

Nu de twee weer gaven zijn gemaakt, definiëren we de query om deze twee weer gaven als volgt toe te voegen:

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

Selecteer **uitvoeren** die de volgende tabel als resultaat geeft:

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="Query resultaten na het toevoegen van de weer gaven StoreDemographics en RetailSales":::

## <a name="model-views-over-containers-with-power-bi"></a>Model weergaven via containers met Power BI

Open vervolgens het Power BI bureau blad en maak verbinding met het Synapse SQL serverloze eind punt met behulp van de volgende stappen:

1. Open de Power BI Desktop-toepassing. Selecteer **gegevens ophalen** en selecteer **meer**.

1. Kies **Azure Synapse Analytics (SQL DW)** in de lijst met verbindings opties.

1. Voer de naam in van het SQL-eind punt waar de data base zich bevindt. Voer `SynapseLinkBI-ondemand.sql.azuresynapse.net` in het veld **Server** in. In dit voor beeld is  **SynapseLinkBI** de naam van de werk ruimte. Vervang deze als u een andere naam hebt opgegeven voor uw werk ruimte. Selecteer **direct query** voor de gegevens connectiviteits modus en klik vervolgens op **OK**.

1. Selecteer de gewenste verificatie methode, zoals Azure AD.

1. Selecteer de **RetailCosmosDB** -data base en de weer gaven **RetailSales**, **StoreDemographics** .

1. Selecteer **laden** om de twee weer gaven in de direct query-modus te laden.

1. Selecteer **model** om een relatie tussen de twee weer gaven te maken via de **storeId** -kolom.

1. Sleep de kolom **StoreID** van de weer gave **RetailSales** naar de kolom **StoreID** in de weer gave **StoreDemographics** .

1. Selecteer veel-op-een (*: 1) relatie omdat er meerdere rijen met dezelfde archief-ID in de **RetailSales** -weer gave zijn, maar de **StoreDemographics** slechts één rij Store ID heeft (het is een dimensie tabel)

Ga nu naar het **rapport** venster en maak een rapport om het relatieve belang van de huishoud grootte te vergelijken met de gemiddelde omzet per opslag op basis van de verstrooide weer gave van omzet-en LargeHH-index:

1. Selecteer een **spreidings diagram**.

1. Sleep de **LargeHH** van de **StoreDemographics** -weer gave naar de X-as en zet deze neer.

1. Sleep de **opbrengst** van de weer gave **RetailSales** naar de Y-as. Selecteer **gemiddeld** om de gemiddelde verkoop per product per winkel en per week te berekenen.

1. Sleep de product code van de weer gave **RetailSales** **naar de** legenda om een specifieke productlijn te selecteren.
Nadat u deze opties hebt gekozen, ziet u een grafiek zoals in de volgende scherm afbeelding:

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="Rapport dat het relatieve belang van de omvang van de huishoudens vergelijkt met de gemiddelde omzet per winkel":::

## <a name="next-steps"></a>Volgende stappen

SQL Server-Synapse gebruiken om [Azure open gegevens sets te analyseren en de resultaten te visualiseren in azure Synapse Studio](../synapse-analytics/sql/tutorial-data-analyst.md)
