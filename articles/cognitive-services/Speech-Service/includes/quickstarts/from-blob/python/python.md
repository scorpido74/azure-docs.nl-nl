---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: fcf2d3513936d50a0a5be8653e9bee5b30821e7d
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671548"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u:

> [!div class="checklist"]
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Een bronbestand uploaden naar een Azure-blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="download-and-install-the-api-client-library"></a>De API-clientbibliotheek downloaden en installeren

Als u het voorbeeld wilt uitvoeren, moet u de Python-bibliotheek genereren voor de REST-API die wordt gegenereerd via [Swagger.](https://swagger.io)

Volg de volgende stappen voor de installatie:

1. Ga naar https://editor.swagger.io.
1. Klik **op Bestand**en klik vervolgens op URL **importeren**.
1. Voer de Swagger-URL in, inclusief `https://<your-region>.cris.ai/docs/v2.0/swagger`de regio voor uw abonnement op spraakservice: .
1. Klik **op Client genereren** en selecteer **Python**.
1. Sla de clientbibliotheek op.
1. Haal de gedownloade python-client generated.zip ergens in je bestandssysteem.
1. Installeer de geëxtraheerde python-client module `pip install path/to/package/python-client`in uw Python-omgeving met behulp van pip: .
1. Het geïnstalleerde pakket `swagger_client`heeft de naam . U controleren of de `python -c "import swagger_client"`installatie heeft gewerkt met behulp van de opdracht.

> [!NOTE]
> Als gevolg van een [bekende bug in de Swagger autogeneratie,](https://github.com/swagger-api/swagger-codegen/issues/7541)u fouten tegenkomen bij het importeren van het `swagger_client` pakket.
> Deze kunnen worden opgelost door de regel met de inhoud te verwijderen
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> van het `swagger_client/models/model.py` bestand en de regel met de inhoud
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> van het `swagger_client/models/inner_error.py` bestand in het geïnstalleerde pakket. Het foutbericht geeft aan waar deze bestanden zich voor uw installatie bevinden.

## <a name="install-other-dependencies"></a>Andere afhankelijkheden installeren

Het voorbeeld `requests` maakt gebruik van de bibliotheek. U het installeren met de opdracht

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Begin met een soort boilerplate-code

Laten we wat code toevoegen die werkt als een skelet voor ons project.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>Een Http-client maken en configureren
Het eerste wat we nodig hebben is een Http-client met een juiste basis-URL en verificatieset.
Deze code `transcribe` invoegen in[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Een transcriptieaanvraag genereren
Vervolgens genereren we het transcriptieverzoek. Deze code `transcribe` toevoegen aan[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Stuur het verzoek en controleer de status
Nu plaatsen we het verzoek bij de Spraakservice en controleren we de initiële reactiecode. Deze antwoordcode geeft eenvoudig aan of de service het verzoek heeft ontvangen. De service retourneert een url in de antwoordkoppen, dat is de locatie waar de transcriptiestatus wordt opgeslagen.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Wachten tot de transcriptie is voltooid
Aangezien de service de transcriptie asynchroon verwerkt, moeten we om de zoveel tijd peilen voor de status ervan. We controleren elke 5 seconden.

We zullen alle transcripties opsommen die deze spraakservicebron verwerkt en zoeken naar de transcripties die we hebben gemaakt.

Hier is de polling code met status weergave voor alles behalve een succesvolle voltooiing, zullen we dat nu doen.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>De transcriptieresultaten weergeven
Zodra de service de transcriptie heeft voltooid, worden de resultaten opgeslagen in een andere url die we kunnen krijgen van de statusrespons.

Hier krijgen we dat resultaat JSON en weer te geven.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Controleer uw code
Op dit punt moet uw code er als volgt uitzien: (We hebben een aantal opmerkingen toegevoegd aan deze versie)[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Nu bent u klaar om uw app te bouwen en onze spraakherkenning te testen met behulp van de Spraakservice.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
