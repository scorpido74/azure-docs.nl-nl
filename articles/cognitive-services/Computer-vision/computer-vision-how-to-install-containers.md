---
title: Containers installeren en uitvoeren-Computer Vision
titleSuffix: Azure Cognitive Services
description: Het downloaden, installeren en uitvoeren van containers voor Computer Vision in deze zelfstudie met stapsgewijze instructies.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: cbf199c391b49518bb595d7d1a0ed47147903a85
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034496"
---
# <a name="install-and-run-recognize-text-containers"></a>Tekst herkennen containers installeren en uitvoeren

Het gedeelte tekst herkennen van Computer Vision-is ook beschikbaar als een Docker-container. Hiermee kunt u om te detecteren en extraheer gedrukte tekst uit afbeeldingen van verschillende objecten met verschillende oppervlakken en achtergronden, zoals ontvangsten, posters en visitekaartjes.

> [!IMPORTANT]
> De tekst herkennen-container wordt op dit moment werkt alleen met Engels.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet voldoen aan de volgende vereisten voordat u Tekst herkennen containers gebruikt:

|Vereist|Doel|
|--|--|
|Docker-engine| De docker-engine moet zijn geïnstalleerd op een [hostcomputer](#the-host-computer). Docker biedt pakketten voor het configureren van de docker-omgeving op [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)en [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Zie voor een uitleg van de basisprincipes van Docker en containers, de [dockeroverzicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker moet worden geconfigureerd, zodat de containers om te verbinden met en facturering gegevens verzenden naar Azure. <br><br> **In Windows**moet docker ook worden geconfigureerd voor de ondersteuning van Linux-containers.<br><br>|
|Vertrouwd met docker | U moet een basis kennis hebben van docker-concepten, zoals registers, opslag plaatsen, containers en container installatie kopieën, en kennis van basis `docker` opdrachten.| 
|Computer Vision resource |Als u de container wilt gebruiken, hebt u het volgende nodig:<br><br>Een Azure **Computer Vision** -resource en de bijbehorende API-sleutel de EINDPUNT-URI. Beide waarden zijn beschikbaar op de pagina overzicht en sleutels voor de resource en zijn vereist om de container te starten.<br><br>**{API_KEY}** : Een van de twee beschik bare bron sleutels op de pagina **sleutels**<br><br>**{ENDPOINT_URI}** : Het eind punt op de pagina **overzicht**|

## <a name="request-access-to-the-private-container-registry"></a>Aanvraag voor toegang tot de privécontainerregister

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>De container installatie kopie ophalen met`docker pull`

Er zijn container installatie kopieën voor Tekst herkennen beschikbaar. 

| Container | Opslagplaats |
|-----------|------------|
|Tekst herkennen | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een container installatie kopie te downloaden.


### <a name="docker-pull-for-the-recognize-text-container"></a>Docker-pull voor de Tekst herkennen container

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>De container gebruiken

Wanneer de container zich op de [hostcomputer](#the-host-computer)bevindt, gebruikt u het volgende proces om met de container te werken.

1. [Voer de container uit](#run-the-container-with-docker-run)met de vereiste facturerings instellingen. Er zijn meer [voor beelden](computer-vision-resource-container-config.md) van de `docker run` opdracht beschikbaar. 
1. [Zoek het Voorspellings eindpunt van de container](#query-the-containers-prediction-endpoint)op. 

## <a name="run-the-container-with-docker-run"></a>Voer de container uit met`docker run`

Gebruik de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. De opdracht maakt gebruik van de volgende para meters:

| Tijdelijke aanduiding | Value |
|-------------|-------|
|{API_KEY} | Deze sleutel wordt gebruikt om de container te starten en is beschikbaar op de pagina `Cognitive Services` met Azure-sleutels.  |
|{ENDPOINT_URI} | De URI-waarde van het facturerings eindpunt. Voor beeld:`https://westus.api.cognitive.microsoft.com/vision/v2.0`|

U moet de `vision/v2.0` route ring toevoegen aan de eindpunt-URI, zoals wordt weer gegeven in het volgende BILLING_ENDPOINT_URI-voor beeld.

Vervang deze para meters door uw eigen waarden in de `docker run` volgende voorbeeld opdracht.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Deze opdracht:

* Hiermee wordt een herkende container uitgevoerd vanuit de container installatie kopie
* Wijst een CPU-kern en 4 GB aan geheugen toe
* Gebruikt TCP-poort 5000 en wijst er een pseudo-TTY voor de container
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer. 

Er zijn meer [voor beelden](./computer-vision-resource-container-config.md#example-docker-run-commands) van de `docker run` opdracht beschikbaar. 

> [!IMPORTANT]
> De `Eula`, `Billing`, en `ApiKey` opties moeten worden opgegeven voor het uitvoeren van de container; anders wordt de container niet start.  Zie voor meer informatie, [facturering](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Query uitvoeren op het prediction-eind punt van de container

De container bevat op REST gebaseerde query Voorspellings eindpunt-Api's. 

Gebruik de host, `http://localhost:5000`voor container-api's.

### <a name="asynchronous-text-recognition"></a>Asynchrone tekstherkenning

U kunt de `POST /vision/v2.0/recognizeText` en `GET /vision/v2.0/textOperations/*{id}*` bewerkingen in concert asynchroon herkennen gedrukte tekst in een afbeelding, vergelijkbaar met hoe de Computer Vision-service maakt gebruik van de bijbehorende REST-bewerkingen. De container tekst herkennen herkent alleen gedrukte tekst, niet handgeschreven tekst op dit moment, zodat de `mode` parameter normaal gesproken opgegeven voor de bewerking van de Computer Vision wordt genegeerd door de tekst herkennen-container.

### <a name="synchronous-text-recognition"></a>Synchrone tekstherkenning

U kunt de `POST /vision/v2.0/recognizeTextDirect` bewerking is synchroon gedrukte tekst in een afbeelding herkennen. Omdat deze bewerking synchroon is, is de aanvraag tekst voor deze bewerking hetzelfde als de `POST /vision/v2.0/recognizeText` bewerking, maar de antwoord tekst voor deze bewerking is hetzelfde als het resultaat van de `GET /vision/v2.0/textOperations/*{id}*` bewerking.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een uitvoer [koppeling](./computer-vision-resource-container-config.md#mount-settings) en logboek registratie ingeschakeld, genereert de container logboek bestanden die handig zijn om problemen op te lossen die optreden tijdens het starten of uitvoeren van de container. 


## <a name="billing"></a>Billing

De Tekst herkennen-containers verzenden facturerings gegevens naar Azure met behulp van een _tekst herkennen_ resource in uw Azure-account. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie voor meer informatie over deze opties [containers configureren](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd om Tekst herkennen containers te downloaden, te installeren en uit te voeren. Samenvatting:

* Tekst herkennen biedt een Linux-container voor docker, inkapseling tekst herkennen.
* Containerinstallatiekopieën worden gedownload uit het Microsoft Container Registry (MCR) in Azure.
* Containerinstallatiekopieën uitvoeren in Docker.
* U kunt de REST API of SDK gebruiken voor het aanroepen van bewerkingen in Tekst herkennen containers door de URI van de host op te geven van de container.
* Bij het instantiëren van een container, moet u informatie over facturering opgeven.

> [!IMPORTANT]
> Cognitive Services-containers zijn geen licentie om uit te voeren zonder verbinding met Azure voor het meten. Klanten moeten de containers om te communiceren factureringsgegevens met de softwarelicentiecontrole-service te allen tijde inschakelen. Cognitive Services containers verzenden geen klant gegevens (bijvoorbeeld de afbeelding of tekst die wordt geanalyseerd) naar micro soft.

## <a name="next-steps"></a>Volgende stappen

* Beoordeling [containers configureren](computer-vision-resource-container-config.md) voor configuratie-instellingen
* Beoordeling [Computer Vision-overzicht](Home.md) voor meer informatie over het herkennen van afdrukken en handgeschreven tekst  
* Raadpleeg de [Computer Vision-API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) voor meer informatie over de methoden die wordt ondersteund door de container.
* Raadpleeg [Veelgestelde vragen (FAQ)](FAQ.md) het oplossen van problemen met betrekking tot de Computer Vision-functionaliteit.
* Meer [Cognitive Services containers](../cognitive-services-container-support.md) gebruiken
