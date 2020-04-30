---
title: 'Zelf studie: SQL op aanvraag (preview) verbinden met Power BI Desktop & rapport maken'
description: In deze zelf studie leert u hoe u in azure Synapse Analytics verbinding maakt met SQL op aanvraag (preview) om Power BI bureau blad en een demo rapport te maken op basis van een weer gave.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e0ac6ccde2443a7b374d9eb85f6f960af79c69dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81769483"
---
# <a name="tutorial-connect-sql-on-demand-preview-to-power-bi-desktop--create-report"></a>Zelf studie: SQL op aanvraag (preview) verbinden met Power BI Desktop & rapport maken

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Demo database maken
> - De weer gave die wordt gebruikt voor het rapport maken
> - Verbinding maken met Power BI Desktop
> - Rapport maken op basis van weer gave

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie hebt u de volgende software nodig:

- Een SQL-query programma, zoals [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), of [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- [Power bi Desktop](https://powerbi.microsoft.com/downloads/).

Waarden voor de volgende para meters:

| Parameter                                 | Beschrijving                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL on-demand service-eindpunt adres    | Gebruikt als server naam                                   |
| SQL on-demand service-eindpunt regio     | Wordt gebruikt om te bepalen welke opslag wordt gebruikt in de voor beelden |
| Gebruikers naam en wacht woord voor endpoint Access | Gebruikt voor toegang tot het eind punt                               |
| Data Base die u gaat gebruiken om weer gaven te maken     | De data base die wordt gebruikt als uitgangs punt in de voor beelden       |

## <a name="1---create-database"></a>1-data base maken

Voor de demo omgeving maakt u uw eigen demo database. U gebruikt deze data base om meta gegevens weer te geven, niet om de werkelijke gegevens op te slaan.

Maak de demo database (en verwijder indien nodig een bestaande data base) door het volgende Transact-SQL (T-SQL)-script uit te voeren:

```sql
-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.databases WHERE name = 'Demo')
BEGIN
    DROP DATABASE Demo
END;
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-credential"></a>2-Referentie maken

Een referentie is vereist voor de SQL on-demand-service om toegang te krijgen tot bestanden in de opslag. Maak de referentie voor een opslag account dat zich in dezelfde regio als uw eind punt bevindt. Hoewel SQL on-demand toegang kan krijgen tot opslag accounts uit verschillende regio's, biedt de opslag en het eind punt in dezelfde regio betere prestaties.

Maak de referentie door het volgende Transact-SQL-script (T-SQL) uit te voeren:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = '';
GO
```

## <a name="3---prepare-view"></a>3: weer gave voorbereiden

Maak de weer gave op basis van de externe demo gegevens voor het gebruik van Power BI om te gebruiken door het volgende Transact-SQL (T-SQL)-script uit te voeren:

Maak de weer `usPopulationView` gave in de `Demo` data base met de volgende query:

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS uspv;
```

De demo gegevens bevatten de volgende gegevens sets:

De Amerikaanse populatie per geslacht en race voor elk Amerikaanse land bron van 2000 en 2010 Decennial telling in de Parquet-indeling.

| Mappad                                                  | Beschrijving                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /release                                                    | Bovenliggende map voor gegevens in het demo-opslag account               |
| /release/us_population_county/                               | Amerikaanse populatie gegevens bestanden in de Parquet-indeling, gepartitioneerd per jaar met hive/Hadoop-partitie schema. |

## <a name="4---create-power-bi-report"></a>4-Power BI rapport maken

Maak het rapport voor Power BI Desktop met behulp van de volgende stappen:

1. Open de toepassing Power BI Desktop en selecteer **gegevens ophalen**.

   ![Open Power BI bureaublad toepassing en selecteer gegevens ophalen.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Selecteer **Azure** > **Azure SQL database**. 

   ![Selecteer een gegevens bron.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Typ de naam van de server waarop de data base zich bevindt in het veld **Server** en `Demo` Typ de naam van de data base. Selecteer de optie **importeren** en selecteer vervolgens **OK**. 

   ![Selecteer Data Base op het eind punt.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Selecteer voorkeurs verificatie methode:

    - Voor beeld voor AAD 
  
    ![Klik op Aanmelden.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Voor beeld voor SQL-aanmelding: Typ uw gebruikers naam en wacht woord.

    ![SQL-aanmelding gebruiken.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Selecteer de weer `usPopulationView`gave en selecteer vervolgens **laden**. 

   ![Selecteer een weer gave op de geselecteerde data base.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Wacht tot de bewerking is voltooid en vervolgens wordt er een pop-upvenster weer gegeven `There are pending changes in your queries that haven't been applied`. Selecteer **wijzigingen Toep assen**. 

   ![Klik op wijzigingen Toep assen.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Wacht totdat het dialoog venster **query wijzigingen Toep assen** wordt verwijderd. Dit kan enkele minuten duren. 

   ![Wacht totdat een query is voltooid.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. Zodra de belasting is voltooid, selecteert u de volgende kolommen in deze volg orde om het rapport te maken:
   - graafschapnaam
   - omvatten
   - stateful

   ![Selecteer interessante kolommen om een kaart rapport te genereren.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met dit rapport, verwijdert u de resources met de volgende stappen:

1. De referentie voor het opslag account verwijderen

   ```sql
   DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
   ```

2. De weer gave verwijderen

   ```sql
   DROP VIEW usPopulationView;
   ```

3. De data base verwijderen

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Volgende stappen

Ga naar de [query-opslag bestanden](develop-storage-files-overview.md) om te leren hoe u een query kunt uitvoeren op opslag bestanden met Synapse SQL.
