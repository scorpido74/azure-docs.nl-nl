---
title: Ontwerpen en runtime sleutels gebruiken met LUIS
titleSuffix: Azure Cognitive Services
description: LUIS maakt gebruik van twee sleutels, de ontwerp sleutel voor het maken van uw model en de runtime sleutel voor het uitvoeren van query's op het Voorspellings eindpunt met gebruiker uitingen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9d213c8fa03ad2ca5e5fd7e620e52aa502749be2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969345"
---
# <a name="authoring-and-runtime-keys"></a>Ontwerp- en runtimesleutels

Language Understanding (LUIS) heeft twee services en API-sets: 

* Ontwerpen (voorheen bekend als _programmatisch_)
* Voorspellings runtime

Er zijn verschillende soorten sleutels, afhankelijk van de service waarmee u wilt werken en hoe u ermee wilt werken.

## <a name="non-azure-resources-for-luis"></a>Niet-Azure-resources voor LUIS

### <a name="starter-key"></a>Start sleutel

Wanneer u LUIS voor het eerst gaat gebruiken, wordt er een **Start sleutel** voor u gemaakt. Deze resource biedt:

* gratis service aanvragen ontwerpen via de LUIS-portal of Api's (inclusief Sdk's)
* gratis 1.000 Voorspellings eindpunt aanvragen per maand via een browser, API of Sdk's

## <a name="azure-resources-for-luis"></a>Azure-resources voor LUIS

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS maakt drie typen Azure-resources mogelijk: 
 
