---
title: Herhaalbare kopie in Azure Data Factory
description: Meer informatie over het voorkomen van duplicaten, ook al wordt een segment dat gegevens kopieert meerdere tijd uitgevoerd.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7188cb5774699fc6e31fc3b8c78068bb33c6f552
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281143"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Herhaalbare kopie in Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Herhaalbaar lezen uit relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevensopslag rekening met herhaalbaarheid om onbedoelde resultaten te voorkomen. In Azure Data Factory u een segment handmatig opnieuw uitvoeren. U ook het beleid voor een wijziging opnieuw configureren, zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment in beide richtingen wordt opnieuw uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht hoe vaak een segment wordt uitgevoerd.  
 
> [!NOTE]
> De volgende voorbeelden zijn voor Azure SQL, maar zijn van toepassing op elk gegevensarchief dat rechthoekige gegevenssets ondersteunt. Mogelijk moet u het **type** bron en de **eigenschap query** (bijvoorbeeld query in plaats van sqlReaderQuery) aanpassen voor het gegevensarchief.   

Meestal, bij het lezen van relationele winkels, wilt u alleen de gegevens die overeenkomen met dat segment te lezen. Een manier om dit te doen is door gebruik te maken van de WindowsStart- en WindowEnd-systeemvariabelen die beschikbaar zijn in Azure Data Factory. Lees meer over de variabelen en functies in Azure Data Factory hier in het artikel [Azure Data Factory - Functions and System Variables.](data-factory-functions-variables.md) Voorbeeld: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Deze query leest gegevens die in het segmentduurbereik (WindowStart-> WindowEnd) vallen uit de tabel MyTable. Het opnieuw uitvoeren van dit segment zou er ook altijd voor zorgen dat dezelfde gegevens worden gelezen. 

In andere gevallen u de hele tabel lezen en de sqlReaderQuery als volgt definiëren:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Herhaalbare schrijven naar SqlSink
Wanneer u gegevens kopieert naar **Azure SQL/SQL Server** vanuit andere gegevensarchieven, moet u rekening houden met herhaalbaarheid om onbedoelde resultaten te voorkomen. 

Wanneer u gegevens kopieert naar Azure SQL/SQL Server Database, voegt de kopieeractiviteit gegevens standaard toe aan de sinktabel. U kopieert bijvoorbeeld gegevens uit een CSV-bestand (door komma's gescheiden waarden) met twee records naar de volgende tabel in een Azure SQL/SQL Server-database. Wanneer een segment wordt uitgevoerd, worden de twee records gekopieerd naar de SQL-tabel. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Stel dat u fouten in het bronbestand hebt gevonden en de hoeveelheid Down Tube hebt bijgewerkt van 2 naar 4. Als u het gegevenssegment voor die periode handmatig opnieuw uitvoert, vindt u twee nieuwe records die zijn toegevoegd aan Azure SQL/SQL Server Database. In dit voorbeeld wordt ervan uitgegaan dat geen van de kolommen in de tabel de primaire sleutelbeperking heeft.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Om dit gedrag te voorkomen, moet u upsert semantiek opgeven met behulp van een van de volgende twee mechanismen:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mechanisme 1: sqlWriterCleanupScript gebruiken
U de eigenschap **sqlWriterCleanupScript** gebruiken om gegevens uit de gootsteentabel op te schonen voordat u de gegevens invoegt wanneer een segment wordt uitgevoerd. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Wanneer een segment wordt uitgevoerd, wordt het opschoningsscript als eerste uitgevoerd om gegevens die overeenkomen met het segment uit de SQL-tabel te verwijderen. De kopieeractiviteit voegt vervolgens gegevens in de SQL-tabel in. Als het segment opnieuw wordt uitgevoerd, wordt de hoeveelheid naar wens bijgewerkt.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Stel dat de record Flat Washer wordt verwijderd uit de oorspronkelijke csv. Als u het segment vervolgens opnieuw uitvoert, wordt het volgende resultaat opgeleverd: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

Met de kopieeractiviteit is het opschoningsscript uitgevoerd om de bijbehorende gegevens voor dat segment te verwijderen. Vervolgens las het de input van de csv (die vervolgens bevatte slechts een record) en ingevoegd in de tabel. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mechanisme 2: sliceIdentifierColumnName gebruiken
> [!IMPORTANT]
> Momenteel wordt sliceIdentifierColumnName niet ondersteund voor Azure SQL Data Warehouse. 

Het tweede mechanisme om herhaalbaarheid te bereiken is door een speciale kolom (sliceIdentifierColumnName) in de doeltabel te hebben. Deze kolom wordt gebruikt door Azure Data Factory om ervoor te zorgen dat de bron en bestemming gesynchroniseerd blijven. Deze aanpak werkt wanneer er flexibiliteit is bij het wijzigen of definiëren van het sql-tabelschema voor doel. 

Deze kolom wordt door Azure Data Factory gebruikt voor herhaalbaarheidsdoeleinden en in het proces brengt Azure Data Factory geen schemawijzigingen aan in de tabel. Manier om deze aanpak te gebruiken:

1. Definieer een kolom met binaire tekst **(32)** in de SQL-tabel van de bestemming. Er mogen geen beperkingen zijn voor deze kolom. Laten we deze kolom voor dit voorbeeld benoemen als AdfSliceIdentifier.


    Brontabel:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Bestemmingstabel: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. Gebruik het in de kopieeractiviteit als volgt:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory vult deze kolom in als de behoefte om ervoor te zorgen dat de bron en bestemming gesynchroniseerd blijven. De waarden van deze kolom mogen niet buiten deze context worden gebruikt. 

Net als bij mechanisme 1 ruimt Copy Activity automatisch de gegevens op voor het opgegeven segment uit de SQL-tabel van bestemming. Vervolgens worden gegevens van de bron in de doeltabel ingevoegd. 

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende connectorartikelen die voor volledige JSON-voorbeelden: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL-gegevensmagazijn](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
