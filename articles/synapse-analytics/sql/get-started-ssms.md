---
title: 'SSMS: Connect and query Synapse SQL'
description: Gebruik SQL Server Management Studio (SSMS) om verbinding te maken met en query's uit te Synapse SQL in azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 704da86fd1d816dbf5d6cd9cf67dfee53fce2622
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81423738"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Verbinding maken met Synapse SQL met SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

U kunt [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) gebruiken om verbinding te maken met en een query uit te Synapse SQL in azure Synapse Analytics via SQL on-demand (preview) of SQL pool-resources. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Ondersteunde hulpprogram ma's voor SQL on-demand (preview)

SSMS wordt gedeeltelijk ondersteund vanaf versie 18,5 met beperkte functies, zoals verbinding maken en query's uitvoeren. [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) wordt volledig ondersteund.

## <a name="prerequisites"></a>Vereisten

Controleer voordat u begint of u aan de volgende vereisten voldoet:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* Voor SQL-groep hebt u een bestaand Data Warehouse nodig. Zie [een SQL-groep maken](../quickstart-create-sql-pool.md)om er een te maken. Voor SQL op aanvraag is er tijdens de aanmaak tijd al een in uw werk ruimte ingericht. 
* De volledig gekwalificeerde SQL Server naam. Zie [verbinding maken met Synapse SQL](connect-overview.md)voor meer informatie.

## <a name="connect"></a>Verbinding maken

### <a name="sql-pool"></a>SQL-pool

Voer de volgende stappen uit om verbinding te maken met Synapse SQL met SQL-groep: 

1. Open SQL Server Management Studio (SSMS). 
1. Vul in het dialoog venster **verbinding maken met server** de velden in en selecteer vervolgens **verbinding maken**: 
  
    ![Verbinding maken met server](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Server naam**: Voer de eerder geïdentificeerde **Server naam** in.
   * **Verificatie**: Kies een verificatie type, zoals **SQL Server verificatie** of **Active Directory geïntegreerde verificatie**.
   * **Gebruikers naam** en **wacht woord**: Voer uw gebruikers naam en wacht woord in als SQL Server verificatie hierboven is geselecteerd.

1. Breid uw Azure-SQL Server uit in **objectverkenner**. U kunt de data bases weer geven die zijn gekoppeld aan de server, zoals de voor beeld-AdventureWorksDW-data base. U kunt de data base uitbreiden om de tabellen te bekijken:
   
    ![AdventureWorksDW verkennen](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="sql-on-demand-preview"></a>SQL op aanvraag (preview-versie)

Voer de volgende stappen uit om verbinding te maken met Synapse SQL met behulp van SQL op aanvraag: 

1. Open SQL Server Management Studio (SSMS).
1. Vul in het dialoog venster **verbinding maken met server** de velden in en selecteer vervolgens **verbinding maken**: 
   
    ![Verbinding maken met server](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Server naam**: Voer de eerder geïdentificeerde **Server naam** in.
   * **Verificatie**: Kies een verificatie type, zoals **SQL Server verificatie** of **Active Directory geïntegreerde verificatie**:
   * **Gebruikers naam** en **wacht woord**: Voer uw gebruikers naam en wacht woord in als SQL Server verificatie hierboven is geselecteerd.
   * Klik op **Verbinden**.

4. U kunt de Azure SQL-server uitvouwen als u deze wilt verkennen. U kunt de databases weergeven die aan de server zijn gekoppeld. Vouw *demo* uit om de inhoud van uw voorbeeld database te bekijken.
   
    ![AdventureWorksDW verkennen](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Een voorbeeldquery uitvoeren

### <a name="sql-pool"></a>SQL-pool

Nu u een database verbinding tot stand hebt gebracht, kunt u een query uitvoeren op de gegevens.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.
2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.
   
    ![Nieuwe query](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Kopieer deze T-SQL-query naar het query venster:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Voer de query uit. Als u dit wilt doen `Execute` , klikt of gebruikt u de `F5`volgende snelkoppeling:.
   
    ![Query uitvoeren](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Bekijk de resultaten van de query. In dit voorbeeld heeft de tabel FactInternetSales 60398 rijen.
   
    ![Queryresultaten](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="sql-on-demand"></a>SQL on-demand

Nu u een database verbinding tot stand hebt gebracht, kunt u een query uitvoeren op de gegevens.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.
2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.
   
    ![Nieuwe query](./media/get-started-ssms/new-query.png)
3. Kopieer de volgende T-SQL-query naar het query venster:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Voer de query uit. Als u dit wilt doen `Execute` , klikt of gebruikt u de `F5`volgende snelkoppeling:.
   
    ![Query uitvoeren](./media/get-started-ssms/execute-query.png)
5. Bekijk de resultaten van de query. In dit voor beeld heeft de usPopulationView-weer gave 3664512 rijen.
   
    ![Queryresultaten](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Volgende stappen
Nu u verbinding maakt en query's uitvoert, kunt u proberen [de gegevens te visualiseren met Power bi](get-started-power-bi-professional.md).

Zie [verifiëren voor Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)om uw omgeving te configureren voor Azure Active Directory authenticatie.

