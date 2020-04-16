---
title: "Azure Data Studio (voorbeeld): Synapse SQL verbinden en query's"
description: Gebruik Azure Data Studio (preview) om verbinding te maken met Synapse SQL en query in Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: edf5a6a6a0f17c21abb818a0d41d0d0b1c39949c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423801"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio-preview"></a>Verbinding maken met Synapse SQL met Azure Data Studio (voorbeeld)

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

U [Azure Data Studio (preview)](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) gebruiken om verbinding te maken met En synapse SQL op te vragen in Azure Synapse Analytics. 

## <a name="connect"></a>Verbinding maken

Als u verbinding wilt maken met Synapse SQL, opent u Azure Data Studio en selecteert u **Nieuwe verbinding**.

![Azure Data Studio openen](./media/get-started-azure-data-studio/1-start.png)

Kies **Microsoft SQL Server** als **verbindingstype**.

De verbinding vereist de volgende parameters:

* **Server:** Server in `<Azure Synapse workspace name>`het formulier -ondemand.sql.azuresynapse.net
* **Database:** Databasenaam

> [!NOTE]
> Als u SQL **on-demand (voorbeeld)** wilt gebruiken, moet de URL er als volgt uitzien:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Als u **SQL-pool** wilt gebruiken, moet de URL er als volgt uitzien:
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

Kies **Windows-verificatie**, **Azure Active Directory**of SQL **Login** als **verificatietype**.

Als u **SQL Login** als verificatietype wilt gebruiken, voegt u de gebruikersnaam/wachtwoordparameters toe:

* **Gebruiker:** Servergebruiker in het formulier`<User>`
* **Wachtwoord:** Wachtwoord dat is gekoppeld aan de gebruiker

Als u Azure Active Directory wilt gebruiken, moet u het benodigde verificatietype kiezen.

![Microsoft Azure Active Directory-verificatie](./media/get-started-azure-data-studio/3-aad-auth.png)

In deze schermafbeelding worden de **verbindingsgegevens** voor **Windows-verificatie**weergegeven:

![Windows-verificatie](./media/get-started-azure-data-studio/3-windows-auth.png)

Deze schermafbeelding toont de **verbindingsdetails** met **SQL Login:**

![SQL-aanmelding](./media/get-started-azure-data-studio/2-database-details.png)

Na een succesvolle aanmelding ziet u ![een dashboard als dit: Dashboard](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Query’s uitvoeren

Eenmaal verbonden, u Synapse SQL opvragen met behulp van ondersteunde [Transact-SQL (T-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) instructies tegen de instantie. Selecteer **Nieuwe query** in de dashboardweergave om aan de slag te gaan.

![Nieuwe query](./media/get-started-azure-data-studio/5-new-query.png)

U bijvoorbeeld de volgende Transact-SQL-instructie gebruiken om [parketbestanden op](query-parquet-files.md) te vragen met SQL on-demand:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Volgende stappen 
Ontdek andere manieren om verbinding te maken met Synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [sqlcmd sqlcmd](get-started-connect-sqlcmd.md)
 
