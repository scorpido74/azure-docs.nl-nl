---
title: Getouwtrek gegevensstroomtransformatiefuncties in Azure Data Factory
description: Een overzicht van de beschikbare gegevensstroomfuncties in Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e2517ec4a02a5d61fb3ce1d9ca9ffa2b5f4e8bf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287033"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Transformatie functies in getouwtrek data stroom

Met de gegevensstroom in Azure Data Factory u codevrije agile gegevensvoorbereiden en op cloudschaal uitdeuken. Wrangling data flow integreert met [Power Query Online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) en maakt Power Query M functies beschikbaar voor data wrangling via spark uitvoering. 

Momenteel worden niet alle Power Query M-functies ondersteund voor gegevensgetouwtrek, ondanks dat ze beschikbaar zijn tijdens het ontwerpen. Tijdens het bouwen van uw getouwtrekgegevensstromen wordt u gevraagd met het volgende foutbericht als een functie niet wordt ondersteund:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Hieronder vindt u een lijst met ondersteunde Power Query M-functies.

## <a name="column-management"></a>Kolombeheer

* Selectie: [Table.SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Verwijderen: [Tabel.Kolommen verwijderen](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Naam wijzigen: [Tabel.Naamkolommen](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [Tabel.Voorvoegselkolommen](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Tabel.TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Opnieuw rangschikken: [Tabel.Opnieuw ordenenKolommen](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Rijfiltering

Gebruik De functie Tabel [M.SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) om de volgende voorwaarden te filteren:

* Gelijkheid en ongelijkheid
* Numerieke, tekst- en datumvergelijkingen (maar niet DateTime)
* Numerieke informatie zoals [Number.IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[Odd](https://docs.microsoft.com/powerquery-m/number-iseven)
* Tekstinsluiting met [Text.Contains](https://docs.microsoft.com/powerquery-m/text-contains), [Text.StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)of [Text.EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Datumbereiken inclusief alle 'IsIn' [Date-functies](https://docs.microsoft.com/powerquery-m/date-functions)) 
* Combinaties hiervan met behulp van en, of, of niet voorwaarden

## <a name="adding-and-transforming-columns"></a>Kolommen toevoegen en transformeren

De volgende M-functies voegen kolommen toe of transformeren: [Tabel.AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Table.TransformColumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [Table.ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table.DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Hieronder staan de ondersteunde transformatiefuncties.

* Numeriek rekenen
* Tekstsamenvoeging
* Datum en tijd rekenkunde (Rekenkundige operatoren, [datum.adddays](https://docs.microsoft.com/powerquery-m/date-adddays), [date.addmonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [date.addquarters](https://docs.microsoft.com/powerquery-m/date-addquarters), [date.addweeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [date.addyears](https://docs.microsoft.com/powerquery-m/date-addyears))
* Duur kan worden gebruikt voor datum- en tijdrekenkunde, maar moet worden omgezet in een ander type voordat u naar een gootsteen wordt geschreven (rekenkundige operatoren, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [Duur.Dagen](https://docs.microsoft.com/powerquery-m/duration-days), [Duur.Uren](https://docs.microsoft.com/powerquery-m/duration-hours), [Duur.Minuten](https://docs.microsoft.com/powerquery-m/duration-minutes), [Duur.Seconden](https://docs.microsoft.com/powerquery-m/duration-seconds), [Duur.TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays), [Duration.TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours), [Duration.TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes), [Duration.TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* Meest standaard-, wetenschappelijke en trigonometrische numerieke functies (Alle functies onder [Bewerkingen,](https://docs.microsoft.com/powerquery-m/number-functions#operations) [Afronding](https://docs.microsoft.com/powerquery-m/number-functions#rounding)en [Trigonometrie,](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) *behalve* Getal.Factorial, Number.Permutations en Number.Combinations)
* Vervanging ([replacer.replacetext](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [replacer.replacevalue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [text.replace](https://docs.microsoft.com/powerquery-m/text-replace), [text.remove](https://docs.microsoft.com/powerquery-m/text-remove))
* Positionele tekstextractie ([Text.PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [Text.Length](https://docs.microsoft.com/powerquery-m/text-length), [Text.Start](https://docs.microsoft.com/powerquery-m/text-start), [Text.End](https://docs.microsoft.com/powerquery-m/text-end), [Text.Middle](https://docs.microsoft.com/powerquery-m/text-middle), [Text.Replacerange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text.RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Basistekstopmaak ([Text.Lower](https://docs.microsoft.com/powerquery-m/text-lower), [Text.Upper](https://docs.microsoft.com/powerquery-m/text-upper), [Text.Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[Start](https://docs.microsoft.com/powerquery-m/text-trimstart)/[End](https://docs.microsoft.com/powerquery-m/text-trimend), [Text.PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[End](https://docs.microsoft.com/powerquery-m/text-padend), [Text.Reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* Datum/tijdfuncties ([datum.dag](https://docs.microsoft.com/powerquery-m/date-day), [datum.maand](https://docs.microsoft.com/powerquery-m/date-month), [datum.jaartijd.uur](https://docs.microsoft.com/powerquery-m/date-year) [Time.Hour](https://docs.microsoft.com/powerquery-m/time-hour), [tijd.minuut](https://docs.microsoft.com/powerquery-m/time-minute), [tijd.seconde](https://docs.microsoft.com/powerquery-m/time-second), [datum.dagofweek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [datum.dagofyear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [datum.daysinmonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Als expressies (maar vertakkingen moeten overeenkomende typen hebben)
* Rijfilters als een logische kolom
* Constanten nummer, tekst, logische datum en datumtijd

<a name="mergingjoining-tables"></a>Tabellen samenvoegen/samenvoegen
----------------------
* Power Query genereert een geneste join (Table.NestedJoin; gebruikers kunnen ook handmatig [Table.AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)schrijven).
    Gebruikers moeten de geneste join-kolom vervolgens uitbreiden naar een niet-geneste join (Table.ExpandTableColumn, niet ondersteund in een andere context).
* De [M-functie Table.Join](https://docs.microsoft.com/powerquery-m/table-join) kan rechtstreeks worden geschreven om te voorkomen dat er een extra uitbreidingsstap nodig is, maar de gebruiker moet ervoor zorgen dat er geen dubbele kolomnamen tussen de samengevoegde tabellen staan
* Ondersteunde Join Kinds: [Inner](https://docs.microsoft.com/powerquery-m/joinkind-inner), [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Zowel [Value.Equals](https://docs.microsoft.com/powerquery-m/value-equals) als [Value.NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) worden ondersteund als key equality comparers

## <a name="group-by"></a>Groeperen op

[Tabel.groep gebruiken](https://docs.microsoft.com/powerquery-m/table-group) om waarden samen te voegen.
* Moet worden gebruikt met een aggregatiefunctie
* Ondersteunde aggregatiefuncties: [Table.RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [List.Sum](https://docs.microsoft.com/powerquery-m/list-sum), [List.count](https://docs.microsoft.com/powerquery-m/list-count), [List.Average](https://docs.microsoft.com/powerquery-m/list-average), [List.Min](https://docs.microsoft.com/powerquery-m/list-min), [List.Max](https://docs.microsoft.com/powerquery-m/list-max), [List.Standarddeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [List.First](https://docs.microsoft.com/powerquery-m/list-first), [List.Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Sorteren

[Tabel.Sorteren gebruiken](https://docs.microsoft.com/powerquery-m/table-sort) om waarden te sorteren.

## <a name="reducing-rows"></a>Rijen verkleinen

Top houden en verwijderen, Bereik behouden (overeenkomstige M-functies, alleen ondersteunende tellingen, geen voorwaarden: [Table.FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [Table.Skip](https://docs.microsoft.com/powerquery-m/table-skip), [Table.RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table.Range](https://docs.microsoft.com/powerquery-m/table-range), [Table.MinN](https://docs.microsoft.com/powerquery-m/table-minn), [Table.MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Bekende niet-ondersteunde functies

| Functie | Status |
| -- | -- |
| Table.PromoteHeaders | Wordt niet ondersteund. Hetzelfde resultaat kan worden bereikt door "Eerste rij als koptekst" in de gegevensset in te stellen. |
| Table.CombineColumns | Dit is een veelvoorkomend scenario dat niet direct wordt ondersteund, maar kan worden bereikt door een nieuwe kolom toe te voegen die twee gegeven kolommen samenvoegt.  Bijvoorbeeld Table.AddColumn(RemoveEmailColumn, "Name", elke [Voornaam] & " & [Achternaam]) |
| Table.TransformColumnTypes | Dit wordt in de meeste gevallen ondersteund. De volgende scenario's worden niet ondersteund: tekenreeks transformeren naar valutatype, tekenreeks transformeren naar tijdtype en tekenreeks transformeren naar type Percentage. |
| Table.NestedJoin | Gewoon het doen van een join zal resulteren in een validatie fout. De kolommen moeten worden uitgebreid om het te laten werken. |
| Table.Distinct | Dubbele rijen verwijderen wordt niet ondersteund. |
| Table.RemoveLastN | Onderste rijen verwijderen wordt niet ondersteund. |
| Table.RowCount | Niet ondersteund, maar kan worden bereikt met een kolom toevoegen met alle cellen leeg (voorwaarde kolom kan worden gebruikt) en vervolgens met behulp van groep door op die kolom. Table.Group wordt ondersteund. | 
| Foutafhandeling rijniveau | Foutafhandeling op rijniveau wordt momenteel niet ondersteund. Als u bijvoorbeeld niet-numerieke waarden uit een kolom wilt filteren, is een benadering het transformeren van de tekstkolom naar een getal. Elke cel die niet transformeert, bevindt zich in een foutstatus en moet worden gefilterd. Dit scenario is niet mogelijk in het getouwtrek data stroom. |
| Table.Transpose | Niet ondersteund |
| Table.Pivot | Niet ondersteund |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een getouwtrek gegevensstroom.](wrangling-data-flow-tutorial.md)