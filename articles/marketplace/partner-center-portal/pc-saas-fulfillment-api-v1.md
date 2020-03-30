---
title: SaaS Fulfillment API's v1 | Azure Marketplace
description: Hier wordt uitgelegd hoe u een SaaS-aanbieding op de Azure Marketplace maakt en beheert met behulp van de bijbehorene Fulfillment v1 API's.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: dsindona
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec8373288a2ea5809ee5d349c52c57051586035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288339"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>SaaS Fulfillment API's versie 1 (afgeschaft)

In dit artikel wordt uitgelegd hoe u een SaaS-aanbieding met API's maken. De API's, bestaande uit REST-methoden en eindpunten, zijn nodig om abonnementen op uw SaaS-aanbieding toe te staan als u Verkopen via Azure hebt geselecteerd.  

> [!WARNING]
> Deze eerste versie van de SaaS Fulfillment API is afgeschaft; Gebruik in plaats daarvan [SaaS Fulfillment API V2](./pc-saas-fulfillment-api-v2.md).  Deze eerste versie van de API wordt momenteel alleen onderhouden om bestaande uitgevers te bedienen. 

De volgende API's worden verstrekt om u te helpen uw SaaS-service te integreren met Azure:

-   Oplossen
-   Abonneren
-   Converteren
-   Afmelden


## <a name="api-methods-and-endpoints"></a>API-methoden en eindpunten

In de volgende secties worden de API-methoden en eindpunten beschreven die beschikbaar zijn voor het inschakelen van abonnementen voor een SaaS-aanbieding.


### <a name="marketplace-api-endpoint-and-api-version"></a>Marketplace API-eindpunt en API-versie

Het eindpunt voor Azure `https://marketplaceapi.microsoft.com`Marketplace API is .

