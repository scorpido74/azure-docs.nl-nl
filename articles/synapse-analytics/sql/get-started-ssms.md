---
title: "SSMS: Synapse SQL verbinden en query's"
description: Gebruik SQL Server Management Studio (SSMS) om verbinding te maken met Synapse SQL in Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 704da86fd1d816dbf5d6cd9cf67dfee53fce2622
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423738"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Verbinding maken met Synapse SQL met SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

U [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) gebruiken om verbinding te maken met Synapse SQL in Azure Synapse Analytics en deze op te vragen via SQL on-demand (preview) of SQL-poolresources. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Ondersteunde hulpprogramma's voor SQL on-demand (voorbeeld)

SSMS wordt gedeeltelijk ondersteund vanaf versie 18.5 met beperkte functies zoals verbinding maken en query's. [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) wordt volledig ondersteund.

## <a name="prerequisites"></a>Vereisten

Controleer voordat u begint de volgende voorwaarden:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* Voor SQL-groep hebt u een bestaand gegevensmagazijn nodig. Zie [Een SQL-pool maken](../quickstart-create-sql-pool.md)als u er een wilt maken. Voor SQL on-demand is er al een ingericht in uw werkruimte bij het maken van de tijd. 
* De volledig gekwalificeerde SQL Server-naam. Zie [Verbinding maken met Synapse SQL om](connect-overview.md)dit te vinden.

## <a name="connect"></a>Verbinding maken

### <a name="sql-pool"></a>SQL-pool

Voer de volgende stappen uit om verbinding te maken met Synapse SQL met SQL-groep: 

1. Open SQL Server Management Studio (SSMS). 
1. Vul in het dialoogvenster **Verbinding maken met server** de velden in en selecteer Verbinding **maken:** 
  
    ![Verbinding maken met server](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Servernaam:** voer de eerder geïdentificeerde **servernaam** in.
   * **Verificatie:** kies een verificatietype, zoals **SQL Server Authentication** of Active Directory Integrated **Authentication**.
   * **Gebruikersnaam** en **wachtwoord:** voer uw gebruikersnaam en wachtwoord in als sql server-verificatie hierboven is geselecteerd.

1. Vouw uw Azure SQL Server uit in **Object Explorer**. U de databases bekijken die aan de server zijn gekoppeld, zoals de voorbeeld-AdventureWorksDW-database. U de database uitbreiden om de tabellen te bekijken:
   
    ![AdventureWorksDW verkennen](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="sql-on-demand-preview"></a>SQL on-demand (voorbeeld)

Voer de volgende stappen uit om verbinding te maken met Synapse SQL met SQL on-demand: 

1. Open SQL Server Management Studio (SSMS).
1. Vul in het dialoogvenster **Verbinding maken met server** de velden in en selecteer Verbinding **maken:** 
   
    ![Verbinding maken met server](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Servernaam:** voer de eerder geïdentificeerde **servernaam** in.
   * **Verificatie:** kies een verificatietype, zoals **SQL Server Authentication** of Active Directory Integrated **Authentication:**
   * **Gebruikersnaam** en **wachtwoord:** voer uw gebruikersnaam en wachtwoord in als sql server-verificatie hierboven is geselecteerd.
   * Klik op **Verbinden**.

4. U kunt de Azure SQL-server uitvouwen als u deze wilt verkennen. U kunt de databases weergeven die aan de server zijn gekoppeld. Vouw *de demo* uit om de inhoud in uw voorbeelddatabase te bekijken.
   
    ![AdventureWorksDW verkennen](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Een voorbeeldquery uitvoeren

### <a name="sql-pool"></a>SQL-pool

Nu er een databaseverbinding is tot stand gekomen, u de gegevens opvragen.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.
2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.
   
    ![Nieuwe query](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Kopieer deze T-SQL-query naar het queryvenster:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Voer de query uit. Klik hierom `Execute` op de volgende `F5`snelkoppeling: .
   
    ![Query uitvoeren](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Bekijk de resultaten van de query. In dit voorbeeld heeft de tabel FactInternetSales 60398 rijen.
   
    ![Queryresultaten](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="sql-on-demand"></a>SQL on-demand

Nu u een databaseverbinding hebt gemaakt, u de gegevens opvragen.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.
2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.
   
    ![Nieuwe query](./media/get-started-ssms/new-query.png)
3. Kopieer de volgende T-SQL-query naar het queryvenster:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Voer de query uit. Klik hierom `Execute` op de volgende `F5`snelkoppeling: .
   
    ![Query uitvoeren](./media/get-started-ssms/execute-query.png)
5. Bekijk de resultaten van de query. In dit voorbeeld heeft de weergave Bevolkingsoverzicht in de VS 3664512 rijen.
   
    ![Queryresultaten](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Volgende stappen
Nu u verbinding maken en query's [uitvoeren,](get-started-power-bi-professional.md)probeert u de gegevens te visualiseren met Power BI.

Zie [Verifiëren voor Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)als u uw omgeving wilt configureren voor Azure Active Directory-verificatie.

