---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 20dfc49acdaa30bea6f0652640e007c16f08c572
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925597"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u:

> [!div class="checklist"]
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programmming-language-cpp)
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Een bronbestand uploaden naar een Azure-blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>Uw project openen in Visual Studio

De eerste stap is ervoor te zorgen dat u uw project open hebt in Visual Studio.

1. Start Visual Studio 2019.
2. Laad uw project `helloworld.cpp`en open .

## <a name="add-a-references"></a>Een referenties toevoegen

Om onze codeontwikkeling te versnellen, gebruiken we een paar externe componenten:
* [CPP Rest SDK](https://github.com/microsoft/cpprestsdk) Een clientbibliotheek voor het maken van REST-gesprekken naar een REST-service.
* [nlohmann/json](https://github.com/nlohmann/json) Handige JSON Parsing / Serialization / Deserialization library.

Beide kunnen worden geïnstalleerd met behulp van [vcpkg](https://github.com/Microsoft/vcpkg/).

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>Begin met een soort boilerplate-code

Laten we wat code toevoegen die werkt als een skelet voor ons project.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON Wikkels

Aangezien de REST API's aanvragen in JSON-formaat aannemen en ook resultaten in JSON retourneren, konden we met hen communiceren met alleen tekenreeksen, maar dat wordt niet aanbevolen.
Om de aanvragen en reacties gemakkelijker te beheren, verklaren we een paar klassen te gebruiken voor het serialiseren / deserialiseren van de JSON en een aantal methoden om nlohmann / json te helpen.

Ga je gang en `recognizeSpeech` zet hun verklaringen voor .
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Een Http-client maken en configureren
Het eerste wat we nodig hebben is een Http-client met een juiste basis-URL en verificatieset.
Deze code invoegen in`recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>Een transcriptieaanvraag genereren
Vervolgens genereren we het transcriptieverzoek. Deze code toevoegen aan`recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>Stuur het verzoek en controleer de status
Nu plaatsen we het verzoek bij de Spraakservice en controleren we de initiële reactiecode. Deze antwoordcode geeft eenvoudig aan of de service het verzoek heeft ontvangen. De service retourneert een url in de antwoordkoppen, dat is de locatie waar de transcriptiestatus wordt opgeslagen.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>Wachten tot de transcriptie is voltooid
Aangezien de service de transcriptie asynchroon verwerkt, moeten we om de zoveel tijd peilen voor de status ervan. We controleren elke 5 seconden.

We kunnen de status controleren door de inhoud op te halen op de Url die we kregen toen het verzoek werd geplaatst. Wanneer we de inhoud terugkrijgen, deserialiseren we deze in een van onze helperklassen om het gemakkelijker te maken om ermee om te gaan.

Hier is de polling code met status weergave voor alles behalve een succesvolle voltooiing, zullen we dat nu doen.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>De transcriptieresultaten weergeven
Zodra de service de transcriptie heeft voltooid, worden de resultaten opgeslagen in een andere url die we kunnen krijgen van de statusrespons.

We downloaden de inhoud van die URL, deserialiseren de JSON en doorlopen de resultaten die de weergavetekst afdrukken.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>Controleer uw code
Op dit punt moet uw code er als volgt uitzien: (We hebben een aantal opmerkingen toegevoegd aan deze versie)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Nu bent u klaar om uw app te bouwen en onze spraakherkenning te testen met behulp van de Spraakservice.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