De huidige API-versie is `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Abonnement oplossen

Post actie op te lossen eindpunt kunnen gebruikers een marketplace token op te lossen om een permanente Resource ID.  De Resource-id is de unieke id voor het SAAS-abonnement. 

Wanneer een gebruiker wordt doorgestuurd naar de website van een ISV, bevat de URL een token in de queryparameters. De ISV zal naar verwachting dit token gebruiken en een verzoek indienen om het op te lossen. Het antwoord bevat de unieke SAAS-abonnements-ID, naam, aanbiedings-ID en plan voor de bron. Dit token is slechts een uur geldig.

*Aanvraag*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Parameternaam** |     **Beschrijving**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-versie        |  De versie van de bewerking die voor dit verzoek moet worden gebruikt.   |
|  |  |


*Headers*

| **Kopteksttoets**     | **Vereist** | **Beschrijving**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.  |
| x-ms-correlatieid | Nee           | Een unieke tekenreekswaarde voor de bewerking op de client. Dit veld correleert alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen. |
| Inhoudstype       | Ja          | `application/json`                                        |
| autorisatie      | Ja          | Het JSON-webtoken (JWT) dragertoken.                    |
| x-ms-marketplace-token| Ja| De parameter tokenquery in de URL wanneer de gebruiker vanuit Azure wordt doorgestuurd naar de website van SaaS ISV. **Let op:** Dit token is slechts 1 uur geldig. Bovendien decodeert URL de tokenwaarde van de browser voordat u deze gebruikt.|
|  |  |  |
  

*Reactie-instantie*

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
| subscriptionName| Tekenreeks| Naam van het SaaS-abonnement dat door de gebruiker in Azure is ingesteld terwijl u zich abonneert op de SaaS-service.|
| AanbiedingId            | Tekenreeks        | Bied id aan waarop de gebruiker zich heeft geabonneerd. |
| planId             | Tekenreeks        | Plan ID waarop de gebruiker zich heeft geabonneerd.  |
|  |  |  |


*Reactiecodes*

| **HTTP-statuscode** | **Foutcode**     | **Beschrijving**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token is opgelost.                                                            |
| 400                  | `BadRequest`         | Vereist headers ontbreken of een ongeldige api-versie is opgegeven. Kan het token niet oplossen omdat het token verkeerd is vervormd of is verlopen (het token is slechts 1 uur geldig nadat het is gegenereerd). |
| 403                  | `Forbidden`          | De beller is niet bevoegd om deze bewerking uit te voeren.                                 |
| 429                  | `RequestThrottleId`  | Service is bezig met het verwerken van aanvragen, probeer het later opnieuw.                                |
| 503                  | `ServiceUnavailable` | Service is tijdelijk uitgeschakeld, probeer het later opnieuw.                                        |
|  |  |  |


*Antwoordheaders*

| **Kopteksttoets**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID ontvangen van de klant.                                                                   |
| x-ms-correlatieid | Ja          | Correlatie-ID als deze door de client wordt doorgegeven, anders is deze waarde de servercorrelatie-id.                   |
| x-ms-activiteit    | Ja          | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de service. Deze ID wordt gebruikt voor eventuele afstemmingen. |
| Opnieuw proberen-Na        | Nee           | Deze waarde is alleen ingesteld voor een reactie van 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Abonneren

Met het abonnementseindpunt kunnen gebruikers een abonnement op een SaaS-service starten voor een bepaald abonnement en facturering in het commercesysteem inschakelen.

**ZET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-versie=2017-04-15**

| **Parameternaam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Unieke ID van SaaS-abonnement dat wordt verkregen na het oplossen van het token via Resolve API.                              |
| api-versie         | De versie van de bewerking die voor dit verzoek moet worden gebruikt. |
|  |  |

*Headers*

|  **Kopteksttoets**        | **Vereist** |  **Beschrijving**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Nee         | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als dit niet wordt verstrekt, zal men worden gegenereerd en verstrekt in de antwoordheaders. |
| x-ms-correlatieid     |   Nee         | Een unieke tekenreekswaarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als dit niet wordt verstrekt, zal men worden gegenereerd en verstrekt in de antwoordheaders. |
| If-Match/If-None-Match |   Nee         |   Sterke validator ETag waarde.                                                          |
| inhoudstype           |   Ja        |    `application/json`                                                                   |
|  autorisatie         |   Ja        |    Het JSON-webtoken (JWT) dragertoken.                                               |
| x-ms-marketplace-session-mode| Nee | Vlag om de droogloopmodus in te schakelen terwijl u zich abonneert op een SaaS-aanbieding. Als dit is ingesteld, worden er geen kosten in rekening gebracht. Dit is handig voor ISV-testscenario's. Zet het in op **'dryrun'**|
|  |  |  |

*Hoofdtekst*

``` json
{
    "lanId": "",
}
```

| **Elementnaam** | **Gegevenstype** | **Beschrijving**                      |
|------------------|---------------|--------------------------------------|
| planId           | (Vereist) Tekenreeks        | Plan ID van de SaaS-servicegebruiker is een abonnement.  |
|  |  |  |

*Reactiecodes*

| **HTTP-statuscode** | **Foutcode**     | **Beschrijving**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS-abonnementsactivering ontvangen voor een bepaald abonnement.                   |
| 400                  | `BadRequest`         | Ofwel vereiste headers ontbreken of het lichaam van de JSON is misvormd. |
| 403                  | `Forbidden`          | De beller is niet bevoegd om deze bewerking uit te voeren.                   |
| 404                  | `NotFound`           | Abonnement niet gevonden met de opgegeven ID                                  |
| 409                  | `Conflict`           | Er is een andere bewerking aan de gang voor het abonnement.                     |
| 429                  | `RequestThrottleId`  | Service is bezig met het verwerken van aanvragen, probeer het later opnieuw.                  |
| 503                  | `ServiceUnavailable` | Service is tijdelijk uitgeschakeld, probeer het later opnieuw.                          |
|  |  |  |

Voor een 202-antwoord moet u de status van de aanvraagbewerking opvolgen op de kop 'Operatielocatie'. De verificatie is hetzelfde als andere Marketplace-API's.

*Antwoordheaders*

| **Kopteksttoets**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID ontvangen van de klant.                                                                   |
| x-ms-correlatieid | Ja          | Correlatie-ID als deze door de client wordt doorgegeven, anders is deze waarde de servercorrelatie-id.                   |
| x-ms-activiteit    | Ja          | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de service. Deze waarde wordt gebruikt voor eventuele afstemmingen. |
| Opnieuw proberen-Na        | Ja          | Interval waarmee de client de status kan controleren.                                                       |
| Operatielocatie | Ja          | Koppeling naar een resource om de bedrijfsstatus te krijgen.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Eindpunt van plan wijzigen

Met het wijzigingseindpunt kan de gebruiker zijn momenteel geabonneerde abonnement converteren naar een nieuw abonnement.

**Patch**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-versie=2017-04-15**

| **Parameternaam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID van SaaS-abonnement.                              |
| api-versie         | De versie van de bewerking die voor dit verzoek moet worden gebruikt. |
|  |  |

*Headers*

| **Kopteksttoets**          | **Vereist** | **Beschrijving**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Nee           | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client. Beveel een GUID aan. Als dit niet wordt verstrekt, zal men worden gegenereerd en verstrekt in de antwoordheaders.   |
| x-ms-correlatieid      | Nee           | Een unieke tekenreekswaarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als dit niet wordt verstrekt, zal men worden gegenereerd en verstrekt in de antwoordheaders. |
| If-Match /If-None-Match | Nee           | Sterke validator ETag waarde.                              |
| inhoudstype            | Ja          | `application/json`                                        |
| autorisatie           | Ja          | Het JSON-webtoken (JWT) dragertoken.                    |
|  |  |  |

*Hoofdtekst*

```json
{
    "planId": ""
}
```

|  **Elementnaam** |  **Gegevenstype**  | **Beschrijving**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  (Vereist) Tekenreeks         | Plan ID van de SaaS-servicegebruiker is een abonnement.          |
|  |  |  |

*Reactiecodes*

| **HTTP-statuscode** | **Foutcode**     | **Beschrijving**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS-abonnementsactivering ontvangen voor een bepaald abonnement.                   |
| 400                  | `BadRequest`         | Ofwel vereiste headers ontbreken of het lichaam van de JSON is misvormd. |
| 403                  | `Forbidden`          | De beller is niet bevoegd om deze bewerking uit te voeren.                   |
| 404                  | `NotFound`           | Abonnement niet gevonden met de opgegeven ID                                  |
| 409                  | `Conflict`           | Er is een andere bewerking aan de gang voor het abonnement.                     |
| 429                  | `RequestThrottleId`  | Service is bezig met het verwerken van aanvragen, probeer het later opnieuw.                  |
| 503                  | `ServiceUnavailable` | Service is tijdelijk uitgeschakeld, probeer het later opnieuw.                          |
|  |  |  |

*Antwoordheaders*

| **Kopteksttoets**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID ontvangen van de klant.                                                                   |
| x-ms-correlatieid | Ja          | Correlatie-ID als deze door de client wordt doorgegeven, anders is deze waarde de servercorrelatie-id.                   |
| x-ms-activiteit    | Ja          | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de service. Deze waarde wordt gebruikt voor eventuele afstemmingen. |
| Opnieuw proberen-Na        | Ja          | Interval waarmee de client de status kan controleren.                                                       |
| Operatielocatie | Ja          | Koppeling naar een resource om de bedrijfsstatus te krijgen.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Abonnement verwijderen

Met de actie Verwijderen op het eindpunt abonneren kan een gebruiker een abonnement met een bepaalde id verwijderen.

*Aanvraag*

**Verwijderen**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-versie=2017-04-15**

| **Parameternaam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID van SaaS-abonnement.                              |
| api-versie         | De versie van de bewerking die voor dit verzoek moet worden gebruikt. |
|  |  |

*Headers*

| **Kopteksttoets**     | **Vereist** | **Beschrijving**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client. Beveel een GUID aan. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.                                                           |
| x-ms-correlatieid | Nee           | Een unieke tekenreekswaarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als dit niet wordt verstrekt, zal men worden gegenereerd en verstrekt in de antwoordheaders. |
| autorisatie      | Ja          | Het JSON-webtoken (JWT) dragertoken.                    |
|  |  |  |

*Reactiecodes*

| **HTTP-statuscode** | **Foutcode**     | **Beschrijving**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | SaaS-abonnementsactivering ontvangen voor een bepaald abonnement.                   |
| 400                  | `BadRequest`         | Ofwel vereiste headers ontbreken of het lichaam van de JSON is misvormd. |
| 403                  | `Forbidden`          | De beller is niet bevoegd om deze bewerking uit te voeren.                   |
| 404                  | `NotFound`           | Abonnement niet gevonden met de opgegeven ID                                  |
| 429                  | `RequestThrottleId`  | Service is druk bezig met het verwerken van aanvragen, probeer het later opnieuw.                  |
| 503                  | `ServiceUnavailable` | De service is tijdelijk uitgeschakeld. Probeer het later opnieuw.                          |
|  |  |  |

Voor een 202-antwoord moet u de status van de aanvraagbewerking opvolgen op de kop 'Operatielocatie'. De verificatie is hetzelfde als andere Marketplace-API's.

*Antwoordheaders*

| **Kopteksttoets**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID ontvangen van de klant.                                                                   |
| x-ms-correlatieid | Ja          | Correlatie-ID als deze door de client wordt doorgegeven, anders is dit de servercorrelatie-id.                   |
| x-ms-activiteit    | Ja          | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor eventuele verzoeningen. |
| Opnieuw proberen-Na        | Ja          | Interval waarmee de client de status kan controleren.                                                       |
| Operatielocatie | Ja          | Koppeling naar een resource om de bedrijfsstatus te krijgen.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Bewerkingsstatus ophalen

Met dit eindpunt kan de gebruiker de status van een geactiveerde synchronisatiebewerking bijhouden (Abonnement/Afmelden/wijzigen).

*Aanvraag*

**Toevoegen**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-versie=2017-04-15**

| **Parameternaam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Unieke ID voor de geactiveerde bewerking.                |
| api-versie         | De versie van de bewerking die voor dit verzoek moet worden gebruikt. |
|  |  |

*Headers*

| **Kopteksttoets**     | **Vereist** | **Beschrijving**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client. Beveel een GUID aan. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.   |
| x-ms-correlatieid | Nee           | Een unieke tekenreekswaarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.  |
| autorisatie      | Ja          | Het JSON-webtoken (JWT) dragertoken.                    |
|  |  |  | 

*Reactie-instantie*

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
| id                 | Tekenreeks        | ID van de operatie.                                                                      |
| status             | Enum          | De bedrijfsstatus, een `In Progress`van `Succeeded`de `Failed`volgende: , of .          |
| resourceLocation   | Tekenreeks        | Koppeling naar het abonnement dat is gemaakt of gewijzigd. Dit helpt de client om bijgewerkte statuspostbewerking te krijgen. Deze waarde is `Unsubscribe` niet ingesteld voor bewerkingen. |
| Gemaakt            | DateTime      | Bewerkingstijd in UTC.                                                           |
| Lastmodified       | DateTime      | Laatste update over de bewerking in UTC.                                                      |
|  |  |  |

*Reactiecodes*

| **HTTP-statuscode** | **Foutcode**     | **Beschrijving**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Resolved the get request successfully and the body contains the response.    |
| 400                  | `BadRequest`         | Vereist headers ontbreken of er is een ongeldige api-versie opgegeven. |
| 403                  | `Forbidden`          | De beller is niet bevoegd om deze bewerking uit te voeren.                      |
| 404                  | `NotFound`           | Abonnement niet gevonden met de opgegeven ID.                                     |
| 429                  | `RequestThrottleId`  | Service is bezig met het verwerken van aanvragen, probeer het later opnieuw.                     |
| 503                  | `ServiceUnavailable` | Service is tijdelijk uitgeschakeld, probeer het later opnieuw.                             |
|  |  |  |

*Antwoordheaders*

| **Kopteksttoets**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID ontvangen van de klant.                                                                   |
| x-ms-correlatieid | Ja          | Correlatie-ID als deze door de client wordt doorgegeven, anders is dit de servercorrelatie-id.                   |
| x-ms-activiteit    | Ja          | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor eventuele verzoeningen. |
| Opnieuw proberen-Na        | Ja          | Interval waarmee de client de status kan controleren.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Abonnement krijgen

Met de actie Ophalen op het eindpunt abonneren kan een gebruiker een abonnement ophalen met een bepaalde resource-id.

*Aanvraag*

**Toevoegen**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-versie=2017-04-15**

| **Parameternaam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID van SaaS-abonnement.                              |
| api-versie         | De versie van de bewerking die voor dit verzoek moet worden gebruikt. |
|  |  |

*Headers*

| **Kopteksttoets**     | **Vereist** | **Beschrijving**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.                                                           |
| x-ms-correlatieid | Nee           | Een unieke tekenreekswaarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen. |
| autorisatie      | Ja          | Het JSON-webtoken (JWT) dragertoken.                                                                    |
|  |  |  |

*Reactie-instantie*

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
| id                     | Tekenreeks        | ID van SaaS-abonnementsbron in Azure.    |
| aanbiedingId                | Tekenreeks        | Bied id aan waarop de gebruiker zich heeft geabonneerd.         |
| planId                 | Tekenreeks        | Plan ID waarop de gebruiker zich heeft geabonneerd.          |
| saasSubscriptionName   | Tekenreeks        | Naam van het SaaS-abonnement.                |
| saasSubscriptionStatus | Enum          | De status van de bewerking.  Een van de volgende producten:  <br/> - `Subscribed`: Abonnement is actief.  <br/> - `Pending`: Gebruiker maakt de bron, maar wordt niet geactiveerd door de ISV.   <br/> - `Unsubscribed`: De gebruiker heeft zich afgemeld.   <br/> - `Suspended`: De gebruiker heeft het abonnement opgeschort.   <br/> - `Deactivated`: Azure-abonnement wordt opgeschort.  |
| Gemaakt                | DateTime      | Waarde van tijdstempelvoor het maken van abonnementen in UTC. |
| Lastmodified           | DateTime      | Gewijzigde tijdstempelwaarde voor abonnementen in UTC. |
|  |  |  |

*Reactiecodes*

| **HTTP-statuscode** | **Foutcode**     | **Beschrijving**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Resolved the get request successfully and the body contains the response.    |
| 400                  | `BadRequest`         | Vereist headers ontbreken of er is een ongeldige api-versie opgegeven. |
| 403                  | `Forbidden`          | De beller is niet bevoegd om deze bewerking uit te voeren.                      |
| 404                  | `NotFound`           | Abonnement niet gevonden met de opgegeven ID                                     |
| 429                  | `RequestThrottleId`  | Service is bezig met het verwerken van aanvragen, probeer het later opnieuw.                     |
| 503                  | `ServiceUnavailable` | Service is tijdelijk uitgeschakeld, probeer het later opnieuw.                             |
|  |  |  |

*Antwoordheaders*

| **Kopteksttoets**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID ontvangen van de klant.                                                                   |
| x-ms-correlatieid | Ja          | Correlatie-ID als deze door de client wordt doorgegeven, anders is dit de servercorrelatie-id.                   |
| x-ms-activiteit    | Ja          | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor eventuele verzoeningen. |
| Opnieuw proberen-Na        | Nee           | Interval waarmee de client de status kan controleren.                                                       |
| eTag               | Ja          | Koppeling naar een resource om de bedrijfsstatus te krijgen.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Abonnementen kopen

Met het eindpunt Ophalen op abonnementen kan een gebruiker alle abonnementen ophalen voor alle aanbiedingen van de ISV.

*Aanvraag*

**Toevoegen**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Parameternaam**  | **Beschrijving**                                       |
|---------------------|-------------------------------------------------------|
| api-versie         | De versie van de bewerking die voor dit verzoek moet worden gebruikt. |
|  |  |

*Headers*

| **Kopteksttoets**     | **Vereist** | **Beschrijving**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Nee           | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client. Beveel een GUID aan. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.             |
| x-ms-correlatieid | Nee           | Een unieke tekenreekswaarde voor de bewerking op de client. Deze waarde is voor het correleren van alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen. |
| autorisatie      | Ja          | Het JSON-webtoken (JWT) dragertoken.                    |
|  |  |  |

*Reactie-instantie*

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
| id                     | Tekenreeks        | ID van SaaS-abonnementsbron in Azure    |
| aanbiedingId                | Tekenreeks        | Aanbiedings-id waarop de gebruiker zich heeft geabonneerd         |
| planId                 | Tekenreeks        | Id plannen waarop de gebruiker zich heeft geabonneerd          |
| saasSubscriptionName   | Tekenreeks        | Naam van het SaaS-abonnement                |
| saasSubscriptionStatus | Enum          | De status van de bewerking.  Een van de volgende producten:  <br/> - `Subscribed`: Abonnement is actief.  <br/> - `Pending`: Gebruiker maakt de bron, maar wordt niet geactiveerd door de ISV.   <br/> - `Unsubscribed`: De gebruiker heeft zich afgemeld.   <br/> - `Suspended`: De gebruiker heeft het abonnement opgeschort.   <br/> - `Deactivated`: Azure-abonnement wordt opgeschort.  |
| Gemaakt                | DateTime      | Waarde van tijdstempelvoor het maken van abonnementen in UTC |
| Lastmodified           | DateTime      | Gewijzigde tijdstempelwaarde voor abonnementen in UTC |
|  |  |  |

*Reactiecodes*

| **HTTP-statuscode** | **Foutcode**     | **Beschrijving**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Resolved the get request successfully and the body contains the response.    |
| 400                  | `BadRequest`         | Vereist headers ontbreken of er is een ongeldige api-versie opgegeven. |
| 403                  | `Forbidden`          | De beller is niet bevoegd om deze bewerking uit te voeren.                      |
| 404                  | `NotFound`           | Abonnement niet gevonden met de opgegeven ID                                     |
| 429                  | `RequestThrottleId`  | Service is druk bezig met het verwerken van aanvragen, probeer het later opnieuw.                     |
| 503                  | `ServiceUnavailable` | De service is tijdelijk uitgeschakeld. Probeer het later opnieuw.                             |
|  |  |  |

*Antwoordheaders*

| **Kopteksttoets**     | **Vereist** | **Beschrijving**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ja          | Aanvraag-ID ontvangen van de klant.                                                                   |
| x-ms-correlatieid | Ja          | Correlatie-ID als deze door de client wordt doorgegeven, anders is dit de servercorrelatie-id.                   |
| x-ms-activiteit    | Ja          | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor eventuele verzoeningen. |
| Opnieuw proberen-Na        | Nee           | Interval waarmee de client de status kan controleren.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS Webhook

Een SaaS webhook wordt gebruikt om wijzigingen proactief aan te melden bij de SaaS-service. Deze POST API is naar verwachting niet geverifieerd en zal worden aangeroepen door de Microsoft-service. De SaaS-service zal naar verwachting de operations API aanroepen om te valideren en te autoriseren voordat deze actie onderneemt op de webhook-melding. 

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
| id  | Tekenreeks       | Unieke ID voor de geactiveerde bewerking.                |
| activiteitId   | Tekenreeks        | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de service. Dit wordt gebruikt voor eventuele verzoeningen.               |
| subscriptionId                     | Tekenreeks        | ID van SaaS-abonnementsbron in Azure.    |
| aanbiedingId                | Tekenreeks        | Bied id aan waarop de gebruiker zich heeft geabonneerd. Alleen voorzien van de actie 'Bijwerken'.        |
| uitgeverId                | Tekenreeks        | Publisher ID van de SaaS aanbieding         |
| planId                 | Tekenreeks        | Plan ID waarop de gebruiker zich heeft geabonneerd. Alleen voorzien van de actie 'Bijwerken'.          |
| action                 | Tekenreeks        | De actie die deze melding activeert. Mogelijke waarden - Activeren, verwijderen, onderbreken, herstellen, bijwerken          |
| Tijdstempel                 | Tekenreeks        | TImestamp-waarde in UTC toen deze melding werd geactiveerd.          |
|  |  |  |


## <a name="next-steps"></a>Volgende stappen

Ontwikkelaars kunnen ook programmatisch workloads, aanbiedingen en uitgeversprofielen ophalen en manipuleren met behulp van de [API's van Cloud Partner Portal REST.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)
