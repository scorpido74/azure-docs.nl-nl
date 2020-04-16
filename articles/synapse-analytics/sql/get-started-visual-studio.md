---
title: Synapse SQL verbinden met en query's maken met Visual Studio en SSDT
description: Gebruik Visual Studio om SQL-groep op te vragen met Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428561"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Verbinding maken met Synapse SQL met Visual Studio en SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Gebruik Visual Studio om SQL-groep op te vragen met Azure Synapse Analytics. Deze methode maakt gebruik van de SQL Server Data Tools (SSDT) extensie in Visual Studio 2019. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Ondersteunde hulpprogramma's voor SQL on-demand (voorbeeld)

Visual studio wordt momenteel niet ondersteund voor SQL on-demand (preview). Azure Data Studi (preview)o is echter een volledig ondersteund hulpmiddel. SQL Server Management Studio wordt gedeeltelijk ondersteund vanaf versie 18.4 en heeft beperkte functies zoals verbinden en query's.

## <a name="prerequisites"></a>Vereisten
Als u deze zelfstudie wilt gebruiken, moet u de volgende onderdelen hebben:

* Een bestaande SQL-pool. Zie [Een SQL-groep maken](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) om deze vereiste te voltooien als u er geen hebt.
* SSDT voor Visual Studio. Als je Visual Studio hebt, heb je dit onderdeel waarschijnlijk al. Voor installatie-instructies en -opties raadpleegt u [Visual Studio en SSDT installeren](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* De volledig gekwalificeerde SQL-servernaam. Zie [Verbinding maken met SQL-pool om](connect-overview.md)dit te vinden.

## <a name="1-connect-to-sql-pool"></a>1. Verbinding maken met SQL-pool
1. Open Visual Studio 2019.
2. Open de SQL Server Object Explorer. Selecteer hiervoor**SQL Server Object Explorer** **weergeven** > .
   
    ![SQL Server-objectverkenner](./media/get-started-visual-studio/open-ssdt.png)
3. Klik op het pictogram **SQL Server toevoegen**.
   
    ![SQL Server toevoegen](./media/get-started-visual-studio/add-server.png)
4. Vul de velden in het venster Connect to Server (Verbinding maken met server) in.
   
    ![Verbinding maken met server](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Servernaam:** voer de eerder geïdentificeerde **servernaam** in.
   * **Verificatie:** selecteer **SQL Server-verificatie** of **geïntegreerde active directory-verificatie:**
   * **Gebruikersnaam** en **wachtwoord:** voer uw gebruikersnaam en wachtwoord in als sql server-verificatie hierboven is geselecteerd.
   * Klik op **Verbinden**.
5. U kunt de Azure SQL-server uitvouwen als u deze wilt verkennen. U kunt de databases weergeven die aan de server zijn gekoppeld. Vouw AdventureWorksDW uit als u de tabellen in de voorbeelddatabase wilt zien.
   
    ![AdventureWorksDW verkennen](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Een voorbeeldquery uitvoeren
Nu er een verbinding met uw database is gemaakt, schrijft u een query.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.
2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.
   
    ![Nieuwe query](./media/get-started-visual-studio/new-query2.png)
3. Kopieer de volgende T-SQL-query naar het queryvenster:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Voer de query uit. Daartoe klikt u op de groene pijl of gebruikt u de volgende snelkoppeling: `CTRL`+`SHIFT`+`E`.
   
    ![Query uitvoeren](./media/get-started-visual-studio/run-query.png)
5. Bekijk de resultaten van de query. In dit voorbeeld heeft de tabel FactInternetSales 60398 rijen.
   
    ![Queryresultaten](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Volgende stappen
Nu u verbinding maken en query's [uitvoeren,](get-started-power-bi-professional.md)probeert u de gegevens te visualiseren met Power BI.
Zie [Verifiëren naar SQL-groep](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)als u uw omgeving wilt configureren voor Azure Active Directory-verificatie.
 