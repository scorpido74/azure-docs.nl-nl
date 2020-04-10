---
title: Opbouwvan expressie in kaartgegevensstroom
description: Expressies maken met Behulp van Expression Builder in toewijzingvan gegevensstromen in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 196d917d31eb08af80587bba30d9f7e67bf8cbea
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991702"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Expressies bouwen in de toewijzingsgegevensstroom

In de toewijzingsgegevensstroom worden veel transformatie-eigenschappen ingevoerd als expressies. Deze expressies bestaan uit kolomwaarden, parameters, functies, operatoren en literals die worden geëvalueerd op een Spark-gegevenstype tijdens de looptijd.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Bouwer van open expressies openen

De bewerkingsinterface voor expressie se in de gebruikerservaring van Azure Data Factory wordt Expression Builder genoemd. Wanneer u uw expressielogica invoert, gebruikt Gegevensfabriek [IntelliSense-codevoltooiing](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) voor het markeren, syntaxiscontrole en automatisch aanvullen.

![Opbouwfunctie voor expressies](media/data-flow/xpb1.png "Opbouwfunctie voor expressies")

Open expressionbuilder in transformaties zoals de afgeleide kolom en het filter, waarbij expressies verplicht zijn, de opdracht voor expressies door het vak blauwe expressie te selecteren.

![Blauw expressievak](media/data-flow/expressionbox.png "Opbouwfunctie voor expressies")

Wanneer u naar kolommen verwijst in een overeenkomende of groep-op-voorwaarde, kan een expressie waarden uit kolommen extraheren. Als u een expressie wilt maken, selecteert u **Berekende kolom**.

![Berekende kolom, optie](media/data-flow/computedcolumn.png "Opbouwfunctie voor expressies")

In gevallen waarin een expressie of een letterlijke waarde geldige invoer is, selecteert u **Dynamische inhoud toevoegen** om een expressie te bouwen die wordt geëvalueerd op een letterlijke waarde.

![De optie Dynamische inhoud toevoegen](media/data-flow/add-dynamic-content.png "Opbouwfunctie voor expressies")

## <a name="expression-language-reference"></a>Naslaginformatie over expressietaal

Het in kaart brengen van gegevensstromen heeft ingebouwde functies en operatoren die kunnen worden gebruikt in expressies. Zie [Expressiefuncties in de toewijzingsgegevensstroom](data-flow-expression-functions.md)voor een lijst met beschikbare functies .

## <a name="column-names-with-special-characters"></a>Kolomnamen met speciale tekens

Wanneer u kolomnamen hebt die speciale tekens of spaties bevatten, omringt u de naam met krullende accolades om ernaar te verwijzen in een expressie.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Voorbeeld van expressieresultaten bekijken

Als [de foutopsporingsmodus](concepts-data-flow-debug-mode.md) is ingeschakeld, u het live Spark-cluster gebruiken om een in-progress-voorbeeld te zien van wat uw expressie evalueert. Terwijl u uw logica opbouwt, u uw expressie in realtime debuggen. 

![Voorbeeld van uitvoering](media/data-flow/exp4b.png "Voorbeeld van expressiegegevens")

Selecteer **Vernieuwen** om de resultaten van uw expressie bij te werken tegen een live voorbeeld van uw bron.

![Knop Vernieuwen](media/data-flow/exp5.png "Voorbeeld van expressiegegevens")

## <a name="string-interpolation"></a>Stringinterpolatie

Gebruik aanhalingstekens om letterlijke tekenreekstekst samen met expressies in te sluiten. U expressiefuncties, kolommen en parameters opnemen. Stringinterpolatie is handig om uitgebreid gebruik van string-concatenatie te voorkomen wanneer parameters zijn opgenomen in querytekenreeksen. Als u expressiesyntaxis wilt gebruiken, sluit u deze in krullende accolades in

Enkele voorbeelden van string interpolatie:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Opmerkingsexpressies

Voeg opmerkingen toe aan uw expressies met behulp van de syntaxis van één regel en opmerking met meerdere regellijnen.

![Syntaxis van éénregel en opmerking met meerdere regelregeling](media/data-flow/comments.png "Opmerkingen")

De volgende voorbeelden zijn geldige opmerkingen:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Als u een opmerking boven aan uw expressie plaatst, wordt deze weergegeven in het tekstvak transformatie om uw transformatieexpressies te documenteren.

![Opmerking in het tekstvak transformatie](media/data-flow/comments2.png "Opmerkingen")

## <a name="regular-expressions"></a>Reguliere expressies

Veel expressietaalfuncties gebruiken de syntaxis van reguliere expressies. Wanneer u reguliere expressiefuncties gebruikt, probeert Expression\\Builder een backslash ( ) te interpreteren als een escape character-reeks. Wanneer u backslashes gebruikt in uw reguliere expressie, sluit u\`de hele regex in backticks () of gebruikt u een dubbele backslash.

Een voorbeeld dat backticks gebruikt:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Een voorbeeld dat dubbele slashes gebruikt:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Arrayindexen

Gebruik bij expressiefuncties die arrays retourneren, haakjes ([]) om specifieke indexen in die retourarrayobjecten aan te pakken. De array is gebaseerd op die.

![Array Expression Builder](media/data-flow/expb2.png "Voorbeeld van expressiegegevens")

## <a name="keyboard-shortcuts"></a>Sneltoetsen

* Ctrl+K Ctrl+C: Volledige regel opmerking.
* Ctrl+K Ctrl+U: Opmerking ongedaan maken.
* F1: Help-opdrachten voor de editor bieden.
* Alt+Pijl-omlaag: Huidige lijn omlaag verplaatsen.
* Alt+Pijl-omhoog: huidige regel omhoog verplaatsen.
* Ctrl+spatiebalk: contexthelp weergeven.

## <a name="convert-to-dates-or-timestamps"></a>Converteren naar datums of tijdstempels

Als u tekenreeksliteralen wilt opnemen in uw ```toString()```tijdstempeluitvoer, wikkelt u uw conversie in .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Als u milliseconden wilt converteren van tijdvak `toTimestamp(<number of milliseconds>)`naar een datum of tijdstempel, gebruikt u . Als de tijd komt in seconden, vermenigvuldigen met 1.000.

```toTimestamp(1574127407*1000l)```

De achterliggende "l" aan het einde van de vorige expressie betekent conversie naar een lang type als inline syntaxis.

## <a name="next-steps"></a>Volgende stappen

[Beginnen met het bouwen van gegevenstransformatie-expressies](data-flow-expression-functions.md)
