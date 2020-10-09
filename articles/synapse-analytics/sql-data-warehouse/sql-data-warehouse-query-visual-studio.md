---
title: Verbinding maken met VSTS
description: Query's uitvoeren voor Azure Synapse Analytics met Visual Studio.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a3e8ea3b0ed2840e5eee7a046fa3a145f82884dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85200695"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Verbinding maken met Azure Synapse Analytics met Visual Studio en SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Gebruik Visual Studio om binnen een paar minuten een SQL-groep in azure Synapse op te vragen. Deze methode maakt gebruik van de uitbrei ding SQL Server Data Tools (SSDT) in Visual Studio 2019. 

## <a name="prerequisites"></a>Vereisten
Voor deze zelfstudie hebt u het volgende nodig:

* Een bestaande SQL-groep. Zie [Een SQL-pool maken](create-data-warehouse-portal.md) voor informatie over het maken van een datawarehouse.
* SSDT voor Visual Studio. Als u Visual Studio hebt, hebt u waarschijnlijk al SSDT voor Visual Studio. Voor installatie-instructies en -opties raadpleegt u [Visual Studio en SSDT installeren](sql-data-warehouse-install-visual-studio.md).
* De volledig gekwalificeerde SQL-servernaam. Zie [verbinding maken met de SQL-groep](../sql/connect-overview.md)om deze informatie te vinden.

## <a name="1-connect-to-your-sql-pool"></a>1. Maak verbinding met de SQL-groep
1. Open Visual Studio 2019.
2. Open SQL Server-objectverkenner door SQL Server-objectverkenner **weer geven**te selecteren  >  **SQL Server Object Explorer**.
   
    ![SQL Server-objectverkenner](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Klik op het pictogram **SQL Server toevoegen**.
   
    ![SQL Server toevoegen](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Vul de velden in het venster Connect to Server (Verbinding maken met server) in.
   
    ![Verbinding maken met server](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Server naam**. Voer de eerder vastgestelde **servernaam** in.
   * **Verificatie**. Selecteer **SQL Server Authentication** (SQL Server-verificatie) of **Active Directory Integrated Authentication** (Geïntegreerde Active Directory-verificatie).
   * **Gebruikers naam** en **wacht woord**. Voer de gebruikersnaam en het wachtwoord in als u hierboven SQL Server-verificatie hebt geselecteerd.
   * Klik op **Verbinden**.
5. U kunt de Azure SQL-server uitvouwen als u deze wilt verkennen. U kunt de databases weergeven die aan de server zijn gekoppeld. Vouw AdventureWorksDW uit als u de tabellen in de voorbeelddatabase wilt zien.
   
    ![AdventureWorksDW verkennen](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Voer een voorbeeld query uit
Nu er een verbinding met uw database is ingesteld, gaat u een query schrijven.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.
2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.
   
    ![Nieuwe query](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Kopieer de volgende T-SQL-query in het queryvenster:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Voer de query uit door op de groene pijl te klikken of de volgende snelkoppeling te gebruiken: `CTRL` + `SHIFT` + `E` .
   
    ![Query uitvoeren](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Bekijk de resultaten van de query. In dit voorbeeld heeft de tabel FactInternetSales 60398 rijen.
   
    ![Queryresultaten](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Volgende stappen
Nu u weet hoe u verbinding maakt en een query uitvoert, kunt u proberen [de gegevens te visualiseren met Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

Zie [verifiëren bij SQL-groep](sql-data-warehouse-authentication.md)om uw omgeving te configureren voor Azure Active Directory-verificatie.
