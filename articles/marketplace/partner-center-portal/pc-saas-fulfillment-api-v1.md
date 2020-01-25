---
title: SaaS-fulfillment-Api's v1 | Azure Marketplace
description: Hierin wordt uitgelegd hoe u een SaaS-aanbieding op de Azure Marketplace maakt en beheert met behulp van de bijbehorende fulfillment v1-Api's.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ROBOTS: NOINDEX
ms.openlocfilehash: f56e9b4f6c3db6fb47452c7478f5a27445955e87
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715382"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>SaaS-fulfillment-Api's versie 1 (afgeschaft)

In dit artikel wordt uitgelegd hoe u een SaaS-aanbieding met Api's maakt. De Api's, bestaande uit REST methoden en eind punten, zijn nodig om abonnementen op uw SaaS-aanbieding toe te staan als u via Azure hebt gekozen voor verkopen.  

> [!WARNING]
> Deze eerste versie van de SaaS-fulfillment-API is afgeschaft. gebruik in plaats daarvan [SaaS fulfillment API v2](./pc-saas-fulfillment-api-v2.md).  Deze eerste versie van de API wordt momenteel alleen onderhouden voor bestaande uitgevers. 

De volgende Api's zijn bedoeld om u te helpen uw SaaS-service te integreren met Azure:

-   Oplossen
-   Abonneer u nu
-   Converteren
-   Afmelden


## <a name="api-methods-and-endpoints"></a>API-methoden en-eind punten

De volgende secties beschrijven de API-methoden en-eind punten die beschikbaar zijn voor het inschakelen van abonnementen voor een SaaS-aanbieding.


### <a name="marketplace-api-endpoint-and-api-version"></a>Marketplace-API-eind punt en API-versie

Het eind punt voor de Azure Marketplace-API is `https://marketplaceapi.microsoft.com`.

