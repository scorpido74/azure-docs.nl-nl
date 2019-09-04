---
title: Ontwerpen en runtime-sleutels-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS maakt gebruik van twee sleutels, de ontwerp sleutel voor het maken van uw model en de runtime sleutel voor het uitvoeren van query's op het Voorspellings eindpunt met gebruiker uitingen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 5a6c87da7ae62af54990e0a1a2c62065717a201a
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256956"
---
# <a name="authoring-and-runtime-keys"></a>Sleutels voor ontwerpen en runtime


>[!NOTE]
>[Migreer](luis-migration-authoring.md) alle apps, die geen gebruikmaken van de Azure-ontwerp bron, voordat u doorgaat.

LUIS maakt gebruik van twee typen Azure-resources, elk type heeft sleutels: 
 
* [Ontwerpen](#programmatic-key) voor het maken van intents, entiteiten en label uitingen, trainen en publiceren. Wanneer u klaar bent om uw LUIS-app te publiceren, hebt u een [voor spelling-eindpunt sleutel nodig voor de runtime](luis-how-to-azure-subscription.md) die is toegewezen aan de app.
* [De eindpunt sleutel voor voor spelling voor de runtime](#prediction-endpoint-runtime-key). Client-toepassingen, zoals een chat-bot, moeten toegang hebben tot het **query Voorspellings eindpunt** van de runtime via deze sleutel. 

|Sleutel|Doel|Cognitieve service`kind`|Cognitieve service`type`|
|--|--|--|--|
|[Sleutel ontwerpen](#programmatic-key)|Ontwerpen, trainen, publiceren, testen.|`LUIS.Authoring`|`Cognitive Services`|
|[Runtime sleutel voor Voorspellings eindpunt](#prediction-endpoint-runtime-key)| Query prediction endpoint runtime met een gebruiker utterance om de intenties en entiteiten te bepalen.|`LUIS`|`Cognitive Services`|

LUIS biedt ook een [Start sleutel](luis-how-to-azure-subscription.md#starter-key) met een eindpunt quotum van 1000 trans acties per maand. 

Hoewel u niet beide sleutels tegelijk hoeft te maken, is het veel eenvoudiger als u dat doet.

Het is belang rijk om LUIS-apps te schrijven in [regio's](luis-reference-regions.md#publishing-regions) waar u wilt publiceren en query's.

<a name="programmatic-key" ></a>

## <a name="authoring-key"></a>Sleutel ontwerpen

Er wordt automatisch een ontwerp sleutel gemaakt wanneer u een LUIS-account maakt en dit gratis is. Wanneer u begint met LUIS, hebt u één start sleutel in al uw LUIS-apps voor elk ontwerp [gebied](luis-reference-regions.md). Het doel van de ontwerp sleutel is om verificatie te bieden om uw LUIS-app te beheren of om Voorspellings eindpunt query's te testen. 

Als u ontwerp sleutels maakt in de Azure Portal, kunt u machtigingen voor de ontwerp bron beheren door personen toe te wijzen aan [de rol Inzender](#contributions-from-other-authors). U hebt machtigingen op het niveau van het Azure-abonnement nodig om mede werkers toe te voegen. 

Als u de bewerkings sleutel wilt vinden, meldt u zich aan bij [Luis](luis-reference-regions.md#luis-website) en klikt u op de account naam in de rechter navigatie balk om de **account instellingen**te openen.

![Sleutel ontwerpen](./media/luis-concept-keys/authoring-key.png)

Wanneer u **runtime query's**wilt maken, maakt u de Azure [Luis-resource](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> Voor het gemak gebruiken veel van de voor beelden de [Start sleutel](#starter-prediction-endpoint-runtime-key) , omdat het een aantal gratis endpoint-aanroepen voor voor spellingen bevat in het [quotum](luis-boundaries.md#key-limits).  

<a name="endpoint-key"></a>

## <a name="prediction-endpoint-runtime-key"></a>Runtime sleutel voor Voorspellings eindpunt 

Wanneer u **runtime-eindpunt query's**nodig hebt, maakt u een language Understanding-resource (Luis) en wijst u deze toe aan de Luis-app. 

[!INCLUDE [Azure runtime resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

Wanneer het proces voor het maken van de resource is voltooid, [wijst u de sleutel](luis-how-to-azure-subscription.md) toe aan de app. 

* De runtime-sleutel (query Voorspellings eindpunt) staat een quotum van eindpunt treffers toe op basis van het gebruiks schema dat u hebt opgegeven bij het maken van de runtime sleutel. Zie [prijzen van Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) voor informatie over prijzen.

* De runtime sleutel kan worden gebruikt voor al uw LUIS-apps of voor specifieke LUIS-apps. 
* Gebruik niet de runtime sleutel voor het ontwerpen van LUIS-apps. 

### <a name="starter-prediction-endpoint-runtime-key"></a>Runtime sleutel voor het uitvoeren van een eind punt

De eindpunt sleutel voor het **starter** prediction-eind punt biedt gratis en bevat 1000 Voorspellings eindpunt query's. Nadat deze query's zijn gebruikt, moet u uw eigen Voorspellings eindpunt resource maken voor Language Understanding.  

Dit is een speciale resource die u voor u hebt gemaakt. Deze wordt niet weer gegeven in de lijst met Azure-resources, omdat deze is bedoeld als tijdelijke begin sleutel. 

<a name="use-endpoint-key-in-query"></a>

### <a name="use-runtime-key-in-query"></a>Runtime sleutel gebruiken in query
Het LUIS-runtime-eind punt accepteert twee stijlen van de query, beide gebruiken de Voorspellings eindpunt runtime sleutel, maar op verschillende plaatsen.

Het eind punt dat wordt gebruikt voor toegang tot de runtime maakt gebruik van een subdomein dat uniek is voor de regio van `{region}` uw resource, aangegeven in de volgende tabel. 

|term|Voorbeeld-url en de sleutel-locatie|
|--|--|
|[GET](https://{region}.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?runtime-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>waarde voor de QueryString voor `runtime-key`<br><br>Wijzig de waarde van uw eindpunt query voor de `runtime-key` uit de authoring (starter)-sleutel, aan de nieuwe eindpuntsleutel als u wilt gebruiken, de snelheid van LUIS-eindpunt sleutel quotum. Als u de sleutel maken en toewijzen van de sleutel, maar wijzig niet de waarde van de query eindpunt voor `runtime-key`, u niet met behulp van uw quotum aanvragen voor de sleutel van eindpunt.|
|[POST](https://{region}.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> de waarde van de header voor `Ocp-Apim-Subscription-Key`<br>Als u de runtime sleutel maakt en de runtime sleutel toewijst, maar de eindpunt query waarde voor niet `Ocp-Apim-Subscription-Key`wijzigt voor, gebruikt u uw runtime sleutel niet.|

De app-ID die wordt gebruikt in de vorige URL's, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, is de openbare IoT-app die wordt gebruikt voor de [interactieve demonstratie](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="assignment-of-the-runtime-key"></a>Toewijzing van de runtime sleutel

U kunt de runtime sleutel [toewijzen](luis-how-to-azure-subscription.md) in de [Luis-Portal](https://www.luis.ai) of via de bijbehorende api's. 

## <a name="key-limits"></a>Limieten

U kunt Maxi maal tien ontwerp sleutels per regio per abonnement maken. 

Zie de [belangrijkste limieten](luis-boundaries.md#key-limits) en [Azure-regio's](luis-reference-regions.md). 

Publicatie-regio's wijken af van het ontwerpen van regio's. Zorg ervoor dat u een app maakt in de ontwerp regio die overeenkomt met het publicatie gebied dat u wilt dat uw client toepassing zich bevindt.

## <a name="key-limit-errors"></a>Belangrijkste limiet fouten
Als u het quotum voor trans acties per seconde (TPS) overschrijdt, ontvangt u een HTTP 429-fout. Als u het quotum voor trans acties per maand (TPS) overschrijdt, ontvangt u een HTTP 403-fout. 

## <a name="contributions-from-other-authors"></a>Bijdragen van andere auteurs



Het beheer van bijdragen van mede werkers is afhankelijk van de huidige status van de app.

**Voor [gemigreerde](luis-migration-authoring.md) apps voor**het maken van een resource: _inzenders_ worden beheerd in de Azure portal voor de ontwerp bron, met behulp van de **toegangs beheer pagina (IAM)** . Meer informatie [over het toevoegen van een gebruiker](luis-how-to-collaborate.md)met behulp van het e-mail adres van de samen werker en de rol _Inzender_ . 

**Voor apps die nog niet zijn gemigreerd**: _alle mede_ werkers worden beheerd in de Luis **>-** portal via de pagina samen werkers beheren.

### <a name="contributor-roles-vs-entity-roles"></a>Inzender rollen versus entiteits rollen

[Entiteits rollen](luis-concept-roles.md) zijn van toepassing op het gegevens model van de Luis-app. De rollen samen werker/Inzender zijn van toepassing op niveaus van toegang tot ontwerpen. 

## <a name="moving-or-changing-ownership"></a>Eigendom verplaatsen of wijzigen

Een app wordt gedefinieerd door de Azure-resources die worden bepaald door het abonnement van de eigenaar. 

U kunt uw LUIS-app verplaatsen. Gebruik de volgende documentatie bronnen in de Azure Portal of Azure CLI:

* [App verplaatsen tussen LUIS-ontwerp resources](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Resource verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../../azure-resource-manager/resource-group-move-resources.md)
* [Resource verplaatsen binnen hetzelfde abonnement of tussen abonnementen](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)
* [Eigendom](../../billing/billing-subscription-transfer.md) van uw abonnement overdragen 

## <a name="access-for-private-and-public-apps"></a>Toegang voor persoonlijke en open bare apps

Voor een **persoonlijke** app is runtime toegang beschikbaar voor eigen aren en mede werkers. Voor een **open bare** app is runtime toegang beschikbaar voor iedereen met hun eigen Azure [cognitieve service](../cognitive-services-apis-create-account.md) of [Luis](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) runtime-resource, en heeft de id van de open bare app. 

Er is momenteel geen catalogus met open bare apps.

### <a name="authoring-access"></a>Toegang ontwerpen
Toegang tot de app vanuit de [Luis](luis-reference-regions.md#luis-website) -portal of de [ontwerp-api's](https://go.microsoft.com/fwlink/?linkid=2092087) wordt bepaald door de Azure authoring-resource. 

De eigenaar en alle mede werkers hebben toegang tot het schrijven van de app. 

|Omvat toegang ontwerpen|Opmerkingen|
|--|--|
|Toevoegen of verwijderen van de eindpunt-sleutels||
|Exporteren van versie||
|Eindpunt-logboeken exporteren||
|Versie wordt geïmporteerd||
|App openbaar maken|Wanneer een app openbaar is, kan iedereen met een sleutel ontwerpen of eindpunt query uitvoeren op de app.|
|Model wijzigen|
|Publiceren|
|Bekijk eindpunt uitingen voor [actief leren](luis-how-to-review-endpoint-utterances.md)|
|Trainen|

### <a name="prediction-endpoint-runtime-access"></a>Runtime-toegang voor voor Spellings eindpunt

Toegang tot het uitvoeren van een query voor het prediction-eind punt wordt bepaald door een instelling op de pagina **toepassings gegevens** in de sectie **beheren** . 

![De app instellen op openbaar](./media/luis-concept-security/set-application-as-public.png)

|[Persoonlijke eindpunt](#runtime-security-for-private-apps)|[Openbaar eindpunt](#runtime-security-for-public-apps)|
|:--|:--|
|Beschikbaar voor eigenaar en mede werkers|Beschikbaar voor eigenaar, bijdrager en anderen die App-ID kent|

U kunt bepalen wie uw LUIS runtime-sleutel kan zien door deze aan te roepen in een server-naar-server-omgeving. Als u LUIS gebruikt van een bot, is de verbinding tussen de bot en LUIS al beveiligd. Als u het eindpunt LUIS rechtstreeks aanroept, moet u een API-serverzijde maken (zoals een Azure [functie](https://azure.microsoft.com/services/functions/)) met beperkte toegang (zoals [AAD](https://azure.microsoft.com/services/active-directory/)). Wanneer de API aan de server zijde wordt aangeroepen en geverifieerd en autorisatie is geverifieerd, geeft u de aanroep door aan LUIS. Hoewel deze strategie niet van toepassing is op man-in-the-middle-aanvallen, verbergt uw sleutel en eind punt-URL van uw gebruikers, kunt u de toegang bijhouden en kunt u de logboek registratie van eindpunt Reacties toevoegen (zoals [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Runtime-beveiliging voor persoonlijke apps

De runtime van een persoonlijke app is alleen beschikbaar voor het volgende:

|Sleutel en de gebruiker|Uitleg|
|--|--|
|De eigenaar van authoring sleutel| Maximaal 1000 eindpunt treffers|
|Ontwerp sleutels voor samen werker/Inzender| Maximaal 1000 eindpunt treffers|
|Een sleutel die is toegewezen aan LUIS door een auteur of samen werker/bijdrager|Op basis van gebruik van de sleutel-laag|

#### <a name="runtime-security-for-public-apps"></a>Runtime-beveiliging voor open bare apps

Nadat een app is geconfigureerd als openbare, _eventuele_ geldig LUIS ontwerpen of LUIS eindpuntsleutel kunt uw app, een query, zolang de sleutel heeft het quotum voor de hele eindpunt niet gebruikt.

Een gebruiker die geen eigenaar of bijdrager is, heeft alleen toegang tot de runtime van een open bare app als deze de App-ID heeft opgegeven. LUIS beschikt niet over een openbare _markt_ of andere manier om te zoeken naar een openbare app.  

Een openbare app is gepubliceerd in alle regio's, zodat een gebruiker met een sleutel op basis van een regio LUIS resource toegang heeft tot de app in welke regio gekoppeld aan de resource-sleutel is.

## <a name="transfer-of-ownership"></a>Overdracht van eigendom

**Voor [gemigreerde](luis-migration-authoring.md) apps voor de ontwerp bron**: 

**Voor apps die nog niet zijn gemigreerd**: Exporteer uw app als een JSON-bestand. Een andere LUIS-gebruiker kan de app importeren, waardoor de eigenaar van de app wordt. De nieuwe app heeft een andere app-ID.  

## <a name="securing-the-endpoint"></a>Beveiligen van het eindpunt 

U kunt bepalen wie uw LUIS prediction runtime-eindpunt sleutel kan zien door deze aan te roepen in een server-naar-server-omgeving. Als u LUIS gebruikt van een bot, is de verbinding tussen de bot en LUIS al beveiligd. Als u het eindpunt LUIS rechtstreeks aanroept, moet u een API-serverzijde maken (zoals een Azure [functie](https://azure.microsoft.com/services/functions/)) met beperkte toegang (zoals [AAD](https://azure.microsoft.com/services/active-directory/)). Wanneer de server-side-API wordt aangeroepen en de verificatie en autorisatie worden geverifieerd, geeft u de aanroep naar LUIS. Terwijl deze strategie niet te voorkomen man-in-the-middle-aanvallen dat, het obfuscates het eindpunt van uw gebruikers, kunt u voor het bijhouden van toegang, en kunt u logboekregistratie van eindpunt antwoord toevoegen (zoals [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>Volgende stappen

* Inzicht in [versiebeheer](luis-concept-version.md) concepten. 
* Meer informatie [over het maken van sleutels](luis-how-to-azure-subscription.md).
