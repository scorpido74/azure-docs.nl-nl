---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 959e035dc632e84595bdb54c7202f19991697fdb
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78924719"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Stel uw ontwikkel omgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=vs&pivots=programmming-language-csharp)
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Een bron bestand uploaden naar een Azure-Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>Uw project openen in Visual Studio

De eerste stap is om ervoor te zorgen dat uw project in Visual Studio is geopend.

1. Start Visual Studio 2019.
2. Laad uw project en open `Program.cs`.

## <a name="add-a-reference-to-newtonsoftjson"></a>Een verwijzing naar Newton soft. json toevoegen

1. Klik in de Solution Explorer met de rechter muisknop op het project **HelloWorld** en selecteer vervolgens **NuGet-pakketten beheren** om de NuGet-pakket manager weer te geven.

1. Zoek in de rechter bovenhoek de vervolg keuzelijst **pakket bron** en zorg ervoor dat **`nuget.org`** is geselecteerd.

1. Selecteer in de linkerbovenhoek de optie **Bladeren**.

1. Typ *Newton soft. json* in het zoekvak en selecteer **Enter**.

1. Selecteer in de zoek resultaten het pakket [**Newton soft. json**](https://www.nuget.org/packages/Newtonsoft.Json) en selecteer vervolgens **installeren** om de nieuwste stabiele versie te installeren.

1. Accepteer alle overeenkomsten en licenties om de installatie te starten.

   Nadat het pakket is geïnstalleerd, wordt er een bevestiging weer gegeven in het console venster van **Package Manager** .

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code

Laten we een code toevoegen die als een skelet voor het project werkt.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-43,138,277)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON-wrappers

Als de aanvragen van de REST API in JSON-indeling worden uitgevoerd en de resultaten worden geretourneerd in JSON, kunnen ze met alleen teken reeksen communiceren, maar dit wordt niet aanbevolen.
Om de aanvragen en reacties gemakkelijker te kunnen beheren, declareren we een aantal klassen die moeten worden gebruikt voor het serialiseren/deserialiseren van de JSON.

Ga verder en plaats de aangiften na `TranscribeAsync`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=140-276)]

## <a name="create-and-configure-an-http-client"></a>Een HTTP-client maken en configureren
Het eerste wat u nodig hebt, is een HTTP-client waarvoor een juiste basis-URL en verificatieset is ingesteld.
Voeg deze code in `TranscribeAsync` [!code-csharpin [](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=46-50)]

## <a name="generate-a-transcription-request"></a>Een transcriptie-aanvraag genereren
Vervolgens genereren we de transcriptie-aanvraag. Voeg deze code toe aan `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=52-57)]

## <a name="send-the-request-and-check-its-status"></a>De aanvraag verzenden en de status controleren
Nu gaan we de aanvraag verzenden naar de speech-service en de eerste respons code controleren. Deze antwoord code geeft eenvoudigweg aan of de service de aanvraag heeft ontvangen. De service retourneert een URL in de antwoord headers die de locatie is waar de transcriptie-status wordt opgeslagen.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=59-70)]

## <a name="wait-for-the-transcription-to-complete"></a>Wacht tot de transcriptie is voltooid
Omdat de transcriptie asynchroon wordt verwerkt, moeten we elke regel vaak de status van de service controleren. We controleren elke vijf seconden.

We kunnen de status controleren door de inhoud op te halen bij de URL die we hebben ontvangen toen de aanvraag werd verzonden. Wanneer de inhoud weer wordt opgehaald, deserialiseren we deze in een van onze helperklasse om gemakkelijker te kunnen communiceren met.

Dit is de polling code met status weergave voor alles, behalve een geslaagde voltooiing, we doen dat nu.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=72-106,121-137)]

## <a name="display-the-transcription-results"></a>De transcriptie-resultaten weer geven
Zodra de service de transcriptie heeft voltooid, worden de resultaten opgeslagen in een andere URL die we vanaf het status antwoord kunnen ontvangen.

Hier maken we een aanvraag om deze resultaten te downloaden naar een tijdelijk bestand voordat ze worden gelezen en gedeserialiseerd.
Zodra de resultaten zijn geladen, kunnen ze worden afgedrukt op de-console.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=107-120)]

## <a name="check-your-code"></a>Controleer uw code
Op dit moment moet uw code er als volgt uitzien: (er zijn enkele opmerkingen aan deze versie toegevoegd) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-277)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Nu bent u klaar om uw app te bouwen en de spraak herkenning te testen met behulp van de speech-service.

1. **De code compileren** : Kies in de menu balk van Visual Studio **Build** > **Build-oplossing**.
2. **Start uw app** -vanuit de menu balk, kies **fout opsporing** > **fout opsporing starten** of druk op **F5**.
3. **Herkenning starten** : u wordt gevraagd om een woord groep in het Engels te spreken. Uw spraak wordt verzonden naar de spraak service, getranscribeerd als tekst en weer gegeven in de-console.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
