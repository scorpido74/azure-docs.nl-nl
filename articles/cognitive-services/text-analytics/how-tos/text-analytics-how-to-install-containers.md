---
title: Containers installeren en uitvoeren - Text Analytics
titleSuffix: Azure Cognitive Services
description: Hoe u containers voor Text Analytics downloaden, installeren en uitvoeren in deze walkthrough-zelfstudie.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2d44df1bb828140e662b06ffbe5fb14f207f68e0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876996"
---
# <a name="install-and-run-text-analytics-containers"></a>De Text Analytics-containers installeren en uitvoeren

Containers stellen u in staat om de TEXT Analytic API's in uw eigen omgeving uit te voeren en zijn ideaal voor uw specifieke vereisten voor beveiliging en gegevensbeheer. De text analytics-containers bieden geavanceerde verwerking van natuurlijke taal over ruwe tekst en bevatten drie belangrijke functies: sentimentanalyse, sleutelzinextractie en taaldetectie. Entiteitskoppeling wordt momenteel niet ondersteund in een container.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

> [!IMPORTANT]
> De gratis account is beperkt tot 5.000 transacties per maand en alleen de <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> prijzenvoor</a> containers **gratis** en **standaard** zijn geldig. Zie [Gegevenslimieten voor](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits)meer informatie over transactieaanvraagtarieven.

## <a name="prerequisites"></a>Vereisten

Als u een van de Text Analytics-containers wilt uitvoeren, moet u de hostcomputer- en containeromgevingen hebben.

## <a name="preparation"></a>Voorbereiding

U moet aan de volgende vereisten voldoen voordat u Text Analytics-containers gebruikt:

|Vereist|Doel|
|--|--|
|Docker-engine| U moet de Docker Engine geïnstalleerd op een [hostcomputer.](#the-host-computer) Docker biedt pakketten waarmee de Docker-omgeving op [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms) kan worden geconfigureerd. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.<br><br> Docker moet zijn geconfigureerd om de containers in staat te stellen verbinding te maken met en factureringsgegevens naar Azure te verzenden. <br><br> **Op Windows**moet Docker ook zijn geconfigureerd om Linux-containers te ondersteunen.<br><br>|
|Vertrouwdheid met Docker | U moet een basiskennis hebben van Docker-concepten, zoals registers, opslagplaatsen, containers en `docker` containerafbeeldingen, evenals kennis van basisopdrachten.| 
|Text Analytics-bron |Om de container te kunnen gebruiken, moet u beschikken over:<br><br>Een Azure [Text Analytics-bron](../../cognitive-services-apis-create-account.md) om de bijbehorende API-sleutel en eindpunt URI op te halen. Beide waarden zijn beschikbaar op de pagina's Text Analytics Overview and Keys van de Azure-portal en zijn vereist om de container te starten.<br><br>**{API_KEY}**: Een van de twee beschikbare resourcesleutels op de pagina **Sleutels**<br><br>**{ENDPOINT_URI}**: Het eindpunt zoals vermeld op de **pagina Overzicht**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

In de volgende tabel worden de minimale en aanbevolen CPU-cores beschreven, ten minste 2,6 gigahertz (GHz) of sneller, en geheugen, in gigabytes (GB), om toe te wijzen voor elke Text Analytics-container.

# <a name="key-phrase-extraction"></a>[Sleuteltermextractie](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detection"></a>[Taaldetectie](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.
* TPS - transacties per seconde

Kern en geheugen `--cpus` komen `--memory` overeen met de en `docker run` instellingen, die worden gebruikt als onderdeel van de opdracht.

## <a name="get-the-container-image-with-docker-pull"></a>De containerafbeelding biju halen`docker pull`

Containerafbeeldingen voor Text Analytics zijn beschikbaar in het Microsoft Container Registry.

# <a name="key-phrase-extraction"></a>[Sleuteltermextractie](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detection"></a>[Taaldetectie](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Koppeling van docker voor de Text Analytics-containers

# <a name="key-phrase-extraction"></a>[Sleuteltermextractie](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Taaldetectie](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>Hoe de container te gebruiken

Zodra de container zich op de [hostcomputer bevindt,](#the-host-computer)gebruikt u het volgende proces om met de container te werken.

1. [Voer de container](#run-the-container-with-docker-run)uit met de vereiste factureringsinstellingen. Meer [voorbeelden](../text-analytics-resource-container-config.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar.
1. [Query the container prediction endpoint](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Voer de container uit met`docker run`

Gebruik de opdracht [Docker run](https://docs.docker.com/engine/reference/commandline/run/) om een van de drie containers uit te voeren. Raadpleeg [de vereiste parameters verzamelen](#gathering-required-parameters) voor `{ENDPOINT_URI}` meer `{API_KEY}` informatie over hoe u de en waarden krijgen.

[Voorbeelden](../text-analytics-resource-container-config.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar.

# <a name="key-phrase-extraction"></a>[Sleuteltermextractie](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Taaldetectie](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> De `Eula` `Billing`opties `ApiKey` en de opties moeten worden opgegeven om de container uit te voeren; Anders start de container niet.  Zie [Facturering voor](#billing)meer informatie.

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Het voorspellingseindpunt van de container opvragen

De container biedt OP RUST gebaseerde queryvoorspellingseindpunt-API's.

Gebruik de `http://localhost:5000`host, voor container API's.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een [uitvoerbevestiging](../text-analytics-resource-container-config.md#mount-settings) en logboekregistratie is ingeschakeld, genereert de container logboekbestanden die handig zijn om problemen op te lossen die zich voordoen tijdens het starten of uitvoeren van de container.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De tekstanalysecontainers verzenden factureringsgegevens naar Azure met behulp van een _Text Analytics-bron_ op uw Azure-account. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie [Containers configureren](../text-analytics-resource-container-config.md)voor meer informatie over deze opties.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werkstroom geleerd voor het downloaden, installeren en uitvoeren van Text Analytics-containers. Samenvatting:

* Text Analytics biedt drie Linux-containers voor Docker, waarin verschillende mogelijkheden worden ingekapseld:
   * *Sleuteltermextractie*
   * *Taaldetectie*
   * *Sentimentanalyse*
* Containerafbeeldingen worden gedownload uit het Microsoft Container Registry (MCR) in Azure.
* Containerafbeeldingen worden uitgevoerd in Docker.
* U de REST API of SDK gebruiken om bewerkingen aan te roepen in Text Analytics-containers door de hostURI van de container op te geven.
* U moet factureringsgegevens opgeven bij het momentmaken van een container.

> [!IMPORTANT]
> Cognitive Services-containers hebben geen licentie om uit te voeren zonder dat deze is verbonden met Azure voor meting. Klanten moeten de containers te allen tijde in staat stellen factureringsgegevens met de meetservice te communiceren. Cognitive Services-containers verzenden geen klantgegevens (bijvoorbeeld de afbeelding of tekst die wordt geanalyseerd) naar Microsoft.

## <a name="next-steps"></a>Volgende stappen

* Containers [configureren controleren](../text-analytics-resource-container-config.md) voor configuratie-instellingen
* Raadpleeg [veelgestelde vragen (FAQ)](../text-analytics-resource-faq.md) om problemen met betrekking tot functionaliteit op te lossen.
