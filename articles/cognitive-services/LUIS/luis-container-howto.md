---
title: Docker-containers-LUIS
titleSuffix: Azure Cognitive Services
description: De LUIS-container laadt uw getrainde of gepubliceerde app in een docker-container en biedt toegang tot de query voorspellingen van de API-eind punten van de container.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: dapine
ms.openlocfilehash: 7de529c9478b78ff9dd042df90a061f0a96f5427
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316404"
---
# <a name="install-and-run-luis-docker-containers"></a>LUIS docker-containers installeren en uitvoeren
 
De Language Understanding-container (LUIS) laadt uw getrainde of gepubliceerd Language Understanding model. Als [Luis-app](https://www.luis.ai)biedt de docker-container toegang tot de query voorspellingen van de API-eind punten van de container. U kunt query logboeken van de container verzamelen en deze opnieuw uploaden naar de Language Understanding-app om de nauw keurigheid van de app te verbeteren.

In de volgende video ziet u hoe u deze container gebruikt.

[![Demonstratie van de container voor Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u de LUIS-container wilt uitvoeren, moet u rekening houden met de volgende vereisten:

|Vereist|Doel|
|--|--|
|Docker-engine| De docker-engine moet zijn geïnstalleerd op een [hostcomputer](#the-host-computer). Docker biedt pakketten voor het configureren van de docker-omgeving op [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)en [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Zie voor een uitleg van de basisprincipes van Docker en containers, de [dockeroverzicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker moet worden geconfigureerd, zodat de containers om te verbinden met en facturering gegevens verzenden naar Azure. <br><br> **In Windows**moet docker ook worden geconfigureerd voor de ondersteuning van Linux-containers.<br><br>|
|Vertrouwd met docker | U moet een basis kennis hebben van docker-concepten, zoals registers, opslag plaatsen, containers en container installatie kopieën, en kennis van basis `docker` opdrachten.| 
|App `Cognitive Services` -bestand voor Azure-resource en Luis- [pakket](luis-how-to-start-new-app.md#export-app-for-containers) |Als u de container wilt gebruiken, hebt u het volgende nodig:<br><br>* Een _Cognitive Services_ Azure-resource en de gekoppelde facturerings sleutel de URI van het facturerings eindpunt. Beide waarden zijn beschikbaar op de pagina overzicht en sleutels voor de resource en zijn vereist om de container te starten. <br>* Een getrainde of gepubliceerde app is verpakt als gekoppelde invoer voor de container met de bijbehorende App-ID. U kunt het verpakte bestand ophalen uit de LUIS-portal of de ontwerp-Api's. Als u een LUIS-app-pakket van de [ontwerp-api's](#authoring-apis-for-package-file)krijgt, hebt u ook uw _ontwerp sleutel_nodig.<br><br>Deze vereisten worden gebruikt om opdracht regel argumenten door te geven aan de volgende variabelen:<br><br>**{AUTHORING_KEY}** : Deze sleutel wordt gebruikt om de verpakte app uit de LUIS-service in de Cloud op te halen en uploadt de query logboeken terug naar de Cloud. De indeling is `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}** : Deze ID wordt gebruikt om de app te selecteren. De indeling is `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}** : Deze sleutel wordt gebruikt om de container te starten. U kunt de eindpunt sleutel op twee plaatsen vinden. De eerste is de Azure Portal in de lijst sleutels van de _Cognitive Services_ resource. De eindpunt sleutel is ook beschikbaar in de LUIS-Portal op de pagina sleutels en eindpunt instellingen. Gebruik niet de start sleutel.<br><br>**{ENDPOINT_URI}** : Het eind punt op de pagina overzicht.<br><br>De [ontwerp sleutel en de eindpunt sleutel](luis-boundaries.md#key-limits) hebben verschillende doel einden. Gebruik deze niet om te zetten. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Api's voor het pakket bestand ontwerpen

Api's voor verpakte apps ontwerpen:

* [API voor gepubliceerd pakket](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Niet-gepubliceerd, alleen getrainde pakket-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

Deze container ondersteunt minimale en aanbevolen waarden voor de instellingen:

|Container| Minimum | Aanbevolen | TPS<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|LUIS|1 Core, 2 GB geheugen|1 kern geheugen van 4 GB|20, 40|

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.
* TPS-trans acties per seconde

Core en geheugen komen overeen met `--cpus` de `--memory` instellingen en, die worden gebruikt als onderdeel van `docker run` de opdracht.

## <a name="get-the-container-image-with-docker-pull"></a>De container installatie kopie ophalen met`docker pull`

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een container installatie kopie te downloaden `mcr.microsoft.com/azure-cognitive-services/luis` uit de opslag plaats:

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een container installatie kopie te downloaden.

Zie Luis op docker hub voor een volledige beschrijving `latest` van de beschik bare Tags, [](https://go.microsoft.com/fwlink/?linkid=2043204) zoals in de voor gaande opdracht wordt gebruikt.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>De container gebruiken

Wanneer de container zich op de [hostcomputer](#the-host-computer)bevindt, gebruikt u het volgende proces om met de container te werken.

![Proces voor het gebruik van Language Understanding-container (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exporteer pakket](#export-packaged-app-from-luis) voor container van Luis-portal-of Luis-api's.
1. Verplaats pakket bestand naar de vereiste **invoer** Directory op de [hostcomputer](#the-host-computer). Wijzig de naam van het pakket bestand van het LUIS niet, wijzig, vervang of Decomprimeer het.
1. [Voer de container uit](##run-the-container-with-docker-run)met de vereiste _invoer koppeling_ en facturerings instellingen. Er zijn meer [voor beelden](luis-container-configuration.md#example-docker-run-commands) van de `docker run` opdracht beschikbaar. 
1. [Query's uitvoeren op het voor Spellings eindpunt van de container](#query-the-containers-prediction-endpoint). 
1. Wanneer u klaar bent met de container, [importeert u de eindpunt logboeken](#import-the-endpoint-logs-for-active-learning) van de uitvoer koppeling in de Luis-Portal en [stopt](#stop-the-container) u de container.
1. Gebruik het [actieve leer proces](luis-how-to-review-endpoint-utterances.md) van de Luis-Portal op de pagina **endpoint uitingen controleren** om de app te verbeteren.

De app die in de container wordt uitgevoerd, kan niet worden gewijzigd. In volg orde van de wijziging van de app in de container moet u de App wijzigen in de LUIS-service met behulp van de [Luis](https://www.luis.ai) -portal of de Luis- [ontwerp-api's](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)gebruiken. Vervolgens traint en/of publiceert u een nieuw pakket en voert u de container opnieuw uit.

De LUIS-app in de container kan niet worden geëxporteerd naar de LUIS-service. Alleen de query logboeken kunnen worden geüpload. 

## <a name="export-packaged-app-from-luis"></a>App-pakket exporteren uit LUIS

De LUIS-container vereist een getrainde of gepubliceerde LUIS-app voor het beantwoorden van voorspellings query's van de gebruikers uitingen. Als u de LUIS-app wilt ophalen, gebruikt u de getrainde of gepubliceerde pakket-API. 

De standaard locatie is de `input` submap in relatie tot waar u de `docker run` opdracht uitvoert.  

Plaats het pakket bestand in een map en verwijs naar deze map als de invoer koppeling wanneer u de docker-container uitvoert. 

### <a name="package-types"></a>Pakket typen

De map invoer koppeling kan de **productie**, **fase ring**en getrainde versie van de app tegelijkertijd bevatten. Alle pakketten zijn gekoppeld. 

|Pakket type|Query eind punt-API|Beschik baarheid van query's|Bestands naam indeling pakket|
|--|--|--|--|
|Getraind|GET, POST|Alleen container|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Fasering|GET, POST|Azure en container|`{APPLICATION_ID}_STAGING.gz`|
|Productie|GET, POST|Azure en container|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Wijzig de namen van de LUIS-pakket bestanden niet, vervang ze en voer deze uit.

### <a name="packaging-prerequisites"></a>Vereisten voor verpakking

Voordat u een LUIS-toepassing kunt inpakken, hebt u het volgende nodig:

|Vereisten voor verpakking|Details|
|--|--|
|Resource-exemplaar van Azure _Cognitive Services_|Ondersteunde regio's zijn onder andere<br><br>VS-West`westus`()<br>Europa-west (`westeurope`)<br>Australië-oost (`australiaeast`)|
|Getrainde of gepubliceerde LUIS-app|Zonder niet- [ondersteunde afhankelijkheden](#unsupported-dependencies). |
|Toegang tot het bestands systeem van de [hostcomputer](#the-host-computer) |De hostcomputer moet een [invoer koppeling](luis-container-configuration.md#mount-settings)toestaan.|
  
### <a name="export-app-package-from-luis-portal"></a>App-pakket exporteren vanuit de LUIS-Portal

De LUIS- [Portal](https://www.luis.ai) biedt de mogelijkheid om het getrainde of gepubliceerde app-pakket te exporteren. 

### <a name="export-published-apps-package-from-luis-portal"></a>Het pakket van de gepubliceerde app exporteren vanuit de LUIS-Portal

Het pakket van de gepubliceerde app is beschikbaar op de lijst pagina **mijn apps** . 

1. Meld u aan bij de LUIS- [Portal](https://www.luis.ai).
1. Schakel het selectie vakje links van de naam van de app in de lijst in. 
1. Selecteer het item **exporteren** in de contextuele werk balk boven de lijst.
1. Selecteer **exporteren voor container (gzip)** .
1. Selecteer de omgeving van **productie sleuf** of **faserings sleuf**.
1. Het pakket wordt gedownload vanuit de browser.

![Het gepubliceerde pakket voor de container exporteren vanuit het menu exporteren van de app-pagina](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>Het pakket met de getrainde app uit de LUIS-Portal exporteren

Het pakket met de getrainde app is beschikbaar op de lijst pagina met **versies** . 

1. Meld u aan bij de LUIS- [Portal](https://www.luis.ai).
1. Selecteer de app in de lijst. 
1. Selecteer **beheren** in de navigatie balk van de app.
1. Selecteer **versies** in de linkernavigatiebalk.
1. Schakel het selectie vakje links van de versie naam in de lijst in.
1. Selecteer het item **exporteren** in de contextuele werk balk boven de lijst.
1. Selecteer **exporteren voor container (gzip)** .
1. Het pakket wordt gedownload vanuit de browser.

![Het getrainde pakket voor de container exporteren vanuit het menu exporteren van de pagina versies](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Het pakket van de gepubliceerde app uit de API exporteren

Gebruik de volgende REST API methode voor het inpakken van een LUIS-app die u al hebt [gepubliceerd](luis-how-to-publish-app.md). Door uw eigen juiste waarden te vervangen door de tijdelijke aanduidingen in de API-aanroep, met behulp van de tabel onder de HTTP-specificatie.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Tijdelijke aanduiding | Value |
|-------------|-------|
| **{APPLICATION_ID}** | De toepassings-ID van de gepubliceerde LUIS-app. |
| **{APPLICATION_ENVIRONMENT}** | De omgeving van de gepubliceerde LUIS-app. Gebruik een van de volgende waarden:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | De ontwerp sleutel van het LUIS-account voor de gepubliceerde LUIS-app.<br/>U kunt uw ontwerp sleutel verkrijgen via de pagina **gebruikers instellingen** op de Luis-Portal. |
| **{AZURE_REGION}** | De juiste Azure-regio:<br/><br/>`westus`-VS-West<br/>`westeurope`-Europa-west<br/>`australiaeast`-Australië-oost |

Als u het gepubliceerde pakket wilt downloaden, raadpleegt u de [API-documentatie hier][download-published-package]. Als het downloaden is voltooid, is het antwoord een LUIS-pakket bestand. Sla het bestand op in de opslag locatie die is opgegeven voor de invoer koppeling van de container. 

### <a name="export-trained-apps-package-from-api"></a>Het pakket met de getrainde app uit de API exporteren

Gebruik de volgende REST API methode voor het inpakken van een LUIS-toepassing die u al hebt [opgeleid](luis-how-to-train.md). Door uw eigen juiste waarden te vervangen door de tijdelijke aanduidingen in de API-aanroep, met behulp van de tabel onder de HTTP-specificatie.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Tijdelijke aanduiding | Value |
|-------------|-------|
| **{APPLICATION_ID}** | De toepassings-ID van de getrainde LUIS-app. |
| **{APPLICATION_VERSION}** | De toepassings versie van de getrainde LUIS-app. |
| **{AUTHORING_KEY}** | De ontwerp sleutel van het LUIS-account voor de gepubliceerde LUIS-app.<br/>U kunt uw ontwerp sleutel verkrijgen via de pagina **gebruikers instellingen** op de Luis-Portal. |
| **{AZURE_REGION}** | De juiste Azure-regio:<br/><br/>`westus`-VS-West<br/>`westeurope`-Europa-west<br/>`australiaeast`-Australië-oost |

Als u het getrainde pakket wilt downloaden, raadpleegt u de [API-documentatie hier][download-trained-package]. Als het downloaden is voltooid, is het antwoord een LUIS-pakket bestand. Sla het bestand op in de opslag locatie die is opgegeven voor de invoer koppeling van de container. 

## <a name="run-the-container-with-docker-run"></a>Voer de container uit met`docker run`

Gebruik de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. Raadpleeg de [vereiste para meters verzamelen](#gathering-required-parameters) voor meer informatie over het `{ENDPOINT_URI}` ophalen `{API_KEY}` van de waarden en.

[Voor beelden](luis-container-configuration.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* In dit voor beeld wordt de Directory `C:` uit het station gebruikt om te voor komen dat er machtigings conflicten optreden in Windows. Als u gebruiken van een specifieke map als de invoermap wilt, moet u mogelijk de docker verlenen machtiging-service. 
* Wijzig de volg orde van de argumenten niet, tenzij u bekend bent met docker-containers.
* Als u een ander besturings systeem gebruikt, gebruikt u de juiste console/terminal, syntaxis voor koppelingen en een regel vervolg teken voor uw systeem. In deze voor beelden wordt ervan uitgegaan dat een Windows `^`-console met een regel voortzettings teken. Omdat de container een Linux-besturings systeem is, gebruikt de doel koppeling een syntaxis voor de Linux-stijl.

Deze opdracht:

* Hiermee wordt een container uit de installatie kopie van de LUIS-container uitgevoerd
* Hiermee wordt de LUIS-app geladen vanuit de invoer koppeling op *C:\Input*, die zich op de container host bevindt
* Wijst twee CPU-kernen en 4 gigabyte (GB) aan geheugen toe
* Gebruikt TCP-poort 5000 en wijst er een pseudo-TTY voor de container
* Slaat container-en LUIS-logboeken op naar de uitvoer koppeling op *C:\output*, dat zich op de container host bevindt
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer. 

Er zijn meer [voor beelden](luis-container-configuration.md#example-docker-run-commands) van de `docker run` opdracht beschikbaar. 

> [!IMPORTANT]
> De `Eula`, `Billing`, en `ApiKey` opties moeten worden opgegeven voor het uitvoeren van de container; anders wordt de container niet start.  Zie voor meer informatie, [facturering](#billing).
> De ApiKey-waarde is de **sleutel** van de **Azure-resources** -pagina in de Luis-Portal en is ook `Cognitive Services` beschikbaar op de pagina Azure-resource sleutels.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Endpoint-Api's die door de container worden ondersteund

Versie 2 en [v3 (preview)](luis-migration-api-v3.md) van de API zijn beschikbaar in de container. 

## <a name="query-the-containers-prediction-endpoint"></a>Query uitvoeren op het prediction-eind punt van de container

De container bevat op REST gebaseerde query Voorspellings eindpunt-Api's. Eind punten voor gepubliceerde (staging of productie) apps hebben een _andere_ route dan eind punten voor getrainde apps. 

Gebruik de host, `http://localhost:5000`voor container-api's. 

|Pakkettype|Methode|Route|Queryparameters|
|--|--|--|--|
|Gepubliceerd|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q={q}<br>& fase ring<br>[&timezoneOffset]<br>[& verbose]<br>[& logboek]<br>|
|Getraind|GET, POST|/luis/v2.0/apps/{appId}/versions/{versionId}?|q={q}<br>[&timezoneOffset]<br>[& verbose]<br>[& logboek]|

De query parameters configureren hoe en wat wordt geretourneerd in de query-antwoord:

|Query parameter|type|Doel|
|--|--|--|
|`q`|string|De utterance van de gebruiker.|
|`timezoneOffset`|nummer|Met de time zone offset kunt u [de tijd zone wijzigen](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) die wordt gebruikt door de vooraf samengestelde entiteit datetimeV2.|
|`verbose`|boolean|Retourneert alle intenten en hun scores als deze zijn ingesteld op waar. De standaard waarde is False, waarmee alleen de hoogste intentie wordt geretourneerd.|
|`staging`|boolean|Retourneert een query van de resultaten van de faserings omgeving indien ingesteld op waar. |
|`log`|boolean|Registreert query's die later kunnen worden gebruikt voor [actief leren](luis-how-to-review-endpoint-utterances.md). De standaard waarde is True.|

### <a name="query-published-app"></a>Gepubliceerde app in de query

Een voor beeld van een krul opdracht voor het uitvoeren van query's op de container voor een gepubliceerde app is:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Als u query's wilt uitvoeren naar de faserings omgeving, wijzigt u de parameter waarde voor de **faserings** query teken reeks in waar: 

`staging=true`

### <a name="query-trained-app"></a>Op query's getrainde app

Een voor beeld van een krul opdracht voor het uitvoeren van query's op de container voor een getrainde app is: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
De versie naam mag Maxi maal 10 tekens bevatten en bevat alleen tekens die in een URL zijn toegestaan. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>De eindpunt logboeken voor actief leren importeren

Als er een uitvoer koppeling is opgegeven voor de Luis-container, worden de logboek bestanden van de app-query opgeslagen `{INSTANCE_ID}` in de uitvoermap, waar de container-ID is. Het logboek van de app-query bevat de query, het antwoord en de tijds tempels voor elke Voorspellings query die wordt verzonden naar de LUIS-container. 

Op de volgende locatie wordt de geneste mapstructuur voor de logboek bestanden van de container weer gegeven.
```
/output/luis/{INSTANCE_ID}/
```
 
Selecteer uw app in de LUIS-Portal en selecteer vervolgens **eindpunt logboeken importeren** om deze logboeken te uploaden. 

![De logboek bestanden van de container importeren voor actief leren](./media/luis-container-how-to/upload-endpoint-log-files.png)

Nadat het logboek is geüpload, [controleert u het eind punt](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) uitingen in de Luis-Portal.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>De container stoppen

Als u de container wilt afsluiten, drukt u in de opdracht regel omgeving waar de container wordt uitgevoerd op **CTRL + C**.

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een uitvoer [koppeling](luis-container-configuration.md#mount-settings) en logboek registratie ingeschakeld, genereert de container logboek bestanden die handig zijn om problemen op te lossen die optreden tijdens het starten of uitvoeren van de container.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De LUIS-container verzendt facturerings gegevens naar Azure met behulp van een _Cognitive Services_ resource in uw Azure-account. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie voor meer informatie over deze opties [containers configureren](luis-container-configuration.md).

## <a name="supported-dependencies-for-latest-container"></a>Ondersteunde afhankelijkheden `latest` voor de container

De meest recente container, uitgebracht op 2019 build, biedt ondersteuning voor:

* Bing spelling controle: aanvragen aan het query Voorspellings eindpunt met de `&spellCheck=true&bing-spell-check-subscription-key={bingKey}` query teken reeks parameters. Gebruik de [Bing spellingcontrole V7-zelf studie](luis-tutorial-bing-spellcheck.md) voor meer informatie. Als deze functie wordt gebruikt, verzendt de container de utterance naar uw Bing Spellingcontrole V7-resource.
* [Nieuwe, vooraf gemaakte domeinen](luis-reference-prebuilt-domains.md): deze domeinen in de onderneming bevatten onder andere entiteiten, voor beeld uitingen en patronen. Breid deze domeinen uit voor eigen gebruik. 

<a name="unsupported-dependencies"></a>

## <a name="unsupported-dependencies-for-latest-container"></a>Niet-ondersteunde afhankelijkheden `latest` voor de container

Als u de [container wilt exporteren](#export-packaged-app-from-luis), moet u niet-ondersteunde afhankelijkheden verwijderen uit uw Luis-app. Wanneer u probeert te exporteren voor container, rapporteert de LUIS-Portal deze niet-ondersteunde functies die u wilt verwijderen.

U kunt een LUIS-toepassing gebruiken als deze geen van de volgende afhankelijkheden **bevat** :

Niet-ondersteunde app-configuraties|Details|
|--|--|
|Niet-ondersteunde container culturen| Nederlands (nl-NL)<br>Japans (ja-JP)<br>Duits wordt alleen ondersteund met de [1.0.2 tokenizer](luis-language-support.md#custom-tokenizer-versions).|
|Niet-ondersteunde entiteiten voor alle cult uren|[](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) Vooraf samengestelde woordgroepen instantie voor alle cult uren|
|Niet-ondersteunde entiteiten voor de Engelse (en-US) cultuur|[GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2) vooraf gemaakte entiteiten|
|Spraak gebeuren|Externe afhankelijkheden worden niet ondersteund in de container.|
|Sentimentanalyse|Externe afhankelijkheden worden niet ondersteund in de container.|

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd voor het downloaden, installeren en uitvoeren van Language Understanding-containers (LUIS). Samenvatting:

* Language Understanding (LUIS) biedt een Linux-container voor docker voor de voor spellingen van eindpunt query's van uitingen.
* Container installatie kopieën worden gedownload uit de micro soft-Container Registry (MCR).
* Containerinstallatiekopieën uitvoeren in Docker.
* U kunt REST API gebruiken om de container-eind punten te doorzoeken door de URI van de host op te geven.
* Bij het instantiëren van een container, moet u informatie over facturering opgeven.

> [!IMPORTANT]
> Cognitive Services-containers zijn geen licentie om uit te voeren zonder verbinding met Azure voor het meten. Klanten moeten de containers om te communiceren factureringsgegevens met de softwarelicentiecontrole-service te allen tijde inschakelen. Cognitive Services containers verzenden geen klant gegevens (bijvoorbeeld de afbeelding of tekst die wordt geanalyseerd) naar micro soft.

## <a name="next-steps"></a>Volgende stappen

* Beoordeling [containers configureren](luis-container-configuration.md) voor configuratie-instellingen
* Raadpleeg [problemen oplossen](troubleshooting.md) voor het oplossen van problemen met betrekking tot de functionaliteit van Luis.
* Meer [Cognitive Services containers](../cognitive-services-container-support.md) gebruiken

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-trained-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip