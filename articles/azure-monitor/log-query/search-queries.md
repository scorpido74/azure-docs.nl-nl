---
title: Zoek query's in Azure Monitor logs | Microsoft Docs
description: Dit artikel bevat een zelf studie om aan de slag te gaan met zoeken in Azure Monitor-logboek query's.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2018
ms.openlocfilehash: d92cd42f0fceadee16035b605e8d25c6bc23bc67
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932999"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Zoek query's in Azure Monitor-logboeken
Azure Monitor-logboek query's kunnen beginnen met een tabel naam of een zoek opdracht. In deze zelf studie worden Zoek query's behandeld. Er zijn voor delen van elke methode.

Query's op basis van een tabel worden gestart door de query te bereiken en zijn daarom meestal efficiënter dan zoek query's. Zoek query's zijn minder gestructureerd, waardoor ze beter kunnen worden gekozen bij het zoeken naar een specifieke waarde in kolommen of tabellen. met **zoeken** kunt u alle kolommen in een bepaalde tabel of in alle tabellen scannen voor de opgegeven waarde. De hoeveelheid gegevens die wordt verwerkt, kan enorm zijn, wat betekent dat deze query's langer kunnen duren en mogelijk zeer grote resultaten sets kunnen retour neren.

## <a name="search-a-term"></a>Een term zoeken
De **Zoek** opdracht wordt doorgaans gebruikt om een specifieke term te doorzoeken. In het volgende voor beeld worden alle kolommen in alle tabellen gescand op de term ' fout ':

```Kusto
search "error"
| take 100
```

Hoewel ze gemakkelijk te gebruiken zijn, zijn onbereikbare query's, zoals hierboven aangegeven, niet efficiënt en kunnen ze veel irrelevante resultaten retour neren. Het is raadzaam om te zoeken in de relevante tabel of zelfs een specifieke kolom.

### <a name="table-scoping"></a>Bereik van tabel
Als u een term in een specifieke tabel wilt doorzoeken, voegt u `in (table-name)` toe na de **Zoek** operator:

```Kusto
search in (Event) "error"
| take 100
```

of in meerdere tabellen:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Bereik tabel en kolom
Standaard **worden alle** kolommen in de gegevensset geëvalueerd. Als u alleen in een specifieke kolom ( *met de naam in* het onderstaande voor beeld) wilt zoeken, gebruikt u de volgende syntaxis:

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Als u `==` gebruikt in plaats van `:`, bevatten de resultaten records waarin de *bron* kolom de exacte waarde ' fout ' heeft en in dit exacte geval. Using ': ' bevat records waarbij de *bron* waarden heeft, zoals ' fout code 404 ' of ' fout '.

## <a name="case-sensitivity"></a>Hoofdletter gevoeligheid
De term zoek opdracht is standaard hoofdletter gevoelig, dus het zoeken naar ' DNS ' kan resulteren in resultaten zoals ' DNS ', ' DNS ' of ' DNS '. Als u de Zoek hoofdletter gevoelig wilt maken, gebruikt u de optie `kind`:

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Joker tekens gebruiken
De **Zoek** opdracht ondersteunt joker tekens, aan het begin of in het midden van een term.

Zoek termen die beginnen met ' win ':
```Kusto
search in (Event) "win*"
| take 100
```

Zoek termen die eindigen op '. com ':
```Kusto
search in (Event) "*.com"
| take 100
```

Zoek termen die ' www ' bevatten:
```Kusto
search in (Event) "*www*"
| take 100
```

Zoek termen die beginnen met "Corp" en eindigen op ". com", zoals "corp.mydomain.com" "

```Kusto
search in (Event) "corp*.com"
| take 100
```

U kunt ook alles in een tabel ophalen door alleen een Joker teken te gebruiken: `search in (Event) *`, maar dit is hetzelfde als het schrijven van alleen `Event`.

> [!TIP]
> Hoewel u `search *` kunt gebruiken om elke kolom uit elke tabel op te halen, is het raadzaam om altijd uw query's op specifieke tabellen te bereiken. Het kan enige tijd duren voordat query's zonder bereik zijn voltooid en mogelijk te veel resultaten retour neren.

## <a name="add-and--or-to-search-queries"></a>Toevoegen *en* / *of* Zoek query's
Gebruiken **en** zoeken naar records die meerdere termen bevatten:

```Kusto
search in (Event) "error" and "register"
| take 100
```

Gebruik **of** om records te verkrijgen die ten minste één van de voor waarden bevatten:

```Kusto
search in (Event) "error" or "register"
| take 100
```

Als u meerdere zoek voorwaarden hebt, kunt u deze combi neren in dezelfde query met behulp van haakjes:

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

De resultaten van dit voor beeld zijn records die de term ' error ' bevatten en ook ' registreren ' bevatten of iets dat begint met ' Mars '.

## <a name="pipe-search-queries"></a>Zoek query's voor pipes
Net als elke andere opdracht kan de **Zoek** opdracht worden gepiped zodat Zoek resultaten kunnen worden gefilterd, gesorteerd en geaggregeerd. Als u bijvoorbeeld het aantal *gebeurtenis* records wilt ophalen dat "win" bevat:

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Volgende stappen

- Zie verdere zelf studies op de [Kusto query language-site](/azure/kusto/query/).
