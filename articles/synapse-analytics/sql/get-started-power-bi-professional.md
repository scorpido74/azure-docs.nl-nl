---
title: Verbinding maken met Power BI Professional
description: In deze zelfstudie doorlopen we stappen om Power BI-bureaublad aan SQL on-demand (preview) te verbinden.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0ce8f3a447f1896ae6d96d343782f8cdb44d4c6f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422562"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Verbinding maken met Synapse SQL met Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

In deze zelfstudie doorlopen we stappen om Power BI-bureaublad aan SQL on-demand (preview) te verbinden.

## <a name="prerequisites"></a>Vereisten

Hulpprogramma om query's uit te geven:

- SQL-client van uw keuze:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI-bureaublad geïnstalleerd

Parameters:

| Parameter                                 | Beschrijving                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL on-demand serviceeindpuntadres    | Wordt gebruikt als servernaam                                   |
| SQL on-demand serviceendpoint-regio     | Zal worden gebruikt om te bepalen welke opslag zullen we gebruiken in monsters |
| Gebruikersnaam en wachtwoord voor toegang tot eindpunten | Wordt gebruikt om toegang te krijgen tot eindpunt                               |
| Database die u gebruikt om weergaven te maken     | Deze database wordt gebruikt als startpunt in       |

## <a name="first-time-setup"></a>First-time setup

Er zijn twee stappen voorafgaand aan het gebruik van monsters:

1. Database maken voor uw weergaven
2. Referenties maken die door SQL on-demand kunnen worden gebruikt om toegang te krijgen tot bestanden in opslag

### <a name="create-database"></a>Database maken

Aangezien u de demo-omgeving gebruikt, moet u uw eigen database maken voor demodoeleinden. Database is nodig om weergaven te maken in het. U gebruikt deze database in sommige voorbeeldquery's in deze documentatie.

> [!NOTE]
> Houd er rekening mee dat databases alleen worden gebruikt voor weergavemetagegevens, niet voor werkelijke gegevens.
>
> Schrijf de naam van de database op die u gebruikt, u zult deze later nodig hebben.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Referenties maken

We moeten referenties maken voordat u query's uitvoeren. Deze referentie wordt gebruikt door SQL on-demand service om toegang te krijgen tot bestanden in opslag.

> [!NOTE]
> Houd er rekening mee dat u referenties moet maken voor toegang tot het opslagaccount. Hoewel SQL on-demand toegang heeft tot opslag vanuit verschillende regio's, biedt opslag en Azure Synapse-werkruimte in dezelfde regio een betere prestatie-ervaring.

**Codefragment over het maken van referenties voor censusgegevenscontainers**, uitvoeren:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="creating-power-bi-desktop-report"></a>Power BI-bureaubladrapport maken

Open de Power BI-bureaubladtoepassing en selecteer de optie Gegevens opvragen.
![Open de Power BI-bureaubladtoepassing en selecteer gegevens opvragen.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Stap 1 - Gegevensbron selecteren

Selecteer 'Azure' in het menu en vervolgens 'Azure SQL Database'.
![Selecteer gegevensbron.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Stap 2 - Database selecteren

Schrijf URL voor de database en de naam van de database waar de weergave zich bevindt.
![Selecteer database op het eindpunt.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Volgende stappen

Ga naar [Query-opslagbestanden](get-started-azure-data-studio.md) voor meer informatie over hoe u verbinding maken met SQL on-demand met Azure Data Studio.
 