De huidige API-versie is `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Abonnement oplossen

Met de actie na het oplossen van een eind punt kunnen gebruikers een Marketplace-token omzetten in een permanente Resource-ID.  De resource-ID is de unieke id voor het SAAS-abonnement. 

Wanneer een gebruiker wordt omgeleid naar de website van een ISV, bevat de URL een token in de query parameters. De ISV wordt verwacht dit token te gebruiken en een aanvraag indienen om deze te kunnen oplossen. Het antwoord bevat de unieke SAAS-abonnements-ID, de naam, de aanbiedings-ID en het plan voor de resource. Dit token is alleen geldig voor een uur.

*Aanvraag*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Parameter naam** |     **Beschrijving**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-version        |  De versie van de bewerking die moet worden gebruikt voor deze aanvraag.   |
|  |  |


*Headers*

| **Header sleutel**     | **Vereist** | **Beschrijving**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID. Als deze waarde niet wordt gegeven, wordt er een gegenereerd en weer gegeven in de antwoord headers.  |
| x-MS-correlationid | Nee           | Een unieke teken reeks waarde voor de bewerking op de client. Dit veld verbindt alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde. Als deze waarde niet wordt gegeven, wordt er een gegenereerd en weer gegeven in de antwoord headers. |
| Inhouds type       | Ja          | `application/json`                                        |
| authorization      | Ja          | Het JWT-Bearer-token (Web token).                    |
| x-MS-Marketplace-token| Ja| De token query-para meter in de URL wanneer de gebruiker wordt omgeleid naar de website van SaaS ISV van Azure. **Opmerking:** Dit token is slechts één uur geldig. Daarnaast moet de URL de token waarde decoderen van de browser voordat u deze gebruikt.|
|  |  |  |
  

*Antwoord tekst*

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **Parameternaam** | **Gegevenstype** | **Beschrijving**                       |
|--------------------|---------------|---------------------------------------|
| id                 | Tekenreeks        | ID van het SaaS-abonnement.          |
| subscriptionName| Tekenreeks| De naam van het SaaS-abonnement dat is ingesteld door de gebruiker in azure terwijl u zich abonneert op de SaaS-service.|
| OfferId            | Tekenreeks        | De aanbiedings-ID waarop de gebruiker zich heeft geabonneerd. |
| planId             | Tekenreeks        | De plan-ID waarop de gebruiker zich heeft geabonneerd.  |
|  |  |  |


*Antwoord codes*

| **HTTP-status code** | **Fout code**     | **Beschrijving**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Het token is opgelost.                                                            |
| 400                  | `BadRequest`         | De vereiste headers ontbreken of er is een ongeldige API-versie opgegeven. Kan het token niet omzetten omdat het token ongeldig is of is verlopen (het token is slechts één uur geldig nadat het is gegenereerd). |
| 403                  | `Forbidden`          | De aanroeper is niet gemachtigd om deze bewerking uit te voeren.                                 |
| 429                  | `RequestThrottleId`  | De service is bezig met het verwerken van aanvragen. Probeer het later opnieuw.                                |
| 503                  | `ServiceUnavailable` | De service is tijdelijk niet beschikbaar. Probeer het later opnieuw.                                        |
|  |  |  |


*Antwoord headers*

| **Header sleutel**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID die is ontvangen van de client.                                                                   |
| x-MS-correlationid | Ja          | Correlatie-ID als door de client is door gegeven, anders is deze waarde de server correlatie-ID.                   |
| x-ms-activityid    | Ja          | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de service. Deze ID wordt gebruikt voor reconciliaties. |
| Opnieuw proberen na        | Nee           | Deze waarde wordt alleen ingesteld voor een 429-antwoord.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Abonneer u nu

Met het abonnements eindpunt kunnen gebruikers een abonnement op een SaaS-service voor een bepaald plan starten en facturering in het commerce-systeem inschakelen.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Parameter naam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Unieke ID van het SaaS-abonnement dat is verkregen na het oplossen van het token via de API voor omzetten.                              |
| api-version         | De versie van de bewerking die moet worden gebruikt voor deze aanvraag. |
|  |  |

*Headers*

|  **Header sleutel**        | **Vereist** |  **Beschrijving**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Nee         | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID. Als u deze niet opgeeft, wordt er een gegenereerd en geleverd in de antwoord headers. |
| x-MS-correlationid     |   Nee         | Een unieke teken reeks waarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde. Als u deze niet opgeeft, wordt er een gegenereerd en geleverd in de antwoord headers. |
| If-match/If-None-Match |   Nee         |   ETag-waarde voor sterke validatie.                                                          |
| content-type           |   Ja        |    `application/json`                                                                   |
|  authorization         |   Ja        |    Het JWT-Bearer-token (Web token).                                               |
| x-ms-marketplace-session-mode| Nee | Vlag waarmee de modus voor droge uitvoering wordt ingeschakeld wanneer u zich abonneert op een SaaS-aanbieding. Indien ingesteld, worden er geen kosten in rekening gebracht voor het abonnement. Dit is handig voor ISV-Test scenario's. Stel dit in op **dryrun**|
|  |  |  |

*Hoofdtekst*

``` json
{
    "lanId": "",
}
```

| **Element naam** | **Gegevenstype** | **Beschrijving**                      |
|------------------|---------------|--------------------------------------|
| planId           | Lang Tekenreeksexpressie        | De plan-ID van de SaaS-service gebruiker is geabonneerd op.  |
|  |  |  |

*Antwoord codes*

| **HTTP-status code** | **Fout code**     | **Beschrijving**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Activering van SaaS-abonnement ontvangen voor een bepaald abonnement.                   |
| 400                  | `BadRequest`         | De vereiste headers ontbreken of de hoofd tekst van de JSON is onjuist gevormd. |
| 403                  | `Forbidden`          | De aanroeper is niet gemachtigd om deze bewerking uit te voeren.                   |
| 404                  | `NotFound`           | Het abonnement is niet gevonden met de opgegeven ID                                  |
| 409                  | `Conflict`           | Er wordt een andere bewerking uitgevoerd voor het abonnement.                     |
| 429                  | `RequestThrottleId`  | De service is bezig met het verwerken van aanvragen. Probeer het later opnieuw.                  |
| 503                  | `ServiceUnavailable` | De service is tijdelijk niet beschikbaar. Probeer het later opnieuw.                          |
|  |  |  |

Voor een 202-antwoord moet u de status van de aanvraag bewerking opvolgen op de koptekst van de bewerkings locatie. De verificatie is hetzelfde als andere Marketplace-Api's.

*Antwoord headers*

| **Header sleutel**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID die is ontvangen van de client.                                                                   |
| x-MS-correlationid | Ja          | Correlatie-ID als door de client is door gegeven, anders is deze waarde de server correlatie-ID.                   |
| x-ms-activityid    | Ja          | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de service. Deze waarde wordt gebruikt voor reconciliaties. |
| Opnieuw proberen na        | Ja          | Het interval waarmee de client de status kan controleren.                                                       |
| Operation-Location | Ja          | Koppeling naar een resource om de bewerkings status op te halen.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Eind punt van abonnement wijzigen

Met het wijzigings eindpunt kan de gebruiker het abonnement dat op dat moment is geabonneerd converteren naar een nieuw abonnement.

**VERZENDEN**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Parameter naam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID van het SaaS-abonnement.                              |
| api-version         | De versie van de bewerking die moet worden gebruikt voor deze aanvraag. |
|  |  |

*Headers*

| **Header sleutel**          | **Vereist** | **Beschrijving**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Nee           | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client. U wordt aangeraden een GUID in te stellen. Als u deze niet opgeeft, wordt er een gegenereerd en geleverd in de antwoord headers.   |
| x-MS-correlationid      | Nee           | Een unieke teken reeks waarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde. Als u deze niet opgeeft, wordt er een gegenereerd en geleverd in de antwoord headers. |
| If-match/If-None-Match | Nee           | ETag-waarde voor sterke validatie.                              |
| content-type            | Ja          | `application/json`                                        |
| authorization           | Ja          | Het JWT-Bearer-token (Web token).                    |
|  |  |  |

*Hoofdtekst*

```json
{
    "planId": ""
}
```

|  **Element naam** |  **Gegevenstype**  | **Beschrijving**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  Lang Tekenreeksexpressie         | De plan-ID van de SaaS-service gebruiker is geabonneerd op.          |
|  |  |  |

*Antwoord codes*

| **HTTP-status code** | **Fout code**     | **Beschrijving**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Activering van SaaS-abonnement ontvangen voor een bepaald abonnement.                   |
| 400                  | `BadRequest`         | De vereiste headers ontbreken of de hoofd tekst van de JSON is onjuist gevormd. |
| 403                  | `Forbidden`          | De aanroeper is niet gemachtigd om deze bewerking uit te voeren.                   |
| 404                  | `NotFound`           | Het abonnement is niet gevonden met de opgegeven ID                                  |
| 409                  | `Conflict`           | Er wordt een andere bewerking uitgevoerd voor het abonnement.                     |
| 429                  | `RequestThrottleId`  | De service is bezig met het verwerken van aanvragen. Probeer het later opnieuw.                  |
| 503                  | `ServiceUnavailable` | De service is tijdelijk niet beschikbaar. Probeer het later opnieuw.                          |
|  |  |  |

*Antwoord headers*

| **Header sleutel**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID die is ontvangen van de client.                                                                   |
| x-MS-correlationid | Ja          | Correlatie-ID als door de client is door gegeven, anders is deze waarde de server correlatie-ID.                   |
| x-ms-activityid    | Ja          | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de service. Deze waarde wordt gebruikt voor reconciliaties. |
| Opnieuw proberen na        | Ja          | Het interval waarmee de client de status kan controleren.                                                       |
| Operation-Location | Ja          | Koppeling naar een resource om de bewerkings status op te halen.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Abonnement verwijderen

Met de actie verwijderen op het abonnements eindpunt kan een gebruiker een abonnement met een opgegeven ID verwijderen.

*Aanvraag*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Parameter naam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID van het SaaS-abonnement.                              |
| api-version         | De versie van de bewerking die moet worden gebruikt voor deze aanvraag. |
|  |  |

*Headers*

| **Header sleutel**     | **Vereist** | **Beschrijving**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client. U wordt aangeraden een GUID in te stellen. Als deze waarde niet wordt gegeven, wordt er een gegenereerd en weer gegeven in de antwoord headers.                                                           |
| x-MS-correlationid | Nee           | Een unieke teken reeks waarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde. Als u deze niet opgeeft, wordt er een gegenereerd en geleverd in de antwoord headers. |
| authorization      | Ja          | Het JWT-Bearer-token (Web token).                    |
|  |  |  |

*Antwoord codes*

| **HTTP-status code** | **Fout code**     | **Beschrijving**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Activering van SaaS-abonnement ontvangen voor een bepaald abonnement.                   |
| 400                  | `BadRequest`         | De vereiste headers ontbreken of de hoofd tekst van de JSON is onjuist gevormd. |
| 403                  | `Forbidden`          | De aanroeper is niet gemachtigd om deze bewerking uit te voeren.                   |
| 404                  | `NotFound`           | Het abonnement is niet gevonden met de opgegeven ID                                  |
| 429                  | `RequestThrottleId`  | De service verwerkt aanvragen. Probeer het later opnieuw.                  |
| 503                  | `ServiceUnavailable` | De service is tijdelijk niet beschikbaar. Probeer het later opnieuw.                          |
|  |  |  |

Voor een 202-antwoord moet u de status van de aanvraag bewerking opvolgen op de koptekst van de bewerkings locatie. De verificatie is hetzelfde als andere Marketplace-Api's.

*Antwoord headers*

| **Header sleutel**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID die is ontvangen van de client.                                                                   |
| x-MS-correlationid | Ja          | Correlatie-ID als deze door de client is door gegeven, anders is dit de correlatie-ID van de server.                   |
| x-ms-activityid    | Ja          | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor reconciliaties. |
| Opnieuw proberen na        | Ja          | Het interval waarmee de client de status kan controleren.                                                       |
| Operation-Location | Ja          | Koppeling naar een resource om de bewerkings status op te halen.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Bewerkingsstatus ophalen

Met dit eind punt kan de gebruiker de status van een geactiveerde async-bewerking (abonneren/afmelden/wijzigings plan) bijhouden.

*Aanvraag*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/ *{bewerkings-id}* ?api-version=2017-04-15**

| **Parameter naam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | De unieke ID voor de geactiveerde bewerking.                |
| api-version         | De versie van de bewerking die moet worden gebruikt voor deze aanvraag. |
|  |  |

*Headers*

| **Header sleutel**     | **Vereist** | **Beschrijving**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client. U wordt aangeraden een GUID in te stellen. Als deze waarde niet wordt gegeven, wordt er een gegenereerd en weer gegeven in de antwoord headers.   |
| x-MS-correlationid | Nee           | Een unieke teken reeks waarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde. Als deze waarde niet wordt gegeven, wordt er een gegenereerd en weer gegeven in de antwoord headers.  |
| authorization      | Ja          | Het JWT-Bearer-token (Web token).                    |
|  |  |  | 

*Antwoord tekst*

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **Parameternaam** | **Gegevenstype** | **Beschrijving**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | Tekenreeks        | De ID van de bewerking.                                                                      |
| status             | Enum          | Bewerkings status, een van de volgende: `In Progress`, `Succeeded`of `Failed`.          |
| resourceLocation   | Tekenreeks        | Koppeling naar het abonnement dat is gemaakt of gewijzigd. Zo kan de client bijgewerkte status post-bewerking ophalen. Deze waarde is niet ingesteld voor `Unsubscribe` bewerkingen. |
| toegevoegd            | Datum/tijd      | Aanmaak tijd van de bewerking in UTC.                                                           |
| lastModified       | Datum/tijd      | Laatste update voor de bewerking in UTC.                                                      |
|  |  |  |

*Antwoord codes*

| **HTTP-status code** | **Fout code**     | **Beschrijving**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | De GET-aanvraag is opgelost en de hoofd tekst bevat de reactie.    |
| 400                  | `BadRequest`         | De vereiste headers ontbreken of er is een ongeldige API-versie opgegeven. |
| 403                  | `Forbidden`          | De aanroeper is niet gemachtigd om deze bewerking uit te voeren.                      |
| 404                  | `NotFound`           | Het abonnement is niet gevonden met de opgegeven ID.                                     |
| 429                  | `RequestThrottleId`  | De service is bezig met het verwerken van aanvragen. Probeer het later opnieuw.                     |
| 503                  | `ServiceUnavailable` | De service is tijdelijk niet beschikbaar. Probeer het later opnieuw.                             |
|  |  |  |

*Antwoord headers*

| **Header sleutel**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID die is ontvangen van de client.                                                                   |
| x-MS-correlationid | Ja          | Correlatie-ID als deze door de client is door gegeven, anders is dit de correlatie-ID van de server.                   |
| x-ms-activityid    | Ja          | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor reconciliaties. |
| Opnieuw proberen na        | Ja          | Het interval waarmee de client de status kan controleren.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Abonnement ophalen

Met het eind punt actie ophalen voor abonneren kan een gebruiker een abonnement met een bepaalde resource-id ophalen.

*Aanvraag*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Parameter naam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID van het SaaS-abonnement.                              |
| api-version         | De versie van de bewerking die moet worden gebruikt voor deze aanvraag. |
|  |  |

*Headers*

| **Header sleutel**     | **Vereist** | **Beschrijving**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID. Als deze waarde niet wordt gegeven, wordt er een gegenereerd en weer gegeven in de antwoord headers.                                                           |
| x-MS-correlationid | Nee           | Een unieke teken reeks waarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde. Als deze waarde niet wordt gegeven, wordt er een gegenereerd en weer gegeven in de antwoord headers. |
| authorization      | Ja          | Het JWT-Bearer-token (Web token).                                                                    |
|  |  |  |

*Antwoord tekst*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Parameternaam**     | **Gegevenstype** | **Beschrijving**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Tekenreeks        | ID van de SaaS-abonnements resource in Azure.    |
| offerId                | Tekenreeks        | De aanbiedings-ID waarop de gebruiker zich heeft geabonneerd.         |
| planId                 | Tekenreeks        | De plan-ID waarop de gebruiker zich heeft geabonneerd.          |
| saasSubscriptionName   | Tekenreeks        | De naam van het SaaS-abonnement.                |
| saasSubscriptionStatus | Enum          | Status van de bewerking.  Een van de volgende producten:  <br/> - `Subscribed`: abonnement is actief.  <br/> - `Pending`: de gebruiker heeft de resource gemaakt maar is niet geactiveerd door de ISV.   <br/> - `Unsubscribed`: de gebruiker heeft zich afgemeld.   <br/> - `Suspended`: de gebruiker heeft het abonnement onderbroken.   <br/> - `Deactivated`: het Azure-abonnement is onderbroken.  |
| toegevoegd                | Datum/tijd      | Time Stamp-waarde voor het maken van abonnementen in UTC. |
| lastModified           | Datum/tijd      | De time stamp-waarde voor het abonnement is gewijzigd in UTC. |
|  |  |  |

*Antwoord codes*

| **HTTP-status code** | **Fout code**     | **Beschrijving**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | De GET-aanvraag is opgelost en de hoofd tekst bevat de reactie.    |
| 400                  | `BadRequest`         | De vereiste headers ontbreken of er is een ongeldige API-versie opgegeven. |
| 403                  | `Forbidden`          | De aanroeper is niet gemachtigd om deze bewerking uit te voeren.                      |
| 404                  | `NotFound`           | Het abonnement is niet gevonden met de opgegeven ID                                     |
| 429                  | `RequestThrottleId`  | De service is bezig met het verwerken van aanvragen. Probeer het later opnieuw.                     |
| 503                  | `ServiceUnavailable` | De service is tijdelijk niet beschikbaar. Probeer het later opnieuw.                             |
|  |  |  |

*Antwoord headers*

| **Header sleutel**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID die is ontvangen van de client.                                                                   |
| x-MS-correlationid | Ja          | Correlatie-ID als deze door de client is door gegeven, anders is dit de correlatie-ID van de server.                   |
| x-ms-activityid    | Ja          | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor reconciliaties. |
| Opnieuw proberen na        | Nee           | Het interval waarmee de client de status kan controleren.                                                       |
| eTag               | Ja          | Koppeling naar een resource om de bewerkings status op te halen.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Abonnementen ophalen

Met het eind punt actie ophalen voor abonnementen kan een gebruiker alle abonnementen ophalen voor alle aanbiedingen van de ISV.

*Aanvraag*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Parameter naam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| api-version         | De versie van de bewerking die moet worden gebruikt voor deze aanvraag. |
|  |  |

*Headers*

| **Header sleutel**     | **Vereist** | **Beschrijving**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client. U wordt aangeraden een GUID in te stellen. Als deze waarde niet wordt gegeven, wordt er een gegenereerd en weer gegeven in de antwoord headers.             |
| x-MS-correlationid | Nee           | Een unieke teken reeks waarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde. Als deze waarde niet wordt gegeven, wordt er een gegenereerd en weer gegeven in de antwoord headers. |
| authorization      | Ja          | Het JWT-Bearer-token (Web token).                    |
|  |  |  |

*Antwoord tekst*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Parameternaam**     | **Gegevenstype** | **Beschrijving**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Tekenreeks        | ID van SaaS-abonnements resource in azure    |
| offerId                | Tekenreeks        | De aanbiedings-ID waarop de gebruiker zich heeft geabonneerd         |
| planId                 | Tekenreeks        | Plan-ID waarop de gebruiker zich heeft geabonneerd          |
| saasSubscriptionName   | Tekenreeks        | Naam van het SaaS-abonnement                |
| saasSubscriptionStatus | Enum          | Status van de bewerking.  Een van de volgende producten:  <br/> - `Subscribed`: abonnement is actief.  <br/> - `Pending`: de gebruiker heeft de resource gemaakt maar is niet geactiveerd door de ISV.   <br/> - `Unsubscribed`: de gebruiker heeft zich afgemeld.   <br/> - `Suspended`: de gebruiker heeft het abonnement onderbroken.   <br/> - `Deactivated`: het Azure-abonnement is onderbroken.  |
| toegevoegd                | Datum/tijd      | Time Stamp-waarde voor het maken van abonnementen in UTC |
| lastModified           | Datum/tijd      | Time Stamp-waarde gewijzigd in UTC |
|  |  |  |

*Antwoord codes*

| **HTTP-status code** | **Fout code**     | **Beschrijving**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | De GET-aanvraag is opgelost en de hoofd tekst bevat de reactie.    |
| 400                  | `BadRequest`         | De vereiste headers ontbreken of er is een ongeldige API-versie opgegeven. |
| 403                  | `Forbidden`          | De aanroeper is niet gemachtigd om deze bewerking uit te voeren.                      |
| 404                  | `NotFound`           | Het abonnement is niet gevonden met de opgegeven ID                                     |
| 429                  | `RequestThrottleId`  | De service verwerkt aanvragen. Probeer het later opnieuw.                     |
| 503                  | `ServiceUnavailable` | De service is tijdelijk niet beschikbaar. Probeer het later opnieuw.                             |
|  |  |  |

*Antwoord headers*

| **Header sleutel**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID die is ontvangen van de client.                                                                   |
| x-MS-correlationid | Ja          | Correlatie-ID als deze door de client is door gegeven, anders is dit de correlatie-ID van de server.                   |
| x-ms-activityid    | Ja          | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor reconciliaties. |
| Opnieuw proberen na        | Nee           | Het interval waarmee de client de status kan controleren.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS Webhook

Een SaaS-webhook wordt gebruikt voor het proactief op de hoogte brengen van wijzigingen aan de SaaS-service. Deze bericht-API wordt naar verwachting niet-geverifieerd en wordt aangeroepen door de micro soft-service. De SaaS-service wordt verwacht de operations API aan te roepen om te valideren en goed te keuren voordat actie wordt ondernomen voor de webhook-melding. 

*Hoofdtekst*

``` json
  {
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "action": "Subscribe", // Subscribe/Unsubscribe/ChangePlan
    "operationRequestSource":"Azure",
    "timeStamp":"2018-12-01T00:00:00"
  }
