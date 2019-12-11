---
title: Opbouw functie voor expressies in gegevens stroom toewijzen
description: Expressies bouwen met de opbouw functie voor expressies in het toewijzen van gegevens stromen in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 01aa2574ac6edd1ce5e1b209eac3e43bbed82fce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969343"
---
# <a name="building-expressions-in-mapping-data-flow"></a>Expressies bouwen in gegevens stroom toewijzen

Bij het toewijzen van gegevens stroom worden veel trans formatie-eigenschappen ingevoerd als expressies. Deze expressies bestaan uit kolom waarden, para meters, functies, Opera tors en letterlijke tekens die tijdens de uitvoering resulteren in een Spark-gegevens type.

## <a name="opening-the-expression-builder"></a>De opbouw functie voor expressies openen

De interface voor het bewerken van expressies in de data factory UX is bekend als de **opbouw functie voor expressies**. Bij het invoeren van de expressie logica gebruikt data factory [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) -code voltooiing voor markeren, syntaxis controle en automatisch invullen.

![Opbouw functie voor expressies](media/data-flow/xpb1.png "Opbouwfunctie voor expressies")

In trans formaties zoals de afgeleide kolom en het filter, waar expressies verplicht zijn, opent u de opbouw functie voor expressies door te klikken op het blauwe expressievak.

![Opbouw functie voor expressies](media/data-flow/expressionbox.png "Opbouwfunctie voor expressies")

Bij het verwijzen naar kolommen in een match of Group by-voor waarde, kan een expressie waarden uit kolommen ophalen. Als u een expressie wilt maken, selecteert u de optie berekende kolom.

![Opbouw functie voor expressies](media/data-flow/computedcolumn.png "Opbouwfunctie voor expressies")

In gevallen waarin een expressie of letterlijke waarde geldige invoer is, kunt u met ' dynamische inhoud toevoegen ' een expressie bouwen die resulteert in een letterlijke teken reeks.

![Opbouw functie voor expressies](media/data-flow/add-dynamic-content.png "Opbouwfunctie voor expressies")

## <a name="expression-language-reference"></a>Naslag informatie voor expressie taal

Het toewijzen van gegevens stromen heeft ingebouwde functies en Opera tors die kunnen worden gebruikt in expressies. U vindt een lijst met beschik bare functies op de pagina [toewijzing van gegevens stroom expressie taal](data-flow-expression-functions.md) .

## <a name="column-names-with-special-characters"></a>Kolom namen met speciale tekens

Wanneer u kolom namen hebt die speciale tekens of spaties bevatten, plaatst u de naam tussen accolades om ernaar te verwijzen in een expressie.

```{[dbo].this_is my complex name$$$}```

## <a name="previewing-expression-results"></a>Expressie resultaten weer geven

Als de [foutopsporingsmodus-modus](concepts-data-flow-debug-mode.md) is ingeschakeld, kunt u het Live Spark-cluster gebruiken om een voor beeld te bekijken van de voortgang van de expressie. Wanneer u uw logica bouwt, kunt u in realtime fouten opsporen in uw expressie. 

![Opbouw functie voor expressies](media/data-flow/exp4b.png "Voor beeld van expressie gegevens")

Klik op de knop Vernieuwen om de resultaten van uw expressie bij te werken met een live voor beeld van uw bron.

![Opbouw functie voor expressies](media/data-flow/exp5.png "Voor beeld van expressie gegevens")

## <a name="string-interpolation"></a>Interpolatie van teken reeksen

Gebruik dubbele aanhalings tekens om letterlijke teken reeks tekst samen met expressies te sluiten. U kunt expressie functies, kolommen en para meters toevoegen. Interpolatie van teken reeksen is handig om te voor komen dat teken reeks samenvoeging uitgebreid wordt gebruikt wanneer para meters in query reeksen worden opgenomen. Als u de syntaxis van de expressie wilt gebruiken, plaatst u deze tussen accolades,

Enkele voor beelden van interpolatie van teken reeksen:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="commenting-expressions"></a>Expressies voor opmerkingen

Voeg opmerkingen toe aan uw expressies met behulp van de syntaxis voor één regel en een opmerking met meerdere regels:

![Opmerkingen](media/data-flow/comments.png "Opmerkingen")

Hieronder vindt u voor beelden van geldige opmerkingen:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Als u een opmerking boven aan de expressie plaatst, wordt deze weer gegeven in het tekstvak trans formatie om uw transformatie expressies te documenteren:

![Opmerkingen](media/data-flow/comments2.png "Opmerkingen")

## <a name="regular-expressions"></a>Reguliere expressies

Veel expressie taal functies gebruiken reguliere expressie syntaxis. Wanneer reguliere-expressie functies worden gebruikt, probeert de opbouw functie voor expressies backslashes (\\) als escape-teken reeks te interpreteren. Wanneer u backslashes in uw reguliere expressie gebruikt, moet u de volledige regex insluiten in Ticks (\`) of een dubbele back slash gebruiken.

Voor beeld met behulp van maat streepjes

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

of gebruik een dubbele slash

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Matrix indexen adresseren

Met expressie functies die matrices retour neren, gebruikt u vier Kante haken [] voor het oplossen van specifieke indexen binnen het object geretourneerde matrix. De matrix is gebaseerd op.

![Expression Builder-matrix](media/data-flow/expb2.png "Voor beeld van expressie gegevens")

## <a name="keyboard-shortcuts"></a>Toetsencombinaties

* ```Ctrl-K Ctrl-C```: volledige regel commentaar
* ```Ctrl-K Ctrl-U```: Opmerking opheffen
* ```F1```: Help-opdrachten voor de editor opgeven
* ```Alt-Down Arrow```: huidige regel omlaag verplaatsen
* ```Alt-Up Arrow```: huidige regel verplaatsen
* ```Cntrl-Space```: context afhankelijke Help weer geven

## <a name="convert-to-dates-or-timestamps"></a>Converteren naar datums of tijds tempels

Als u letterlijke teken reeksen wilt toevoegen aan de uitvoer van de tijds tempel, moet u de conversie in ```toString()```afronden.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Gebruik `toTimestamp(<number of milliseconds>)`om milliseconden van de epoche om te zetten in een datum of tijds tempel. Als de tijd in seconden is, vermenigvuldigt u met 1000.

```toTimestamp(1574127407*1000l)```

De achteraan ' l ' aan het einde van de bovenstaande expressie geeft een conversie naar een lang type als de regel syntaxis.

## <a name="next-steps"></a>Volgende stappen

[Beginnen met het bouwen van gegevens transformatie expressies](data-flow-expression-functions.md)
