---
title: Verbinding maken met VSTS
description: Query Azure Synapse Analytics met Visual Studio.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e986515a911cf1bbd88dfc73c56efcc6e81826d6
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351409"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Verbinding maken met Azure Synapse Analytics met Visual Studio en SSDT
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [Ssms](sql-data-warehouse-query-ssms.md)
> 
> 

Gebruik Visual Studio om binnen enkele minuten een SQL-groep op te vragen binnen Azure Synapse. Deze methode maakt gebruik van de SQL Server Data Tools (SSDT) extensie in Visual Studio 2019. 

## <a name="prerequisites"></a>Vereisten
Voor deze zelfstudie hebt u het volgende nodig:

* Een bestaande SQL-pool. Zie [Een SQL-pool maken](create-data-warehouse-portal.md)als u er een wilt maken.
* SSDT voor Visual Studio. Als je Visual Studio hebt, heb je waarschijnlijk al SSDT voor Visual Studio. Voor installatie-instructies en -opties raadpleegt u [Visual Studio en SSDT installeren](sql-data-warehouse-install-visual-studio.md).
* De volledig gekwalificeerde SQL-servernaam. Zie Verbinding maken [met SQL-pool om](sql-data-warehouse-connect-overview.md)deze informatie te vinden.

## <a name="1-connect-to-your-sql-pool"></a>1. Verbinding maken met uw SQL-groep
1. Open Visual Studio 2019.
2. Open SQL Server Object Explorer door**SQL Server Object Explorer** **weergeven** > te selecteren .
   
    ![SQL Server-objectverkenner](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Klik op het pictogram **SQL Server toevoegen**.
   
    ![SQL Server toevoegen](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Vul de velden in het venster Connect to Server (Verbinding maken met server) in.
   
    ![Verbinding maken met server](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Servernaam**. Voer de eerder vastgestelde **servernaam** in.
   * **Verificatie**. Selecteer **SQL Server Authentication** (SQL Server-verificatie) of **Active Directory Integrated Authentication** (Geïntegreerde Active Directory-verificatie).
   * **User Name** (Gebruikersnaam) en **Password** (Wachtwoord). Voer de gebruikersnaam en het wachtwoord in als u hierboven SQL Server-verificatie hebt geselecteerd.
   * Klik op **Verbinden**.
5. U kunt de Azure SQL-server uitvouwen als u deze wilt verkennen. U kunt de databases weergeven die aan de server zijn gekoppeld. Vouw AdventureWorksDW uit als u de tabellen in de voorbeelddatabase wilt zien.
   
    ![AdventureWorksDW verkennen](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Een voorbeeldquery uitvoeren
Nu er een verbinding met uw database is ingesteld, gaat u een query schrijven.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.
2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.
   
    ![Nieuwe query](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Kopieer de volgende T-SQL-query naar het queryvenster:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Voer de query uit door op de groene `CTRL` + `SHIFT` +pijl te klikken of gebruik de volgende snelkoppeling: `E`.
   
    ![Query uitvoeren](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Bekijk de resultaten van de query. In dit voorbeeld heeft de tabel FactInternetSales 60398 rijen.
   
    ![Queryresultaten](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Volgende stappen
Nu u verbinding maken en query's [uitvoeren,](sql-data-warehouse-get-started-visualize-with-power-bi.md)probeert u de gegevens te visualiseren met Power BI.

Zie [Verifiëren naar SQL-groep](sql-data-warehouse-authentication.md)als u uw omgeving wilt configureren voor Azure Active Directory-verificatie.