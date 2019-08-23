---
title: Containers installeren en uitvoeren-Text Analytics
titleSuffix: Azure Cognitive Services
description: Het downloaden, installeren en uitvoeren van containers voor Tekstanalyse overschrijd in deze zelfstudie met stapsgewijze instructies.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dapine
ms.openlocfilehash: ff0be2e9dada758cce96ba7c5eebbf03b00f56c6
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971449"
---
# <a name="install-and-run-text-analytics-containers"></a>Text Analytics containers installeren en uitvoeren

Met containers kunt u de tekst analyse-Api's in uw eigen omgeving uitvoeren en zijn geweldig voor uw specifieke vereisten voor beveiliging en gegevens beheer. De Text Analytics-containers bieden geavanceerde verwerking van natuurlijke taal via onbewerkte tekst en bevatten drie belang rijke functies: sentiment analyse, extractie van sleutel zinnen en taal detectie. Het koppelen van entiteiten wordt momenteel niet ondersteund in een container.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u een van de Text Analytics containers wilt uitvoeren, moet u beschikken over de host-computer en de container omgeving.

## <a name="preparation"></a>Voorbereiding

U moet voldoen aan de volgende vereisten voordat u met behulp van Text Analytics-containers:

|Vereist|Doel|
|--|--|
|Docker-engine| De docker-engine moet zijn geïnstalleerd op een [hostcomputer](#the-host-computer). Docker biedt pakketten voor het configureren van de docker-omgeving op [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)en [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Zie voor een uitleg van de basisprincipes van Docker en containers, de [dockeroverzicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker moet worden geconfigureerd, zodat de containers om te verbinden met en facturering gegevens verzenden naar Azure. <br><br> **In Windows**moet docker ook worden geconfigureerd voor de ondersteuning van Linux-containers.<br><br>|
|Vertrouwd met docker | U moet een basis kennis hebben van docker-concepten, zoals registers, opslag plaatsen, containers en container installatie kopieën, en kennis van basis `docker` opdrachten.| 
|Text Analytics resource |Als u de container wilt gebruiken, hebt u het volgende nodig:<br><br>Een Azure [Text Analytics-resource](../../cognitive-services-apis-create-account.md) om de bijbehorende API-sleutel en eind punt-URI op te halen. Beide waarden zijn beschikbaar op het Text Analytics overzicht van de Azure Portal en de pagina sleutels en zijn vereist om de container te starten.<br><br>**{API_KEY}** : Een van de twee beschik bare bron sleutels op de pagina **sleutels**<br><br>**{ENDPOINT_URI}** : Het eind punt op de pagina **overzicht**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

De volgende tabel beschrijft de minimale en aanbevolen CPU-kernen, ten minste 2,6 GHz (gigahertz) of sneller, en het geheugen, in gigabytes (GB), om toe te wijzen voor elke container Text Analytics.

| Container | Minimum | Aanbevolen | TPS<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|Sleuteltermextractie | 1 Core, 2 GB geheugen | 1 kern geheugen van 4 GB |15, 30|
|Taaldetectie | 1 Core, 2 GB geheugen | 1 kern geheugen van 4 GB |15, 30|
|Sentimentanalyse | 1 Core, 2 GB geheugen | 1 kern geheugen van 4 GB |15, 30|

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.
* TPS-trans acties per seconde

Core en geheugen komen overeen met `--cpus` de `--memory` instellingen en, die worden gebruikt als onderdeel van `docker run` de opdracht.

## <a name="get-the-container-image-with-docker-pull"></a>De container installatie kopie ophalen met`docker pull`

Containerinstallatiekopieën voor Text Analytics zijn beschikbaar via Microsoft Container Registry.

| Container | Opslagplaats |
|-----------|------------|
|Sleuteltermextractie | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Taaldetectie | `mcr.microsoft.com/azure-cognitive-services/language` |
|Sentimentanalyse| `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een container installatie kopie te downloaden van micro soft container Registry.

Zie voor een volledige beschrijving van de beschikbare labels voor de Text Analytics-containers, de volgende containers op de Docker-Hub:

* [Sleuteltermextractie](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Taaldetectie](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Sentimentanalyse](https://go.microsoft.com/fwlink/?linkid=2018654)

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een container installatie kopie te downloaden.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker-pull voor de container voor de extractie van sleutel woorden

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>Docker-pull voor de taal detectie container

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>Docker-pull voor de sentiment-container

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>De container gebruiken

Wanneer de container zich op de [hostcomputer](#the-host-computer)bevindt, gebruikt u het volgende proces om met de container te werken.

1. [Voer de container uit](#run-the-container-with-docker-run)met de vereiste facturerings instellingen. Er zijn meer [voor beelden](../text-analytics-resource-container-config.md#example-docker-run-commands) van de `docker run` opdracht beschikbaar.
1. [Zoek het Voorspellings eindpunt van de container](#query-the-containers-prediction-endpoint)op.

## <a name="run-the-container-with-docker-run"></a>Voer de container uit met`docker run`

Gebruik de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) om een van de drie containers uit te voeren. Raadpleeg de [vereiste para meters verzamelen](#gathering-required-parameters) voor meer informatie over het `{Endpoint_URI}` ophalen `{API_Key}` van de waarden en.

[Voor beelden](../text-analytics-resource-container-config.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar.

### <a name="run-container-example-of-docker-run-command"></a>Container voorbeeld van de opdracht docker run uitvoeren

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Deze opdracht:

* Voert een sleutel woordgroepen container uit vanuit de container installatie kopie
* Wijst een CPU-kern en 4 GB aan geheugen toe
* Gebruikt TCP-poort 5000 en wijst er een pseudo-TTY voor de container
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer.


> [!IMPORTANT]
> De `Eula`, `Billing`, en `ApiKey` opties moeten worden opgegeven voor het uitvoeren van de container; anders wordt de container niet start.  Zie voor meer informatie, [facturering](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Query uitvoeren op het prediction-eind punt van de container

De container bevat op REST gebaseerde query Voorspellings eindpunt-Api's.

Gebruik de host, `http://localhost:5000`voor container-api's.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een uitvoer [koppeling](../text-analytics-resource-container-config.md#mount-settings) en logboek registratie ingeschakeld, genereert de container logboek bestanden die handig zijn om problemen op te lossen die optreden tijdens het starten of uitvoeren van de container.

## <a name="billing"></a>Billing

De Text Analytics-containers verzenden facturerings gegevens naar Azure met behulp van een _Text Analytics_ resource in uw Azure-account. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie voor meer informatie over deze opties [containers configureren](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u geleerd concepten en werkstroom voor het downloaden, installeren en Text Analytics-containers uitvoeren. Samenvatting:

* Text Analytics biedt drie Linux-containers voor Docker, encapsulating sleuteltermextractie, taaldetectie en sentimentanalyse.
* Containerinstallatiekopieën worden gedownload uit het Microsoft Container Registry (MCR) in Azure.
* Containerinstallatiekopieën uitvoeren in Docker.
* U kunt de REST-API of de SDK gebruiken om aan te roepen van bewerkingen in Text Analytics-containers door de host-URI van de container op te geven.
* Bij het instantiëren van een container, moet u informatie over facturering opgeven.

> [!IMPORTANT]
> Cognitive Services-containers zijn geen licentie om uit te voeren zonder verbinding met Azure voor het meten. Klanten moeten de containers om te communiceren factureringsgegevens met de softwarelicentiecontrole-service te allen tijde inschakelen. Cognitive Services-containers verzenden klantgegevens (zoals de afbeelding of tekst die wordt geanalyseerd) niet naar Microsoft.

## <a name="next-steps"></a>Volgende stappen

* Beoordeling [containers configureren](../text-analytics-resource-container-config.md) voor configuratie-instellingen
* Raadpleeg Veelgestelde [vragen (FAQ)](../text-analytics-resource-faq.md) om problemen met betrekking tot de functionaliteit op te lossen.
