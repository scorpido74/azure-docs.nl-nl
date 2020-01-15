---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: bfecfa1918d2e9199971b2f9738530dc1b4e3c4c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942728"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md)
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Een bron bestand uploaden naar een Azure-Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="download-and-install-the-api-client-library"></a>Down load en installeer de API-client bibliotheek

Als u het voor beeld wilt uitvoeren, moet u de python-bibliotheek genereren voor de REST API die wordt gegenereerd via [Swagger](https://swagger.io).

Voer de volgende stappen uit om de installatie uit te voeren:

1. Ga naar https://editor.swagger.io.
1. Klik op **bestand**en klik vervolgens op **URL importeren**.
1. Voer de URL voor Swagger in, inclusief de regio voor uw speech service-abonnement: `https://<your-region>.cris.ai/docs/v2.0/swagger`.
1. Klik op **client genereren** en selecteer **python**.
1. Sla de client bibliotheek op.
1. Pak de gedownloade python-client-generated. zip ergens in het bestands systeem uit.
1. Installeer de uitgepakte python-client module in uw python-omgeving met PIP: `pip install path/to/package/python-client`.
1. Het geïnstalleerde pakket heeft de naam `swagger_client`. U kunt controleren of de installatie is uitgevoerd met behulp van de opdracht `python -c "import swagger_client"`.

> [!NOTE]
> Als gevolg [van een bekende fout in de Swagger automatisch genereren](https://github.com/swagger-api/swagger-codegen/issues/7541), kunnen er fouten optreden bij het importeren van het `swagger_client`-pakket.
> Deze kunnen worden opgelost door de regel met de inhoud te verwijderen
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> uit het bestand `swagger_client/models/model.py` en de regel met de inhoud
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> vanuit het bestands `swagger_client/models/inner_error.py` binnen het geïnstalleerde pakket. In het fout bericht wordt aangegeven waar deze bestanden zich bevinden voor uw installatie.

## <a name="install-other-dependencies"></a>Andere afhankelijkheden installeren

In het voor beeld wordt gebruikgemaakt van de `requests`-bibliotheek. U kunt deze installeren met behulp van de opdracht

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code

Laten we een code toevoegen die als een skelet voor het project werkt.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>Een HTTP-client maken en configureren
Het eerste wat u nodig hebt, is een HTTP-client waarvoor een juiste basis-URL en verificatieset is ingesteld.
Voeg deze code in `transcribe` [!code-pythonin [](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Een transcriptie-aanvraag genereren
Vervolgens genereren we de transcriptie-aanvraag. Voeg deze code toe aan `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>De aanvraag verzenden en de status controleren
Nu gaan we de aanvraag verzenden naar de speech-service en de eerste respons code controleren. Deze antwoord code geeft eenvoudigweg aan of de service de aanvraag heeft ontvangen. De service retourneert een URL in de antwoord headers die de locatie is waar de transcriptie-status wordt opgeslagen.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Wacht tot de transcriptie is voltooid
Omdat de transcriptie asynchroon wordt verwerkt, moeten we elke regel vaak de status van de service controleren. We controleren elke vijf seconden.

Alle transcripties die door deze speech service-resource worden verwerkt, worden geïnventariseerd en er wordt gezocht naar het account dat we hebben gemaakt.

Dit is de polling code met status weergave voor alles, behalve een geslaagde voltooiing, we doen dat nu.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>De transcriptie-resultaten weer geven
Zodra de service de transcriptie heeft voltooid, worden de resultaten opgeslagen in een andere URL die we vanaf het status antwoord kunnen ontvangen.

Hier krijgen we dat resultaat JSON en weer gegeven.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Controleer uw code
Op dit moment moet uw code er als volgt uitzien: (er zijn enkele opmerkingen aan deze versie toegevoegd) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Nu bent u klaar om uw app te bouwen en de spraak herkenning te testen met behulp van de speech-service.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
