---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: trbye
ms.openlocfilehash: 2a8deaa76c02e86c9304c2221c0617041ae2e138
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376345"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programmming-language-java)
> * [Een resource voor de Azure-spraakservice maken](../../../../overview.md#try-the-speech-service-for-free)
> * [Een bronbestand uploaden naar een Azure-blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)


## <a name="open-your-project-in-eclipse"></a>Uw project in Eclipse openen

De eerste stap is om ervoor te zorgen dat uw project open is in Eclipse.

1. Eclipse starten
2. Laad uw project en open `Main.java`.

## <a name="add-a-reference-to-gson"></a>Een referentie naar Gson toevoegen
In deze quickstart maken we gebruik van een JSON-serializer/-deserializer. Voor Java hebben we gekozen voor [Gson](https://github.com/google/gson).

Open pom.xml en voeg de volgende verwijzing toe.

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Beginnen met standaardcode

We gaan wat code toevoegen die als basis voor het project gaat dienen.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON-wrappers

Aangezien de aanvragen van de REST API in JSON-indeling worden uitgevoerd en de resultaten in JSON worden geretourneerd, is het mogelijk om met alleen tekenreeksen te communiceren, maar dit wordt niet aanbevolen.
Om ervoor te zorgen dat de aanvragen en antwoorden gemakkelijker te gebruiken zijn, declareren we een aantal klassen die moeten worden gebruikt voor het serialiseren/deserialiseren van de JSON.

Ga verder en plaats de declaraties voor `Main`.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Een HTTP-client maken en configureren
Het eerste wat u nodig hebt, is een HTTP-client waarvoor een juiste basis-URL en verificatieset is ingesteld.
Voeg deze code toe in `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Een transcriptie-aanvraag genereren
Genereeer vervolgens de transcriptie-aanvraag. Voeg deze code toe aan `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>De aanvraag verzenden en de status controleren
Nu gaan we de aanvraag naar de Speech-service verzenden en de eerste responscode controleren. Deze responscode geeft simpelweg aan of de service de aanvraag heeft ontvangen. De service retourneert in de antwoordheaders een URL met de locatie waar de transcriptiestatus wordt opgeslagen.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Wacht totdat de transcriptie is voltooid
Omdat de transcriptie asynchroon wordt verwerkt, moeten we regelmatig de status van de service controleren. We controleren deze elke vijf seconden.

We kunnen de status controleren door de inhoud op te halen via de URL die we hebben ontvangen toen de aanvraag werd verzonden. Wanneer de inhoud is opgehaald, deserialiseren we deze in een van onze helperklassen om gemakkelijker te kunnen communiceren.

Dit is de polling-code met statusweergave voor alles, behalve een geslaagde voltooiing, dat gaan we nu doen.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>De transcriptieresultaten weergeven
Zodra de service de transcriptie heeft voltooid, worden de resultaten opgeslagen in een andere URL die we via het statusantwoord ontvangen.

We downloaden de inhoud van die URL, deserialiseren de JSON en doorlopen de resultaten terwijl we de weergavetekst afdrukken.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Uw code controleren
Op dit punt moet uw code er als volgt uitzien: (We hebben enkele opmerkingen toegevoegd aan deze versie) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>De app bouwen en uitvoeren

U bent nu klaar om uw app te bouwen en de spraakherkenning te testen met behulp van de Speech-service.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
