---
title: Gegevens uit een CSV-bestand laden in een Data Base (BCP)
description: Maak bij een kleine gegevensomvang gebruik van bcp om gegevens in de Azure SQL-database te importeren.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: b0df3d588f1d9b0a50c3ea7a583b0704e7e85c39
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73827491"
---
# <a name="load-data-from-csv-into-azure-sql-database-flat-files"></a>Gegevens vanuit een CSV-bestand laden in een Azure SQL-database (platte bestanden)

U kunt het opdrachtregelhulpprogramma bcp gebruiken om gegevens uit een csv-bestand te importeren in Azure SQL Database.

## <a name="before-you-begin"></a>Voordat u begint

### <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

* Een Azure SQL Database-Server en-data base
* Het opdrachtregelprogramma BCP (moet zijn geïnstalleerd)
* Het opdrachtregelprogramma SQLCMD (moet zijn geïnstalleerd)

U kunt de opdrachtregelprogramma's BCP en SQLCMD downloaden van het [Microsoft Downloadcentrum][Microsoft Download Center].

### <a name="data-in-ascii-or-utf-16-format"></a>Gegevens in ASCII- of UTF-16-indeling

Als u deze zelfstudie wilt uitvoeren met uw eigen gegevens, moeten deze zijn gecodeerd in de ASCII- of UTF-16-indeling, omdat de indeling UTF-8 niet wordt ondersteund in BCP. 

## <a name="1-create-a-destination-table"></a>1. Maak een doel tabel

Definieer een tabel in SQL Database als de doeltabel. De kolommen in de tabel moeten overeenkomen met de gegevens in elke rij van het gegevensbestand.

Open een opdrachtprompt en voer de volgende opdracht uit met sqlcmd.exe om een tabel te maken:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```


## <a name="2-create-a-source-data-file"></a>2. een brongegevens bestand maken

Open Kladblok, kopieer de volgende regels met gegevens naar een nieuw tekstbestand en sla dit bestand op in de lokale tijdelijke map C:\Temp\DimDate2.txt. Dit zijn gegevens in ASCII-indeling.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(Optioneel) Open een opdrachtprompt en voer de volgende opdracht uit om uw eigen gegevens uit een SQL Server-database te exporteren. Vervang TableName, ServerName, DatabaseName, Username en Password door uw eigen waarden.

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t , 
```

## <a name="3-load-the-data"></a>3. de gegevens laden

Open een opdrachtprompt en voer de volgende opdracht uit om de gegevens te laden. Vervang de waarden voor ServerName, DatabaseName, Username en Password door uw eigen waarden.

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

Gebruik deze opdracht om te controleren of de gegevens correct zijn geladen

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

De resultaten horen er als volgt uit te zien:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="next-steps"></a>Volgende stappen

Zie [SQL Server-database migreren](sql-database-single-database-migrate.md) om een SQL-serverdatabase te migreren.

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
