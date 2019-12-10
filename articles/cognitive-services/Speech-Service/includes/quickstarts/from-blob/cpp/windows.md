---
title: 'Quick Start: spraak herkennen die zijn opgeslagen in C++ Blob Storage,-Speech Service'
titleSuffix: Azure Cognitive Services
description: Nader te bepalen
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 54c6f920b581a0bbd00910a3b3ddeebecdbb595f
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74954923"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Een bron bestand uploaden naar een Azure-Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Uw project openen in Visual Studio

De eerste stap is om ervoor te zorgen dat uw project in Visual Studio is geopend.

1. Start Visual Studio 2019.
2. Laad uw project en open `helloworld.cpp`.

## <a name="add-a-references"></a>Een verwijzing toevoegen

Om onze code ontwikkeling te versnellen, gebruiken we een aantal externe onderdelen:
* [Cpp rest SDK](https://github.com/microsoft/cpprestsdk) Een client bibliotheek voor het aanbrengen van REST-aanroepen naar een REST-service.
* [nlohmann/JSON](https://github.com/nlohmann/json) Handige JSON-parsering/serialisatie/deserialisatie bibliotheek.

Beide kunnen worden geïnstalleerd met behulp van [vcpkg](https://github.com/Microsoft/vcpkg/).

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code

Laten we een code toevoegen die als een skelet voor het project werkt.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]
(U moet de waarden van `YourSubscriptionKey`, `YourServiceRegion`en `YourFileUrl` vervangen door uw eigen waarden.)

## <a name="json-wrappers"></a>JSON-wrappers

Als de aanvragen van de REST API in JSON-indeling worden uitgevoerd en de resultaten worden geretourneerd in JSON, kunnen ze met alleen teken reeksen communiceren, maar dit wordt niet aanbevolen.
Om ervoor te zorgen dat de aanvragen en antwoorden gemakkelijker te beheren zijn, declareren we een aantal klassen die moeten worden gebruikt voor het serialiseren/deserialiseren van de JSON en een aantal methoden om nlohmann/JSON te helpen.

Plaats de declaraties vóór `recognizeSpeech`.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Een HTTP-client maken en configureren
Het eerste wat u nodig hebt, is een HTTP-client waarvoor een juiste basis-URL en verificatieset is ingesteld.
Deze code invoegen in `recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>Een transcriptie-aanvraag genereren
Vervolgens genereren we de transcriptie-aanvraag. Voeg deze code toe aan `recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>De aanvraag verzenden en de status controleren
Nu gaan we de aanvraag verzenden naar de speech-service en de eerste respons code controleren. Deze antwoord code geeft eenvoudigweg aan of de service de aanvraag heeft ontvangen. De service retourneert een URL in de antwoord headers die de locatie is waar de transcriptie-status wordt opgeslagen.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>Wacht tot de transcriptie is voltooid
Omdat de transcriptie asynchroon wordt verwerkt, moeten we elke regel vaak de status van de service controleren. We controleren elke vijf seconden.

We kunnen de status controleren door de inhoud op te halen bij de URL die we hebben ontvangen toen de aanvraag werd verzonden. Wanneer de inhoud weer wordt opgehaald, deserialiseren we deze in een van onze helperklasse om gemakkelijker te kunnen communiceren met.

Dit is de polling code met status weergave voor alles, behalve een geslaagde voltooiing, we doen dat nu.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>De transcriptie-resultaten weer geven
Zodra de service de transcriptie heeft voltooid, worden de resultaten opgeslagen in een andere URL die we vanaf het status antwoord kunnen ontvangen.

De inhoud van de URL wordt gedownload, de JSON gedeserialiseerd en de resultaten door lopen die worden afgedrukt op de weergave tekst.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>Controleer uw code
Op dit moment moet uw code er als volgt uitzien: (er zijn enkele opmerkingen aan deze versie toegevoegd)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Nu bent u klaar om uw app te bouwen en de spraak herkenning te testen met behulp van de speech-service.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
