---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: d69cb782b3be7035b7571653dcb434e44d22da93
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377200"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * [Een resource voor de Azure-spraakservice maken](../../../../overview.md#try-the-speech-service-for-free)
> * [Een bronbestand uploaden naar een Azure-blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="download-and-install-the-api-client-library"></a>Download en installeer de API-clientbibliotheek

Als u het voorbeeld wilt uitvoeren, moet u de Python-bibliotheek genereren voor de REST API die via [Swagger](https://swagger.io) wordt gegenereerd.

Voer de volgende stappen uit om de installatie uit te voeren:

1. Ga naar https://editor.swagger.io.
1. Klik op **bestand** en klik vervolgens op **URL importeren**.
1. Voer de URL voor Swagger in, inclusief de regio voor uw Speech Service-abonnement: `https://<your-region>.cris.ai/docs/v2.0/swagger`.
1. Klik op **Client genereren** en selecteer **Python**.
1. Sla de clientbibliotheek op.
1. Pak het gedownloade bestand python-client-generated.zip ergens in het bestandssysteem uit.
1. Installeer de uitgepakte python-clientmodule in uw Python-omgeving met pip: `pip install path/to/package/python-client`.
1. Het geïnstalleerde pakket heeft de naam `swagger_client`. U kunt controleren of de installatie is uitgevoerd met behulp van de opdracht `python -c "import swagger_client"`.

> [!NOTE]
> Vanwege een [bekende fout bij het automatisch genereren van Swagger](https://github.com/swagger-api/swagger-codegen/issues/7541) kunnen er fouten optreden bij het importeren van het `swagger_client`-pakket.
> Deze kunnen worden opgelost door de regel te verwijderen met
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> van het bestand `swagger_client/models/model.py` en de regel met
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> van het bestand `swagger_client/models/inner_error.py` in het geïnstalleerde pakket. In het foutbericht wordt aangegeven waar deze bestanden zich bevinden voor uw installatie.

## <a name="install-other-dependencies"></a>Andere afhankelijkheden installeren

In het voorbeeld wordt gebruikgemaakt van de `requests` -bibliotheek. U kunt het installeren met de opdracht

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Beginnen met standaardcode

We gaan wat code toevoegen die als basis voor het project gaat dienen.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>Een HTTP-client maken en configureren
Het eerste wat u nodig hebt, is een HTTP-client waarvoor een juiste basis-URL en verificatieset is ingesteld.
Voeg deze code toe in `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Een transcriptie-aanvraag genereren
Genereeer vervolgens de transcriptie-aanvraag. Voeg deze code toe aan `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>De aanvraag verzenden en de status controleren
Nu gaan we de aanvraag naar de Speech-service verzenden en de eerste responscode controleren. Deze responscode geeft simpelweg aan of de service de aanvraag heeft ontvangen. De service retourneert in de antwoordheaders een URL met de locatie waar de transcriptiestatus wordt opgeslagen.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Wacht totdat de transcriptie is voltooid
Omdat de transcriptie asynchroon wordt verwerkt, moeten we regelmatig de status van de service controleren. We controleren deze elke vijf seconden.

Alle transcripties die door deze Speech Service-resource worden verwerkt, worden opgelijst en er wordt gezocht naar degene die we hebben aangemaakt.

Dit is de polling-code met statusweergave voor alles, behalve een geslaagde voltooiing, dat gaan we nu doen.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>De transcriptieresultaten weergeven
Zodra de service de transcriptie heeft voltooid, worden de resultaten opgeslagen in een andere URL die we via het statusantwoord ontvangen.

Hier krijgen we de resulterende JSON en geven we deze weer.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Uw code controleren
Op dit punt moet uw code er als volgt uitzien: (Er zijn enkele opmerkingen toegevoegd aan deze versie.) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>De app bouwen en uitvoeren

U bent nu klaar om uw app te bouwen en de spraakherkenning te testen met behulp van de Speech-service.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
