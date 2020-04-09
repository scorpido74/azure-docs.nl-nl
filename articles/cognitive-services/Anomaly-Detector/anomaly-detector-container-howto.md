---
title: Containers installeren en uitvoeren voor het gebruik van Anomaly Detector API
titleSuffix: Azure Cognitive Services
description: Gebruik de geavanceerde algoritmen van de Anomaly Detector API om afwijkingen in uw tijdreeksgegevens te identificeren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fa25d27e99a9516d461a84dde184e2a6412baa0b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875034"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Anomaliedetectorcontainers installeren en uitvoeren (voorbeeld)

De Anomaliedetector heeft de volgende functionaliteit voor containerfuncties:

| Functie | Functies |
|--|--|
| Anomaliedetector | <li> Detecteert afwijkingen zoals ze zich in real-time voordoen. <li> Detecteert afwijkingen in uw gegevensset als een batch. <li> Leidt het verwachte normale bereik van uw gegevens af. <li> Ondersteunt aanpassing van de gevoeligheid van anomaliedetectie om beter bij uw gegevens te passen. |

Zie voor meer informatie over de API's:
* [Meer informatie over de API-service voor Anomalie Detector](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet aan de volgende voorwaarden voldoen voordat u Anomalie Detector-containers gebruikt:

|Vereist|Doel|
|--|--|
|Docker-engine| U moet de Docker Engine geïnstalleerd op een [hostcomputer.](#the-host-computer) Docker biedt pakketten waarmee de Docker-omgeving op [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms) kan worden geconfigureerd. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.<br><br> Docker moet zijn geconfigureerd om de containers in staat te stellen verbinding te maken met en factureringsgegevens naar Azure te verzenden. <br><br> **Op Windows**moet Docker ook zijn geconfigureerd om Linux-containers te ondersteunen.<br><br>|
|Vertrouwdheid met Docker | U moet een basiskennis hebben van Docker-concepten, zoals registers, opslagplaatsen, containers en `docker` containerafbeeldingen, evenals kennis van basisopdrachten.| 
|Anomaliedetectorbron |Om deze containers te kunnen gebruiken, moet u beschikken over:<br><br>Een Azure _Anomaly Detector-bron_ om de bijbehorende API-sleutel en eindpunt URI te krijgen. Beide waarden zijn beschikbaar op de pagina's Anomaliedetectoroverzicht en Sleutels van de **Azure-portal** en zijn vereist om de container te starten.<br><br>**{API_KEY}**: Een van de twee beschikbare resourcesleutels op de pagina **Sleutels**<br><br>**{ENDPOINT_URI}**: Het eindpunt zoals vermeld op de **pagina Overzicht**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Toegang tot het containerregister aanvragen

U moet eerst het [formulier anomaliedetectorcontainerformulier](https://aka.ms/adcontainer) invullen en indienen om toegang tot de container aan te vragen.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

In de volgende tabel worden de minimale en aanbevolen CPU-cores en het geheugen beschreven die moeten worden toegewezen voor de Anomaliedetectorcontainer.

| QPS(Query's per seconde) | Minimum | Aanbevolen |
|-----------|---------|-------------|
| 10 QPS | 4-core geheugen met 1 GB | 8-core geheugen van 2 GB |
| 20 QPS | 8-cores geheugen met 2 GB | 16-core 4 GB geheugen |

Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.

Kern en geheugen `--cpus` komen `--memory` overeen met de en `docker run` instellingen, die worden gebruikt als onderdeel van de opdracht.

## <a name="get-the-container-image-with-docker-pull"></a>De containerafbeelding biju halen`docker pull`

Gebruik [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) de opdracht om een containerafbeelding te downloaden.

| Container | Opslagplaats |
|-----------|------------|
| cognitief-diensten-anomalie-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker pull voor de Anomaly Detector container

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Hoe de container te gebruiken

Zodra de container zich op de [hostcomputer bevindt,](#the-host-computer)gebruikt u het volgende proces om met de container te werken.

1. [Voer de container](#run-the-container-with-docker-run)uit met de vereiste factureringsinstellingen. Meer [voorbeelden](anomaly-detector-container-configuration.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar.
1. [Query the container prediction endpoint](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Voer de container uit met`docker run`

Gebruik de opdracht [Docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. Raadpleeg [het verzamelen van vereiste parameters](#gathering-required-parameters) `{ENDPOINT_URI}` voor `{API_KEY}` meer informatie over hoe u de en waarden krijgen.

[Voorbeelden](anomaly-detector-container-configuration.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Een Anomaliedetectorcontainer uit de containerafbeelding worden uitgevoerd
* Hiermee wordt één CPU-kern en 4 gigabyte (GB) geheugen toegewezen
* Stelt TCP-poort 5000 bloot en wijst een pseudo-TTY toe voor de container
* Verwijdert automatisch de container nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer. 

> [!IMPORTANT]
> De `Eula` `Billing`opties `ApiKey` en de opties moeten worden opgegeven om de container uit te voeren; Anders start de container niet.  Zie [Facturering voor](#billing)meer informatie.

### <a name="running-multiple-containers-on-the-same-host"></a>Meerdere containers op dezelfde host uitvoeren

Als u van plan bent om meerdere containers met blootgestelde poorten uit te voeren, moet u ervoor zorgen dat elke container met een andere poort wordt uitgevoerd. Voer bijvoorbeeld de eerste container uit op poort 5000 en de tweede container op poort 5001.

Vervang `<container-registry>` de `<container-name>` en met de waarden van de containers die u gebruikt. Deze hoeven niet dezelfde container te zijn. U de Anomaliedetectorcontainer en de LUIS-container samen op de HOST laten draaien of u meerdere Anomaly Detector-containers laten uitvoeren. 

Voer de eerste container op poort 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Voer de tweede container op poort 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Elke volgende container moet zich op een andere poort bevindt. 

## <a name="query-the-containers-prediction-endpoint"></a>Het voorspellingseindpunt van de container opvragen

De container biedt OP RUST gebaseerde queryvoorspellingseindpunt-API's. 

Gebruik de http://localhost:5000host, voor container API's.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een [uitvoerbevestiging](anomaly-detector-container-configuration.md#mount-settings) en logboekregistratie is ingeschakeld, genereert de container logboekbestanden die handig zijn om problemen op te lossen die zich voordoen tijdens het starten of uitvoeren van de container.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De containers Anomaliedetector verzenden factureringsgegevens naar Azure met behulp van een _Anomaliedetectorbron_ op uw Azure-account. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie [Containers configureren](anomaly-detector-container-configuration.md)voor meer informatie over deze opties.

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werkstroom geleerd voor het downloaden, installeren en uitvoeren van Anomaly Detector-containers. Samenvatting:

* Anomalie Detector biedt een Linux-container voor Docker, inkapselen anomalie detectie met batch vs streaming, verwachte bereik gevolgtrekking, en gevoeligheid tuning.
* Containerafbeeldingen worden gedownload van een privéAzure Container Registry dat is toegewezen aan het voorbeeld van containers.
* Containerafbeeldingen worden uitgevoerd in Docker.
* U de REST API of SDK gebruiken om bewerkingen aan te roepen in Anomaly Detector-containers door de hostURI van de container op te geven.
* U moet factureringsgegevens opgeven bij het momentmaken van een container.

> [!IMPORTANT]
> Cognitive Services-containers hebben geen licentie om uit te voeren zonder dat deze is verbonden met Azure voor meting. Klanten moeten de containers te allen tijde in staat stellen factureringsgegevens met de meetservice te communiceren. Cognitive Services-containers verzenden geen klantgegevens (bijvoorbeeld de tijdreeksgegevens die worden geanalyseerd) naar Microsoft.

## <a name="next-steps"></a>Volgende stappen

* Containers [configureren controleren](anomaly-detector-container-configuration.md) voor configuratie-instellingen
* [Een anomaliedetectorcontainer implementeren in Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Meer informatie over de API-service voor Anomalie Detector](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
