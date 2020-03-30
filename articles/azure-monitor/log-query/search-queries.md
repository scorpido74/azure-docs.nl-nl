---
title: Zoekopdrachten in Azure Monitor-logboeken | Microsoft Documenten
description: In dit artikel vindt u een zelfstudie voor het gebruik van zoekopdrachten in Azure Monitor-logboekquery's.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2018
ms.openlocfilehash: e13f4abc37e348759e7d0b8a2f7d890c82fe0d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660237"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Zoekopdrachten in Azure Monitor-logboeken
Azure Monitor-logboekquery's kunnen beginnen met een tabelnaam of een zoekopdracht. Deze zelfstudie heeft betrekking op zoekopdrachten. Er zijn voordelen aan elke methode.

Query's in tabelen beginnen met het doorzoeken van de query en zijn daarom meestal efficiënter dan zoekopdrachten. Zoekopdrachten zijn minder gestructureerd, waardoor ze de betere keuze zijn bij het zoeken naar een specifieke waarde in kolommen of tabellen. **zoeken** kan alle kolommen in een bepaalde tabel of in alle tabellen scannen op de opgegeven waarde. De hoeveelheid gegevens die worden verwerkt kan enorm zijn, dat is de reden waarom deze query's langer kunnen duren om te voltooien en kunnen zeer grote resultaatsets retourneren.

## <a name="search-a-term"></a>Een term zoeken
De **zoekopdracht** wordt meestal gebruikt om een specifieke term te zoeken. In het volgende voorbeeld worden alle kolommen in alle tabellen gescand op de term "fout":

```Kusto
search "error"
| take 100
```

Hoewel ze gemakkelijk te gebruiken zijn, zijn niet-scoped query's zoals hierboven getoond niet efficiënt en zullen ze waarschijnlijk veel irrelevante resultaten opleveren. Een betere praktijk zou zijn om te zoeken in de relevante tabel, of zelfs een specifieke kolom.

### <a name="table-scoping"></a>Tabelscoping
Als u een term in `in (table-name)` een bepaalde tabel wilt doorzoeken, voegt u vlak na de **zoekoperator** toe:

```Kusto
search in (Event) "error"
| take 100
```

of in meerdere tabellen:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Tabel- en kolomscoping
Standaard **evalueert zoeken** alle kolommen in de gegevensset. Als u alleen een specifieke kolom *wilt* zoeken (bron in het onderstaande voorbeeld), gebruikt u de onderstaande syntaxis:

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Als u `==` in `:`plaats van , de resultaten zou bevatten records waarin de kolom *Bron* heeft de exacte waarde "fout", en in dit exacte geval. Het gebruik van ':' omvat records waarin *de bron* waarden heeft zoals 'foutcode 404' of 'Fout'.

## <a name="case-sensitivity"></a>Hoofdlettergevoeligheid
Standaard is term search case-ongevoelig, dus zoeken naar "dns" kan resultaten opleveren zoals "DNS", "dns" of "Dns". Gebruik de `kind` optie om de zoekcase gevoelig te maken:

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Jokerkaarten gebruiken
Het **zoekcommando** ondersteunt wildcards, aan het begin, einde of midden van een term.

Ga als u op zoek gaan naar termen die beginnen met 'winnen':
```Kusto
search in (Event) "win*"
| take 100
```

Ga als u op zoek gaat naar termen die eindigen met ".com":
```Kusto
search in (Event) "*.com"
| take 100
```

Ga als een zoekfunctie op zoek naar termen die "www" bevatten:
```Kusto
search in (Event) "*www*"
| take 100
```

Om termen te zoeken die beginnen met "corp" en eindigt op ".com", zoals "corp.mydomain.com""

```Kusto
search in (Event) "corp*.com"
| take 100
```

U ook alles in een tabel met `search in (Event) *`behulp van slechts een wild `Event`card:, maar dat zou hetzelfde zijn als het schrijven van gewoon.

> [!TIP]
> Hoewel u `search *` kunt gebruiken om elke kolom uit elke tabel te halen, wordt aanbevolen dat u uw query's altijd naar specifieke tabellen houdt. Het kan even duren voordat niet-scopedquery's zijn voltooid en dat er mogelijk te veel resultaten zijn.

## <a name="add-and--or-to-search-queries"></a>Query's toevoegen *en* / *of* aan zoeken
Gebruik **en** zoek naar records die meerdere termen bevatten:

```Kusto
search in (Event) "error" and "register"
| take 100
```

Gebruik **of** om records op te halen die ten minste één van de termen bevatten:

```Kusto
search in (Event) "error" or "register"
| take 100
```

Als u meerdere zoekvoorwaarden hebt, u deze combineren tot dezelfde query met haakjes:

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

De resultaten van dit voorbeeld zijn records die de term "fout" bevatten en ook "registreren" of iets dat begint met "marshal".

## <a name="pipe-search-queries"></a>Zoekopdrachten voor leidingen
Net als elke andere opdracht kan **zoeken** worden gepipeteerd, zodat zoekresultaten kunnen worden gefilterd, gesorteerd en samengevoegd. Bijvoorbeeld om het aantal *gebeurtenisrecords* te krijgen dat 'winnen' bevat:

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Volgende stappen

- Zie verdere tutorials op de [Kusto query taal site](/azure/kusto/query/).
