---
title: Gegevens stroom script toewijzen
description: Overzicht van de script code voor de gegevens stroom van Data Factory-behind-taal
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/10/2019
ms.openlocfilehash: d861a4355158dfe18ac3aa40a7f98dc11ebda90b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930260"
---
# <a name="data-flow-script-dfs"></a>Gegevens stroom script (DFS)

Data flow script (DFS) is de onderliggende meta gegevens, vergelijkbaar met een coderings taal, die wordt gebruikt voor het uitvoeren van de trans formaties die zijn opgenomen in een toewijzings gegevens stroom. Elke trans formatie wordt vertegenwoordigd door een reeks eigenschappen die de benodigde informatie biedt om de taak correct uit te voeren. Het script is zichtbaar en kan worden bewerkt vanuit ADF door te klikken op de knop script in het bovenste lint van de gebruikers interface van de browser.

![Script knop](media/data-flow/scriptbutton.png "Script knop")

`allowSchemaDrift: true,` in een bron transformatie vertelt de service bijvoorbeeld dat alle kolommen uit de bron-gegevensset in de gegevens stroom moeten worden opgenomen, zelfs als deze niet zijn opgenomen in de schema projectie.

## <a name="use-cases"></a>Use cases
De DFS wordt automatisch geproduceerd door de gebruikers interface. U kunt op de knop script klikken om het script weer te geven en aan te passen. U kunt ook scripts maken buiten de ADF-gebruikers interface en vervolgens door geven aan de Power shell-cmdlet. Wanneer u fouten opspoort in complexe gegevens stromen, is het mogelijk dat u de script code achtereen kunt scannen in plaats van de UI-grafiek weergave van uw stromen te scannen.

Hier volgen enkele voor beelden van use cases:
- Een programmatische productie maakt van een groot aantal gegevens stromen die tamelijk vergelijkbaar zijn, d.w.z. het afstempelen van gegevens stromen.
- Complexe expressies die moeilijk te beheren zijn in de gebruikers interface of die het gevolg zijn van validatie problemen.
- Fout opsporing en betere inzicht in verschillende fouten die tijdens de uitvoering worden geretourneerd.

Wanneer u een gegevensstroom script bouwt voor gebruik met Power shell of een API, moet u de opgemaakte tekst in één regel samen vouwen. U kunt tabs en nieuwe regels gebruiken als escape tekens. Maar de tekst moet zijn opgemaakt om in een JSON-eigenschap te passen. Er bevindt zich een knop in de gebruikers interface van de Script Editor aan de onderkant waarmee het script wordt opgemaakt als één regel voor u.

![Knop kopiëren](media/data-flow/copybutton.png "knop Kopiëren")

## <a name="how-to-add-transforms"></a>Trans formaties toevoegen
Voor het toevoegen van trans formaties zijn drie basis stappen vereist: het toevoegen van de kern transformatie gegevens, het omleiden van de invoer stroom en het omleiden van de uitvoer stroom. Dit kan eenvoudig worden weer gegeven in een voor beeld.
Stel dat we beginnen met een eenvoudige bron om gegevens stroom te sinken, zoals in het volgende:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Als we besluiten een afgeleide trans formatie toe te voegen, moet u eerst de tekst van de kern transformatie maken, met een eenvoudige expressie om een nieuwe hoofd kolom met de naam `upperCaseTitle`toe te voegen:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Daarna maken we de bestaande DFS en voegen we de trans formatie toe:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

De inkomende stroom wordt nu omgeleid door de trans formatie te identificeren waarnaar de nieuwe trans formatie moet worden verzonden (in dit geval `source1`) en het kopiëren van de naam van de stroom naar de nieuwe trans formatie:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Ten slotte wordt de trans formatie geïdentificeerd die na deze nieuwe trans formatie moet worden uitgevoerd en wordt de invoer stroom (in dit geval `sink1`) vervangen door de naam van de uitvoer stroom van de nieuwe trans formatie:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Basis beginselen van DFS
De DFS bestaat uit een reeks gekoppelde trans formaties, waaronder bronnen, sinks en verschillende andere, waarmee nieuwe kolommen kunnen worden toegevoegd, gegevens worden gefilterd, gegevens kunnen worden samengevoegd en nog veel meer. Normaal gesp roken wordt het script met een of meer bronnen gevolgd door veel trans formaties en eindigend met een of meer Sinks.

Bronnen hebben allemaal dezelfde basis constructie:
```
source(
  source properties
) ~> source_name
```

Zo zou een eenvoudige bron met drie kolommen (movieId, title, genres) er als volgt uitzien:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Alle trans formaties, behalve bronnen, hebben dezelfde basis constructie:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Een eenvoudige afleiding van een trans formatie die een kolom (titel) maakt en overschrijft met een hoofd versie is als volgt:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

En een Sink zonder schema zou gewoon zijn:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="next-steps"></a>Volgende stappen

Gegevens stromen verkennen door te beginnen met het [artikel overzicht van gegevens stromen](concepts-data-flow-overview.md)
