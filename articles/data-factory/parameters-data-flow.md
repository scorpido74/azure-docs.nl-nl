---
title: Toewijzingsgegevensstromen parameteriseren
description: Meer informatie over het parameteriseren van een toewijzingsgegevensstroom van pijplijnen in gegevensfabrieken
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c717d9ab44493d15589030073cd2ab260ef52e1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760212"
---
# <a name="parameterizing-mapping-data-flows"></a>Toewijzingsgegevensstromen parameteriseren

Het toewijzen van gegevensstromen in Azure Data Factory ondersteunen het gebruik van parameters. U parameters definiëren binnen uw gegevensstroomdefinitie, die u vervolgens in uw expressies gebruiken. De parameterwaarden kunnen worden ingesteld door de aanroepende pijplijn via de activiteit Gegevensstroom uitvoeren. U hebt drie opties voor het instellen van de waarden in de expressies van gegevensstromenactiviteit:

* De taal van de pijplijnbesturingselementstroomexpressie gebruiken om een dynamische waarde in te stellen
* De taal van de gegevensstroomexpressie gebruiken om een dynamische waarde in te stellen
* Een van beide expressietaal gebruiken om een statische letterlijke waarde in te stellen

Gebruik deze mogelijkheid om uw gegevensstromen voor algemene doeleinden, flexibel en herbruikbaar te maken. U met deze parameters de instellingen en expressies van gegevensstromen parameteriseren.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Parameters maken in een toewijzingsgegevensstroom

Als u parameters wilt toevoegen aan uw gegevensstroom, klikt u op het lege gedeelte van het canvas van de gegevensstroom om de algemene eigenschappen te bekijken. In het instellingenvenster ziet u een tabblad met de naam **Parameter**. Selecteer **Nieuw** om een nieuwe parameter te genereren. Voor elke parameter moet u een naam toewijzen, een type selecteren en optioneel een standaardwaarde instellen.

![Gegevensstroomparameters maken](media/data-flow/create-params.png "Gegevensstroomparameters maken")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Parameters gebruiken in een toewijzingsgegevensstroom 

Parameters kunnen worden verwezen in elke gegevensstroomexpressie. Parameters beginnen met $ en zijn onveranderlijk. U vindt de lijst met beschikbare parameters in de opbouwfunctie voor expressies onder het tabblad **Parameters.**

![Parameterexpressie gegevensstroom](media/data-flow/parameter-expression.png "Parameterexpressie gegevensstroom")

U snel extra parameters toevoegen door **De parameter Nieuw** te selecteren en de naam en het type op te geven.

![Parameterexpressie gegevensstroom](media/data-flow/new-parameter-expression.png "Parameterexpressie gegevensstroom")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Een kolomnaam doorgeven als parameter

Een gemeenschappelijk patroon is om in een kolomnaam als parameterwaarde door te geven. Als u wilt verwijzen naar de `byName()` kolom die aan de parameter is gekoppeld, gebruikt u de functie. Vergeet niet om de kolom te casten `toString()`naar het juiste type met een casting functie zoals .

Als u bijvoorbeeld een tekenreekskolom op basis `columnName`van een parameter wilt toewijzen, `toString(byName($columnName))`u een afgeleide kolomtransformatie toevoegen die gelijk is aan.

![Een kolomnaam doorgeven als parameter](media/data-flow/parameterize-column-name.png "Het doorgeven van een kolomnaam als een paramete")

## <a name="assign-parameter-values-from-a-pipeline"></a>Parameterwaarden uit een pijplijn toewijzen

Nadat u uw gegevensstroom met parameters hebt gemaakt, u deze uitvoeren vanuit een pijplijn met de activiteit Gegevensstroom uitvoeren. Nadat u de activiteit aan uw pijplijncanvas hebt toegevoegd, krijgt u de beschikbare gegevensstroomparameters te zien op het tabblad **Parameters** van de activiteit.

![Een parameter Gegevensstroom instellen](media/data-flow/parameter-assign.png "Een parameter Gegevensstroom instellen")

Als het parametergegevenstype tekenreeks is, u ervoor kiezen om een pijplijn of een gegevensstroomexpressie in te voeren wanneer u op het tekstvak klikt om parameterwaarden in te stellen. Als u pijplijnexpressie kiest, wordt u het deelvenster pijplijnexpressie weergegeven. Zorg ervoor dat u de pijplijnfuncties `'@{<expression>}'`opneemt in de syntaxis van stringinterpolatie met behulp van:

```'@{pipeline().RunId}'```

Als uw parameter geen typetekenreeks is, wordt u altijd gepresenteerd met de Opbouwfunctie gegevensstroomexpressie. Hier u elke expressie of letterlijke waarden invoeren die overeenkomen met het gegevenstype van de parameter. Hieronder vindt u voorbeelden van gegevensstroomexpressie en een letterlijke tekenreeks van de expressiebouwer:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Elke mapping gegevensstroom kan elke combinatie van pijplijn en gegevensstroom expressie parameters. 

![Voorbeeld van gegevensstroomparameters](media/data-flow/parameter-example.png "Voorbeeld van gegevensstroomparameters")



## <a name="next-steps"></a>Volgende stappen
* [Gegevensstroomactiviteit uitvoeren](control-flow-execute-data-flow-activity.md)
* [Stroomexpressieexpressieexpressies beheren](control-flow-expression-language-functions.md)
