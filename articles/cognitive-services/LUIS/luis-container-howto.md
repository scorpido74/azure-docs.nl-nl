---
title: Docker containers - LUIS
titleSuffix: Azure Cognitive Services
description: De LUIS-container laadt uw getrainde of gepubliceerde app in een dockercontainer en biedt toegang tot de queryvoorspellingen van de API-eindpunten van de container.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fec6b16eb7f80369904eefc407a9a9c8d6629c9a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879321"
---
# <a name="install-and-run-luis-docker-containers"></a>Luis dockercontainers installeren en uitvoeren
 
De LUIS-container (Language Understanding) laadt uw getrainde of gepubliceerde taalbegripsmodel. Als [LUIS-app](https://www.luis.ai)biedt de dockercontainer toegang tot de queryvoorspellingen van de API-eindpunten van de container. U querylogboeken uit de container verzamelen en deze uploaden naar de app Taalbegrip om de nauwkeurigheid van de voorspelling van de app te verbeteren.

De volgende video toont het gebruik van deze container.

[![Containerdemonstratie voor Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u de LUIS-container wilt uitvoeren, houdt u de volgende voorwaarden op:

|Vereist|Doel|
|--|--|
|Docker-engine| U moet de Docker Engine geïnstalleerd op een [hostcomputer.](#the-host-computer) Docker biedt pakketten waarmee de Docker-omgeving op [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms) kan worden geconfigureerd. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.<br><br> Docker moet zijn geconfigureerd om de containers in staat te stellen verbinding te maken met en factureringsgegevens naar Azure te verzenden. <br><br> **Op Windows**moet Docker ook zijn geconfigureerd om Linux-containers te ondersteunen.<br><br>|
|Vertrouwdheid met Docker | U moet een basiskennis hebben van Docker-concepten, zoals registers, opslagplaatsen, containers en `docker` containerafbeeldingen, evenals kennis van basisopdrachten.| 
|Azure-bron- `Cognitive Services` en [LUIS-pakket-appbestand](luis-how-to-start-new-app.md) |Om de container te kunnen gebruiken, moet u beschikken over:<br><br>* Een _Azure-bron voor Cognitive Services_ en de bijbehorende factureringssleutel het factureringseindpunt URI. Beide waarden zijn beschikbaar op de pagina's Overzicht en Sleutels voor de resource en zijn vereist om de container te starten. <br>* Een getrainde of gepubliceerde app verpakt als een gemonteerde ingang naar de container met de bijbehorende App ID. U het verpakte bestand ophalen via de LUIS-portal of de auteurs-API's. Als u luis verpakte app krijgt van de [authoring API's,](#authoring-apis-for-package-file)moet u ook uw _authoring key_.<br><br>Deze vereisten worden gebruikt om opdrachtregelargumenten door te geven aan de volgende variabelen:<br><br>**{AUTHORING_KEY}**: Deze sleutel wordt gebruikt om de verpakte app uit de LUIS-service in de cloud te halen en de querylogboeken terug te uploaden naar de cloud. De indeling is `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APP_ID}**: Deze id wordt gebruikt om de App te selecteren. De indeling is `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}**: Deze sleutel wordt gebruikt om de container te starten. U vindt de eindpuntsleutel op twee plaatsen. De eerste is de Azure-portal in de lijst met sleutels van de _bron van Cognitive Services._ De eindpuntsleutel is ook beschikbaar in de LUIS-portal op de pagina Sleutels en Eindpuntinstellingen. Gebruik de startsleutel niet.<br><br>**{ENDPOINT_URI}**: Het eindpunt zoals vermeld op de pagina Overzicht.<br><br>De [ontwerpsleutel en de eindpunttoets](luis-boundaries.md#key-limits) hebben verschillende doeleinden. Gebruik ze niet door elkaar. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>API's voor pakketbestand maken

API's voor verpakte apps maken:

* [Gepubliceerde pakket-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Niet-gepubliceerde, alleen getrainde pakket-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

Deze container ondersteunt minimum- en aanbevolen waarden voor de instellingen:

|Container| Minimum | Aanbevolen | Tps<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|LUIS|1 core, 2 GB geheugen|1 core, 4 GB geheugen|20, 40|

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.
* TPS - transacties per seconde

Kern en geheugen `--cpus` komen `--memory` overeen met de en `docker run` instellingen, die worden gebruikt als onderdeel van de opdracht.

## <a name="get-the-container-image-with-docker-pull"></a>De containerafbeelding biju halen`docker pull`

Gebruik [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) de opdracht om een `mcr.microsoft.com/azure-cognitive-services/luis` containerafbeelding uit de opslagplaats te downloaden:

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Zie LUIS op Docker Hub `latest` voor een volledige beschrijving [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) van beschikbare tags, zoals die in de vorige opdracht worden gebruikt.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Hoe de container te gebruiken

Zodra de container zich op de [hostcomputer bevindt,](#the-host-computer)gebruikt u het volgende proces om met de container te werken.

![Proces voor het gebruik van LUIS-container (Language Understanding)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportpakket](#export-packaged-app-from-luis) voor container van LUIS-portal of LUIS API's.
1. Pakketbestand verplaatsen naar de vereiste **invoermap** op de [hostcomputer](#the-host-computer). Wijzig, wijzig, overschrijf of decomprimeer het LUIS-pakketbestand niet.
1. [Voer de container](#run-the-container-with-docker-run)uit met de vereiste _invoerbevestigings-_ en factureringsinstellingen. Meer [voorbeelden](luis-container-configuration.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar. 
1. [Het voorspellingseindpunt van de container opvragen](#query-the-containers-prediction-endpoint). 
1. Wanneer u klaar bent met de container, [importeert u de eindpuntlogboeken](#import-the-endpoint-logs-for-active-learning) van de uitvoerbevestiging in de LUIS-portal en [stopt u](#stop-the-container) de container.
1. Gebruik luis portal's [active learning](luis-how-to-review-endpoint-utterances.md) op de pagina **Eindpuntuitingen controleren** om de app te verbeteren.

De app die in de container wordt uitgevoerd, kan niet worden gewijzigd. Als u de app in de container wilt wijzigen, moet u de app in de LUIS-service wijzigen via de [LUIS-portal](https://www.luis.ai) of de [LUIS-ontwerp-API's](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)gebruiken. Vervolgens trainen en /of publiceren, download dan een nieuw pakket en voer de container opnieuw uit.

De LUIS-app in de container kan niet worden geëxporteerd naar de LUIS-service. Alleen de querylogboeken kunnen worden geüpload. 

## <a name="export-packaged-app-from-luis"></a>Verpakte app exporteren vanuit LUIS

De LUIS-container vereist een getrainde of gepubliceerde LUIS-app om voorspellingsquery's van gebruikersuitingen te beantwoorden. Gebruik de API voor het getrainde of gepubliceerde pakket om de LUIS-app te krijgen. 

De standaardlocatie `input` is de submap ten `docker run` opzichte van waar u de opdracht uitvoert.  

Plaats het pakketbestand in een map en verwijs naar deze map als de invoerhouder wanneer u de dockercontainer uitvoert. 

### <a name="package-types"></a>Pakkettypen

De invoerbevestigingsmap kan tegelijkertijd de modellen **Productie,** **Staging**en **Versioned** van de app bevatten. Alle pakketten zijn gemonteerd.

|Pakkettype|Query-eindpunt-API|Beschikbaarheid van query's|Bestandsnaamindeling voor pakket|
|--|--|--|--|
|Versie|GET, POST|Alleen container|`{APP_ID}_v{APP_VERSION}.gz`|
|Faseren|GET, POST|Azure en container|`{APP_ID}_STAGING.gz`|
|Productie|GET, POST|Azure en container|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Wijzig de naam, wijziging, overschrijven of decomprimeren van de LUIS-pakketbestanden niet.

### <a name="packaging-prerequisites"></a>Verpakkingsvereisten

Voordat u een LUIS-toepassing verpakt, moet u het volgende hebben:

|Verpakkingsvereisten|Details|
|--|--|
|Azure _Cognitive Services-broninstantie_|Ondersteunde regio's zijn o.a.<br><br>West US`westus`( )<br>West-Europa`westeurope`( )<br>Australië-Oost`australiaeast`( )|
|Getrainde of gepubliceerde LUIS-app|Zonder [niet-ondersteunde afhankelijkheden.][unsupported-dependencies] |
|Toegang tot het bestandssysteem van de [hostcomputer](#the-host-computer) |De hostcomputer moet een [invoerbevestiging](luis-container-configuration.md#mount-settings)toestaan.|
  
### <a name="export-app-package-from-luis-portal"></a>App-pakket exporteren vanuit LUIS-portal

De [LUIS-portal](https://www.luis.ai) biedt de mogelijkheid om het pakket van de getrainde of gepubliceerde app te exporteren.

### <a name="export-published-apps-package-from-luis-portal"></a>Het pakket van gepubliceerde apps exporteren vanuit LUIS-portal

Het pakket van de gepubliceerde app is beschikbaar op de **lijstpagina Mijn apps.** 

1. Meld u aan bij het [LUIS-portaal](https://www.luis.ai).
1. Schakel het selectievakje links van de app-naam in de lijst in. 
1. Selecteer het item **Exporteren** op de contextuele werkbalk boven de lijst.
1. Selecteer **Exporteren voor container (GZIP)**.
1. Selecteer de omgeving van **productiesleuf** of **faseringssleuf**.
1. Het pakket wordt gedownload van de browser.

![Het gepubliceerde pakket voor de container exporteren vanuit het menu Exporteren van de app](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Het pakket van de versieversie-app exporteren vanuit LUIS-portal

Het pakket van de versie-app is beschikbaar op de **lijstpagina Versies.**

1. Meld u aan bij het [LUIS-portaal](https://www.luis.ai).
1. Selecteer de app in de lijst. 
1. Selecteer **Beheren** in de navigatiebalk van de app.
1. Selecteer **Versies** op de linkernavigatiebalk.
1. Schakel het selectievakje links van de versienaam in de lijst in.
1. Selecteer het item **Exporteren** op de contextuele werkbalk boven de lijst.
1. Selecteer **Exporteren voor container (GZIP)**.
1. Het pakket wordt gedownload van de browser.

![Het getrainde pakket voor de container exporteren vanuit het menu Exporteren van de pagina Versies](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Het pakket van gepubliceerde apps exporteren uit API

Gebruik de volgende REST API-methode om een LUIS-app te verpakken die u al hebt [gepubliceerd.](luis-how-to-publish-app.md) Vervang uw eigen juiste waarden voor de tijdelijke aanduidingen in de API-aanroep met behulp van de tabel onder de HTTP-specificatie.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Tijdelijke aanduiding | Waarde |
|-------------|-------|
| **{APP_ID}** | De toepassings-ID van de gepubliceerde LUIS-app. |
| **{SLOT_NAME}** | De omgeving van de gepubliceerde LUIS-app. Gebruik een van de volgende waarden:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | De ontwerpsleutel van het LUIS-account voor de gepubliceerde LUIS-app.<br/>U uw ontwerpsleutel ophalen via de pagina **Gebruikersinstellingen** op de LUIS-portal. |
| **{AZURE_REGION}** | De juiste Azure-regio:<br/><br/>`westus`- West US<br/>`westeurope`- West-Europa<br/>`australiaeast`- Australië Oost |

Als u het gepubliceerde pakket wilt downloaden, raadpleegt u hier de [API-documentatie.][download-published-package] Als deze is gedownload, is het antwoord een LUIS-pakketbestand. Sla het bestand op in de opslaglocatie die is opgegeven voor de invoerbevestiging van de container. 

### <a name="export-versioned-apps-package-from-api"></a>Het pakket van versieversies exporteren uit API

Gebruik de volgende REST API-methode om een LUIS-toepassing te verpakken die u al hebt [getraind.](luis-how-to-train.md) Vervang uw eigen juiste waarden voor de tijdelijke aanduidingen in de API-aanroep met behulp van de tabel onder de HTTP-specificatie.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Tijdelijke aanduiding | Waarde |
|-------------|-------|
| **{APP_ID}** | De applicatie-id van de getrainde LUIS-app. |
| **{APP_VERSION}** | De toepassingsversie van de getrainde LUIS-app. |
| **{AUTHORING_KEY}** | De ontwerpsleutel van het LUIS-account voor de gepubliceerde LUIS-app.<br/>U uw ontwerpsleutel ophalen via de pagina **Gebruikersinstellingen** op de LUIS-portal. |
| **{AZURE_REGION}** | De juiste Azure-regio:<br/><br/>`westus`- West US<br/>`westeurope`- West-Europa<br/>`australiaeast`- Australië Oost |

Als u het versiepakket wilt downloaden, raadpleegt u hier de [API-documentatie.][download-versioned-package] Als deze is gedownload, is het antwoord een LUIS-pakketbestand. Sla het bestand op in de opslaglocatie die is opgegeven voor de invoerbevestiging van de container. 

## <a name="run-the-container-with-docker-run"></a>Voer de container uit met`docker run`

Gebruik de opdracht [Docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. Raadpleeg [het verzamelen van vereiste parameters](#gathering-required-parameters) `{ENDPOINT_URI}` voor `{API_KEY}` meer informatie over hoe u de en waarden krijgen.

[Voorbeelden](luis-container-configuration.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar.

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

* In dit voorbeeld wordt `C:` de map buiten het station gebruikt om eventuele machtigingsconflicten op Windows te voorkomen. Als u een specifieke map als invoermap moet gebruiken, moet u mogelijk de dockerservicetoestemming verlenen. 
* Wijzig de volgorde van de argumenten niet, tenzij u bekend bent met dockercontainers.
* Als u een ander besturingssysteem gebruikt, gebruikt u de juiste console/terminal, mapsyntaxis voor bevestigingen en regelvervolgteken voor uw systeem. In deze voorbeelden wordt uitgegaan van `^`een Windows-console met een regelvervolgteken . Omdat de container een Linux-besturingssysteem is, maakt de doelhouder gebruik van een syntaxis van de Linux-stijl.

Met deze opdracht gebeurt het volgende:

* Een container uit de LUIS-containerafbeelding worden uitgevoerd
* Laadt LUIS-app vanaf invoerbevestiging bij *C:\-invoer*, zich op containerhost
* Hiermee worden twee CPU-cores en 4 gigabyte (GB) geheugen toegewezen
* Stelt TCP-poort 5000 bloot en wijst een pseudo-TTY toe voor de container
* Slaat container- en LUIS-logboeken op om de uitvoerte vast te zetten bij *C:\output*, op containerhost
* Verwijdert automatisch de container nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer. 

Meer [voorbeelden](luis-container-configuration.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar. 

> [!IMPORTANT]
> De `Eula` `Billing`opties `ApiKey` en de opties moeten worden opgegeven om de container uit te voeren; Anders start de container niet.  Zie [Facturering voor](#billing)meer informatie.
> De ApiKey-waarde is de **sleutel** van de pagina **Azure Resources** `Cognitive Services` in de LUIS-portal en is ook beschikbaar op de pagina Azure-bronsleutels.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Eindpunt API's ondersteund door de container

Zowel V2- als [V3-versies](luis-migration-api-v3.md) van de API zijn beschikbaar met de container. 

## <a name="query-the-containers-prediction-endpoint"></a>Het voorspellingseindpunt van de container opvragen

De container biedt OP RUST gebaseerde queryvoorspellingseindpunt-API's. Eindpunten voor gepubliceerde (staging of productie)apps hebben een _andere_ route dan eindpunten voor versie-apps.

Gebruik de `http://localhost:5000`host, voor container API's.

# <a name="v3-prediction-endpoint"></a>[V3 voorspelling eindpunt](#tab/v3)

|Pakkettype|HTTP-woord|Route|Queryparameters|
|--|--|--|--|
|Gepubliceerd|GET, POST|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Versie|GET, POST|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

De queryparameters configureren hoe en wat wordt geretourneerd in het queryantwoord:

|Queryparameter|Type|Doel|
|--|--|--|
|`query`|tekenreeks|De uiting van de gebruiker.|
|`verbose`|booleaans|Een booleaanse waarde die aangeeft of alle metagegevens voor de voorspelde modellen moeten worden retourneren. De standaardinstelling is onwaar.|
|`log`|booleaans|Hiermee worden query's logboeken opgeslagen, die later kunnen worden gebruikt voor [actief leren.](luis-how-to-review-endpoint-utterances.md) De standaardinstelling is onwaar.|
|`show-all-intents`|booleaans|Een booleaanse waarde die aangeeft of alle intenties of alleen de intentie voor het hoogste scoren moeten worden retourneren. De standaardinstelling is onwaar.|

# <a name="v2-prediction-endpoint"></a>[V2 voorspellingeindpunt](#tab/v2)

|Pakkettype|HTTP-woord|Route|Queryparameters|
|--|--|--|--|
|Gepubliceerd|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Versie|GET, POST|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

De queryparameters configureren hoe en wat wordt geretourneerd in het queryantwoord:

|Queryparameter|Type|Doel|
|--|--|--|
|`q`|tekenreeks|De uiting van de gebruiker.|
|`timezoneOffset`|getal|Met de tijdzoneOffset u [de tijdzone wijzigen](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) die wordt gebruikt door de vooraf gebouwde entiteitsdatumV2.|
|`verbose`|booleaans|Geeft als resultaat alle intenties en hun scores wanneer ze op true zijn ingesteld. Standaard is false, wat alleen de bovenste intentie retourneert.|
|`staging`|booleaans|Retourneert query uit resultaten van de faseringsomgeving als deze is ingesteld op true. |
|`log`|booleaans|Hiermee worden query's logboeken opgeslagen, die later kunnen worden gebruikt voor [actief leren.](luis-how-to-review-endpoint-utterances.md) De standaardwaarde is true.|

***

### <a name="query-the-luis-app"></a>De LUIS-app opvragen

Een voorbeeld van de opdracht CURL voor het opvragen van de container voor een gepubliceerde app is:

# <a name="v3-prediction-endpoint"></a>[V3 voorspelling eindpunt](#tab/v3)

Als u een model in een sleuf wilt opvragen, gebruikt u de volgende API:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Als u query's wilt `production` maken in `staging`de **omgeving Fasen,** vervangt u in de route:

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Als u een versiemodel wilt opvragen, gebruikt u de volgende API:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[V2 voorspellingeindpunt](#tab/v2)

Als u een model in een sleuf wilt opvragen, gebruikt u de volgende API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Als u query's wilt maken in de **omgeving Fasering,** wijzigt u de parameterwaarde van de **faseringsqueryreeks** in true: 

`staging=true`

Als u een versiemodel wilt opvragen, gebruikt u de volgende API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
De versienaam heeft een maximum van 10 tekens en bevat alleen tekens die zijn toegestaan in een URL.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>De eindpuntlogboeken importeren voor actief leren

Als een uitvoerbevestiging is opgegeven voor de LUIS-container, worden app-querylogboekbestanden opgeslagen in de uitvoermap, waar `{INSTANCE_ID}` is de container-ID. Het app-querylogboek bevat de query-, respons- en tijdstempels voor elke voorspellingsquery die is ingediend bij de LUIS-container. 

De volgende locatie toont de geneste mapstructuur voor de logboekbestanden van de container.
```
/output/luis/{INSTANCE_ID}/
```
 
Selecteer in de LUIS-portal uw app en selecteer **Vervolgens Eindpuntlogboeken importeren** om deze logboeken te uploaden. 

![Logbestanden van containers importeren voor actief leren](./media/luis-container-how-to/upload-endpoint-log-files.png)

Nadat het logboek is geüpload, [controleert u de eindpuntuitingen](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) in de LUIS-portal.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>De container stoppen

Als u de container wilt afsluiten, drukt u in de opdrachtregelomgeving waar de container wordt uitgevoerd op **Ctrl+C**.

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een [uitvoerbevestiging](luis-container-configuration.md#mount-settings) en logboekregistratie is ingeschakeld, genereert de container logboekbestanden die handig zijn om problemen op te lossen die zich voordoen tijdens het starten of uitvoeren van de container.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De LUIS-container verzendt factureringsgegevens naar Azure met behulp van een _resource voor Cognitive Services_ op uw Azure-account. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie [Containers configureren](luis-container-configuration.md)voor meer informatie over deze opties.

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werkstroom geleerd voor het downloaden, installeren en uitvoeren van LUIS-containers (Language Understanding). Samenvatting:

* Language Understanding (LUIS) biedt één Linux-container voor Docker die eindpuntqueryvoorspellingen van uitingen biedt.
* Containerafbeeldingen worden gedownload uit het Microsoft Container Registry (MCR).
* Containerafbeeldingen worden uitgevoerd in Docker.
* U REST API gebruiken om de eindpunten van de container op te vragen door de hostURI van de container op te geven.
* U moet factureringsgegevens opgeven bij het momentmaken van een container.

> [!IMPORTANT]
> Cognitive Services-containers hebben geen licentie om uit te voeren zonder dat deze is verbonden met Azure voor meting. Klanten moeten de containers te allen tijde in staat stellen factureringsgegevens met de meetservice te communiceren. Cognitive Services-containers verzenden geen klantgegevens (bijvoorbeeld de afbeelding of tekst die wordt geanalyseerd) naar Microsoft.

## <a name="next-steps"></a>Volgende stappen

* Controleer [Containers configureren](luis-container-configuration.md) voor configuratie-instellingen.
* Zie [LUIS-containerbeperkingen](luis-container-limitations.md) voor bekende capaciteitsbeperkingen.
* Raadpleeg [Probleemoplossing](troubleshooting.md) om problemen met luis-functionaliteit op te lossen.
* Meer [Cognitive Services-containers gebruiken](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container
