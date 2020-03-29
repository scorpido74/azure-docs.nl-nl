---
title: Container installeren en uitvoeren voor Formulierherkenning
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u de Azure Cognitive Services Form Recognizer-container gebruiken om formulier- en tabelgegevens te ontken.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: dapine
ms.openlocfilehash: c8ce4b913548429ff83e0b8aa3cb65455fc9b4c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474693"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>Formulierherkenningscontainers installeren en uitvoeren (voorbeeld)

Azure Form Recognizer past machine learning-technologie toe om sleutelwaardeparen en tabellen uit formulieren te identificeren en te extraheren. Het koppelt waarden en tabelvermeldingen aan de sleutelwaardeparen en levert vervolgens gestructureerde gegevens op die de relaties in het oorspronkelijke bestand bevatten. 

Om de complexiteit te verminderen en eenvoudig een aangepast Formulierherkenningsmodel te integreren in uw workflowautomatiseringsproces of andere toepassing, u het model aanroepen met behulp van een eenvoudige REST API. Er zijn slechts vijf formulierdocumenten (of één leeg formulier en twee ingevulde formulieren) nodig, zodat u snel, nauwkeurig en op maat van uw specifieke inhoud resultaten behalen. Er is geen zware handmatige interventie of uitgebreide data science expertise nodig. En het vereist geen gegevensetikettering of gegevensannotatie.

> [!IMPORTANT]
> De form recognizer-containers gebruiken momenteel versie 1.0 van de API voor formulierherkenning. U hebt toegang tot de nieuwste versie van de API door in plaats daarvan de beheerde service te gebruiken.

| Functie | Functies |
|----------|----------|
| Form Recognizer | <li>Pdf-, PNG- en JPG-bestanden verwerken<li>Traint aangepaste modellen met minimaal vijf vormen van dezelfde lay-out <li>Extracten van sleutelwaardeparen en tabelgegevens <li>Gebruikt de azure cognitive services computer vision API Herkennen tekst functie te detecteren en extraheren afgedrukte tekst uit afbeeldingen in formulieren<li>Vereist geen annotatie of etikettering |

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voordat u form recognizer-containers gebruikt, moet u aan de volgende voorwaarden voldoen:

