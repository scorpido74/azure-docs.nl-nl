---
title: Containers installeren en uitvoeren voor het gebruik van anomalie detectie-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het installeren en gebruiken van een container voor het gebruik van de geavanceerde algoritmen van de anomalie detectie-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 2a4ff7da16524e0706601e43dff39325952990ff
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903547"
---
# <a name="install-and-run-anomaly-detector-containers"></a>Anomaly Detector-containers installeren en uitvoeren 

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

De Anomaly Detector heeft de volgende functionaliteit voor containerfuncties:

| Functie | Functies |
|--|--|
| Anomalie detectie | <li> Detecteert afwijkingen die in realtime optreden. <li> Detecteert afwijkingen in uw gegevensset als een batch. <li> Detecteert trend wijzigings punten in uw gegevensset als een batch.<li> Het verwachte normale bereik van uw gegevens afleiden. <li> Ondersteunt de gevoeligheids aanpassing van anomalie detectie om uw gegevens beter aan te passen. |

Voor gedetailleerde informatie over de Api's raadpleegt u:
* [Meer informatie over de API-service voor anomalie detectie](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet voldoen aan de volgende vereisten voordat u afwijkende detector containers gebruikt:

|Vereist|Doel|
|--|--|
|Docker-engine| De docker-engine moet zijn geïnstalleerd op een [hostcomputer](#the-host-computer). Docker biedt pakketten waarmee de Docker-omgeving op [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms) kan worden geconfigureerd. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.<br><br> Docker moet worden geconfigureerd zodat de containers verbinding kunnen maken met en facturerings gegevens kunnen verzenden naar Azure. <br><br> **In Windows**moet docker ook worden geconfigureerd voor de ondersteuning van Linux-containers.<br><br>|
|Vertrouwd met docker | U moet een basis kennis hebben van docker-concepten, zoals registers, opslag plaatsen, containers en container installatie kopieën, en kennis van basis `docker` opdrachten.|
|Anomalie detector bron |Als u deze containers wilt gebruiken, hebt u het volgende nodig:<br><br>Een Azure _anomalie detector_ -bron om de bijbehorende API-sleutel en eind punt-URI op te halen. Beide waarden zijn beschikbaar op het overzicht van de **anomalie detectie** en de pagina's van de Azure Portal en zijn vereist om de container te starten.<br><br>**{API_KEY}**: een van de twee beschik bare bron sleutels op de pagina **sleutels**<br><br>**{ENDPOINT_URI}**: het eind punt op de pagina **overzicht**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Container vereisten en aanbevelingen

In de volgende tabel worden de minimale en aanbevolen CPU-kernen en het geheugen beschreven die moeten worden toegewezen aan een afwijkende detector-container.

| QPS (Query's per seconde) | Minimum | Aanbevolen |
|-----------|---------|-------------|
| 10 QPS | 4-core, 1 GB geheugen | 8 kern geheugen van 2 GB |
| 20 QPS | 8-core, 2 GB geheugen | 16-core 4 GB geheugen |

Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.

Core en geheugen komen overeen met `--cpus` de `--memory` instellingen en, die worden gebruikt als onderdeel van de `docker run` opdracht.

## <a name="get-the-container-image-with-docker-pull"></a>De container installatie kopie ophalen met `docker pull`

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een container installatie kopie te downloaden.

| Container | Opslagplaats |
|-----------|------------|
| cognitieve Services-anomaliey-detector | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker-pull voor de anomalie detectie container

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>De container gebruiken

Wanneer de container zich op de [hostcomputer](#the-host-computer)bevindt, gebruikt u het volgende proces om met de container te werken.

1. [Voer de container uit](#run-the-container-with-docker-run)met de vereiste facturerings instellingen. Er zijn meer [voor beelden](anomaly-detector-container-configuration.md#example-docker-run-commands) van de `docker run` opdracht beschikbaar.
1. [Zoek het Voorspellings eindpunt van de container](#query-the-containers-prediction-endpoint)op.

## <a name="run-the-container-with-docker-run"></a>Voer de container uit met `docker run`

Gebruik de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. Raadpleeg de [vereiste para meters verzamelen](#gathering-required-parameters) voor meer informatie over het ophalen van de `{ENDPOINT_URI}` `{API_KEY}` waarden en.

[Voor beelden](anomaly-detector-container-configuration.md#example-docker-run-commands) van de `docker run` opdracht zijn beschikbaar.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Voert een anomalie detectie container uit vanuit de container installatie kopie
* Wijst een CPU-kern en 4 GB aan geheugen toe
* Beschrijft TCP-poort 5000 en wijst een pseudo-TTY toe voor de container
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer.

> [!IMPORTANT]
> De `Eula` `Billing` Opties, en `ApiKey` moeten worden opgegeven om de container uit te voeren. anders wordt de container niet gestart.  Zie [facturering](#billing)voor meer informatie.

### <a name="running-multiple-containers-on-the-same-host"></a>Meerdere containers op dezelfde host uitvoeren

Als u van plan bent om meerdere containers met blootgestelde poorten uit te voeren, moet u ervoor zorgen dat elke container met een andere poort wordt uitgevoerd. Voer bijvoorbeeld de eerste container uit op poort 5000 en de tweede container op poort 5001.

Vervang en door de `<container-registry>` `<container-name>` waarden van de containers die u gebruikt. Deze hoeven niet dezelfde container te zijn. U kunt de afwijkende detector container en de LUIS-container samen op de HOST uitvoeren, maar u kunt ook meerdere afwijkende detector containers uitvoeren.

Voer de eerste container uit op poort 5000.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Voer de tweede container uit op poort 5001.


```bash
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Elke volgende container moet zich op een andere poort bevinden.

## <a name="query-the-containers-prediction-endpoint"></a>Een query uitvoeren op het voorspellingseindpunt van de container

De container bevat op REST gebaseerde eindpunt-API's voor queryvoorspelling.

Gebruik de host, http://localhost:5000, voor container-API's.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een uitvoer [koppeling](anomaly-detector-container-configuration.md#mount-settings) en logboek registratie ingeschakeld, genereert de container logboek bestanden die handig zijn om problemen op te lossen die optreden tijdens het starten of uitvoeren van de container.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De afwijkende detector containers verzenden facturerings gegevens naar Azure met behulp van een _afwijkende detector_ bron in uw Azure-account.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie [containers configureren](anomaly-detector-container-configuration.md)voor meer informatie over deze opties.

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd voor het downloaden, installeren en uitvoeren van afwijkende detector containers. Samenvatting:

* Afwijkings detectie biedt één Linux-container voor docker, die anomalie detectie inkapselt met batch versus streaming, verwachte bereik reductie en gevoeligheids afstemming.
* Container installatie kopieën worden gedownload van een persoonlijke Azure Container Registry toegewezen voor containers.
* Container installatie kopieën worden uitgevoerd in docker.
* U kunt de REST API of SDK gebruiken voor het aanroepen van bewerkingen in afwijkende detector containers door de URI van de host op te geven van de container.
* U moet factuur gegevens opgeven bij het instantiëren van een container.

> [!IMPORTANT]
> Cognitive Services-containers mogen niet worden uitgevoerd zonder te zijn verbonden met Azure voor meting. Klanten moeten de containers in staat stellen om de facturerings gegevens te allen tijde met de meet service te communiceren. Cognitive Services containers verzenden geen klant gegevens (zoals de gegevens van de tijd reeks die worden geanalyseerd) naar micro soft.

## <a name="next-steps"></a>Volgende stappen

* Containers voor configuratie-instellingen [configureren](anomaly-detector-container-configuration.md) controleren
* [Een anomalie detectie container implementeren naar Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Meer informatie over de API-service voor anomalie detectie](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
