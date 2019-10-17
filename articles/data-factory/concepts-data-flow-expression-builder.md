---
title: Opbouw functie voor gegevensstroom Expression Azure Data Factory koppelen
description: De opbouw functie voor expressies voor het Azure Data Factory toewijzen van gegevens stromen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 872c7ce6a0c39ab19165a5f16ea3e4f6ef8bd6a5
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388056"
---
# <a name="mapping-data-flow-expression-builder"></a>Opbouw functie voor gegevens stroom expressie koppelen



In Azure Data Factory gegevens stroom toewijzen vindt u expressie vakken waarin u expressies voor gegevens transformatie kunt invoeren. Gebruik kolommen, velden, variabelen, para meters, functies uit uw gegevens stroom in deze vakken. Als u de expressie wilt maken, gebruikt u de opbouw functie voor expressies, die wordt gestart door te klikken in het tekstvak expressie in de trans formatie. Soms ziet u ook de opties berekende kolom bij het selecteren van kolommen voor trans formatie. Als u hierop klikt, ziet u ook de opbouw functie voor expressies.

![Opbouw functie voor expressies](media/data-flow/xpb1.png "Opbouwfunctie voor expressies")

Het hulp programma Expression Builder wordt standaard ingesteld op de tekst editor optie. de functie voor automatisch aanvullen leest uit het hele Azure Data Factory data flow-object model met syntaxis controle en markering.

![Opbouw functie voor expressies automatisch aanvullen](media/data-flow/expb1.png "Opbouw functie voor expressies automatisch aanvullen")

## <a name="build-schemas-in-output-schema-pane"></a>Schema's maken in het deel venster uitvoer schema

![Complexe kolom toevoegen](media/data-flow/complexcolumn.png "Kolommen toevoegen")

In het deel venster uitvoer schema links ziet u de kolommen die u wijzigt en aan uw schema toevoegt. U kunt hier interactief eenvoudige en complexe gegevens structuren bouwen. Voeg extra velden toe met behulp van kolom toevoegen en maak hiërarchieën met behulp van ' subkolom toevoegen '.

![Subkolom toevoegen](media/data-flow/addsubcolumn.png "Subkolom toevoegen")

## <a name="data-preview-in-debug-mode"></a>Voor beeld van gegevens in foutopsporingsmodus

![Opbouw functie voor expressies](media/data-flow/exp4b.png "Voor beeld van expressie gegevens")

Wanneer u aan uw gegevensstroom expressies werkt, schakelt u over op de foutopsporingsmodus in het ontwerp oppervlak van de Azure Data Factory gegevens stroom, waardoor de resultaten van een live in voortgang van uw gegevens worden weer gegeven op basis van de expressie die u bouwt. Real-time live fout opsporing is ingeschakeld voor uw expressies.

![Foutopsporingsmodus](media/data-flow/debugbutton.png "Knop fout opsporing")

Klik op de knop Vernieuwen om de resultaten van uw expressie bij te werken met een live voor beeld van uw bron in realtime.

![Opbouw functie voor expressies](media/data-flow/exp5.png "Voor beeld van expressie gegevens")

## <a name="comments"></a>Opmerkingen

Voeg opmerkingen toe aan uw expressies met behulp van de syntaxis voor één regel en een opmerking met meerdere regels:

![Opmerkingen](media/data-flow/comments.png "Opmerkingen")

## <a name="regular-expressions"></a>Reguliere expressies

In de Azure Data Factory data flow-expressie taal, [volledige referentie documentatie](https://aka.ms/dataflowexpressions), kunt u hier functies gebruiken die een reguliere expressie syntaxis bevatten. Wanneer u reguliere-expressie functies gebruikt, probeert de opbouw functie voor expressies backslashes (@no__t 0) als escape-teken reeks te interpreteren. Wanneer u backslashes in uw reguliere expressie gebruikt, moet u de volledige regex insluiten in Ticks (\`) of een dubbele back slash gebruiken.

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

## <a name="handling-names-with-special-characters"></a>Namen verwerken met speciale tekens

Wanneer u kolom namen hebt die speciale tekens of spaties bevatten, plaatst u de naam tussen accolades.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Volgende stappen

[Beginnen met het bouwen van gegevens transformatie expressies](data-flow-expression-functions.md)
