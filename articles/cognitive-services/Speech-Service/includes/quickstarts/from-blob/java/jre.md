---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: b58ca353bffb99b21b1049dd23620f575bde687b
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668635"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programmming-language-java)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md?pivots=programmming-language-java)
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Een bron bestand uploaden naar een Azure-Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)


## <a name="open-your-project-in-eclipse"></a>Open uw project in eclips

De eerste stap is om ervoor te zorgen dat uw project in eclips is geopend.

1. Eclipse starten
2. Laad uw project en open `Main.java`.

## <a name="add-a-reference-to-gson"></a>Een verwijzing naar Gson toevoegen
In deze Quick Start maakt u gebruik van een externe JSON-serialisatiefunctie/deserialisatie. Voor Java hebben we gekozen voor [Gson](https://github.com/google/gson).

Open pom. XML en voeg de volgende verwijzing toe.

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code

Laten we een code toevoegen die als een skelet voor het project werkt.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON-wrappers

Als de aanvragen van de REST API in JSON-indeling worden uitgevoerd en de resultaten worden geretourneerd in JSON, kunnen ze met alleen teken reeksen communiceren, maar dit wordt niet aanbevolen.
Om de aanvragen en reacties gemakkelijker te kunnen beheren, declareren we een aantal klassen die moeten worden gebruikt voor het serialiseren/deserialiseren van de JSON.

Plaats de declaraties vóór `Main`.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Een HTTP-client maken en configureren
Het eerste wat u nodig hebt, is een HTTP-client waarvoor een juiste basis-URL en verificatieset is ingesteld.
Voeg deze code in `Main` [!code-javain [](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Een transcriptie-aanvraag genereren
Vervolgens genereren we de transcriptie-aanvraag. Voeg deze code toe aan `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>De aanvraag verzenden en de status controleren
Nu gaan we de aanvraag verzenden naar de speech-service en de eerste respons code controleren. Deze antwoord code geeft eenvoudigweg aan of de service de aanvraag heeft ontvangen. De service retourneert een URL in de antwoord headers die de locatie is waar de transcriptie-status wordt opgeslagen.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Wacht tot de transcriptie is voltooid
Omdat de transcriptie asynchroon wordt verwerkt, moeten we elke regel vaak de status van de service controleren. We controleren elke vijf seconden.

We kunnen de status controleren door de inhoud op te halen bij de URL die we hebben ontvangen toen de aanvraag werd verzonden. Wanneer de inhoud weer wordt opgehaald, deserialiseren we deze in een van onze helperklasse om gemakkelijker te kunnen communiceren met.

Dit is de polling code met status weergave voor alles, behalve een geslaagde voltooiing, we doen dat nu.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>De transcriptie-resultaten weer geven
Zodra de service de transcriptie heeft voltooid, worden de resultaten opgeslagen in een andere URL die we vanaf het status antwoord kunnen ontvangen.

De inhoud van de URL wordt gedownload, de JSON gedeserialiseerd en de resultaten door lopen die worden afgedrukt op de weergave tekst.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Controleer uw code
Op dit moment moet uw code er als volgt uitzien: (er zijn enkele opmerkingen aan deze versie toegevoegd) [! code-Java [] (~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Nu bent u klaar om uw app te bouwen en de spraak herkenning te testen met behulp van de speech-service.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
