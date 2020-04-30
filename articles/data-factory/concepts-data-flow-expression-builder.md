---
title: Opbouw functie voor expressies in gegevens stroom toewijzen
description: Expressies bouwen met behulp van Expression Builder in toewijzing van gegevens stromen in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: dda2812b5e2cc79d53658d568ba0845d593f41d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605384"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Expressies bouwen in gegevens stroom toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bij het toewijzen van gegevens stroom worden veel trans formatie-eigenschappen ingevoerd als expressies. Deze expressies bestaan uit kolom waarden, para meters, functies, Opera tors en letterlijke tekens die tijdens de uitvoering resulteren in een Spark-gegevens type.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Opbouw functie voor expressies openen

De interface voor het bewerken van expressies in de gebruikers ervaring van Azure Data Factory wordt Expression Builder genoemd. Wanneer u de expressie logica invoert, gebruikt Data Factory [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) -code voltooiing voor markeren, syntaxis controle en automatisch invullen.

![Opbouw functie voor expressies](media/data-flow/xpb1.png "Opbouwfunctie voor expressies")

In trans formaties zoals de afgeleide kolom en het filter, waar expressies verplicht zijn, opent u de opbouw functie voor expressies door het vak blauwe expressie te selecteren.

![Vak met blauwe expressie](media/data-flow/expressionbox.png "Opbouwfunctie voor expressies")

Wanneer u verwijst naar kolommen in een matching-of Group-by-voor waarde, kan een expressie waarden uit kolommen ophalen. Selecteer **berekende kolom**om een expressie te maken.

![Optie berekende kolom](media/data-flow/computedcolumn.png "Opbouwfunctie voor expressies")

In gevallen waarin een expressie of letterlijke waarde geldige invoer is, selecteert u **dynamische inhoud toevoegen** om een expressie te bouwen die resulteert in een letterlijke waarde.

![Optie voor dynamische inhoud toevoegen](media/data-flow/add-dynamic-content.png "Opbouwfunctie voor expressies")

## <a name="expression-language-reference"></a>Naslag informatie voor expressie taal

Het toewijzen van gegevens stromen heeft ingebouwde functies en Opera tors die kunnen worden gebruikt in expressies. Zie [expressie functies in de stroom toewijzings gegevens](data-flow-expression-functions.md)voor een lijst met beschik bare functies.

## <a name="column-names-with-special-characters"></a>Kolom namen met speciale tekens

Wanneer u kolom namen hebt die speciale tekens of spaties bevatten, plaatst u de naam tussen accolades om ernaar te verwijzen in een expressie.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Preview-expressie resultaten

Als de [foutopsporingsmodus](concepts-data-flow-debug-mode.md) is ingeschakeld, kunt u het Live Spark-cluster gebruiken om een voor beeld te bekijken van de voortgang van de expressie. Wanneer u uw logica bouwt, kunt u in realtime fouten opsporen in uw expressie. 

![Preview in uitvoering](media/data-flow/exp4b.png "Voor beeld van expressie gegevens")

Selecteer **vernieuwen** om de resultaten van uw expressie bij te werken met een live voor beeld van uw bron.

![Knop Vernieuwen](media/data-flow/exp5.png "Voor beeld van expressie gegevens")

## <a name="string-interpolation"></a>Interpolatie van teken reeksen

Gebruik aanhalings tekens om letterlijke teken reeks tekst samen met expressies in te sluiten. U kunt expressie functies, kolommen en para meters toevoegen. Interpoleren van teken reeksen is handig om te voor komen dat teken reeksen worden samengevoegd wanneer para meters worden opgenomen in query strings. Als u de syntaxis van de expressie wilt gebruiken, plaatst u deze tussen accolades,

Enkele voor beelden van interpolatie van teken reeksen:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Commentaar expressies

Voeg opmerkingen toe aan uw expressies door gebruik te maken van de syntaxis van een meerregelige opmerking met één regel.

![Syntaxis van één regel en een opmerking in meerdere regels](media/data-flow/comments.png "Opmerkingen")

De volgende voor beelden zijn geldige opmerkingen:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Als u een opmerking boven aan de expressie plaatst, wordt deze weer gegeven in het tekstvak trans formatie om uw transformatie expressies te documenteren.

![Opmerking in het tekstvak trans formatie](media/data-flow/comments2.png "Opmerkingen")

## <a name="regular-expressions"></a>Reguliere expressies

Veel expressie taal functies gebruiken reguliere expressie syntaxis. Wanneer u reguliere-expressie functies gebruikt, probeert Expression Builder een back slash (\\) als escape-teken reeks te interpreteren. Wanneer u backslashes in uw reguliere expressie gebruikt, moet u de volledige regex insluiten in accents graves\`() of een dubbele back slash gebruiken.

Een voor beeld waarin accents graves wordt gebruikt:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Een voor beeld waarbij gebruik wordt gemaakt van dubbele slashes:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Matrix indexen van adres

Met expressie functies die matrices retour neren, gebruikt u vier Kante haken ([]) voor het oplossen van specifieke indexen binnen die matrix objecten retour neren. De matrix is gebaseerd op deze.

![Expression Builder-matrix](media/data-flow/expb2.png "Voor beeld van expressie gegevens")

## <a name="keyboard-shortcuts"></a>Sneltoetsen

* CTRL + K CTRL + C: de volledige regel opmerking.
* CTRL + K CTRL + U: opmerkingen opheffen.
* F1: Help-opdrachten voor de editor opgeven.
* Alt + pijl-omlaag-toets: huidige regel omlaag verplaatsen.
* Alt + pijl-omhoog-toets: huidige regel omhoog verplaatsen.
* Ctrl + spatie balk: context Help weer geven.

## <a name="convert-to-dates-or-timestamps"></a>Converteren naar datums of tijds tempels

Als u letterlijke teken reeksen wilt toevoegen aan de uitvoer van de tijds ```toString()```tempel, zet u de conversie in.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Als u milliseconden wilt converteren van epoche naar een datum of `toTimestamp(<number of milliseconds>)`tijds tempel, gebruikt u. Als de tijd in seconden is, vermenigvuldigt u met 1.000.

```toTimestamp(1574127407*1000l)```

De eerste ' l ' aan het einde van de vorige expressie geeft een conversie naar een lang type als inline syntaxis.

## <a name="next-steps"></a>Volgende stappen

[Beginnen met het bouwen van gegevens transformatie expressies](data-flow-expression-functions.md)