| Vereist | Doel |
|----------|---------|
| Docker-engine | U moet de Docker Engine geïnstalleerd op een [hostcomputer.](#the-host-computer) Docker biedt pakketten waarmee de Docker-omgeving op [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms) kan worden geconfigureerd. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.<br><br> Docker moet zijn geconfigureerd om de containers in staat te stellen verbinding te maken met en factureringsgegevens naar Azure te verzenden. <br><br> Op Windows moet Docker ook zijn geconfigureerd om Linux-containers te ondersteunen.<br><br> |
| Vertrouwdheid met Docker | U moet een basiskennis hebben van Docker-concepten, zoals registers, opslagplaatsen, containers `docker` en containerafbeeldingen en kennis van basisopdrachten. |
| De Azure CLI | Installeer de [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) op uw host. |
| Computer Vision API-bron | Als u gescande documenten en afbeeldingen wilt verwerken, hebt u een Computer Vision-bron nodig. U hebt toegang tot de functie Tekst herkennen als een Azure-bron (de REST API of SDK) of een [container](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull) *voor het herkennen van cognitief services-herkennen-tekst.* De gebruikelijke factureringskosten zijn van toepassing. <br><br>Geef zowel de API-sleutel als de eindpunten voor uw Computer Vision-bron (Azure cloud- of Cognitive Services-container) door. Gebruik deze API-toets en het eindpunt als **{COMPUTER_VISION_API_KEY}** en **{COMPUTER_VISION_ENDPOINT_URI}**.<br><br> Als u de container *cognitive-services-recognize-text* gebruikt, moet u ervoor zorgen dat:<br><br>Uw Computer Vision-toets voor de form recognizer-container `docker run` is de sleutel die is opgegeven in de opdracht Computer Vision voor de container *cognitive-services-recognize-text.*<br>Uw factureringseindpunt is het eindpunt van de `http://localhost:5000`container (bijvoorbeeld ). Als u zowel de container Computer Vision als de container Formulierherkenning samen op dezelfde host gebruikt, kunnen ze niet beide worden gestart met de standaardpoort van *5000*. |
| Bron formulierherkenning | Als u deze containers wilt gebruiken, moet u het volgende hebben:<br><br>Een Azure **Form Recognizer-bron** om de bijbehorende API-sleutel en eindpunt URI te krijgen. Beide waarden zijn beschikbaar op de pagina's **Formulierherkenningsoverzicht** en Sleutels van de Azure-portal formulier en sleutels en beide waarden zijn vereist om de container te starten.<br><br>**{FORM_RECOGNIZER_API_KEY}**: Een van de twee beschikbare resourcesleutels op de pagina Sleutels<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**: Het eindpunt zoals vermeld op de pagina Overzicht |

> [!NOTE]
> De resourcenaam Computer Vision moet één woord `-` zijn, zonder koppelteken of andere speciale tekens. Deze beperking is van kracht om de compatibiliteit van formulierherkenning en tekstcontainer te garanderen.

## <a name="gathering-required-parameters"></a>Vereiste parameters verzamelen

Er zijn drie primaire parameters voor alle containers van Cognitive Services die vereist zijn. De licentieovereenkomst voor eindgebruikers (EULA) moet aanwezig `accept`zijn met een waarde van . Daarnaast zijn zowel een Endpoint URL als API Key nodig.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Eindpunt URI `{COMPUTER_VISION_ENDPOINT_URI}` en`{FORM_RECOGNIZER_ENDPOINT_URI}`

De **WAARDE ENDpoint** URI is beschikbaar op de pagina *Overzicht* van azure-portal van de bijbehorende Cognitive Service-bron. Navigeer naar de *pagina Overzicht,* plaats de `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> plaats boven het eindpunt en er wordt een pictogram weergegeven. Kopieer en gebruik waar nodig.

![Verzamel de eindpunturi voor later gebruik](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Toetsen `{COMPUTER_VISION_API_KEY}` en`{FORM_RECOGNIZER_API_KEY}`

Deze sleutel wordt gebruikt om de container te starten en is beschikbaar op de pagina Sleutels van de Azure-portal van de bijbehorende Cognitive Service-bron. Navigeer naar de pagina *Sleutels* `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> en klik op het pictogram.

![Een van de twee toetsen ophalen voor later gebruik](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Deze abonnementssleutels worden gebruikt om toegang te krijgen tot uw Cognitive Service API. Deel je sleutels niet. Sla ze veilig op, bijvoorbeeld met Azure Key Vault. We raden ook aan om deze sleutels regelmatig te regenereren. Er is slechts één sleutel nodig om een API-aanroep te maken. Bij het regenereren van de eerste sleutel u de tweede sleutel gebruiken voor verdere toegang tot de service.

## <a name="request-access-to-the-container-registry"></a>Toegang tot het containerregister aanvragen

U moet eerst het [toegangsformulier voor het toegangsformulier voor het herkennen van cognitive services-containers](https://aka.ms/FormRecognizerContainerRequestAccess) invullen en indienen om toegang tot de container aan te vragen. Dit meldt u ook voor Computer Vision. U hoeft zich niet apart aan te melden voor het formulier Computer Vision. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

De minimale en aanbevolen CPU-cores en geheugen die voor elke form recognizeer-container moeten worden toegewezen, worden beschreven in de volgende tabel:

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Form Recognizer | 2-core, 4 GB geheugen | 4-core geheugen met 8 GB |
| Tekst herkennen | 1 core, 8 GB geheugen | 2 cores, 8 GB geheugen |

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.
* Kern en geheugen `--cpus` komen `--memory` overeen met de en `docker run` instellingen, die worden gebruikt als onderdeel van de opdracht.

> [!Note]
> De minimum- en aanbevolen waarden zijn gebaseerd op dockerlimieten en *niet* op de resources voor de hostmachine.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>De containerafbeeldingen krijgen met de opdracht Docker Pull

Containerafbeeldingen voor zowel de aanbiedingen **Formulierherkenning** als **Tekst herkennen** zijn beschikbaar in het volgende containerregister:

| Container | Volledig gekwalificeerde afbeeldingsnaam |
|-----------|------------|
| Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| Tekst herkennen | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

U hebt beide containers nodig, houd er rekening mee dat de **tekstcontainer herkennen** [buiten dit artikel wordt beschreven.](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker pull voor de Form Recognizer container

#### <a name="form-recognizer"></a>Form Recognizer

Als u de container Formulierherkenning wilt openen, gebruikt u de volgende opdracht:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>Koppeling van docker voor de container Tekst herkennen

#### <a name="recognize-text"></a>Tekst herkennen

Als u de container Tekst herkennen wilt krijgen, gebruikt u de volgende opdracht:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>Hoe de container te gebruiken

Nadat de container zich op de [hostcomputer bevindt,](#the-host-computer)gebruikt u het volgende proces om met de container te werken.

1. [Voer de container](#run-the-container-by-using-the-docker-run-command)uit met de vereiste factureringsinstellingen. Meer [voorbeelden](form-recognizer-container-configuration.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar.
1. [Query the container prediction endpoint](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>De container uitvoeren met de opdracht Docker run

Gebruik de opdracht [Docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. Raadpleeg [het verzamelen van vereiste parameters](#gathering-required-parameters) `{COMPUTER_VISION_ENDPOINT_URI}`voor `{COMPUTER_VISION_API_KEY}` `{FORM_RECOGNIZER_ENDPOINT_URI}` meer `{FORM_RECOGNIZER_API_KEY}` informatie over hoe u de, en waarden krijgen.

[Voorbeelden](form-recognizer-container-configuration.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar.

### <a name="form-recognizer"></a>Form Recognizer

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Met deze opdracht gebeurt het volgende:

* Voert een form recognizer-container uit de containerafbeelding.
* Hiermee worden 2 CPU-cores en 8 gigabyte (GB) geheugen toegewezen.
* Stelt TCP-poort 5000 bloot en wijst een pseudo-TTY toe voor de container.
* Verwijdert automatisch de container nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.
* Monteert een /input en een /output volume aan de container.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Afzonderlijke containers uitvoeren als afzonderlijke dockerrunopdrachten

Voer de volgende twee voorbeeld-docker-CLI-opdrachten in voor de combinatie Formulierherkenning en Tekstherkenning die lokaal op dezelfde host wordt gehost:

Voer de eerste container op poort 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Voer de tweede container op poort 5001.

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Elke volgende container moet zich op een andere poort bevindt. 

### <a name="run-separate-containers-with-docker-compose"></a>Afzonderlijke containers uitvoeren met Docker Compose

Zie het volgende voorbeeld Docker Compose YAML-bestand voor de combinatie Formulierherkenning en Tekstherkenning die lokaal op dezelfde host wordt gehost. De tekstherkenningmoet `{COMPUTER_VISION_API_KEY}` hetzelfde zijn `formrecognizer` voor `ocr` zowel de containers als de containers. Het `{COMPUTER_VISION_ENDPOINT_URI}` wordt alleen `ocr` gebruikt in `formrecognizer` de container, omdat de container de naam en poort `ocr` gebruikt. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: "{COMPUTER_VISION_API_KEY}"

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"
```

> [!IMPORTANT]
> De `Eula` `Billing`, `ApiKey`, en , `FormRecognizer:ComputerVisionApiKey` `FormRecognizer:ComputerVisionEndpointUri` evenals de en opties, moeten worden gespecificeerd om de container te laten draaien; Anders start de container niet. Zie [Facturering voor](#billing)meer informatie.

## <a name="query-the-containers-prediction-endpoint"></a>Het voorspellingseindpunt van de container opvragen

|Container|Eindpunt|
|--|--|
|vormherkenning|http://localhost:5000

### <a name="form-recognizer"></a>Form Recognizer

De container biedt websocket-gebaseerde queryeindpunt API's, die u toegang hebt via [De Dienst Van form Recognizer services SDK-documentatie.](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)

Standaard maakt de Form Recognizer SDK gebruik van de online services. Als u de container wilt gebruiken, moet u de initialisatiemethode wijzigen. Zie de onderstaande voorbeelden.

#### <a name="for-c"></a>Voor C #

Wijziging van het gebruik van deze Azure-cloud initialisatie-oproep:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
aan deze aanroep, die het eindpunt van de container gebruikt:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Voor Python

Wijziging van het gebruik van deze Azure-cloud initialisatie-oproep:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

aan deze aanroep, die het eindpunt van de container gebruikt:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Form Recognizer

De container biedt REST-eindpuntAPI's, die u vinden op de [API-pagina formulierherkenning.](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel)


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een [uitvoerbevestiging](form-recognizer-container-configuration.md#mount-settings) en logboekregistratie is ingeschakeld, genereert de container logboekbestanden die handig zijn om problemen op te lossen die zich voordoen tijdens het starten of uitvoeren van de container.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De containers Form Recognizer verzenden factureringsgegevens naar Azure met behulp van een _Bron voor formulierherkenning_ op uw Azure-account.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie [Containers configureren](form-recognizer-container-configuration.md)voor meer informatie over deze opties.

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werkstroom geleerd voor het downloaden, installeren en uitvoeren van Form Recognizer-containers. Samenvatting:

* Form Recognizer biedt één Linux-container voor Docker.
* Containerafbeeldingen worden gedownload uit het register van privécontainers in Azure.
* Containerafbeeldingen worden uitgevoerd in Docker.
* U de REST API of de REST SDK gebruiken om bewerkingen aan te roepen in de container Form Recognizer door de hostURI van de container op te geven.
* U moet de factureringsgegevens opgeven wanneer u een container instantiate.

> [!IMPORTANT]
>  Cognitive Services-containers hebben geen licentie om uit te voeren zonder dat deze is verbonden met Azure voor meting. Klanten moeten de containers te allen tijde in staat stellen factureringsgegevens met de meetservice te communiceren. Cognitive Services-containers verzenden geen klantgegevens (bijvoorbeeld de afbeelding of tekst die wordt geanalyseerd) naar Microsoft.

## <a name="next-steps"></a>Volgende stappen

* Controleer [Containers configureren](form-recognizer-container-configuration.md) voor configuratie-instellingen.
* Gebruik meer [Cognitive Services Containers.](../cognitive-services-container-support.md)
