---
title: Verbinding maken met Sqlcmd
description: Gebruik het opdracht regel hulpprogramma Sqlcmd om verbinding te maken met en een query uit te zoeken op een Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e4b432e0be0cdded5089965b9d272aa82e31bd36
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685748"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Verbinding maken met SQL Data Warehouse met sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Gebruik het opdracht regel hulpprogramma [Sqlcmd][sqlcmd] om verbinding te maken met en een query uit te zoeken op een Azure SQL Data Warehouse.  

## <a name="1-connect"></a>1. verbinding maken
U gaat als volgt aan de slag met [sqlcmd][sqlcmd]: open de opdrachtprompt en voer **sqlcmd** in, gevolgd door de verbindingstekenreeks voor uw SQL Data Warehouse-database. De verbindingstekenreeks moet de volgende parameters bevatten:

* **Server (-S):** server in de notatie `<`servernaam`>`.database.windows.net
* **Database (-d):** databasenaam.
* **Id's tussen aanhalingstekens inschakelen (-I):** id's tussen aanhalingstekens moeten zijn ingeschakeld om verbinding te kunnen maken met een exemplaar van SQL Data Warehouse.

Als u gebruik wilt maken van SQL Server-verificatie, moet u de gebruikersnaam- en wachtwoordparameters toevoegen:

* **Gebruiker (-U):** servergebruiker in de notatie `<`gebruiker`>`
* **Wachtwoord (-P):** wachtwoord dat is gekoppeld aan de gebruiker.

Een voorbeeld: uw verbindingstekenreeks kan er als volgt uitzien:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Als u geïntegreerde verificatie van Azure Active Directory wilt gebruiken, moet u de Azure Active Directory-parameters toevoegen:

* **Azure Active Directory Authentication (-G):** Azure Active Directory gebruiken voor verificatie

Een voorbeeld: uw verbindingstekenreeks kan er als volgt uitzien:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> U moet [Azure Active Directory Authentication inschakelen](sql-data-warehouse-authentication.md) om te verifiëren met Active Directory.
> 
> 

## <a name="2-query"></a>2. query
Wanneer verbinding is gemaakt, kunt u elke ondersteunde Transact-SQL-instructie voor het exemplaar uitvoeren.  In dit voorbeeld worden query's in de interactieve modus verzonden.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

In de volgende voorbeelden ziet u hoe u uw query's in de batchmodus uitvoert met behulp van de optie -Q of door uw SQL naar sqlcmd te sluizen.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Volgende stappen
Zie [Sqlcmd-documentatie][sqlcmd] voor meer informatie over de beschik bare opties in Sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
