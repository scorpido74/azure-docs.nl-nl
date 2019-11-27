---
title: Uw app plannen-LUIS
titleSuffix: Azure Cognitive Services
description: Overzicht van relevante app intenties en entiteiten en maak vervolgens uw toepassing plannen in Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 6a155f4c43da03ccdc40d289742918973aa6da7b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326782"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Uw LUIS-app-schema plannen met onderwerp domein en gegevens extractie

Een LUIS-app-schema bevat [intents](luis-glossary.md#intent) en [entiteiten](luis-glossary.md#entity) die relevant zijn voor uw onderwerps [domein](luis-glossary.md#domain). De intenties classificeren de gebruiker [uitingen](luis-glossary.md#utterance)en de entiteiten halen gegevens op uit de uitingen van de gebruiker.

## <a name="identify-your-domain"></a>Identificeren van uw domein

Een LUIS-app wordt gecentreerd rond een onderwerps domein. U kunt bijvoorbeeld een reis-app hebben die de boeking van tickets, vluchten, hotels en huur auto's afhandelt. Een andere app bieden mogelijk inhoud met betrekking tot te oefenen, geschiktheid inspanningen bijhouden en doelen instellen. Het identificeren van het domein helpt u bij het vinden van woorden of zinsdelen die relevant zijn voor uw domein.

> [!TIP]
> LUIS biedt [vooraf gemaakte domeinen](luis-how-to-use-prebuilt-domains.md) voor veel algemene scenario's. Controleer als u een vooraf gedefinieerde domein als uitgangspunt voor uw app gebruiken kunt.

## <a name="identify-your-intents"></a>Uw intenties identificeren

Denk na over de [intenties](luis-concept-intent.md) die belang rijk zijn voor de taak van uw toepassing.

We gaan het voorbeeld van een reis-app, met functies voor een vlucht reserveren en controleert u het weer op de bestemming van de gebruiker. U kunt de `BookFlight` en `GetWeather` intentie voor deze acties definiëren.

In een complexere app met meer functies hebt u meer intenties en moet u ze zorgvuldig definiëren, zodat de intenties niet te specifiek zijn. `BookFlight` en `BookHotel` moeten bijvoorbeeld mogelijk afzonderlijke intenties zijn, maar `BookInternationalFlight` en `BookDomesticFlight` mogelijk te vergelijken.

> [!NOTE]
> Het is een best practice u alleen zo veel intents als nodig is voor de functies van uw app uitvoeren. Als u te veel intents definieert, wordt het moeilijker voor LUIS uitingen correct classificeren. Als u te weinig definieert, zijn ze mogelijk zo algemeen dat ze elkaar overlappen.

Als u geen algemene gebruikers intentie wilt identificeren, voegt u alle voor beeld-gebruikers uitingen toe aan de `None` intentie. Als uw app groeit tot meer intenties, kunt u deze later maken.

## <a name="create-example-utterances-for-each-intent"></a>Voorbeeld-uitingen voor elk doel maken

Voorkom dat u begint met het maken van te veel uitingen voor elke intentie. Nadat u de intenties hebt bepaald, maakt u 15 tot 30 voorbeeld uitingen per intentie. Elke utterance moet afwijken van de eerder door gegeven uitingen. Een goed voor deel in uitingen omvat het totale aantal woorden, woorden, werkwoordsvormen en lees tekens.

Zie goed te weten [uitingen voor Luis-apps](luis-concept-utterance.md)voor meer informatie.

## <a name="identify-your-entities"></a>Uw entiteiten identificeren

In de voorbeeld-uitingen identificeren de entiteiten die u wilt dat opgehaald. Als u een vlucht wilt boeken, hebt u informatie nodig zoals de doel-, datum-, vlieg tickets-, ticket categorie-en reis klasse. Maak entiteiten voor deze gegevens typen en markeer vervolgens de [entiteiten](luis-concept-entity-types.md) in het voor beeld uitingen. Entiteiten zijn belang rijk voor het uitvoeren van een intentie.

Wanneer u bepaalt welke entiteiten in uw app moeten worden gebruikt, moet u er ook voor zorgen dat er verschillende typen entiteiten zijn voor het vastleggen van relaties tussen object typen. [Entiteiten in Luis](luis-concept-entity-types.md) bieden meer details over de verschillende typen.

> [!TIP]
> LUIS biedt [vooraf gemaakte entiteiten](luis-prebuilt-entities.md) voor veelvoorkomende, gespreks gebruikers scenario's. Overweeg het gebruik van vooraf gemaakte entiteiten als uitgangs punt voor de ontwikkeling van uw toepassing.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Learning the LUIS Development lifecylce](luis-concept-app-iteration.md)