|Sleutel|Doel|`kind` van de cognitieve service|`type` van de cognitieve service|
|--|--|--|--|
|[Sleutel ontwerpen](#programmatic-key)|Toegang tot en het beheren van gegevens van toepassingen met ontwerpen, training, publicatie en testen. Maak een LUIS-ontwerp sleutel als u van plan bent om LUIS-apps programmatisch te ontwerpen.<br><br>Het doel van de `LUIS.Authoring` sleutel is om het volgende toe te staan:<br>* Language Understanding-apps en-modellen, inclusief training en publicatie, programmatisch beheren<br> * beheer machtigingen voor de ontwerp bron door personen toe te wijzen aan [de rol Inzender](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|[Voorspellings sleutel](#prediction-endpoint-runtime-key)| Query's voor Voorspellings eindpunt aanvragen. Maak een LUIS-Voorspellings sleutel voordat u de voor spellingen van de client-app voorspelde aanvragen overschrijdt met de 1.000-aanvragen van de eerste resource. |`LUIS`|`Cognitive Services`|
|[Bron sleutel voor de cognitieve service met meerdere services](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Query Voorspellings eindpunt aanvragen gedeeld met LUIS en andere ondersteunde Cognitive Services.|`CognitiveServices`|`Cognitive Services`|

Wanneer het proces voor het maken van de resource is voltooid, [wijst u de sleutel](luis-how-to-azure-subscription.md) toe aan de app in de Luis-Portal.

Het is belang rijk om LUIS-apps te schrijven in [regio's](luis-reference-regions.md#publishing-regions) waar u wilt publiceren en query's.

> [!CAUTION]
> Voor het gemak gebruiken veel van de voor beelden de [Start sleutel](#starter-key) , omdat het een aantal gratis endpoint-aanroepen voor voor spellingen bevat in het [quotum](luis-boundaries.md#key-limits).  


### <a name="query-prediction-resources"></a>Query Voorspellings resources

* De runtime sleutel kan worden gebruikt voor al uw LUIS-apps of voor specifieke LUIS-apps. 
* Gebruik niet de runtime sleutel voor het ontwerpen van LUIS-apps. 

Het LUIS-runtime-eind punt accepteert twee stijlen van de query, beide gebruiken de Voorspellings eindpunt runtime sleutel, maar op verschillende plaatsen.

Het eind punt dat wordt gebruikt voor toegang tot de runtime maakt gebruik van een subdomein dat uniek is voor de regio van uw resource, aangeduid met `{region}` in de volgende tabel. 

## <a name="assignment-of-the-key"></a>Toewijzing van de sleutel

U kunt de runtime sleutel [toewijzen](luis-how-to-azure-subscription.md) in de [Luis-Portal](https://www.luis.ai) of via de bijbehorende api's. 

## <a name="key-limits"></a>Limieten

U kunt Maxi maal tien ontwerp sleutels per regio per abonnement maken. 

Zie de [belangrijkste limieten](luis-boundaries.md#key-limits) en [Azure-regio's](luis-reference-regions.md). 

Publicatie-regio's wijken af van het ontwerpen van regio's. Zorg ervoor dat u een app maakt in de ontwerp regio die overeenkomt met het publicatie gebied dat u wilt dat uw client toepassing zich bevindt.

## <a name="key-limit-errors"></a>Belangrijkste limiet fouten
Als u het quotum voor trans acties per seconde (TPS) overschrijdt, ontvangt u een HTTP 429-fout. Als u het quotum voor trans acties per maand (TPS) overschrijdt, ontvangt u een HTTP 403-fout. 

## <a name="contributions-from-other-authors"></a>Bijdragen van andere auteurs

**Voor [gemigreerde](luis-migration-authoring.md) apps voor**het maken van een resource: _inzenders_ worden beheerd in de Azure portal voor de ontwerp bron, met behulp van de **toegangs beheer pagina (IAM)** . Meer informatie [over het toevoegen van een gebruiker](luis-how-to-collaborate.md)met behulp van het e-mail adres van de samen werker en de rol _Inzender_ . 

**Voor apps die nog niet zijn gemigreerd**: _alle mede_ werkers worden beheerd in de Luis **>-** portal via de pagina samen werkers beheren.

## <a name="move-transfer-or-change-ownership"></a>Verplaatsen, overdragen of eigendom wijzigen

Een app wordt gedefinieerd door de Azure-resources die worden bepaald door het abonnement van de eigenaar. 

U kunt uw LUIS-app verplaatsen. Gebruik de volgende documentatie bronnen in de Azure Portal of Azure CLI:

* [App verplaatsen tussen LUIS-ontwerp resources](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Resource verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Resource verplaatsen binnen hetzelfde abonnement of tussen abonnementen](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Het [eigendom](../../cost-management-billing/manage/billing-subscription-transfer.md) van het abonnement overdragen: 

**Voor gebruikers die [gemigreerde](luis-migration-authoring.md) apps hebben**gemigreerd: als eigenaar van de resource kunt u een `contributor`toevoegen.

**Voor gebruikers die nog niet zijn gemigreerd**: Exporteer uw app als een JSON-bestand. Een andere LUIS-gebruiker kan de app importeren, waardoor de eigenaar van de app wordt. De nieuwe app heeft een andere app-ID.  

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

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Runtime-toegang voor voor Spellings eindpunt

Toegang tot het uitvoeren van een query voor het prediction-eind punt wordt bepaald door een instelling op de pagina **toepassings gegevens** in de sectie **beheren** . 

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

LUIS is niet het concept van het overdragen van het eigendom van een resource. 

## <a name="securing-the-endpoint"></a>Beveiligen van het eindpunt 

U kunt bepalen wie uw LUIS prediction runtime-eindpunt sleutel kan zien door deze aan te roepen in een server-naar-server-omgeving. Als u LUIS gebruikt van een bot, is de verbinding tussen de bot en LUIS al beveiligd. Als u het eindpunt LUIS rechtstreeks aanroept, moet u een API-serverzijde maken (zoals een Azure [functie](https://azure.microsoft.com/services/functions/)) met beperkte toegang (zoals [AAD](https://azure.microsoft.com/services/active-directory/)). Wanneer de server-side-API wordt aangeroepen en de verificatie en autorisatie worden geverifieerd, geeft u de aanroep naar LUIS. Terwijl deze strategie niet te voorkomen man-in-the-middle-aanvallen dat, het obfuscates het eindpunt van uw gebruikers, kunt u voor het bijhouden van toegang, en kunt u logboekregistratie van eindpunt antwoord toevoegen (zoals [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>Volgende stappen

* Inzicht in [versiebeheer](luis-concept-version.md) concepten. 
* Meer informatie [over het maken van sleutels](luis-how-to-azure-subscription.md).
