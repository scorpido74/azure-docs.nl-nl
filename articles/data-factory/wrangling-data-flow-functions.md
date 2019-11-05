---
title: Wrangling voor gegevens stromen in Azure Data Factory | Microsoft Docs
description: Een overzicht van de beschik bare wrangling-functies voor gegevens stromen in Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 73ad0b39e119a9ef33fa1fa595ddcc47f61ae163
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518552"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Transformatie functies in wrangling-gegevens stroom

Met Wrangling data flow in Azure Data Factory kunt u met code gratis flexibele gegevens voorbereiding en Wrangling op Cloud schaal uitvoeren. De Wrangling-gegevens stroom is geïntegreerd met [Power query online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) en maakt Power query M-functies beschikbaar voor gegevens Wrangling via Spark-uitvoering. 

Momenteel niet alle Power Query M-functies worden ondersteund voor gegevens wrangling ondanks dat deze beschikbaar zijn tijdens het ontwerpen. Tijdens het maken van uw wrangling-gegevens stromen wordt u met het volgende fout bericht gevraagd als een functie niet wordt ondersteund:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Hieronder vindt u een lijst met ondersteunde functies van Power Query M.

## <a name="column-management"></a>Kolom beheer

* Selectie: [Table. select columns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Verwijderen: [Table. RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Naam wijzigen: [Table. RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [Table. PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Table. TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Volg orde wijzigen: [Table. ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Rijen filteren

Gebruik M Function [Table. SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) om te filteren op de volgende voor waarden:

* Gelijkheid en ongelijkheid
* Cijfers, tekst en datum vergelijkingen (maar geen DateTime)
* Numerieke gegevens, zoals [Number. IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[oneven](https://docs.microsoft.com/powerquery-m/number-iseven)
* Tekst insluiting met behulp van [tekst. contains](https://docs.microsoft.com/powerquery-m/text-contains), [Text. StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)of [Text. EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Datumbereiken, inclusief alle ' IsIn' [date-functies](https://docs.microsoft.com/powerquery-m/date-functions)) 
* Combi Naties van deze met en, of of geen voor waarden

## <a name="adding-and-transforming-columns"></a>Kolommen toevoegen en transformeren

Met de volgende functies worden kolommen toegevoegd of omgezet: [Table. AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Table. TransformColumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [Table. ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table. DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Hieronder vindt u de ondersteunde transformatie functies.

* Numeriek reken kundig
* Tekst samenvoeging
* Date andTime aritmetische (reken kundige operators, [datum. addDays](https://docs.microsoft.com/powerquery-m/date-adddays), [date. AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [date. AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters), [date. AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [date. AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* De duur kan worden gebruikt voor de reken kundige datum en tijd, maar moet worden omgezet in een ander type voordat naar een Sink (reken kundige Opera Tors, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [duur. dagen](https://docs.microsoft.com/powerquery-m/duration-days), [duur. uren](https://docs.microsoft.com/powerquery-m/duration-hours), [duur. minuten](https://docs.microsoft.com/powerquery-m/duration-minutes), [ Duration. seconden](https://docs.microsoft.com/powerquery-m/duration-seconds), [duration. TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays), [duration. TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours), [duration. TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes), [duration. TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* De meeste standaard-, weten schappelijke en trigonometrische numerieke functies (alle functies onder [bewerkingen](https://docs.microsoft.com/powerquery-m/number-functions#operations), [afronding](https://docs.microsoft.com/powerquery-m/number-functions#rounding)en [trigonometrie](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) , *behalve* aantal. faculteit, aantal permutaties en aantal combi Naties)
* Vervangen (vervanging[. ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext), vervanging [. ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [Text. Replace](https://docs.microsoft.com/powerquery-m/text-replace), [Text. Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* Positie tekst extractie ([tekst. PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [tekst. lengte](https://docs.microsoft.com/powerquery-m/text-length), [tekst. begin](https://docs.microsoft.com/powerquery-m/text-start), [tekst. End](https://docs.microsoft.com/powerquery-m/text-end), [tekst. Midden](https://docs.microsoft.com/powerquery-m/text-middle), [tekst. ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text. RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Basis tekst opmaak ([tekst. Lower](https://docs.microsoft.com/powerquery-m/text-lower), [tekst. Upper](https://docs.microsoft.com/powerquery-m/text-upper), [text. Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[begin](https://docs.microsoft.com/powerquery-m/text-trimstart)/[End](https://docs.microsoft.com/powerquery-m/text-trimend), [Text. PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[End](https://docs.microsoft.com/powerquery-m/text-padend), [Text. reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* Datum-en tijd functies ([datum. dag](https://docs.microsoft.com/powerquery-m/date-day), [datum. maand](https://docs.microsoft.com/powerquery-m/date-month), [datum. jaar](https://docs.microsoft.com/powerquery-m/date-year) [. uur](https://docs.microsoft.com/powerquery-m/time-hour), [tijd. minuut](https://docs.microsoft.com/powerquery-m/time-minute), [tijd. seconde](https://docs.microsoft.com/powerquery-m/time-second), [datum. DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [datum. DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [date. DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Als-expressies (maar vertakkingen moeten overeenkomende typen hebben)
* Rij filters als logische kolom
* De constanten getal, tekst, logische, datum en datum/tijd

<a name="mergingjoining-tables"></a>Tabellen samen voegen/koppelen
----------------------
* Power Query wordt een geneste koppeling gegenereerd (Table. NestedJoin; gebruikers kunnen ook hand matig [Table. AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)) schrijven.
    Gebruikers moeten vervolgens de geneste samenvoegings kolom uitvouwen tot een niet-geneste koppeling (Table. ExpandTableColumn, die niet wordt ondersteund in een andere context).
* De functie [Table. join](https://docs.microsoft.com/powerquery-m/table-join) kan rechtstreeks worden geschreven om te voor komen dat er een extra uitbreidings stap nodig is, maar de gebruiker moet er zeker van zijn dat er geen dubbele kolom namen zijn in de gekoppelde tabellen
* Ondersteunde typen joins: [inner](https://docs.microsoft.com/powerquery-m/joinkind-inner), [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Beide [waarden. equals](https://docs.microsoft.com/powerquery-m/value-equals) en [Value. NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) worden ondersteund als Key gelijkheids vergelijkingen

## <a name="group-by"></a>Groeperen op

Gebruik [Table. Group](https://docs.microsoft.com/powerquery-m/table-group) om waarden samen te voegen.
* Moet worden gebruikt met een statistische functie
* Ondersteunde aggregatie functies: [tabel. RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [lijst. Sum](https://docs.microsoft.com/powerquery-m/list-sum), [lijst. Count](https://docs.microsoft.com/powerquery-m/list-count), [List. Average](https://docs.microsoft.com/powerquery-m/list-average), [List. min](https://docs.microsoft.com/powerquery-m/list-min), [List. Max](https://docs.microsoft.com/powerquery-m/list-max), [List. StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [List. First](https://docs.microsoft.com/powerquery-m/list-first), [List. last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Sorter

Gebruik [Table. sort](https://docs.microsoft.com/powerquery-m/table-sort) om waarden te sorteren.

## <a name="reducing-rows"></a>Rijen beperken

Hoog behoud en verwijderen, bereik (overeenkomende M-functies, alleen ondersteunings tellingen, niet-voor waarden): [tabel. voors](https://docs.microsoft.com/powerquery-m/table-firstn)telling, tabel. [overs Laan](https://docs.microsoft.com/powerquery-m/table-skip), [tabel.](https://docs.microsoft.com/powerquery-m/table-removefirstn)reMinNe, [tabel. bereik](https://docs.microsoft.com/powerquery-m/table-range), [tabel.](https://docs.microsoft.com/powerquery-m/table-minn), [tabel. MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functionality"></a>Bekende niet-ondersteunde functionaliteit

Hieronder vindt u functies die niet worden ondersteund. Deze lijst is niet volledig en kan worden gewijzigd.
* Kolommen samen voegen (kan worden bereikt met AddColumn)
* Kolom splitsen
* Query's toevoegen
* ' Gebruik de eerste rij als kopteksten ' en ' headers gebruiken als eerste rij '

## <a name="next-steps"></a>Volgende stappen
