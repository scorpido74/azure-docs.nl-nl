---
title: Naslag informatie voor KQL
description: Een lijst met handige KQL-functies en de bijbehorende definities met syntaxis voorbeelden.
author: yossi-karp
ms.author: v-yokarp
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: b53890afd10a3aee131ab3897d01e6b6fdf261a6
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429265"
---
# <a name="kql-quick-reference"></a>Naslag informatie voor KQL

In dit artikel vindt u een lijst met functies en beschrijvingen waarmee u aan de slag kunt gaan met de Kusto-query taal.

| Operator/functie                               | Beschrijving                           | Syntaxis                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filteren/zoeken/voor waarde**                      |**_Relevante gegevens zoeken door te filteren of te zoeken_** |                      |
| [positie](/azure/kusto/query/whereoperator.md)                      | Filters op een specifiek predicaat           | `T | where Predicate`                         |
| [waar bevat/heeft](/azure/kusto/query/whereoperator.md)        | `Contains`: zoekt naar overeenkomende subtekenreeks <br> `Has`: zoekt naar een specifiek woord (betere prestaties)  | `T | where col1 contains/has "[search term]"`|
| [opdracht](/azure/kusto/query/searchoperator.md)                    | Zoekt in alle kolommen in de tabel naar de waarde | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [Houd](/azure/kusto/query/takeoperator.md)                        | Retourneert het opgegeven aantal records. Gebruiken om een query te testen<br>**_Opmerking_** : `_take`_ en `_limit`_ zijn synoniemen. | `T | take NumberOfRows` |
| [casu](/azure/kusto/query/casefunction.md)                        | Hiermee voegt u een voorwaarde instructie toe, vergelijkbaar met if/then/elseif in andere systemen. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [onderscheiden](/azure/kusto/query/distinctoperator.md)                | Produceert een tabel met de unieke combi natie van de opgegeven kolommen van de invoer tabel | `distinct [ColumnName], [ColumnName]` |
| **Datum en tijd**                                   |**_Bewerkingen waarbij gebruik wordt gemaakt van datum-en tijd functies_**               |                          |
|[later](/azure/kusto/query/agofunction.md)                           | Retourneert de tijd verschuiving ten opzichte van de tijd waarop de query wordt uitgevoerd. `ago(1h)` is bijvoorbeeld één uur voor het lezen van de huidige klok. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction.md)  | Retourneert gegevens in [verschillende datum notaties](/azure/kusto/query/format-datetimefunction.md#supported-formats). | `format_datetime(datetime , format)` |
| [dockopslaglocatie](/azure/kusto/query/binfunction.md)                          | Hiermee worden alle waarden in een tijds bestek afgerond en gegroepeerd | `bin(value,roundTo)` |
| **Kolommen maken/verwijderen**                   |**_Kolommen toevoegen aan of verwijderen uit een tabel_** |                                                    |
| [Afdruk](/azure/kusto/query/printoperator.md)                      | Hiermee wordt één rij met een of meer scalaire expressies uitgevoerd | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](/azure/kusto/query/projectoperator.md)                  | De kolommen selecteren die u wilt gebruiken in de opgegeven volg orde | `T | project ColumnName [= Expression] [, ...]` <br> of <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-weg](/azure/kusto/query/projectawayoperator.md)         | De kolommen selecteren die u wilt uitsluiten van de uitvoer | `T | project-away ColumnNameOrPattern [, ...]` |
| [uitbreidbaar](/azure/kusto/query/extendoperator.md)                    | Hiermee maakt u een berekende kolom en voegt u deze toe aan de resultatenset | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Gegevensset sorteren en verzamelen**                 |**_De gegevens herstructureren door ze op zinvolle manieren te sorteren of groeperen_**|                  |
| [acties](/azure/kusto/query/sortoperator.md)                        | Hiermee worden de rijen van de invoer tabel gesorteerd op een of meer kolommen in oplopende of aflopende volg orde | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [Boven](/azure/kusto/query/topoperator.md)                          | Retourneert de eerste N rijen van de gegevensset wanneer de gegevensset wordt gesorteerd met `by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [samenvatten](/azure/kusto/query/summarizeoperator.md)              | Hiermee worden de rijen gegroepeerd op basis van de kolommen van de `by` groep en worden aggregaties voor elke groep berekend | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator.md)                       | Telt records in de invoer tabel (bijvoorbeeld T)<br>Deze operator is steno voor `summarize count() `| `T | count` |
| [join](/azure/kusto/query/joinoperator.md)                        | De rijen van twee tabellen worden samengevoegd om een nieuwe tabel te vormen door te voldoen aan de waarden van de opgegeven kolom (men) in elke tabel. Ondersteunt een breed scala aan jointypen: `flouter`, `inner`, `innerunique`, `leftanti`, `leftantisemi`, `leftouter`, `leftsemi`, `rightanti`, `rightantisemi`, `rightouter``rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [Réunion](/azure/kusto/query/unionoperator.md)                      | Neemt twee of meer tabellen en retourneert alle rijen | `[T1] | union [T2], [T3], …` |
| [bereik](/azure/kusto/query/rangeoperator.md)                      | Hiermee wordt een tabel met een reken kundige reeks waarden gegenereerd | `range columnName from start to stop step step` |
| **Gegevens opmaken**                                 | **_De gegevens op een handige manier herstructureren voor uitvoer_** | |
| [unmp](/azure/kusto/query/lookupoperator.md)                    | Hiermee worden de kolommen van een feiten tabel uitgebreid met waarden die zijn opgezocht in een dimensie tabel | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [MV-uitvouwen](/azure/kusto/query/mvexpandoperator.md)               | Hiermee worden dynamische matrices omgezet in rijen (meerdere waarden) | `T | mv-expand Column` |
| [parseren](/azure/kusto/query/parseoperator.md)                      | Evalueert een teken reeks expressie en parseert de waarde ervan in een of meer berekende kolommen. Gebruiken voor het structureren van ongestructureerde gegevens. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [reeks maken](/azure/kusto/query/make-seriesoperator.md)          | Hiermee maakt u een reeks opgegeven geaggregeerde waarden langs een opgegeven as | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [bekijken](/azure/kusto/query/letstatement.md)                         | Verbindt een naam met expressies die kunnen verwijzen naar de gebonden waarde. Waarden kunnen lambda-expressies zijn voor het maken van ad-hoc functies als onderdeel van de query. Gebruik `let` om expressies te maken over tabellen waarvan de resultaten eruitzien als een nieuwe tabel. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Algemeen**                                     | **_Diverse bewerkingen en functie_** | |
| [trigger](/azure/kusto/query/invokeoperator.md)                    | De functie wordt uitgevoerd op de tabel die als invoer wordt ontvangen. | `T | invoke function([param1, param2])` |
| [Pluginnaam evalueren](/azure/kusto/query/evaluateoperator.md)     | Evalueert de query taal uitbreidingen (invoeg toepassingen) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Visualisatie**                               | **_Bewerkingen die de gegevens weer geven in een grafische indeling_** | |
| [waardoor](/azure/kusto/query/renderoperator.md) | Hiermee worden resultaten weer gegeven als een grafische uitvoer | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
