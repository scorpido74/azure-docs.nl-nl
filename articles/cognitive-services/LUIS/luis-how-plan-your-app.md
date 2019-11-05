---
title: Uw app plannen-LUIS
titleSuffix: Azure Cognitive Services
description: Betrek relevante app-intenties en entiteiten en maak vervolgens uw toepassings abonnementen in Language Understanding intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b5e5df111b81cb60b6d194be190421bdb5ce2683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467695"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Uw LUIS-app-schema plannen met onderwerp domein en gegevens extractie

Een LUIS-app-schema bevat intents en entiteiten die relevant zijn voor uw onderwerps domein. De intenties classificeren de gebruiker uitingen en de entiteiten halen gegevens op uit de uitingen van de gebruiker. 

## <a name="identify-your-domain"></a>Uw domein identificeren

Een LUIS-app is gecentreerd rond een specifiek domein onderwerp.  U kunt bijvoorbeeld een reis-app hebben die de boeking van tickets, vluchten, hotels en huur auto's uitvoert. Een andere app kan inhoud bevatten die betrekking heeft op de uitoefening, het bijhouden van de geschiktheid en het instellen van doelen. Het identificeren van het domein helpt u bij het vinden van woorden of zinsdelen die belang rijk zijn voor uw domein.

> [!TIP]
> LUIS biedt [vooraf gemaakte domeinen](luis-how-to-use-prebuilt-domains.md) voor veel algemene scenario's.
> Controleer of u een vooraf gebouwd domein kunt gebruiken als uitgangs punt voor uw app.

## <a name="identify-your-intents"></a>Uw intenties identificeren

Denk na over de [intenties](luis-concept-intent.md) die belang rijk zijn voor de taak van uw toepassing. 

Laten we het voor beeld van een reis-app bekijken, met functies voor het boeken van een vlucht en het weer geven van de gebruikers bestemming. U kunt de `BookFlight` en `GetWeather` intentie voor deze acties definiëren. 

In een complexere app met meer functies hebt u meer intenties en moet u ze zorgvuldig definiëren, zodat de intenties niet te specifiek zijn. `BookFlight` en `BookHotel` moeten bijvoorbeeld mogelijk afzonderlijke intenties zijn, maar `BookInternationalFlight` en `BookDomesticFlight` mogelijk te vergelijken.

> [!NOTE]
> Het is een best practice voor het gebruik van slechts zoveel intenties als u nodig hebt om de functies van uw app uit te voeren. Als u te veel intenties definieert, wordt het moeilijker voor LUIS om uitingen correct te classificeren. Als u te weinig definieert, zijn ze mogelijk zo algemeen dat ze elkaar overlappen.

Als u geen algemene gebruikers intentie wilt identificeren, voegt u alle voor beeld-gebruikers uitingen toe aan de geen intentie. Als uw app groeit tot meer intenties, kunt u deze later maken. 

## <a name="create-example-utterances-for-each-intent"></a>Voor beeld uitingen maken voor elke intentie

Wanneer u de intenties hebt bepaald, maakt u voor elke intentie 15 tot 30 voor beeld uitingen. Als u met wilt beginnen, hoeft u niet minder dan dit aantal te hebben of te veel uitingen voor elke intentie te maken. Elke utterance moet afwijken van de vorige utterance. Een goed voor deel in de uitingen omvat het totale aantal woorden, woorden, werkwoordsvormen en lees tekens. 

Raadpleeg [uitingen](luis-concept-utterance.md) voor meer informatie.

## <a name="identify-your-entities"></a>Uw entiteiten identificeren

In het voor beeld uitingen identificeert u de entiteiten die u wilt extra heren. Als u een vlucht wilt boeken, hebt u informatie nodig zoals de doel-, datum-, vlieg tickets-, ticket categorie-en reis klasse. Maak entiteiten voor deze gegevens typen en markeer vervolgens de [entiteiten](luis-concept-entity-types.md) in het voor beeld uitingen omdat ze belang rijk zijn om een intentie te bereiken. 

Wanneer u bepaalt welke entiteiten in uw app moeten worden gebruikt, moet u er ook voor zorgen dat er verschillende typen entiteiten zijn voor het vastleggen van relaties tussen typen objecten. [Entiteiten in Luis](luis-concept-entity-types.md) bieden meer details over de verschillende typen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de typische [ontwikkel cyclus](luis-concept-app-iteration.md).  