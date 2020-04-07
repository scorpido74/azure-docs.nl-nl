---
title: Verbinden met SSMS
description: Gebruik SQL Server Management Studio (SSMS) om verbinding te maken met Azure Synapse Analytics en op te vragen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0d65ea9b684439cab384bbaa09fcdef1302b6bcf
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743860"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Verbinding maken met Azure Synapse Analytics met SQL Server Management Studio (SSMS)

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Gebruik SQL Server Management Studio (SSMS) om verbinding te maken met en een gegevensmagazijn op te vragen binnen Azure Synapse.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Een bestaande SQL-pool. Zie [Een SQL-pool maken](create-data-warehouse-portal.md)als u er een wilt maken.
* SQL Server Management Studio (SSMS) geïnstalleerd. [Download SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) gratis als je het nog niet hebt.
* De volledig gekwalificeerde SQL-servernaam. Zie Verbinding maken [met SQL-pool om](sql-data-warehouse-connect-overview.md)deze informatie te vinden.

## <a name="1-connect-to-your-sql-pool"></a>1. Verbinding maken met uw SQL-groep

1. Open SQL Server Management Studio.
2. Open Object Explorer door **Verkenner** > **objectverkenner te**selecteren .

    ![SQL Server-objectverkenner](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Vul de velden in het venster Connect to Server (Verbinding maken met server) in.

   ![Verbinding maken met server](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * **Servernaam**. Voer de eerder vastgestelde **servernaam** in.
   * **Verificatie**. Selecteer **SQL Server Authentication** (SQL Server-verificatie) of **Active Directory Integrated Authentication** (Geïntegreerde Active Directory-verificatie).
   * **User Name** (Gebruikersnaam) en **Password** (Wachtwoord). Voer de gebruikersnaam en het wachtwoord in als u hierboven SQL Server-verificatie hebt geselecteerd.
   * Klik op **Verbinden**.
4. U kunt de Azure SQL-server uitvouwen als u deze wilt verkennen. U kunt de databases weergeven die aan de server zijn gekoppeld. Vouw AdventureWorksDW uit als u de tabellen in de voorbeelddatabase wilt zien.

   ![AdventureWorksDW verkennen](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. Een voorbeeldquery uitvoeren

Nu er een verbinding met uw database is ingesteld, gaat u een query schrijven.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.
2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.

   ![Nieuwe query](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Kopieer de volgende T-SQL-query naar het queryvenster:

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. Voer de query `Execute` uit door op `F5`de volgende snelkoppeling te klikken of deze te gebruiken: .

   ![Query uitvoeren](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Bekijk de resultaten van de query. In dit voorbeeld heeft de tabel FactInternetSales 60398 rijen.

   ![Queryresultaten](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Volgende stappen

Nu u verbinding maken en query's [uitvoeren,](sql-data-warehouse-get-started-visualize-with-power-bi.md)probeert u de gegevens te visualiseren met Power BI. Zie [Verifiëren naar SQL-groep](sql-data-warehouse-authentication.md)als u uw omgeving wilt configureren voor Azure Active Directory-verificatie.
