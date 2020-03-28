---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 02e0e1494a897b31cb6ef28083677fa48f854c91
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925263"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u:

> [!div class="checklist"]
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programmming-language-java)
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Een bronbestand uploaden naar een Azure-blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)


## <a name="open-your-project-in-eclipse"></a>Open uw project in Eclipse

De eerste stap is om ervoor te zorgen dat u uw project open in Eclipse.

1. Eclipse starten
2. Laad uw project `Main.java`en open .

## <a name="add-a-reference-to-gson"></a>Een verwijzing naar Gson toevoegen
We zullen gebruik maken van een externe JSON serializer / deserializer in deze quickstart. Voor Java hebben we gekozen voor [Gson.](https://github.com/google/gson)

Open uw pom.xml en voeg de volgende verwijzing toe.

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Begin met een soort boilerplate-code

Laten we wat code toevoegen die werkt als een skelet voor ons project.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON Wikkels

Aangezien de REST API's aanvragen in JSON-formaat aannemen en ook resultaten in JSON retourneren, konden we met hen communiceren met alleen tekenreeksen, maar dat wordt niet aanbevolen.
Om de aanvragen en reacties gemakkelijker te beheren, verklaren we een paar klassen te gebruiken voor het serialiseren / deserialiseren van de JSON.

Ga je gang en `Main`zet hun verklaringen voor .
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Een Http-client maken en configureren
Het eerste wat we nodig hebben is een Http-client met een juiste basis-URL en verificatieset.
Deze code `Main` invoegen in[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Een transcriptieaanvraag genereren
Vervolgens genereren we het transcriptieverzoek. Deze code `Main` toevoegen aan[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>Stuur het verzoek en controleer de status
Nu plaatsen we het verzoek bij de Spraakservice en controleren we de initiële reactiecode. Deze antwoordcode geeft eenvoudig aan of de service het verzoek heeft ontvangen. De service retourneert een url in de antwoordkoppen, dat is de locatie waar de transcriptiestatus wordt opgeslagen.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Wachten tot de transcriptie is voltooid
Aangezien de service de transcriptie asynchroon verwerkt, moeten we om de zoveel tijd peilen voor de status ervan. We controleren elke 5 seconden.

We kunnen de status controleren door de inhoud op te halen op de Url die we kregen toen het verzoek werd geplaatst. Wanneer we de inhoud terugkrijgen, deserialiseren we deze in een van onze helperklassen om het gemakkelijker te maken om ermee om te gaan.

Hier is de polling code met status weergave voor alles behalve een succesvolle voltooiing, zullen we dat nu doen.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>De transcriptieresultaten weergeven
Zodra de service de transcriptie heeft voltooid, worden de resultaten opgeslagen in een andere url die we kunnen krijgen van de statusrespons.

We downloaden de inhoud van die URL, deserialiseren de JSON en doorlopen de resultaten die de weergavetekst afdrukken.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Controleer uw code
Op dit punt, uw code moet er zo uitzien: (We hebben een aantal opmerkingen toegevoegd aan deze versie) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Nu bent u klaar om uw app te bouwen en onze spraakherkenning te testen met behulp van de Spraakservice.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
