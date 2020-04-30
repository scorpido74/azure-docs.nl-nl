---
title: 'Azure Data Studio (preview): verbinding maken en Synapse SQL opvragen'
description: Gebruik Azure Data Studio (preview) om verbinding te maken met en query's uit te Synapse SQL in azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 561ea1a4f3577e037708c3b090188c59bd3c4aad
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82187542"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio-preview"></a>Verbinding maken met Synapse SQL met Azure Data Studio (preview-versie)

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

U kunt [Azure Data Studio (preview)](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) gebruiken om verbinding te maken met en query's uit te Synapse SQL in azure Synapse Analytics. 

## <a name="connect"></a>Verbinding maken

Als u verbinding wilt maken met Synapse SQL, opent u Azure Data Studio en selecteert u **nieuwe verbinding**.

![Azure Data Studio openen](./media/get-started-azure-data-studio/1-start.png)

Kies **Microsoft SQL Server** als het **verbindings type**.

Voor de verbinding zijn de volgende para meters vereist:

* **Server:** Server in de vorm `<Azure Synapse workspace name>`-OnDemand.SQL.azuresynapse.net
* **Data Base:** Database naam

> [!NOTE]
> Als u **SQL on-demand wilt gebruiken (preview),** ziet de URL er als volgt uit:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Als u de **SQL-groep** wilt gebruiken, moet de URL er als volgt uitzien:
>
> - `<Azure Synapse workspace name>`. sql.azuresynapse.net

Kies **Windows-verificatie**, **Azure Active Directory**of **SQL-aanmelding** als **verificatie type**.

Als u **SQL-aanmelding** als verificatie type wilt gebruiken, voegt u de para meters voor gebruikers naam/wacht woord toe:

* **Gebruiker:** Server gebruiker in het formulier`<User>`
* **Wacht woord:** Wacht woord dat is gekoppeld aan de gebruiker

Als u Azure Active Directory wilt gebruiken, moet u het gewenste verificatie type kiezen.

![Microsoft Azure Active Directory-verificatie](./media/get-started-azure-data-studio/3-aad-auth.png)

Op de volgende scherm afbeelding ziet u de **verbindings Details** voor **Windows-verificatie**:

![Windows-verificatie](./media/get-started-azure-data-studio/3-windows-auth.png)

In de volgende scherm afbeelding ziet u de **verbindings Details** met **SQL-aanmelding**:

![SQL-aanmelding](./media/get-started-azure-data-studio/2-database-details.png)

Na een geslaagde aanmelding ziet u een dash board als volgt ![: dash board](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Query’s uitvoeren

Nadat de verbinding tot stand is gebracht, kunt u een query uitvoeren op Synapse SQL met ondersteunde [Transact-SQL (T-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) -instructies voor het exemplaar. Selecteer **nieuwe query** in de weer gave dash board om aan de slag te gaan.

![Nieuwe query](./media/get-started-azure-data-studio/5-new-query.png)

U kunt bijvoorbeeld de volgende Transact-SQL-instructie gebruiken om een [query](query-parquet-files.md) uit te voeren op Parquet-bestanden met behulp van SQL op aanvraag:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Volgende stappen 
Verken andere manieren om verbinding te maken met Synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Sqlcmd](get-started-connect-sqlcmd.md)
 
