---
title: Verbinding maken met Power BI Professional
description: In deze zelf studie gaan we stappen uitvoeren om Power BI bureau blad verbinding te maken met SQL op aanvraag (preview).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a9db42bcd69d9a24a454c02c9bb0e2d339cb4860
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82185775"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Verbinding maken met Synapse SQL met Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

In deze zelf studie worden de stappen beschreven voor het verbinden van Power BI bureau blad naar SQL op aanvraag (preview).

## <a name="prerequisites"></a>Vereisten

Hulp programma voor het verlenen van query's:

- Gewenste SQL-client:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI bureau blad geïnstalleerd

Parameters:

| Parameter                                 | Beschrijving                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL on-demand service-eindpunt adres    | Wordt gebruikt als server naam                                   |
| SQL on-demand service-eindpunt regio     | Wordt gebruikt om te bepalen welke opslag wordt gebruikt in voor beelden |
| Gebruikers naam en wacht woord voor endpoint Access | Wordt gebruikt voor toegang tot het eind punt                               |
| Data Base die u gebruikt voor het maken van weer gaven     | Deze data base wordt gebruikt als uitgangs punt in voor beelden       |

## <a name="first-time-setup"></a>Eerste keer instellen

Er zijn twee stappen voorafgaand aan het gebruik van voor beelden:

1. Data base maken voor uw weer gaven
2. Referenties maken voor gebruik door SQL op aanvraag om toegang te krijgen tot bestanden in de opslag

### <a name="create-database"></a>Database maken

Voor dit aan de slag-artikel moet u uw eigen data base maken om te gebruiken als demo. Er is een data base nodig voor het maken van weer gaven. U gebruikt deze data base in enkele van de voorbeeld query's in deze documentatie.

> [!NOTE]
> Data bases worden alleen gebruikt voor het weer geven van meta gegevens, niet voor werkelijke gegevens.
>
> Noteer de naam van de data base die u gebruikt. u hebt deze later nodig.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Referenties maken

We moeten referenties maken voordat u query's kunt uitvoeren. De referenties worden gebruikt door SQL service op aanvraag om toegang te krijgen tot bestanden in de opslag.

> [!NOTE]
> U moet een referentie maken voor toegang tot het opslag account. Hoewel SQL on-demand toegang kan krijgen tot opslag vanuit verschillende regio's, biedt opslag-en Azure Synapse-werk ruimte in dezelfde regio betere prestaties.

**Code fragment voor het maken van referenties voor gegevens containers met tellingen**, uitvoeren:

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

## <a name="creating-power-bi-desktop-report"></a>Power BI bureau blad-rapport maken

Open Power BI bureaublad toepassing en selecteer optie **gegevens ophalen** .
![Open Power BI bureaublad toepassing en selecteer gegevens ophalen.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Stap 1: gegevens bron selecteren

Selecteer **Azure** in het menu en klik vervolgens **Azure SQL database**.
![Selecteer een gegevens bron.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Stap 2: data base selecteren

Schrijf de URL voor de data base en de naam van de Data Base waarin de weer gave zich bevindt.
![Selecteer Data Base op het eind punt.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Volgende stappen

Ga naar [opslag bestanden opvragen](get-started-azure-data-studio.md) om te leren hoe u verbinding kunt maken met SQL op aanvraag met behulp van Azure Data Studio.
 