```

| **Parameternaam**     | **Gegevenstype** | **Beschrijving**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | Tekenreeks       | De unieke ID voor de geactiveerde bewerking.                |
| activityId   | Tekenreeks        | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor reconciliaties.               |
| subscriptionId                     | Tekenreeks        | ID van de SaaS-abonnements resource in Azure.    |
| offerId                | Tekenreeks        | De aanbiedings-ID waarop de gebruiker zich heeft geabonneerd. Alleen gegeven bij de actie ' update '.        |
| publisherId                | Tekenreeks        | Uitgevers-ID van de SaaS-aanbieding         |
| planId                 | Tekenreeks        | De plan-ID waarop de gebruiker zich heeft geabonneerd. Alleen gegeven bij de actie ' update '.          |
| action                 | Tekenreeks        | De actie waarmee deze melding wordt geactiveerd. Mogelijke waarden: activeren, verwijderen, onderbreken, opnieuw invoeren, bijwerken          |
| Neem                 | Tekenreeks        | Time Stamp-waarde in UTC wanneer deze melding is geactiveerd.          |
|  |  |  |


## <a name="next-steps"></a>Volgende stappen

Ontwikkel aars kunnen werk belastingen, aanbiedingen en Publisher-profielen ook programmatisch ophalen en manipuleren met behulp van de [Cloud Partner-Portal rest-api's](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
