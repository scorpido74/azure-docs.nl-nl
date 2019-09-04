---
title: Spraak containers installeren-spraak service
titleSuffix: Azure Cognitive Services
description: Installeer en voer spraak containers uit. Met spraak naar tekst worden audio stromen naar tekst getranscribeerd in realtime die uw toepassingen, hulpprogram ma's of apparaten kunnen gebruiken of weer geven. Tekst-naar-spraak zet invoer tekst om in humane-achtige, gesynthesizerde spraak.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 7708133fcba0d594ecd420afd8da1b2881055aa7
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241021"
---
# <a name="install-and-run-speech-service-containers"></a>Speech Service-containers installeren en uitvoeren

Met spraak containers kunnen klanten één spraak toepassings architectuur maken die is geoptimaliseerd om te profiteren van zowel robuuste Cloud mogelijkheden als Edge-locatie. 

De twee spraak containers zijn **spraak-naar-tekst** en **tekst-naar-spraak**. 

|Function|Functies|Meest recent|
|-|-|--|
|Spraak-naar-tekst| <li>Transcribeert doorlopend realtime spraak of batch opnames in tekst met tussenliggende resultaten.|1.2.0|
|Tekst naar spraak| <li>Converteert tekst naar natuurlijk klinkende spraak. met SSML (tekst zonder opmaak of spraak-synthese Markup Language). |1.2.0|

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet voldoen aan de volgende vereisten voordat u spraak containers kunt gebruiken:

