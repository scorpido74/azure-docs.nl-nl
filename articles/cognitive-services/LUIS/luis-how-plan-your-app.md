---
title: Uw app plannen - LUIS
description: Geef een overzicht van relevante app-intenties en -entiteiten en maak vervolgens uw toepassingsplannen in Luis (Language Understanding Intelligent Services).
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382310"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Uw LUIS-app-schema plannen met onderwerpdomein en gegevensextractie

Een LUIS-app-schema bevat [intenties](luis-glossary.md#intent) en [entiteiten die](luis-glossary.md#entity) relevant zijn voor uw [onderwerpdomein.](luis-glossary.md#domain) De intenties classificeren [gebruikersuitingen](luis-glossary.md#utterance)en de entiteiten extraheren gegevens uit de gebruikersuitingen.

## <a name="identify-your-domain"></a>Uw domein identificeren

Een LUIS-app is gecentreerd rond een onderwerpdomein. U bijvoorbeeld een reis-app hebben die het boeken van tickets, vluchten, hotels en huurauto's afhandelt. Een andere app kan inhoud bieden met betrekking tot het uitoefenen, het bijhouden van fitness-inspanningen en het stellen van doelen. Als u het domein identificeert, u woorden of zinnen vinden die relevant zijn voor uw domein.

> [!TIP]
> LUIS biedt [vooraf gebouwde domeinen](luis-how-to-use-prebuilt-domains.md) voor veelvoorkomende scenario's. Controleer of u een vooraf gebouwd domein gebruiken als uitgangspunt voor uw app.

## <a name="identify-your-intents"></a>Identificeer uw intenties

Denk na over de [intenties](luis-concept-intent.md) die belangrijk zijn voor de taak van uw toepassing.

Laten we het voorbeeld van een reis-app nemen, met functies om een vlucht te boeken en het weer op de bestemming van de gebruiker te controleren. U `BookFlight` de `GetWeather` intenties en intenties voor deze acties definiëren.

In een complexere app met meer functies hebt u meer intenties en moet u ze zorgvuldig definiëren, zodat de intenties niet te specifiek zijn. Bijvoorbeeld, `BookFlight` en `BookHotel` kan nodig zijn om `BookInternationalFlight` afzonderlijke `BookDomesticFlight` intents, maar en kan te vergelijkbaar zijn.

> [!NOTE]
> Het is een aanbevolen manier om alleen zoveel intenties te gebruiken als nodig is om de functies van uw app uit te voeren. Als u te veel intents definieert, wordt het moeilijker voor LUIS om uitingen correct te classificeren. Als u te weinig definieert, kunnen ze zo algemeen zijn dat ze elkaar overlappen.

Als u de algemene gebruikersintentie niet hoeft te identificeren, voegt `None` u alle voorbeelduitingen van gebruikers toe aan de intentie. Als uw app uitgroeit tot meer intents nodig heeft, u deze later maken.

## <a name="create-example-utterances-for-each-intent"></a>Voorbeelduitingen maken voor elke intentie

Om te beginnen, vermijd het maken van te veel uitingen voor elke intentie. Zodra u de intenties hebt bepaald, maakt u 15 tot 30 voorbeelduitingen per intentie. Elke utterance moet afwijken van de eerder verstrekte uitingen. Een goede verscheidenheid in uitingen zijn algemene woordtelling, woordkeuze, werkwoordstijd en [interpunctie.](luis-reference-application-settings.md#punctuation-normalization)

Zie voor meer informatie [goede uitingen voor LUIS-apps begrijpen.](luis-concept-utterance.md)

## <a name="identify-your-entities"></a>Uw entiteiten identificeren

Identificeer in de voorbeelduitingen de entiteiten die u wilt extraheren. Als u een vlucht wilt boeken, hebt u informatie nodig zoals de bestemming, datum, luchtvaartmaatschappij, ticketcategorie en reisklasse. Maak entiteiten voor deze gegevenstypen en markeer vervolgens de [entiteiten](luis-concept-entity-types.md) in de voorbeelduitingen. Entiteiten zijn belangrijk voor het bereiken van een intentie.

Wanneer u bepaalt welke entiteiten u in uw app wilt gebruiken, moet u er rekening mee houden dat er verschillende typen entiteiten zijn voor het vastleggen van relaties tussen objecttypen. [Entiteiten in LUIS](luis-concept-entity-types.md) geven meer details over de verschillende typen.

> [!TIP]
> LUIS biedt [vooraf gebouwde entiteiten](luis-prebuilt-entities.md) voor algemene, gespreksgebruikersscenario's. Overweeg om vooraf gebouwde entiteiten te gebruiken als uitgangspunt voor uw toepassingsontwikkeling.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Het leren van de LUIS ontwikkeling lifecylce](luis-concept-app-iteration.md)

