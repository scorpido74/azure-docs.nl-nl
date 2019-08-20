---
title: Verbinding maken met Azure SQL Data Warehouse-SSMS | Microsoft Docs
description: Gebruik SQL Server Management Studio (SSMS) om verbinding te maken met en een query uit te Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: b96896c0366c6fba2548480b166bdb9cf6ca692c
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575344"
---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>Verbinding maken met SQL Data Warehouse met SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Gebruik SQL Server Management Studio (SSMS) om verbinding te maken met en een query uit te Azure SQL Data Warehouse. 

## <a name="prerequisites"></a>Vereisten
Voor deze zelfstudie hebt u het volgende nodig:

* Een bestaande SQL Data Warehouse. Zie [Een SQL Data Warehouse maken][Create a SQL Data Warehouse] als u er een wilt maken.
* SQL Server Management Studio (SSMS) is geïnstalleerd. [Installeer SSMS][Install SSMS] gratis als u dit nog niet hebt gedaan.
* De volledig gekwalificeerde SQL-servernaam. Zie [Verbinding maken met SQL Data Warehouse][Connect to SQL Data Warehouse] om dit te vinden.

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Verbinding maken met uw SQL Data Warehouse
1. Open SQL Server Management Studio.
2. Open Objectverkenner. Selecteer hiervoor **File** > **Connect objectverkenner**.
   
    ![SQL Server-objectverkenner][1]
3. Vul de velden in het venster Connect to Server (Verbinding maken met server) in.
   
    ![Verbinding maken met server][2]
   
   * **Server name** (Servernaam). Voer de eerder vastgestelde **servernaam** in.
   * **Authentication** (Verificatie). Selecteer **SQL Server Authentication** (SQL Server-verificatie) of **Active Directory Integrated Authentication** (Geïntegreerde Active Directory-verificatie).
   * **User Name** (Gebruikersnaam) en **Password** (Wachtwoord). Voer de gebruikersnaam en het wachtwoord in als u hierboven SQL Server-verificatie hebt geselecteerd.
   * Klik op **Connect** (Verbinden).
4. U kunt de Azure SQL-server uitvouwen als u deze wilt verkennen. U kunt de databases weergeven die aan de server zijn gekoppeld. Vouw AdventureWorksDW uit als u de tabellen in de voorbeelddatabase wilt zien.
   
    ![AdventureWorksDW verkennen][3]

## <a name="2-run-a-sample-query"></a>2. Een voorbeeldquery uitvoeren
Nu er een verbinding met uw database is ingesteld, gaat u een query schrijven.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.
2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.
   
    ![Nieuwe query][4]
3. Kopieer de volgende TSQL-query naar het queryvenster:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Voer de query uit. Als u dit wilt doen `Execute` , klikt of gebruikt u de `F5`volgende snelkoppeling:.
   
    ![Query uitvoeren][5]
5. Bekijk de resultaten van de query. In dit voorbeeld heeft de tabel FactInternetSales 60398 rijen.
   
    ![Queryresultaten][6]

## <a name="next-steps"></a>Volgende stappen
Nu u weet hoe u verbinding maakt en een query uitvoert, kunt u proberen [de gegevens te visualiseren met Power BI][visualizing the data with PowerBI].

Zie [Verifiëren bij SQL Data Warehouse][Authenticate to SQL Data Warehouse] om uw omgeving te configureren voor Azure Active Directory-verificatie.

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Azure portal]: https://portal.azure.com
[Install SSMS]: https://msdn.microsoft.com/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png
