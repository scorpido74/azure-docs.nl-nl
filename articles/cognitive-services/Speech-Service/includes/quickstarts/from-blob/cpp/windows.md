---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: trbye
ms.openlocfilehash: e1d2493474736ebbcdb54aeb697716e1dc7d6ee6
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377011"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programmming-language-cpp)
> * [Een resource voor de Azure-spraakservice maken](../../../../overview.md#try-the-speech-service-for-free)
> * [Een bronbestand uploaden naar een Azure-blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>Open uw project in Visual Studio

De eerste stap is het openen van uw project in Visual Studio.

1. Start Visual Studio 2019.
2. Laad uw project en open `helloworld.cpp`.

## <a name="add-a-references"></a>Referenties toevoegen

Om de ontwikkeling van onze code te bespoedigen, gebruiken we enkele externe componenten:
* [CPP REST SDK](https://github.com/microsoft/cpprestsdk) Een clientbibliotheek om REST-aanroepen te doen aan een REST-service.
* [nlohmann/JSON](https://github.com/nlohmann/json) Handige bibliotheek voor JSON-parsering/serialisatie/deserialisatie.

Beide kunnen worden geïnstalleerd met [vcpkg](https://github.com/Microsoft/vcpkg/).

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>Beginnen met standaardcode

We gaan wat code toevoegen die als basis voor het project gaat dienen.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON-wrappers

Aangezien de aanvragen van de REST API in JSON-indeling worden uitgevoerd en de resultaten in JSON worden geretourneerd, is het mogelijk om met alleen tekenreeksen te communiceren, maar dit wordt niet aanbevolen.
Om ervoor te zorgen dat de aanvragen en antwoorden gemakkelijker te gebruiken zijn, declareren we een aantal klassen die moeten worden gebruikt voor het serialiseren/deserialiseren van de JSON en enkele methodes ter ondersteuning van nlohmann/json.

Ga verder en plaats de declaraties voor `recognizeSpeech` .
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Een HTTP-client maken en configureren
Het eerste wat u nodig hebt, is een HTTP-client waarvoor een juiste basis-URL en verificatieset is ingesteld.
Voeg deze code toe in `recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>Een transcriptie-aanvraag genereren
Genereeer vervolgens de transcriptie-aanvraag. Voeg deze code toe aan `recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>De aanvraag verzenden en de status controleren
Nu gaan we de aanvraag naar de Speech-service verzenden en de eerste responscode controleren. Deze responscode geeft simpelweg aan of de service de aanvraag heeft ontvangen. De service retourneert in de antwoordheaders een URL met de locatie waar de transcriptiestatus wordt opgeslagen.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>Wacht totdat de transcriptie is voltooid
Omdat de transcriptie asynchroon wordt verwerkt, moeten we regelmatig de status van de service controleren. We controleren deze elke vijf seconden.

We kunnen de status controleren door de inhoud op te halen via de URL die we hebben ontvangen toen de aanvraag werd verzonden. Wanneer de inhoud is opgehaald, deserialiseren we deze in een van onze helperklassen om gemakkelijker te kunnen communiceren.

Dit is de polling-code met statusweergave voor alles, behalve een geslaagde voltooiing, dat gaan we nu doen.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>De transcriptieresultaten weergeven
Zodra de service de transcriptie heeft voltooid, worden de resultaten opgeslagen in een andere URL die we via het statusantwoord ontvangen.

We downloaden de inhoud van die URL, deserialiseren de JSON en doorlopen de resultaten terwijl we de weergavetekst afdrukken.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>Uw code controleren
Op dit punt moet uw code er als volgt uitzien: (Er zijn enkele opmerkingen toegevoegd aan deze versie.)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>De app bouwen en uitvoeren

U bent nu klaar om uw app te bouwen en de spraakherkenning te testen met behulp van de Speech-service.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
