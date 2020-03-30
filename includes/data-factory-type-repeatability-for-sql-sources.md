---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 24bb7a1fcb1569922fb34034fb3c0d003cdd7061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176502"
---
## <a name="repeatability-during-copy"></a>Herhaalbaarheid tijdens kopiëren
Bij het kopiëren van gegevens naar Azure SQL/SQL Server vanuit andere gegevensarchieven moet men rekening houden met herhaalbaarheid om onbedoelde resultaten te voorkomen. 

Wanneer u gegevens kopieert naar Azure SQL/SQL Server Database, wordt de gegevensset standaard standaard toegevoegd aan de sinktabel. Wanneer u bijvoorbeeld gegevens kopieert van een CSV-bestandsbron (comma separate values data) met twee records naar Azure SQL/SQL Server Database, ziet de tabel er zo uit:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Stel dat u fouten in het bronbestand hebt gevonden en de hoeveelheid Down Tube hebt bijgewerkt van 2 naar 4 in het bronbestand. Als u het gegevenssegment voor die periode opnieuw uitvoert, vindt u twee nieuwe records die zijn toegevoegd aan Azure SQL/SQL Server Database. In de onderstaande gaat ervan uitgegaan dat geen van de kolommen in de tabel de primaire sleutelbeperking heeft.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Om dit te voorkomen, moet u upsert semantiek opgeven door gebruik te maken van een van de onderstaande 2 mechanismen hieronder vermeld.

> [!NOTE]
> Een segment kan automatisch opnieuw worden uitgevoerd in Azure Data Factory volgens het opgegeven beleid voor opnieuw proberen.
> 
> 

### <a name="mechanism-1"></a>Mechanisme 1
U **sqlWriterCleanupScript-eigenschap** gebruiken om eerst opschoningsactie uit te voeren wanneer een segment wordt uitgevoerd. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Het opschoningsscript wordt eerst uitgevoerd tijdens het kopiëren voor een bepaald segment dat de gegevens uit de SQL-tabel zou verwijderen die overeenkomt met dat segment. De activiteit voegt de gegevens vervolgens in de SQL-tabel. 

Als het segment nu opnieuw wordt uitgevoerd, dan zult u merken dat de hoeveelheid naar wens wordt bijgewerkt.

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
Er hoefde niets nieuws gedaan te worden. Met de kopieeractiviteit is het opschoningsscript uitgevoerd om de bijbehorende gegevens voor dat segment te verwijderen. Vervolgens las het de input van de csv (die vervolgens bevatte slechts 1 record) en ingevoegd in de tabel. 

### <a name="mechanism-2"></a>Mechanisme 2
> [!IMPORTANT]
> sliceIdentifierColumnName wordt op dit moment niet ondersteund voor Azure SQL Data Warehouse. 

Een ander mechanisme om herhaalbaarheid te bereiken is door een speciale kolom **(sliceIdentifierColumnName)** in de doeltabel te hebben. Deze kolom wordt gebruikt door Azure Data Factory om ervoor te zorgen dat de bron en bestemming gesynchroniseerd blijven. Deze aanpak werkt wanneer er flexibiliteit is bij het wijzigen of definiëren van het sql-tabelschema voor doel. 

Deze kolom wordt door Azure Data Factory gebruikt voor herhaalbaarheidsdoeleinden en in het proces zal Azure Data Factory geen schemawijzigingen aanbrengen in de tabel. Manier om deze aanpak te gebruiken:

1. Definieer een kolom met binaire tekst (32) in de SQL-tabel van de bestemming. Er mogen geen beperkingen zijn voor deze kolom. Laten we deze kolom voor dit voorbeeld 'ColumnForADFuseOnly' noemen.
2. Gebruik het in de kopieeractiviteit als volgt:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory vult deze kolom in als deze nodig is om ervoor te zorgen dat de bron en bestemming gesynchroniseerd blijven. De waarden van deze kolom mogen niet buiten deze context door de gebruiker worden gebruikt. 

Net als bij mechanisme 1 zal Kopieeractiviteit automatisch eerst de gegevens voor het opgegeven segment opschonen vanuit de SQL-tabel van bestemming en vervolgens de kopieeractiviteit normaal uitvoeren om de gegevens van bron naar bestemming voor dat segment in te voegen. 

