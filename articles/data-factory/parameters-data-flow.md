---
title: Gegevens stroom parameters van Azure Data Factory toewijzen
description: Meer informatie over het para meters van een toewijzings gegevens stroom van data factory-pijp lijnen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 0a1051d67bf45e96f82833ef8190008204cdc90b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387539"
---
# <a name="mapping-data-flow-parameters"></a>Gegevens stroom parameters toewijzen



Het toewijzen van gegevens stromen in Azure Data Factory ondersteunen het gebruik van para meters. U kunt para meters definiëren in de definitie van de gegevens stroom, die u vervolgens kunt gebruiken in uw expressies. De parameter waarden kunnen worden ingesteld door de aanroepende pijp lijn via de activiteit gegevens stroom uitvoeren. U hebt drie opties voor het instellen van de waarden in de gegevens stroom activiteit expressies:

* De expressie taal voor de controle stroom van de pijp lijn gebruiken om een dynamische waarde in te stellen
* De data flow-expressie taal gebruiken om een dynamische waarde in te stellen
* Een van de expressie taal gebruiken om een statische letterlijke waarde in te stellen

Gebruik deze mogelijkheid om uw gegevens te stroom, flexibel en herbruikbaar te maken. U kunt para meters en expressies voor gegevens stromen met deze para meters.

> [!NOTE]
> Als u de controle stroom expressies voor de pijp lijn wilt gebruiken, moet u de para meter voor de gegevens stroom van het type teken reeks zijn.

## <a name="create-parameters-in-mapping-data-flow"></a>Para meters maken in gegevens stroom toewijzen

Als u para meters wilt toevoegen aan uw gegevens stroom, klikt u op het lege deel van het canvas voor gegevens stromen om de algemene eigenschappen weer te geven. In het deel venster instellingen ziet u een tabblad met de naam para meters. Klik op de knop Nieuw om een nieuwe para meter te genereren. Voor elke para meter moet u een naam toewijzen, een type selecteren en optioneel een standaard waarde instellen.

![Data flow-para meters maken](media/data-flow/create-params.png "Data flow-para meters maken")

Para meters kunnen worden gebruikt in elke Data flow-expressie. Para meters beginnen met $ en zijn onveranderbaar. De lijst met beschik bare para meters kunt u vinden in de opbouw functie voor expressies op het tabblad para meters.

![Expressie voor de gegevens stroom parameter](media/data-flow/parameter-expression.png "Expressie voor de gegevens stroom parameter")

## <a name="use-parameters-in-your-data-flow"></a>Para meters gebruiken in uw gegevens stroom

* U kunt parameter waarden binnen uw transformatie expressies gebruiken. U vindt de lijst met para meters op het tabblad para meters in de opbouw functie voor expressies. ![Data flow-para meters gebruiken](media/data-flow/params9.png "Use data flow-para meters ")

* Para meters worden ook gebruikt voor het configureren van dynamische waarden voor de instellingen van de bron-en Sink-trans formatie. Wanneer u in Configureer bare velden klikt, wordt de koppeling ' dynamische contect toevoegen ' weer gegeven. Als u op deze knop klikt, wordt u naar de opbouw functie voor expressies geleid, waar u de para meters kunt gebruiken om dynamische waarden te gebruiken. ![Dynamische inhoud van gegevens stroom](media/data-flow/params6.png "Ddynamische inhoud ATA flow ")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>Gegevens stroom parameters van de toewijzing instellen vanuit de pijp lijn

Wanneer u de gegevens stroom hebt gemaakt met para meters, kunt u deze uitvoeren vanuit een pijp lijn met de activiteit gegevens stroom uitvoeren. Nadat u de activiteit aan uw pijplijn doek hebt toegevoegd, worden de beschik bare gegevensstroom parameters weer gegeven op het tabblad para meters van de activiteit.

![Een para meter voor een gegevens stroom instellen](media/data-flow/parameter-assign.png "Een para meter voor een gegevens stroom instellen")

Als uw parameter gegevens type teken reeks is en u op het tekstvak klikt om parameter waarden in te stellen, kunt u een pijp lijn of een gegevensstroom expressie opgeven. Als u pijplijn expressie kiest, wordt het deel venster pijplijn expressie weer gegeven. Zorg ervoor dat u pijplijn functies in de syntaxis van de teken reeks interpolatie opneemt met behulp van `'@{<expression>}'`, bijvoorbeeld:

```'@{pipeline().RunId}'```

Als uw para meter niet van het type teken reeks is, wordt u altijd weer gegeven met de opbouw functie voor de data flow-expressie. Hier kunt u elke gewenste expressie of letterlijke waarde invoeren die overeenkomt met het gegevens type van de para meter. Hieronder vindt u voor beelden van een gegevens stroom expressie en een letterlijke teken reeks uit de opbouw functie voor expressies:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Elke toewijzings gegevens stroom kan een combi natie van de para meters van de pijplijn-en data flow-expressie hebben. 

![Voor beeld van gegevens stroom parameters](media/data-flow/parameter-example.png "Voor beeld van gegevens stroom parameters")



## <a name="next-steps"></a>Volgende stappen
* [Activiteit gegevens stroom uitvoeren](control-flow-execute-data-flow-activity.md)
* [Controle stroom expressies](control-flow-expression-language-functions.md)