|Vereist|Doel|
|--|--|
|Docker-engine| De docker-engine moet zijn geïnstalleerd op een [hostcomputer](#the-host-computer). Docker biedt pakketten voor het configureren van de docker-omgeving op [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)en [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Zie voor een uitleg van de basisprincipes van Docker en containers, de [dockeroverzicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker moet worden geconfigureerd, zodat de containers om te verbinden met en facturering gegevens verzenden naar Azure. <br><br> **In Windows**moet docker ook worden geconfigureerd voor de ondersteuning van Linux-containers.<br><br>|
|Vertrouwd met docker | U moet een basis kennis hebben van docker-concepten, zoals registers, opslag plaatsen, containers en container installatie kopieën, en kennis van basis `docker` opdrachten.| 
|Spraak resource |Als u deze containers wilt gebruiken, hebt u het volgende nodig:<br><br>Een Azure- _spraak_ bron om de bijbehorende API-sleutel en eind punt-URI op te halen. Beide waarden zijn beschikbaar op het **spraak** overzicht van de Azure Portal en op de pagina sleutels. Ze zijn beide nodig om de container te starten.<br><br>**{API_KEY}** : Een van de twee beschik bare bron sleutels op de pagina **sleutels**<br><br>**{ENDPOINT_URI}** : Het eind punt op de pagina **overzicht**|

## <a name="request-access-to-the-container-registry"></a>Toegang aanvragen tot het container register

U moet eerst het [aanvraag formulier voor de Cognitive Services spraak containers](https://aka.ms/speechcontainerspreview/) volt ooien en verzenden om toegang tot de container aan te vragen. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Ondersteuning voor geavanceerde vector uitbreidingen

De **host** is de computer waarop de docker-container wordt uitgevoerd. De host moet ondersteuning bieden voor [Geavanceerde vector uitbreidingen](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). U kunt deze ondersteuning op Linux-hosts controleren met de volgende opdracht: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

In de volgende tabel worden de minimale en aanbevolen CPU-kernen en het geheugen dat moet worden toegewezen voor elke spraak container beschreven.

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
|cognitieve Services-spraak naar tekst | 2 kern geheugen<br>2 GB geheugen  | 4 kern<br>4 GB geheugen  |
|cognitieve-Services-tekst-naar-spraak | 1 kern geheugen van 0,5 GB| 2 Core, 1 GB geheugen |

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.

Core en geheugen komen overeen met `--cpus` de `--memory` instellingen en, die worden gebruikt als onderdeel van `docker run` de opdracht.

**Opmerking**; De minimale en aanbevolen waarde zijn gebaseerd op de limieten van docker, *niet* op de hostcomputer. Bijvoorbeeld: spraak naar tekst containers delen van een groot taal model en het wordt _Aanbevolen_ dat het hele bestand in het geheugen past, wat een extra 4-6 GB is. Het is ook mogelijk dat de eerste uitvoering van een van de containers langer duurt, omdat modellen in het geheugen worden gewisseld.

## <a name="get-the-container-image-with-docker-pull"></a>De container installatie kopie ophalen met`docker pull`

Er zijn container installatie kopieën voor spraak beschikbaar.

| Container | Opslagplaats |
|-----------|------------|
| cognitieve Services-spraak naar tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitieve-Services-tekst-naar-spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Taal instelling is in container label

De `latest` tag haalt de land `en-us` instellingen en `jessarus` de stem op.

#### <a name="speech-to-text-locales"></a>Spraak-naar-tekst-land instellingen

Alle tags, met uitzonde ring van voor `latest` , hebben de volgende indeling, waarbij de `<culture>` container de land instellingen aangeeft:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

De volgende code is een voor beeld van de indeling:

```
1.2.0-amd64-en-us-preview
```

De volgende tabel bevat de ondersteunde land instellingen voor **spraak naar tekst** in de 1.2.0-versie van de container:

|Taal instelling|Labels|
|--|--|
|Chinees|`zh-cn`|
|Engels |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|Frans |`fr-ca`<br>`fr-fr`|
|Duits|`de-de`|
|Italiaans|`it-it`|
|Japans|`ja-jp`|
|Koreaans|`ko-kr`|
|Portugees|`pt-br`|
|Spaans|`es-es`<br>`es-mx`|

#### <a name="text-to-speech-locales"></a>Land instellingen voor tekst naar spraak

Alle tags, met uitzonde ring van voor `latest` , hebben de volgende indeling, waarbij de `<culture>` land `<voice>` instellingen en de de stem van de container worden aangegeven:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

De volgende code is een voor beeld van de indeling:

```
1.2.0-amd64-en-us-jessarus-preview
```

De volgende tabel bevat de ondersteunde land instellingen voor **tekst naar spraak** in de 1.2.0-versie van de container:

|Taal instelling|Labels|Ondersteunde stemmen|
|--|--|--|
|Chinees|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao-apollo|
|Engels |`en-au`|catherine<br>hayleyrus|
|Engels |`en-gb`|George-Apollo<br>hazelrus<br>susan-apollo|
|Engels |`en-in`|heera-apollo<br>priyarus<br>ravi-apollo<br>|
|Engels |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|Frans|`fr-ca`|caroline<br>harmonierus|
|Frans|`fr-fr`|hortenserus<br>julie-apollo<br>Paul-Apollo|
|Duits|`de-de`|hedda<br>heddarus<br>stefan-apollo|
|Italiaans|`it-it`|cosimo-apollo<br>luciarus|
|Japans|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|Koreaans|`ko-kr`|heamirus|
|Portugees|`pt-br`|daniel-apollo<br>heloisarus|
|Spaans|`es-es`|elenarus<br>Laura-Apollo<br>pablo-apollo<br>|
|Spaans|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Docker-pull voor de spraak containers

#### <a name="speech-to-text"></a>Spraak-naar-tekst

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Tekst naar spraak

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>De container gebruiken

Wanneer de container zich op de [hostcomputer](#the-host-computer)bevindt, gebruikt u het volgende proces om met de container te werken.

1. [Voer de container uit](#run-the-container-with-docker-run)met de vereiste facturerings instellingen. Er zijn meer [voor beelden](speech-container-configuration.md#example-docker-run-commands) van de `docker run` opdracht beschikbaar.
1. [Zoek het Voorspellings eindpunt van de container](#query-the-containers-prediction-endpoint)op.

## <a name="run-the-container-with-docker-run"></a>Voer de container uit met`docker run`

Gebruik de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) om een van de drie containers uit te voeren. De opdracht maakt gebruik van de volgende para meters:

**Tijdens de preview**moeten de facturerings instellingen geldig zijn voor het starten van de container, maar worden er geen kosten in rekening gebracht voor gebruik.

| Tijdelijke aanduiding | Value |
|-------------|-------|
|{API_KEY} | Deze sleutel wordt gebruikt om de container te starten en is beschikbaar op de pagina spraak sleutels van de Azure Portal.  |
|{ENDPOINT_URI} | De waarde voor de URL van het facturerings eindpunt is beschikbaar op de overzichts pagina van het Azure Portal.|

Vervang deze para meters door uw eigen waarden in de `docker run` volgende voorbeeld opdracht.

### <a name="text-to-speech"></a>Tekst naar spraak

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="speech-to-text"></a>Spraak-naar-tekst

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Deze opdracht:

* Voert een spraak container uit vanuit de container installatie kopie
* Wijst twee CPU-kernen en 2 gigabytes (GB) aan geheugen toe
* Gebruikt TCP-poort 5000 en wijst er een pseudo-TTY voor de container
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer.

> [!IMPORTANT]
> De `Eula`, `Billing`, en `ApiKey` opties moeten worden opgegeven voor het uitvoeren van de container; anders wordt de container niet start.  Zie voor meer informatie, [facturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Query uitvoeren op het prediction-eind punt van de container

|Container|Eindpunt|
|--|--|
|Spraak-naar-tekst|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Tekst naar spraak|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Spraak-naar-tekst

De container biedt Api's voor query-eind punten op basis van websockets die worden geopend via de [Speech SDK](index.yml).

De Speech SDK maakt standaard gebruik van online spraak Services. Als u de container wilt gebruiken, moet u de initialisatie methode wijzigen. Zie de onderstaande voor beelden.

#### <a name="for-c"></a>ZoC#

Wijzigen van het gebruik van deze Azure-Cloud initialisatie aanroep:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

voor deze aanroep met behulp van het container eindpunt:

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"),
    "YourSubscriptionKey");
```

#### <a name="for-python"></a>Voor python

Wijzigen van het gebruik van deze Azure-Cloud initialisatie aanroep

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

voor deze aanroep met behulp van het container eindpunt:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Tekst naar spraak

De container bevat REST-endpoint Api's die [hier](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech) kunnen worden gevonden en voor beelden kunnen [hier](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/)worden gevonden.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer u de container uitvoert, gebruikt de container **stdout** en **stderr** voor het uitvoeren van informatie die nuttig is voor het oplossen van problemen die zich voordoen tijdens het starten of uitvoeren van de container.

## <a name="billing"></a>Billing

De spraak containers verzenden facturerings gegevens naar Azure met behulp van een _spraak_ bron op uw Azure-account.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie voor meer informatie over deze opties [containers configureren](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd om spraak containers te downloaden, te installeren en uit te voeren. Samenvatting:

* Speech biedt twee Linux-containers voor docker, zodat spraak op tekst en tekst naar spraak kan worden ingekapseld.
* Container installatie kopieën worden gedownload uit het persoonlijke container register in Azure.
* Containerinstallatiekopieën uitvoeren in Docker.
* U kunt de REST API of SDK gebruiken voor het aanroepen van bewerkingen in spraak containers door de URI van de host op te geven van de container.
* U moet facturerings gegevens opgeven bij het instantiëren van een container.

> [!IMPORTANT]
>  Cognitive Services-containers zijn geen licentie om uit te voeren zonder verbinding met Azure voor het meten. Klanten moeten de containers om te communiceren factureringsgegevens met de softwarelicentiecontrole-service te allen tijde inschakelen. Cognitive Services-containers verzenden klantgegevens (zoals de afbeelding of tekst die wordt geanalyseerd) niet naar Microsoft.

## <a name="next-steps"></a>Volgende stappen

* Beoordeling [containers configureren](speech-container-configuration.md) voor configuratie-instellingen
* Meer [Cognitive Services containers](../cognitive-services-container-support.md) gebruiken
