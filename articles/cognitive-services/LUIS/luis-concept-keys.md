---
title: Ontwerpen en runtime-toetsen gebruiken met LUIS
titleSuffix: Azure Cognitive Services
description: LUIS gebruikt twee sleutels, de ontwerpsleutel om uw model te maken en de runtime-toets voor het opvragen van het voorspellingseindpunt met gebruikersuitingen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220961"
---
# <a name="authoring-and-runtime-keys"></a>Ontwerp- en runtimesleutels

Language Understanding (LUIS) heeft twee services en API-sets: 

* Authoring (voorheen bekend als _programmatisch)_
* Voorspelling runtime

Er zijn verschillende sleuteltypen, afhankelijk van met welke service u wilt werken en hoe u ermee wilt werken.

## <a name="non-azure-resources-for-luis"></a>Niet-Azure-resources voor LUIS

### <a name="starter-key"></a>Startsleutel

Wanneer u LUIS voor het eerst gaat gebruiken, wordt er een **startsleutel** voor u gemaakt. Deze bron biedt:

* gratis aanvragen voor het aanvragen van artikelen via de LUIS-portal of API's (inclusief SDK's)
* gratis 1.000 voorspellingseindpuntaanvragen per maand via een browser, API of SDKs

## <a name="azure-resources-for-luis"></a>Azure-bronnen voor LUIS

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS maakt drie typen Azure-bronnen mogelijk: 
 
