---
title: Containers installeren en uitvoeren - Computer Vision
titleSuffix: Azure Cognitive Services
description: Hoe containers voor Computer Vision te downloaden, te installeren en uit te voeren in deze walkthrough-zelfstudie.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 5f36c429041a8182551d1f077f0a1229f520e8c1
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879340"
---
# <a name="install-and-run-read-containers-preview"></a>Lees-containers installeren en uitvoeren (voorbeeld)

Met containers u de Computer Vision API's in uw eigen omgeving uitvoeren. Containers zijn ideaal voor specifieke vereisten op het gebied van beveiliging en gegevensbeheer. In dit artikel leert u hoe u een Computer Vision-container downloaden, installeren en uitvoeren.

Een enkele Docker-container, *Read*, is beschikbaar voor Computer Vision. Met de *leescontainer* u *afgedrukte tekst* detecteren en extraheren uit afbeeldingen van verschillende objecten met verschillende oppervlakken en achtergronden, zoals ontvangstbewijzen, posters en visitekaartjes. Daarnaast detecteert de *container Lezen* *handgeschreven tekst* in afbeeldingen en biedt het PDF-, TIFF- en meerpaginabestandsondersteuning. Zie de [API-documentatie lezen](concept-recognizing-text.md#read-api) voor meer informatie.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet aan de volgende voorwaarden voldoen voordat u de containers gebruikt:

|Vereist|Doel|
|--|--|
|Docker-engine| U moet de Docker Engine geïnstalleerd op een [hostcomputer.](#the-host-computer) Docker biedt pakketten waarmee de Docker-omgeving op [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms) kan worden geconfigureerd. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.<br><br> Docker moet zijn geconfigureerd om de containers in staat te stellen verbinding te maken met en factureringsgegevens naar Azure te verzenden. <br><br> **Op Windows**moet Docker ook zijn geconfigureerd om Linux-containers te ondersteunen.<br><br>|
|Vertrouwdheid met Docker | U moet een basiskennis hebben van Docker-concepten, zoals registers, opslagplaatsen, containers en `docker` containerafbeeldingen, evenals kennis van basisopdrachten.| 
|Computer Vision-bron |Om de container te kunnen gebruiken, moet u beschikken over:<br><br>Een Azure **Computer Vision-bron** en de bijbehorende API-sleutel het eindpunt URI. Beide waarden zijn beschikbaar op de pagina's Overzicht en Sleutels voor de resource en zijn vereist om de container te starten.<br><br>**{API_KEY}**: Een van de twee beschikbare resourcesleutels op de pagina **Sleutels**<br><br>**{ENDPOINT_URI}**: Het eindpunt zoals vermeld op de **pagina Overzicht**|

## <a name="request-access-to-the-private-container-registry"></a>Toegang aanvragen tot het register van privécontainers

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Ondersteuning voor geavanceerde vectorextensie

De **hostcomputer** is de computer waarop de dockercontainer wordt uitgevoerd. De host moet [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2) *ondersteunen.* U met de volgende opdracht controleren op AVX2-ondersteuning op Linux-hosts:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> De hostcomputer is *vereist* om AVX2 te ondersteunen. De container *werkt niet* goed zonder AVX2-ondersteuning.

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>De containerafbeelding biju halen`docker pull`

Containerafbeeldingen voor Lezen zijn beschikbaar.

| Container | Containerregister / Repository / Afbeeldingsnaam |
|-----------|------------|
| Lezen | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

Gebruik [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) de opdracht om een containerafbeelding te downloaden.

### <a name="docker-pull-for-the-read-container"></a>Docker pull voor de Lees-container

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Hoe de container te gebruiken

Zodra de container zich op de [hostcomputer bevindt,](#the-host-computer)gebruikt u het volgende proces om met de container te werken.

1. [Voer de container](#run-the-container-with-docker-run)uit met de vereiste factureringsinstellingen. Meer [voorbeelden](computer-vision-resource-container-config.md) van `docker run` de opdracht zijn beschikbaar. 
1. [Query the container prediction endpoint](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Voer de container uit met`docker run`

Gebruik de opdracht [Docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. Raadpleeg [het verzamelen van vereiste parameters](#gathering-required-parameters) `{ENDPOINT_URI}` voor `{API_KEY}` meer informatie over hoe u de en waarden krijgen.

[Voorbeelden](computer-vision-resource-container-config.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Voert de container Lezen uit de containerafbeelding uit.
* Hiermee wordt 8 CPU-kern en 16 gigabyte (GB) geheugen toegewezen.
* Stelt TCP-poort 5000 bloot en wijst een pseudo-TTY toe voor de container.
* Verwijdert automatisch de container nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.

Meer [voorbeelden](./computer-vision-resource-container-config.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar. 

> [!IMPORTANT]
> De `Eula` `Billing`opties `ApiKey` en de opties moeten worden opgegeven om de container uit te voeren; Anders start de container niet.  Zie [Facturering voor](#billing)meer informatie.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Het voorspellingseindpunt van de container opvragen

De container biedt OP RUST gebaseerde queryvoorspellingseindpunt-API's. 

Gebruik de `http://localhost:5000`host, voor container API's.

### <a name="asynchronous-read"></a>Asynchrone lezen

U `POST /vision/v2.0/read/core/asyncBatchAnalyze` de `GET /vision/v2.0/read/operations/{operationId}` en bewerkingen in overleg gebruiken om een afbeelding asynchroon te lezen, vergelijkbaar met de manier waarop de Computer Vision-service deze bijbehorende REST-bewerkingen gebruikt. De asynchrone POST-methode retourneert een `operationId` die wordt gebruikt als de identifer naar de HTTP GET-aanvraag.

Selecteer in de gebruikersinterface van `asyncBatchAnalyze` de branie de optie om deze uit te breiden in de browser. Selecteer vervolgens **Probeer het uit** > **Bestand kiezen**. In dit voorbeeld gebruiken we de volgende afbeelding:

![tabbladen vs spaties](media/tabs-vs-spaces.png)

Wanneer de asynchrone post is uitgevoerd, wordt een **HTTP 202-statuscode** geretourneerd. Als onderdeel van het antwoord `operation-location` is er een koptekst met het resultaateindpunt voor de aanvraag.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

Het `operation-location` is de volledig gekwalificeerde URL en is toegankelijk via een HTTP GET. Hier is het JSON-antwoord `operation-location` van het uitvoeren van de URL van de voorgaande afbeelding:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>Synchroon lezen

U `POST /vision/v2.0/read/core/Analyze` de bewerking gebruiken om een afbeelding synchroon te lezen. Wanneer de afbeelding in zijn geheel wordt gelezen, dan en alleen dan geeft de API een JSON-antwoord. De enige uitzondering hierop is als er een fout optreedt. Wanneer er een fout optreedt, wordt de volgende JSON geretourneerd:

```json
{
    status: "Failed"
}
```

Het JSON-antwoordobject heeft dezelfde objectgrafiek als de asynchrone versie. Als u een JavaScript-gebruiker bent en de veiligheid van het type wilt, `AnalyzeResult` kunnen de volgende typen worden gebruikt om de JSON-respons als object te casten.

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export enum Confidence {
    High = 0,
    Low = 1
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: Confidence | null;
}
```

Zie de <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">TypeScript-sandbox <span class="docon docon-navigate-external x-hidden-focus"></span> hier</a> voor een voorbeeldgebruiksvoorbeeld en selecteer **Uitvoeren** om het gebruiksgemak ervan te visualiseren.

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een [uitvoerbevestiging](./computer-vision-resource-container-config.md#mount-settings) en logboekregistratie is ingeschakeld, genereert de container logboekbestanden die handig zijn om problemen op te lossen die zich voordoen tijdens het starten of uitvoeren van de container.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De containers Cognitive Services verzenden factureringsgegevens naar Azure met behulp van de bijbehorende bron op uw Azure-account.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie [Containers configureren](./computer-vision-resource-container-config.md)voor meer informatie over deze opties.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werkstroom geleerd voor het downloaden, installeren en uitvoeren van Computer Vision-containers. Samenvatting:

* Computer Vision biedt een Linux-container voor Docker, inkapselen Lezen.
* Containerafbeeldingen worden gedownload uit het containerregister 'Container Preview' in Azure.
* Containerafbeeldingen worden uitgevoerd in Docker.
* U de REST API of SDK gebruiken om bewerkingen aan te roepen in leescontainers door de hostURI van de container op te geven.
* U moet factureringsgegevens opgeven bij het momentmaken van een container.

> [!IMPORTANT]
> Cognitive Services-containers hebben geen licentie om uit te voeren zonder dat deze is verbonden met Azure voor meting. Klanten moeten de containers te allen tijde in staat stellen factureringsgegevens met de meetservice te communiceren. Cognitive Services-containers verzenden geen klantgegevens (bijvoorbeeld de afbeelding of tekst die wordt geanalyseerd) naar Microsoft.

## <a name="next-steps"></a>Volgende stappen

* Containers [configureren controleren](computer-vision-resource-container-config.md) voor configuratie-instellingen
* Bekijk [het overzicht Computer Vision](Home.md) voor meer informatie over het herkennen van afgedrukte en handgeschreven tekst
* Raadpleeg de [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) voor meer informatie over de methoden die door de container worden ondersteund.
* Raadpleeg [veelgestelde vragen (FAQ)](FAQ.md) om problemen met betrekking tot de Computer Vision-functionaliteit op te lossen.
* Meer [Cognitive Services-containers gebruiken](../cognitive-services-container-support.md)
