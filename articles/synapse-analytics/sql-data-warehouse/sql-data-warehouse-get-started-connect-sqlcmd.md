---
title: Verbinding maken met sqlcmd
description: Gebruik sqlcmd command-line utility om verbinding te maken met en query een Synapse SQL pool.
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
ms.openlocfilehash: cfb1b7558f8d7675009a0ebc729cc1a560f03d12
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633380"
---
# <a name="connect-to-synapse-sql-pool-with-sqlcmd"></a>Verbinding maken met Synapse SQL-pool met sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Gebruik het hulpprogramma [sqlcmd][sqlcmd] command-line om verbinding te maken met een SQL-groep en op te vragen.  

## <a name="1-connect"></a>1.
Om aan de slag te gaan met [sqlcmd][sqlcmd], opent u de opdrachtprompt en voert **u sqlcmd** in, gevolgd door de verbindingstekenreeks voor uw SQL-pooldatabase. De verbindingstekenreeks moet de volgende parameters bevatten:

* **Server (-S):** server in de notatie `<`servernaam`>`.database.windows.net
* **Database (-d):** databasenaam.
* **Geciteerde id's inschakelen (-I):** Genoteerde id's moeten zijn ingeschakeld om verbinding te maken met een SQL-poolinstantie.

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

## <a name="2-query"></a>2. Query
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
Zie [sqlcmd-documentatie](https://msdn.microsoft.com/library/ms162773.aspx) voor meer informatie over de opties die beschikbaar zijn in sqlcmd.