|Sleutel|Doel|Cognitieve service`kind`|Cognitieve service`type`|
|--|--|--|--|
|[Ontwerpsleutel](#programmatic-key)|Toegang tot en beheer van gegevens van toepassingen met authoring, training, publishing en testen. Maak een LUIS-ontwerpsleutel als u luis-apps programmatisch wilt schrijven.<br><br>Het doel `LUIS.Authoring` van de sleutel is om u in staat te stellen:<br>* programmatisch beheren van apps en modellen voor taalbegrip, inclusief training en publicatie<br> * beheer machtigingen voor de auteur bron door het toewijzen van mensen aan [de bijdrager rol](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|[Voorspellingstoets](#prediction-endpoint-runtime-key)| Eindpuntaanvragen voor queryvoorspelling. Maak een LUIS-voorspellingssleutel voordat uw client-app voorspellingen opvraagt die verder gaan dan de 1.000 aanvragen die door de starterbron worden verstrekt. |`LUIS`|`Cognitive Services`|
|[Multi-service multiservicebronsleutel voor cognitive service](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Eindpuntaanvragen voor queryvoorspelling die worden gedeeld met LUIS en andere ondersteunde cognitieve services.|`CognitiveServices`|`Cognitive Services`|

Wanneer het proces voor het maken van resources is voltooid, [wijst u de sleutel toe](luis-how-to-azure-subscription.md) aan de app in de LUIS-portal.

Het is belangrijk om LUIS-apps te maken in [regio's](luis-reference-regions.md#publishing-regions) waar u wilt publiceren en query's.

> [!CAUTION]
> Voor het gemak gebruiken veel van de monsters de [starter-toets](#starter-key) omdat het een paar gratis voorspellingseindpuntoproepen in het [quotum](luis-boundaries.md#key-limits)biedt.  


### <a name="query-prediction-resources"></a>Bronnen voor queryvoorspelling

* De runtime-toets kan worden gebruikt voor al uw LUIS-apps of voor specifieke LUIS-apps. 
* Gebruik de runtime-sleutel niet voor het maken van LUIS-apps. 

Het luis-runtime-eindpunt accepteert twee querystijlen, beide gebruiken de toets voor runtime van het voorspellingseindpunt, maar op verschillende plaatsen.

Het eindpunt dat wordt gebruikt om toegang te krijgen tot de runtime, `{region}` maakt gebruik van een subdomein dat uniek is voor het gebied van uw resource, aangeduid met in de volgende tabel. 

## <a name="assignment-of-the-key"></a>Toewijzing van de sleutel

U de runtime-sleutel [toewijzen](luis-how-to-azure-subscription.md) in de [LUIS-portal](https://www.luis.ai) of via de bijbehorende API's. 

## <a name="key-limits"></a>Sleutellimieten

Per abonnement u maximaal 10 ontwerpsleutels per regio maken. 

Zie [Sleutellimieten](luis-boundaries.md#key-limits) en [Azure-regio's](luis-reference-regions.md). 

Publicatieregio's verschillen van authoring regio's. Zorg ervoor dat u een app maakt in het ontwerpgebied dat overeenkomt met het publicatiegebied dat u wilt dat uw clienttoepassing zich bevindt.

## <a name="key-limit-errors"></a>Fouten in de sleutellimiet
Als u het TPS-quotum (Transactions-per-second) overschrijdt, ontvangt u een HTTP 429-fout. Als u het TPS-quotum (Transaction-per-month) overschrijdt, ontvangt u een HTTP 403-fout. 

## <a name="contributions-from-other-authors"></a>Bijdragen van andere auteurs

**Voor [het maken van door resources gemigreerde](luis-migration-authoring.md) apps:** _bijdragers_ worden beheerd in de Azure-portal voor de ontwerpbron, met behulp van de pagina **IAM (Access control).** Meer informatie over [het toevoegen van een gebruiker,](luis-how-to-collaborate.md)met behulp van het e-mailadres van de medewerker en de rol van de _bijdrager._ 

**Voor apps die nog niet zijn gemigreerd:** alle _bijdragers_ worden beheerd in de LUIS-portal vanaf de pagina **> bijdragers beheren.**

## <a name="move-transfer-or-change-ownership"></a>Van eigenaar verplaatsen, overdragen of van eigenaar veranderen

Een app wordt gedefinieerd door de Azure-resources, die wordt bepaald door het abonnement van de eigenaar. 

U uw LUIS-app verplaatsen. Gebruik de volgende documentatiebronnen in de Azure-portal of Azure CLI:

* [App verplaatsen tussen LUIS-bronnen voor het maken van auteurs](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Bron verplaatsen naar nieuwe resourcegroep of -abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Bron verplaatsen binnen hetzelfde abonnement of tussen abonnementen](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Ga als [het gaat](../../cost-management-billing/manage/billing-subscription-transfer.md) om het eigendom van uw abonnement over te dragen: 

**Voor gebruikers die zijn gemigreerd - [het maken van brongemigreerde](luis-migration-authoring.md) apps:** als eigenaar van de bron u een `contributor`.

**Voor gebruikers die nog niet zijn gemigreerd:** Exporteer uw app als JSON-bestand. Een andere LUIS-gebruiker kan de app importeren en zo de eigenaar van de app worden. De nieuwe app heeft een andere app-id.  

## <a name="access-for-private-and-public-apps"></a>Toegang voor privé- en openbare apps

Voor een **privé-app** is runtime-toegang beschikbaar voor eigenaren en bijdragers. Voor een **openbare** app is runtime-toegang beschikbaar voor iedereen die zijn eigen Azure [Cognitive Service-](../cognitive-services-apis-create-account.md) of [LUIS-runtime-bron](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) heeft en de id van de openbare app heeft. 

Momenteel is er geen catalogus met openbare apps.

### <a name="authoring-access"></a>Toegang tot het ontwerpen
Toegang tot de app vanuit de [LUIS-portal](luis-reference-regions.md#luis-website) of de [ontwerp-API's](https://go.microsoft.com/fwlink/?linkid=2092087) wordt beheerd door de Azure-ontwerpbron. 

De eigenaar en alle bijdragers hebben toegang tot de auteur van de app. 

|Toegang tot het ontwerpen omvat|Opmerkingen|
|--|--|
|Eindpunttoetsen toevoegen of verwijderen||
|Versie exporteren||
|Eindpuntlogboeken exporteren||
|Versie importeren||
|App openbaar maken|Wanneer een app openbaar is, kan iedereen met een ontwerp- of eindpunttoets de app opvragen.|
|Model wijzigen|
|Publiceren|
|Eindpuntuitingen controleren voor [actief leren](luis-how-to-review-endpoint-utterances.md)|
|Trainen|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Runtime-toegang voor eindpunten voorspellen

Toegang tot query het voorspellingseindpunt wordt beheerd door een instelling op de pagina **Toepassingsgegevens** in de sectie **Beheren.** 

|[Privéeindpunt](#runtime-security-for-private-apps)|[Openbaar eindpunt](#runtime-security-for-public-apps)|
|:--|:--|
|Beschikbaar voor eigenaar en bijdragers|Beschikbaar voor eigenaar, bijdragers en iedereen die app-id kent|

U bepalen wie uw LUIS-runtime-sleutel ziet door deze in een server-naar-server-omgeving te noemen. Als u LUIS van een bot gebruikt, is de verbinding tussen de bot en LUIS al beveiligd. Als u het LUIS-eindpunt rechtstreeks aanroept, moet u een API aan de serverzijde (zoals een [Azure-functie)](https://azure.microsoft.com/services/functions/)maken met gecontroleerde toegang (zoals [AAD).](https://azure.microsoft.com/services/active-directory/) Wanneer de API aan de serverzijde wordt aangeroepen en geverifieerd en de autorisatie is geverifieerd, geeft u de aanroep door aan LUIS. Hoewel deze strategie geen man-in-the-middle-aanvallen voorkomt, versluiert het uw sleutel- en eindpunt-URL van uw gebruikers, u toegang bijhouden en u endpoint response logging toevoegen (zoals [Application Insights).](https://azure.microsoft.com/services/application-insights/)

#### <a name="runtime-security-for-private-apps"></a>Runtime-beveiliging voor privé-apps

De runtime van een privé-app is alleen beschikbaar voor het volgende:

|Sleutel en gebruiker|Uitleg|
|--|--|
|De ontwerpsleutel van de eigenaar| Tot 1000 endpoint hits|
|Toetsen voor het maken van bijdragende bijdragen aan de redactie| Tot 1000 endpoint hits|
|Elke sleutel die door een auteur of medewerker/bijdrager aan LUIS is toegewezen|Op basis van de toetsgebruikslaag|

#### <a name="runtime-security-for-public-apps"></a>Runtime-beveiliging voor openbare apps

Zodra een app als openbaar is geconfigureerd, kan _elke_ geldige LUIS-ontwerpsleutel of LUIS-eindpuntsleutel uw app opvragen, zolang de sleutel het volledige eindpuntquotum niet heeft gebruikt.

Een gebruiker die geen eigenaar of bijdrager is, heeft alleen toegang tot de runtime van een openbare app als hij de app-id krijgt. LUIS heeft geen openbare _markt_ of een andere manier om te zoeken naar een openbare app.  

Een openbare app wordt in alle regio's gepubliceerd, zodat een gebruiker met een op een regio gebaseerde LUIS-bronsleutel toegang heeft tot de app in welke regio dan ook is gekoppeld aan de resourcesleutel.

## <a name="transfer-of-ownership"></a>Eigendomsoverdracht

LUIS heeft niet het concept van de overdracht van eigendom van een bron. 

## <a name="securing-the-endpoint"></a>Het eindpunt beveiligen 

U bepalen wie uw LUIS-voorspelling runtime-eindpuntsleutel kan zien door deze in een server-naar-server-omgeving te noemen. Als u LUIS van een bot gebruikt, is de verbinding tussen de bot en LUIS al beveiligd. Als u het LUIS-eindpunt rechtstreeks aanroept, moet u een API aan de serverzijde (zoals een [Azure-functie)](https://azure.microsoft.com/services/functions/)maken met gecontroleerde toegang (zoals [AAD).](https://azure.microsoft.com/services/active-directory/) Wanneer de api aan de serverzijde wordt aangeroepen en de verificatie en autorisatie worden geverifieerd, geeft u de aanroep door aan LUIS. Hoewel deze strategie geen man-in-the-middle-aanvallen voorkomt, versluiert het uw eindpunt van uw gebruikers, u toegang bijhouden en u endpoint response logging toevoegen (zoals [Application Insights).](https://azure.microsoft.com/services/application-insights/)  

## <a name="next-steps"></a>Volgende stappen

* Concepten [voor versiebeheer](luis-concept-version.md) begrijpen. 
* Meer informatie over [het maken van toetsen](luis-how-to-azure-subscription.md).
