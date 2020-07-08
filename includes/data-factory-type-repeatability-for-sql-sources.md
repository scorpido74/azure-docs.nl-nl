---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 24bb7a1fcb1569922fb34034fb3c0d003cdd7061
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67176502"
---
## <a name="repeatability-during-copy"></a>Herhaal baarheid tijdens kopiëren
Bij het kopiëren van gegevens naar Azure SQL/SQL Server van andere gegevens archieven, moeten er een herhaling worden gehouden om onbedoelde resultaten te voor komen. 

Bij het kopiëren van gegevens naar Azure SQL/SQL Server Data Base, wordt standaard de gegevensset automatisch aan de Sink-tabel toegevoegd. Als u bijvoorbeeld gegevens kopieert uit een CSV-bestand (Comma Separated Values) met twee records naar Azure SQL/SQL Server Data Base, ziet de tabel er als volgt uit:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Stel dat u fouten hebt gevonden in het bron bestand en de hoeveelheid omlaag-buis hebt bijgewerkt van 2 naar 4 in het bron bestand. Als u het gegevens segment voor die periode opnieuw uitvoert, vindt u twee nieuwe records die zijn toegevoegd aan Azure SQL/SQL Server Data Base. In de onderstaande stappen wordt ervan uitgegaan dat geen van de kolommen in de tabel de beperking Primary Key heeft.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Om dit te voor komen, moet u UPSERT-semantiek opgeven door gebruik te maken van een van de hieronder vermelde twee mechanismen.

> [!NOTE]
> Een segment kan automatisch opnieuw worden uitgevoerd in Azure Data Factory volgens het opgegeven beleid voor opnieuw proberen.
> 
> 

### <a name="mechanism-1"></a>Mechanisme 1
U kunt gebruikmaken van de **sqlWriterCleanupScript** -eigenschap om eerst opschoon acties uit te voeren wanneer een segment wordt uitgevoerd. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Het opschoon script wordt eerst uitgevoerd tijdens het kopiëren van een opgegeven segment, waardoor de gegevens uit de SQL-tabel die overeenkomt met dat segment worden verwijderd. Met de activiteit worden de gegevens vervolgens in de SQL-tabel ingevoegd. 

Als het segment nu opnieuw wordt uitgevoerd, wordt het aantal naar wens bijgewerkt.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Stel dat de record voor een vlakke ring is verwijderd uit de oorspronkelijke CSV. Vervolgens wordt het volgende resultaat door het segment opnieuw uitgevoerd: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Er is niets nieuw. De Kopieer activiteit heeft het opschoon script uitgevoerd om de bijbehorende gegevens voor dat segment te verwijderen. Vervolgens wordt de invoer gelezen van het CSV (dat vervolgens slechts 1 record bevat) en ingevoegd in de tabel. 

### <a name="mechanism-2"></a>Mechanisme 2
> [!IMPORTANT]
> sliceIdentifierColumnName wordt op dit moment niet ondersteund voor Azure SQL Data Warehouse. 

Een ander mechanisme om Herhaal baarheid te bereiken, is door een toegewezen kolom (**sliceIdentifierColumnName**) te hebben in de doel tabel. Deze kolom wordt gebruikt door Azure Data Factory om ervoor te zorgen dat de bron en het doel gesynchroniseerd blijven. Deze aanpak werkt als er flexibiliteit is bij het wijzigen of definiëren van het doel-SQL-tabel schema. 

Deze kolom wordt gebruikt door Azure Data Factory voor Herhaal bare doel einden en in het proces Azure Data Factory worden geen schema wijzigingen in de tabel aangebracht. Manier om deze aanpak te gebruiken:

1. Een kolom van het type binary (32) in de doel-SQL-tabel definiëren. Er mogen zich geen beperkingen voor deze kolom voordoen. We noemen deze kolom als ' ColumnForADFuseOnly ' voor dit voor beeld.
2. Gebruik het in de Kopieer activiteit als volgt:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory vult deze kolom in aan de hand van de nood zaak om ervoor te zorgen dat de bron en de bestemming synchroon blijven. De waarden van deze kolom mogen niet buiten deze context worden gebruikt door de gebruiker. 

Net als bij mechanisme 1 worden met de Kopieer activiteit automatisch de gegevens voor het opgegeven segment opgeschoond vanuit de doel-SQL-tabel en wordt vervolgens de Kopieer activiteit normaal uitgevoerd om de gegevens van de bron naar het doel voor dat segment in te voegen. 

