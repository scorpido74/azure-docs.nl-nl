---
title: Verbinding maken met en een query uitvoeren op Synapse SQL met Visual Studio en SSDT
description: Gebruik Visual Studio voor het opvragen van een SQL-pool met behulp van Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a8839609856bda5304712405ec57accb4afb095
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201677"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Verbinding maken met Synapse SQL met Visual Studio en SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Gebruik Visual Studio voor het opvragen van een SQL-pool met behulp van Azure Synapse Analytics. Deze methode maakt gebruik van de uitbrei ding SQL Server Data Tools (SSDT) in Visual Studio 2019. 

> [!NOTE]
> SQL on-demand (preview) wordt niet ondersteund door SSDT.

## <a name="prerequisites"></a>Vereisten
Als u deze zelf studie wilt gebruiken, moet u beschikken over de volgende onderdelen:

* Een bestaande SQL-groep. Als u er nog geen hebt, raadpleegt u [een SQL-groep maken](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) om dit vereiste te volt ooien.
* SSDT voor Visual Studio. Als u Visual Studio hebt, beschikt u waarschijnlijk al over dit onderdeel. Voor installatie-instructies en -opties raadpleegt u [Visual Studio en SSDT installeren](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* De volledig gekwalificeerde SQL-servernaam. Zie [verbinding maken met de SQL-groep](connect-overview.md)om dit te vinden.

## <a name="1-connect-to-sql-pool"></a>1. Maak verbinding met de SQL-groep
1. Open Visual Studio 2019.
2. Open de SQL Server-objectverkenner. Als u dit wilt doen, selecteert u SQL Server-objectverkenner **weer geven**  >  **SQL Server Object Explorer**.
   
    ![SQL Server-objectverkenner](./media/get-started-visual-studio/open-ssdt.png)
3. Klik op het pictogram **SQL Server toevoegen**.
   
    ![SQL Server toevoegen](./media/get-started-visual-studio/add-server.png)
4. Vul de velden in het venster Connect to Server (Verbinding maken met server) in.
   
    ![Verbinding maken met server](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Server naam**: Voer de eerder geïdentificeerde **Server naam** in.
   * **Verificatie**: Selecteer **SQL Server verificatie** of **Active Directory geïntegreerde verificatie**:
   * **Gebruikers naam** en **wacht woord**: Voer uw gebruikers naam en wacht woord in als SQL Server verificatie hierboven is geselecteerd.
   * Klik op **Verbinden**.
5. U kunt de Azure SQL-server uitvouwen als u deze wilt verkennen. U kunt de databases weergeven die aan de server zijn gekoppeld. Vouw AdventureWorksDW uit als u de tabellen in de voorbeelddatabase wilt zien.
   
    ![AdventureWorksDW verkennen](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Voer een voorbeeld query uit
Nu er een verbinding tot stand is gebracht met uw data base, schrijft u een query.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.
2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.
   
    ![Nieuwe query](./media/get-started-visual-studio/new-query2.png)
3. Kopieer de volgende T-SQL-query naar het query venster:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Voer de query uit. Daartoe klikt u op de groene pijl of gebruikt u de volgende snelkoppeling: `CTRL`+`SHIFT`+`E`.
   
    ![Query uitvoeren](./media/get-started-visual-studio/run-query.png)
5. Bekijk de resultaten van de query. In dit voorbeeld heeft de tabel FactInternetSales 60398 rijen.
   
    ![Queryresultaten](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Volgende stappen
Nu u verbinding maakt en query's uitvoert, kunt u proberen [de gegevens te visualiseren met Power bi](get-started-power-bi-professional.md).
Zie [verifiëren bij SQL-groep](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)om uw omgeving te configureren voor Azure Active Directory-verificatie.
 