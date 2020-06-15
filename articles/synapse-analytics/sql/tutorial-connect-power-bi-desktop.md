---
title: 'Zelfstudie: SQL on-demand (preview) verbinden met Power BI Desktop en rapport maken'
description: In deze zelfstudie leert u hoe u SQL on-demand (preview) in Azure Synapse Analytics verbindt met Power BI Desktop en een demorapport kunt maken op basis van een weergave.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1b71e8108ecab0f6d420e404bd602a900ebfcfb2
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84258280"
---
# <a name="tutorial-use-sql-on-demand-preview-with-power-bi-desktop--create-a-report"></a>Zelfstudie: SQL on-demand (preview) gebruiken met Power BI Desktop en een rapport maken

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Demodatabase maken
> - De weergave maken die wordt gebruikt voor het rapport
> - Power BI Desktop verbinden met SQL on-demand
> - Rapport maken op basis van weergave

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

- [Power BI Desktop](https://powerbi.microsoft.com/downloads/): nodig voor het visualiseren van de gegevens en het maken van een rapport.
- [Azure Synapse-werkruimte](https://docs.microsoft.com/azure/synapse-analytics/quickstart-synapse-studio): nodig voor het maken van een database, externe gegevensbron en-weergave.

Optioneel:

- Een SQL-query-hulpprogramma, zoals [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) of [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

Waarden voor de volgende parameters:

| Parameter                                 | Beschrijving                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adres van SQL on-demand service-eindpunt    | Gebruikt als servernaam                                   |
| Regio van SQL on-demand service-eindpunt     | Wordt gebruikt om te bepalen welke opslag wordt gebruikt in de voorbeelden |
| Gebruikersnaam en wachtwoord voor eindpunttoegang | Gebruikt voor toegang tot het eindpunt                               |
| Database die u gaat gebruiken om weergaven te maken     | De database die wordt gebruikt als uitgangspunt in de voorbeelden       |

## <a name="1---create-database"></a>1 - Database maken

Voor de demo-omgeving maakt u uw eigen demodatabase. U gebruikt deze database om metagegevens weer te geven, niet om werkelijke gegevens op te slaan.

Maak de demodatabase (en verwijder indien nodig een bestaande database) door het volgende Transact-SQL (T-SQL)-script uit te voeren:

```sql
-- Drop database if it exists
DROP DATABASE IF EXISTS Demo
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-data-source"></a>2 - Gegevensbron maken

Een gegevensbron is vereist voor de SQL on-demand-service om toegang te krijgen tot bestanden in de opslag. Maak de gegevensbron voor een opslagaccount dat zich in dezelfde regio als uw eindpunt bevindt. Hoewel SQL on-demand toegang kan krijgen tot opslagaccounts uit verschillende regio's, verkrijgt u betere prestaties als de opslag en het eindpunt zich in dezelfde regio bevinden.

Maak de gegevensbron door het volgende Transact-SQL-script (T-SQL) uit te voeren:

```sql
-- There is no credential in data surce. We are using public storage account which doesn't need a secret.
CREATE EXTERNAL DATA SOURCE AzureOpenData
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/')
```

## <a name="3---prepare-view"></a>3 - Weergave voorbereiden

Maak de weergave op basis van de externe demogegevens voor Power BI om te gebruiken door het volgende T-SQL-script (Transact-SQL) uit te voeren:

Maak de weergave `usPopulationView` binnenin de database `Demo` met de volgende query:

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS uspv;
```

De demogegevens bevatten de volgende gegevenssets:

De Amerikaanse populatie per geslacht en ras voor elke Amerikaanse provincie, met gegevens uit de Decennial Census van 2000 en 2010 in de Parquet-indeling.

| Mappad                                                  | Beschrijving                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /release/                                                    | Bovenliggende map voor gegevens in het demo-opslagaccount               |
| /release/us_population_county/                               | Gegevensbestanden over de Amerikaanse populatie in de Parquet-indeling, gepartitioneerd per jaar met Hive/Hadoop-partitieschema. |

## <a name="4---create-power-bi-report"></a>4 - Power BI-rapport maken

Maak het rapport voor Power BI Desktop met de volgende stappen:

1. Open de toepassing Power BI Desktop en selecteer **Gegevens ophalen**.

   ![Open de toepassing Power BI Desktop en selecteer Gegevens ophalen.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Selecteer **Azure** > **Azure SQL-database**. 

   ![Gegevensbron selecteren.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Typ de naam van de server waarop de database zich bevindt in het veld **Server** en typ `Demo` de naam van de database. Selecteer de optie **Importeren** en selecteer vervolgens **OK**. 

   ![Selecteer database op het eindpunt.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Selecteer verificatiemethode van voorkeur:

    - Voorbeeld voor AAD 
  
        ![Klik op Aanmelden.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Voorbeeld voor SQL-aanmelding: typ uw gebruikersnaam en wachtwoord.

        ![SQL-aanmelding gebruiken.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Selecteer de weergave `usPopulationView` en selecteer vervolgens **Laden**. 

   ![Selecteer een Weergave op de geselecteerde database.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Wacht totdat de bewerking is voltooid. Hierna wordt een pop-upitem weergegeven `There are pending changes in your queries that haven't been applied`. Selecteer **Wijzigingen toepassen**. 

   ![Klik op wijzigingen toepassen.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Wacht tot het dialoogvenster **Querywijzigingen toepassen** is verdwenen. Dit kan enkele minuten duren. 

   ![Wacht totdat een query is voltooid.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. Zodra het laden is voltooid, selecteert u de volgende kolommen in deze volgorde om het rapport te maken:
   - countyName
   - population
   - stateName

   ![Selecteer interessante kolommen om een kaartrapport te genereren.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met dit rapport, verwijdert u de resources met de volgende stappen:

1. De referentie voor het opslagaccount verwijderen

   ```sql
   DROP EXTENAL DATA SOURCE AzureOpenData
   ```

2. De weergave verwijderen

   ```sql
   DROP VIEW usPopulationView;
   ```

3. De database verwijderen

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Volgende stappen

Ga naar de [Query-opslagbestanden](develop-storage-files-overview.md) voor meer informatie over het uitvoeren van een query op opslagbestanden met Synapse SQL.
