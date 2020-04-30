---
title: Verbinding maken met Synapse SQL met Sqlcmd
description: Gebruik het opdracht regel hulpprogramma Sqlcmd om verbinding te maken met SQL op aanvraag (preview) en SQL-groep.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 7ccb30cdd77e511572147a0b0f7287f931a45df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82186835"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Verbinding maken met Synapse SQL met Sqlcmd

> [!div class="op_single_selector"]
>
> * [Azure Data Studio (preview-versie)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

U kunt het opdracht regel hulpprogramma [Sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) gebruiken om verbinding te maken met SQL op aanvraag (preview) en SQL-groep in Synapse SQL.  

## <a name="1-connect"></a>1. verbinding maken
Open de opdracht prompt en voer **Sqlcmd** in, gevolgd door de Connection String voor uw Synapse-SQL database om aan de slag te gaan met [Sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). De verbindingstekenreeks moet de volgende parameters bevatten:

* **Server (-S):** server in de notatie `<`servernaam`>`.database.windows.net
* **Data Base (-d):** Database naam
* **Id's van aanhalings tekens inschakelen (-I):** Id's tussen aanhalings tekens moeten zijn ingeschakeld om verbinding te maken met een Synapse SQL-exemplaar

Als u SQL Server-verificatie wilt gebruiken, moet u de gebruikers naam-en wachtwoord parameters toevoegen:

* **Gebruiker (-U):** servergebruiker in de notatie `<`gebruiker`>`
* **Wacht woord (-P):** Wacht woord dat is gekoppeld aan de gebruiker

Uw connection string kan er als volgt uitzien:

**SQL on-demand**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**SQL-pool**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Als u geïntegreerde verificatie van Azure Active Directory wilt gebruiken, moet u de Azure Active Directory-parameters toevoegen:

* **Azure Active Directory Authentication (-G):** Azure Active Directory gebruiken voor verificatie

Uw connection string kan er als volgt uitzien:

**SQL on-demand**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**SQL-pool**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> U moet [Azure Active Directory Authentication inschakelen](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) om te verifiëren met Active Directory.

## <a name="2-query"></a>2. query

### <a name="use-sql-pool"></a>SQL-groep gebruiken

Na de verbinding kunt u alle ondersteunde [Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) -instructies (T-SQL) voor het exemplaar uitgeven. In dit voor beeld worden query's verzonden in de interactieve modus:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

In de volgende voor beelden voor SQL-groep ziet u hoe u query's in batch-modus kunt uitvoeren met de optie-Q of door uw SQL to Sqlcmd:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-sql-on-demand"></a>SQL on-demand gebruiken

Nadat u verbinding hebt gemaakt, kunt u alle ondersteunde [Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL)-instructies voor het exemplaar uitgeven.  In het volgende voor beeld worden query's verzonden in de interactieve modus:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

De volgende voor beelden laten zien hoe u query's in batch modus kunt uitvoeren met de optie-Q of door uw SQL to Sqlcmd:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Volgende stappen

Zie de [Sqlcmd-documentatie](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)voor meer informatie over de Sqlcmd-opties.
