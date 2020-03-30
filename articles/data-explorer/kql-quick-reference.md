---
title: KQL-snelreferentie
description: Een lijst met nuttige KQL-functies en hun definities met syntaxisvoorbeelden.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: e7196ccccb1203ce56b2e53fa358d091374cd3f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139076"
---
# <a name="kql-quick-reference"></a>KQL-snelreferentie

In dit artikel ziet u een lijst met functies en hun beschrijvingen om u op weg te helpen met kusto-querytaal.

| Operator/functie                               | Beschrijving                           | Syntaxis                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filter/zoeken/voorwaarde**                      |**_Relevante gegevens zoeken door te filteren of te zoeken_** |                      |
| [Waar](/azure/kusto/query/whereoperator)                      | Filters op een specifiek predicaat           | `T | where Predicate`                         |
| [waar contains/has](/azure/kusto/query/whereoperator)        | `Contains`: Zoekt naar een substring match <br> `Has`: Zoekt naar een specifiek woord (betere prestaties)  | `T | where col1 contains/has "[search term]"`|
| [Zoek](/azure/kusto/query/searchoperator)                    | Hiermee zoekt u alle kolommen in de tabel naar de waarde | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [Nemen](/azure/kusto/query/takeoperator)                        | Geeft als resultaat het opgegeven aantal records. Een query testen<br>**_Opmerking_** `_take`: `_limit`_ en _ zijn synoniemen. | `T | take NumberOfRows` |
| [Geval](/azure/kusto/query/casefunction)                        | Hiermee voegt u een voorwaardeverklaring toe, vergelijkbaar met als/dan/elseif in andere systemen. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](/azure/kusto/query/distinctoperator)                | Produceert een tabel met de afzonderlijke combinatie van de meegeleverde kolommen van de invoertabel | `distinct [ColumnName], [ColumnName]` |
| **Datum/tijd**                                   |**_Bewerkingen die datum- en tijdfuncties gebruiken_**               |                          |
|[Geleden](/azure/kusto/query/agofunction)                           | Geeft als resultaat de tijdverschuiving ten opzichte van de tijd die de query uitvoert. Bijvoorbeeld, `ago(1h)` is een uur voor de huidige klok lezen. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | Retourneert gegevens in [verschillende datumnotaties](/azure/kusto/query/format-datetimefunction#supported-formats). | `format_datetime(datetime , format)` |
| [Bin](/azure/kusto/query/binfunction)                          | Hiermee worden alle waarden in een tijdsbestek rondt en worden deze groepen | `bin(value,roundTo)` |
| **Kolommen maken/verwijderen**                   |**_Kolommen in een tabel toevoegen of verwijderen_** |                                                    |
| [Afdrukken](/azure/kusto/query/printoperator)                      | Hiermee wordt één rij met een of meer scalaire expressies uitgevoerd | `print [ColumnName =] ScalarExpression [',' ...]` |
| [Project](/azure/kusto/query/projectoperator)                  | Selecteert de kolommen die u wilt opnemen in de opgegeven volgorde | `T | project ColumnName [= Expression] [, ...]` <br> of <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](/azure/kusto/query/projectawayoperator)         | Selecteert de kolommen die u wilt uitsluiten van de uitvoer | `T | project-away ColumnNameOrPattern [, ...]` |
| [Uitbreiden](/azure/kusto/query/extendoperator)                    | Hiermee maakt u een berekende kolom en voegt deze toe aan de resultaatset | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Gegevensset sorteren en samenvoegen**                 |**_De gegevens herstructureren door ze op zinvolle manieren te sorteren of te groeperen_**|                  |
| [Sorteren](/azure/kusto/query/sortoperator)                        | Hiermee sorteert u de rijen van de invoertabel op een of meer kolommen in oplopende of aflopende volgorde | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [top](/azure/kusto/query/topoperator)                          | Geeft als resultaat de eerste N-rijen van de gegevensset wanneer de gegevensset wordt gesorteerd met`by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [Samenvatten](/azure/kusto/query/summarizeoperator)              | Groepeert de rijen `by` op basis van de groepskolommen en berekent aggregaties over elke groep | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [Tellen](/azure/kusto/query/countoperator)                       | Telt records in de invoertabel (bijvoorbeeld T)<br>Deze operator is steno voor`summarize count() `| `T | count` |
| [join](/azure/kusto/query/joinoperator)                        | Hiermee worden de rijen van twee tabellen samengevoegd tot een nieuwe tabel door de waarden van de opgegeven kolom(en) van elke tabel af te voegen. Ondersteunt een volledig scala `flouter`aan `inner` `innerunique`jointypes: , `rightanti` `rightantisemi`, `rightouter`, `leftanti` `leftantisemi` `leftouter`, `leftsemi`, , , ,`rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [Unie](/azure/kusto/query/unionoperator)                      | Neemt twee of meer tabellen en retourneert al hun rijen | `[T1] | union [T2], [T3], …` |
| [Bereik](/azure/kusto/query/rangeoperator)                      | Hiermee genereert u een tabel met een rekenkundige reeks waarden | `range columnName from start to stop step step` |
| **Gegevens opmaken**                                 | **_De gegevens op een nuttige manier herstructureren naar uitvoer_** | |
| [Lookup](/azure/kusto/query/lookupoperator)                    | Breidt de kolommen van een feitentabel uit met waarden die worden opgezocht in een dimensietabel | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-uit te breiden](/azure/kusto/query/mvexpandoperator)               | Dynamische arrays omzetten in rijen (uitbreiding met meerdere waarden) | `T | mv-expand Column` |
| [parse](/azure/kusto/query/parseoperator)                      | Evalueert een tekenreeksexpressie en ontsluit de waarde ervan in een of meer berekende kolommen. Gebruiken voor het structureren van ongestructureerde gegevens. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](/azure/kusto/query/make-seriesoperator)          | Hiermee maakt u reeksen opgegeven geaggregeerde waarden langs een opgegeven as | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [Laat](/azure/kusto/query/letstatement)                         | Bindt een naam aan expressies die kunnen verwijzen naar de gebonden waarde. Waarden kunnen lambda-expressies zijn om ad-hocfuncties te maken als onderdeel van de query. Met `let` deze neigen expressies over tabellen waarvan de resultaten er uitzien als een nieuwe tabel. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Algemeen**                                     | **_Diverse bewerkingen en functie_** | |
| [beroep doen op](/azure/kusto/query/invokeoperator)                    | Hiermee wordt de functie uitgevoerd op de tabel die deze ontvangt als invoer. | `T | invoke function([param1, param2])` |
| [pluginName evalueren](/azure/kusto/query/evaluateoperator)     | Evalueert taalextensies voor query's (plug-ins) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Visualisatie**                               | **_Bewerkingen die de gegevens in een grafische indeling weergeven_** | |
| [Render](/azure/kusto/query/renderoperator) | Hiermee worden resultaten weergegeven als een grafische uitvoer | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
