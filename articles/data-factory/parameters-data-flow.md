---
title: Gegevens stromen voor Parameterizing-toewijzing
description: Meer informatie over het para meters van een toewijzings gegevens stroom van data factory-pijp lijnen
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c589cfeab7a812e09ce7f7620e93b72bd362859a
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75746122"
---
# <a name="parameterizing-mapping-data-flows"></a>Gegevens stromen voor Parameterizing-toewijzing

Het toewijzen van gegevens stromen in Azure Data Factory ondersteunen het gebruik van para meters. U kunt para meters definiëren in de definitie van de gegevens stroom, die u vervolgens kunt gebruiken in uw expressies. De parameter waarden kunnen worden ingesteld door de aanroepende pijp lijn via de activiteit gegevens stroom uitvoeren. U hebt drie opties voor het instellen van de waarden in de gegevens stroom activiteit expressies:

* De expressie taal voor de controle stroom van de pijp lijn gebruiken om een dynamische waarde in te stellen
* De data flow-expressie taal gebruiken om een dynamische waarde in te stellen
* Een van de expressie taal gebruiken om een statische letterlijke waarde in te stellen

Gebruik deze mogelijkheid om uw gegevens te stroom, flexibel en herbruikbaar te maken. U kunt para meters en expressies voor gegevens stromen met deze para meters.

> [!NOTE]
> Als u de controle stroom expressies voor de pijp lijn wilt gebruiken, moet u de para meter voor de gegevens stroom van het type teken reeks zijn.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Para meters maken in een toewijzings gegevens stroom

Als u para meters wilt toevoegen aan uw gegevens stroom, klikt u op het lege deel van het canvas voor gegevens stromen om de algemene eigenschappen weer te geven. In het deel venster instellingen ziet u een tabblad met de naam **para meter**. Selecteer **Nieuw** om een nieuwe para meter te genereren. Voor elke para meter moet u een naam toewijzen, een type selecteren en optioneel een standaard waarde instellen.

![Data flow-para meters maken](media/data-flow/create-params.png "Data flow-para meters maken")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Para meters in een toewijzings gegevens stroom gebruiken 

In elke gegevens stroom expressie kan naar para meters worden verwezen. Para meters beginnen met $ en zijn onveranderbaar. De lijst met beschik bare para meters kunt u vinden in de opbouw functie voor expressies op het tabblad **para meters** .

![Expressie voor de gegevens stroom parameter](media/data-flow/parameter-expression.png "Expressie voor de gegevens stroom parameter")

U kunt snel extra para meters toevoegen door **nieuwe para meter** te selecteren en de naam en het type op te geven.

![Expressie voor de gegevens stroom parameter](media/data-flow/new-parameter-expression.png "Expressie voor de gegevens stroom parameter")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Het door geven van een kolom naam als para meter

Een gemeen schappelijk patroon is het door geven van een kolom naam als een parameter waarde. Gebruik de functie `byName()` om te verwijzen naar de kolom die aan de para meter is gekoppeld. Vergeet niet om de kolom naar het juiste type te casten met een cast-functie zoals `toString()`.

Als u bijvoorbeeld een teken reeks kolom wilt toewijzen op basis van een para meter `columnName`, kunt u een afgeleide kolom transformatie toevoegen die gelijk is aan `toString(byName($columnName))`.

![Het door geven van een kolom naam als para meter](media/data-flow/parameterize-column-name.png "Het door geven van een kolom naam als een paramete")

## <a name="assign-parameter-values-from-a-pipeline"></a>Parameter waarden toewijzen vanuit een pijp lijn

Wanneer u de gegevens stroom hebt gemaakt met para meters, kunt u deze uitvoeren vanuit een pijp lijn met de activiteit gegevens stroom uitvoeren. Nadat u de activiteit aan uw pijplijn doek hebt toegevoegd, worden de beschik bare gegevensstroom parameters weer gegeven op het tabblad **para meters** van de activiteit.

![Een para meter voor een gegevens stroom instellen](media/data-flow/parameter-assign.png "Een para meter voor een gegevens stroom instellen")

Als uw parameter gegevens type teken reeks is en u op het tekstvak klikt om parameter waarden in te stellen, kunt u een pijp lijn of een gegevensstroom expressie opgeven. Als u pijplijn expressie kiest, wordt het deel venster pijplijn expressie weer gegeven. Zorg ervoor dat u pijplijn functies met behulp van `'@{<expression>}'`in de teken reeks-interpolatie syntaxis opneemt, bijvoorbeeld:

```'@{pipeline().RunId}'```

Als uw para meter niet van het type teken reeks is, wordt u altijd weer gegeven met de opbouw functie voor de data flow-expressie. Hier kunt u elke gewenste expressie of letterlijke waarde invoeren die overeenkomt met het gegevens type van de para meter. Hieronder vindt u voor beelden van een gegevens stroom expressie en een letterlijke teken reeks uit de opbouw functie voor expressies:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Elke toewijzings gegevens stroom kan een combi natie van de para meters van de pijplijn-en data flow-expressie hebben. 

![Voor beeld van gegevens stroom parameters](media/data-flow/parameter-example.png "Voor beeld van gegevens stroom parameters")



## <a name="next-steps"></a>Volgende stappen
* [Activiteit gegevens stroom uitvoeren](control-flow-execute-data-flow-activity.md)
* [Controle stroom expressies](control-flow-expression-language-functions.md)
