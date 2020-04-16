---
title: 'Zelfstudie: SQL on-demand (voorbeeld) verbinden met Power BI-bureaublad& rapport maken'
description: In deze zelfstudie leert u hoe u SQL on-demand (preview) in Azure Synapse Analytics aansluit op Power BI-bureaublad en een demorapport maakt op basis van een weergave.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0df8ac495b6aca81e46dffc248019483b1c82202
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422450"
---
# <a name="tutorial-connect-sql-on-demand-preview-to-power-bi-desktop--create-report"></a>Zelfstudie: SQL on-demand (voorbeeld) verbinden met Power BI-bureaublad& rapport maken

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Demodatabase maken
> - Weergave maken die wordt gebruikt voor rapport
> - Verbinding maken met Power BI-bureaublad
> - Rapport maken op basis van weergave

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende software nodig:

- Een SQL-querytool, zoals [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)of SQL Server Management Studio [(SSMS).](/sql/ssms/download-sql-server-management-studio-ssms)
- [Power BI-bureaublad](https://powerbi.microsoft.com/downloads/).

Waarden voor de volgende parameters:

| Parameter                                 | Beschrijving                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL on-demand serviceeindpuntadres    | Gebruikt als servernaam                                   |
| SQL on-demand serviceendpoint-regio     | Wordt gebruikt om de opslag te bepalen die in de monsters wordt gebruikt |
| Gebruikersnaam en wachtwoord voor toegang tot eindpunten | Wordt gebruikt om toegang te krijgen tot eindpunt                               |
| Database die u gebruikt om weergaven te maken     | De database die als uitgangspunt wordt gebruikt in de monsters       |

## <a name="1---create-database"></a>1 - Database maken

Maak voor de demo-omgeving je eigen demodatabase. U gebruikt deze database om metagegevens weer te geven, niet om werkelijke gegevens op te slaan.

Maak de demodatabase (en laat indien nodig een bestaande database neerzetten) door het volgende Transact-SQL -script (T-SQL) uit te voeren:

```sql
-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'Demo')
BEGIN
    DROP DATABASE Demo
END;
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-credential"></a>2 - Referentie maken

Er is een referentie nodig voor de SQL on-demand service om toegang te krijgen tot bestanden in opslag. Maak de referenties voor een opslagaccount dat zich in dezelfde regio bevindt als uw eindpunt. Hoewel SQL on-demand toegang heeft tot opslagaccounts uit verschillende regio's, biedt het hebben van het opslag- en eindpunt in dezelfde regio betere prestaties.

Maak de referentie door het volgende Transact-SQL -script (T-SQL) uit te voeren:

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

## <a name="3---prepare-view"></a>3 - Weergave voorbereiden

Maak de weergave op basis van de externe demogegevens die Power BI kan gebruiken door het volgende Transact-SQL-script (T-SQL) uit te voeren:

Maak de `usPopulationView` weergave `Demo` in de database met de volgende query:

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

De demogegevens bevatten de volgende gegevenssets:

Amerikaanse bevolking per geslacht en ras voor elke Amerikaanse provincie afkomstig uit 2000 en 2010 Decennial Census in parket formaat.

| Mappad                                                  | Beschrijving                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /release/                                                    | Bovenliggende map voor gegevens in demo-opslagaccount               |
| /release/us_population_county/                               | Amerikaanse populatie gegevensbestanden in Parket formaat, verdeeld per jaar met behulp van Hive / Hadoop partitionering regeling. |

## <a name="4---create-power-bi-report"></a>4 - Power BI-rapport maken

Maak het rapport voor Power BI Desktop met de volgende stappen:

1. Open de Power BI-bureaubladtoepassing en selecteer **Gegevens opvragen**.

   ![Open de Power BI-bureaubladtoepassing en selecteer gegevens opvragen.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Selecteer **Azure** > **Azure SQL Database**. 

   ![Selecteer gegevensbron.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Typ de naam van de server waar **Server** de database zich `Demo` in het veld Server bevindt en typ de databasenaam. Selecteer de optie **Importeren** en selecteer **OK**. 

   ![Selecteer database op het eindpunt.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Voorkeursverificatiemethode selecteren:

    - Voorbeeld voor AAD 
  
    ![Klik op Aanmelden.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Voorbeeld voor SQL Login - Typ uw gebruikersnaam en wachtwoord.

    ![Gebruik SQL-login.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Selecteer de `usPopulationView`weergave en selecteer **Laden**. 

   ![Selecteer een weergave in de geselecteerde database.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Wacht tot de bewerking is voltooid en dan `There are pending changes in your queries that haven't been applied`verschijnt er een pop-up met vermelding van . Selecteer **Wijzigingen toepassen**. 

   ![Klik op Wijzigingen toepassen.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Wacht tot het dialoogvenster **Querywijzigingen toepassen** verdwijnt, wat enkele minuten kan duren. 

   ![Wacht tot een query is voltooid.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. Zodra de belasting is voltooid, selecteert u de volgende kolommen in deze volgorde om het rapport te maken:
   - provincieNaam
   - Bevolking
   - stateName

   ![Selecteer kolommen die interessant zijn om een kaartrapport te genereren.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Resources opschonen

Zodra u klaar bent met het gebruik van dit rapport, verwijdert u de bronnen met de volgende stappen:

1. De referenties voor het opslagaccount verwijderen

   ```sql
   DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
   ```

2. De weergave verwijderen

   ```sql
   DROP VIEW usPopulationView;
   ```

3. De database laten vallen

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Volgende stappen

Ga naar de [queryopslagbestanden](develop-storage-files-overview.md) voor meer informatie over het opvragen van opslagbestanden met Synapse SQL.
