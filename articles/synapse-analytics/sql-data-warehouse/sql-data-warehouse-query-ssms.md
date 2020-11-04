---
title: Verbinden met SSMS
description: Gebruik SQL Server Management Studio (SSMS) om verbinding te maken met en een query uit te zoeken naar een toegewezen SQL-groep in azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c2fab8f02f31897a2e3d38094b9bd365f8e921e9
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325321"
---
# <a name="connect-to-a-dedicated-sql-pool-in-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Verbinding maken met een toegewezen SQL-groep in azure Synapse Analytics met SQL Server Management Studio (SSMS)

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Gebruik SQL Server Management Studio (SSMS) om verbinding te maken met en een query uit te brengen op een Data Warehouse in azure Synapse.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Een bestaande exclusieve SQL-groep. Zie [een toegewezen SQL-groep maken](create-data-warehouse-portal.md)om er een te maken.
* SQL Server Management Studio (SSMS) is geïnstalleerd. [Down load SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) gratis als u dit nog niet hebt gedaan.
* De volledig gekwalificeerde SQL-servernaam. Zie [verbinding maken met een toegewezen SQL-groep](sql-data-warehouse-connect-overview.md)om deze informatie te vinden.

## <a name="1-connect-to-your-dedicated-sql-pool"></a>1. Maak verbinding met uw toegewezen SQL-groep

1. Open SQL Server Management Studio.
2. Open objectverkenner door **bestand**  >  **Connect objectverkenner** te selecteren.

    ![SQL Server-objectverkenner](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Vul de velden in het venster Connect to Server (Verbinding maken met server) in.

   ![Verbinding maken met server](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * **Server naam**. Voer de eerder vastgestelde **servernaam** in.
   * **Verificatie**. Selecteer **SQL Server Authentication** (SQL Server-verificatie) of **Active Directory Integrated Authentication** (Geïntegreerde Active Directory-verificatie).
   * **Gebruikers naam** en **wacht woord**. Voer de gebruikersnaam en het wachtwoord in als u hierboven SQL Server-verificatie hebt geselecteerd.
   * Klik op **Verbinden**.
4. U kunt de Azure SQL-server uitvouwen als u deze wilt verkennen. U kunt de databases weergeven die aan de server zijn gekoppeld. Vouw AdventureWorksDW uit als u de tabellen in de voorbeelddatabase wilt zien.

   ![AdventureWorksDW verkennen](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. Voer een voorbeeld query uit

Nu er een verbinding met uw database is ingesteld, gaat u een query schrijven.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.
2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.

   ![Nieuwe query](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Kopieer de volgende T-SQL-query in het queryvenster:

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. Voer de query uit door te klikken `Execute` of de volgende snelkoppeling te gebruiken: `F5` .

   ![Query uitvoeren](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Bekijk de resultaten van de query. In dit voorbeeld heeft de tabel FactInternetSales 60398 rijen.

   ![Queryresultaten](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u verbinding maakt en een query uitvoert, kunt u proberen [de gegevens te visualiseren met Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md). Zie [verifiëren voor exclusieve SQL-groep](sql-data-warehouse-authentication.md)om uw omgeving te configureren voor Azure Active Directory-verificatie.
