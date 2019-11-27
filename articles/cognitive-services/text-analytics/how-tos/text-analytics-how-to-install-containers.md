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
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 6e05dc2136211bcd15a9f0583358b05ccbf96f5a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383165"
---
# <a name="install-and-run-text-analytics-containers"></a>De Text Analytics-containers installeren en uitvoeren

Met containers kunt u de tekst analyse-Api's in uw eigen omgeving uitvoeren en zijn geweldig voor uw specifieke vereisten voor beveiliging en gegevens beheer. De Text Analytics-containers bieden geavanceerde verwerking van natuurlijke taal via onbewerkte tekst en bevatten drie belang rijke functies: sentiment analyse, extractie van sleutel zinnen en taal detectie. Het koppelen van entiteiten wordt momenteel niet ondersteund in een container.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u een van de Text Analytics containers wilt uitvoeren, moet u beschikken over de host-computer en de container omgeving.

## <a name="preparation"></a>Voorbereiding

U moet voldoen aan de volgende vereisten voordat u met behulp van Text Analytics-containers:

|Vereist|Doel|
|--|--|
|Docker-engine| De docker-engine moet zijn geïnstalleerd op een [hostcomputer](#the-host-computer). Docker biedt pakketten voor het configureren van de docker-omgeving op [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)en [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Zie het [docker-overzicht](https://docs.docker.com/engine/docker-overview/)voor een primer op basis van docker en container.<br><br> Docker moet worden geconfigureerd, zodat de containers om te verbinden met en facturering gegevens verzenden naar Azure. <br><br> **In Windows**moet docker ook worden geconfigureerd voor de ondersteuning van Linux-containers.<br><br>|
|Vertrouwd met docker | U moet een basis kennis hebben van docker-concepten, zoals registers, opslag plaatsen, containers en container installatie kopieën, en kennis van basis `docker`-opdrachten.| 
|Text Analytics resource |Als u de container wilt gebruiken, hebt u het volgende nodig:<br><br>Een Azure [Text Analytics-resource](../../cognitive-services-apis-create-account.md) om de bijbehorende API-sleutel en eind punt-URI op te halen. Beide waarden zijn beschikbaar op het Text Analytics overzicht van de Azure Portal en de pagina sleutels en zijn vereist om de container te starten.<br><br>**{API_KEY}** : een van de twee beschik bare bron sleutels op de pagina **sleutels**<br><br>**{ENDPOINT_URI}** : het eind punt op de pagina **overzicht**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

De volgende tabel beschrijft de minimale en aanbevolen CPU-kernen, ten minste 2,6 GHz (gigahertz) of sneller, en het geheugen, in gigabytes (GB), om toe te wijzen voor elke container Text Analytics.

# <a name="key-phrase-extractiontabkeyphrase"></a>[Sleuteltermextractie](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detectiontablanguage"></a>[Taaldetectie](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysistabsentiment"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.
* TPS-trans acties per seconde

Core en geheugen komen overeen met de instellingen `--cpus` en `--memory` die worden gebruikt als onderdeel van de `docker run` opdracht.

## <a name="get-the-container-image-with-docker-pull"></a>De container installatie kopie met `docker pull` ophalen

Container installatie kopieën voor Text Analytics zijn beschikbaar op de micro soft-Container Registry.

# <a name="key-phrase-extractiontabkeyphrase"></a>[Sleuteltermextractie](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detectiontablanguage"></a>[Taaldetectie](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysistabsentiment"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Docker-pull voor de Text Analytics containers

# <a name="key-phrase-extractiontabkeyphrase"></a>[Sleuteltermextractie](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detectiontablanguage"></a>[Taaldetectie](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysistabsentiment"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>De container gebruiken

Wanneer de container zich op de [hostcomputer](#the-host-computer)bevindt, gebruikt u het volgende proces om met de container te werken.

1. [Voer de container uit](#run-the-container-with-docker-run)met de vereiste facturerings instellingen. Er zijn meer [voor beelden](../text-analytics-resource-container-config.md#example-docker-run-commands) van de `docker run`-opdracht beschikbaar.
1. [Zoek het Voorspellings eindpunt van de container](#query-the-containers-prediction-endpoint)op.

## <a name="run-the-container-with-docker-run"></a>De container met `docker run` uitvoeren

Gebruik de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) om een van de drie containers uit te voeren. Raadpleeg de [vereiste para meters verzamelen](#gathering-required-parameters) voor meer informatie over het ophalen van de `{ENDPOINT_URI}` en `{API_KEY}` waarden.

[Voor beelden](../text-analytics-resource-container-config.md#example-docker-run-commands) van de opdracht `docker run` zijn beschikbaar.

# <a name="key-phrase-extractiontabkeyphrase"></a>[Sleuteltermextractie](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detectiontablanguage"></a>[Taaldetectie](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysistabsentiment"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> De opties `Eula`, `Billing`en `ApiKey` moeten worden opgegeven om de container uit te voeren. anders wordt de container niet gestart.  Zie [facturering](#billing)voor meer informatie.

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Query uitvoeren op het prediction-eind punt van de container

De container bevat op REST gebaseerde query Voorspellings eindpunt-Api's.

Gebruik de host, `http://localhost:5000`voor container-Api's.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een uitvoer [koppeling](../text-analytics-resource-container-config.md#mount-settings) en logboek registratie ingeschakeld, genereert de container logboek bestanden die handig zijn om problemen op te lossen die optreden tijdens het starten of uitvoeren van de container.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De Text Analytics-containers verzenden facturerings gegevens naar Azure met behulp van een _Text Analytics_ resource in uw Azure-account. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie [containers configureren](../text-analytics-resource-container-config.md)voor meer informatie over deze opties.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u geleerd concepten en werkstroom voor het downloaden, installeren en Text Analytics-containers uitvoeren. Samenvatting:

* Text Analytics biedt drie Linux-containers voor docker, met inkapseling van diverse mogelijkheden:
   * *Sleuteltermextractie*
   * *Taaldetectie*
   * *Sentimentanalyse*
* Containerinstallatiekopieën worden gedownload uit het Microsoft Container Registry (MCR) in Azure.
* Containerinstallatiekopieën uitvoeren in Docker.
* U kunt de REST-API of de SDK gebruiken om aan te roepen van bewerkingen in Text Analytics-containers door de host-URI van de container op te geven.
* Bij het instantiëren van een container, moet u informatie over facturering opgeven.

> [!IMPORTANT]
> Cognitive Services-containers zijn geen licentie om uit te voeren zonder verbinding met Azure voor het meten. Klanten moeten de containers om te communiceren factureringsgegevens met de softwarelicentiecontrole-service te allen tijde inschakelen. Cognitive Services-containers verzenden klantgegevens (zoals de afbeelding of tekst die wordt geanalyseerd) niet naar Microsoft.

## <a name="next-steps"></a>Volgende stappen

* Containers voor configuratie-instellingen [configureren](../text-analytics-resource-container-config.md) controleren
* Raadpleeg Veelgestelde [vragen (FAQ)](../text-analytics-resource-faq.md) om problemen met betrekking tot de functionaliteit op te lossen.
