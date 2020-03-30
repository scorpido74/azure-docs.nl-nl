---
title: SaaS Fulfillment API v2 | Azure Marketplace
description: In dit artikel wordt uitgelegd hoe u een SaaS-aanbieding op de AppSource- en Azure Marketplace maken en beheren met behulp van de bijbehorende v2-API's voor vervulling.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: dsindona
ms.openlocfilehash: ca49418013357ecaae62ea5e91374eaa1cbde59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275761"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS-leverings-API's, versie 2 

In dit artikel worden de API's beschreven waarmee partners hun SaaS-toepassingen kunnen verkopen in de AppSource-marktplaats en de Azure Marketplace. Deze API's zijn een vereiste voor verhandelbare SaaS-aanbiedingen op de AppSource- en Azure Marketplace.

## <a name="managing-the-saas-subscription-life-cycle"></a>Levenscyclus van het SaaS-abonnement beheren

Azure SaaS beheert de volledige levenscyclus van een SaaS-abonnement. Het maakt gebruik van de vervulling API's als een mechanisme om de werkelijke vervulling, wijzigingen in plannen, en verwijdering van het abonnement met de partner rijden. De factuur van de klant is gebaseerd op de status van het SaaS-abonnement dat Microsoft onderhoudt. In het volgende diagram worden de toestanden en bewerkingen weergegeven die de wijzigingen tussen de statussen stimuleren.

![SaaS-abonnementsleeftijd](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Staten van een SaaS-abonnement

In de volgende tabel worden de provisioningstoestanden voor een SaaS-abonnement weergegeven, inclusief een beschrijvingen- en sequentiediagram voor elke (indien van toepassing). 

#### <a name="provisioning"></a>Inrichten

Wanneer een klant een aankoop initieert, ontvangt de partner deze informatie in een autorisatiecode op een klantinteractieve webpagina die een URL-parameter gebruikt. Een voorbeeld `https://contoso.com/signup?token=..`is , terwijl de URL `https://contoso.com/signup`van de bestemmingspagina in partnercentrum is . De autorisatiecode kan worden gevalideerd en geruild voor de details van de inrichtingsservice door de API voor oplossen aan te roepen.  Wanneer een SaaS-service klaar is met inrichten, wordt een activerend gesprek uitgevoerd om aan te geven dat de afhandeling is voltooid en dat de klant in rekening kan worden gebracht. 

In het volgende diagram ziet u de volgorde van API-aanroepen voor een inrichtingsscenario.  

![API vraagt om het inrichten van een SaaS-service](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Ingericht

Deze toestand is de stabiele toestand van een ingerichte dienst.

##### <a name="provisioning-for-update"></a>Inrichting voor actualisering 

Deze status betekent dat een update voor een bestaande service in behandeling is. Een dergelijke update kan worden geïnitieerd door de klant, hetzij vanaf de marktplaats of op de SaaS-service (alleen voor direct-to-customer transacties).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Voorzieningen voor update (wanneer deze wordt gestart vanuit de markt)

In het volgende diagram ziet u de volgorde van acties wanneer een update wordt gestart vanuit de markt.

![API-aanroepen wanneer de update wordt gestart vanuit de marketplace](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Inrichting voor update (wanneer deze wordt gestart vanuit de SaaS-service)

In het volgende diagram ziet u de acties wanneer een update wordt gestart vanuit de SaaS-service. (De webhook-oproep wordt vervangen door een update van het abonnement dat door de SaaS-service is gestart.) 

![API-aanroepen wanneer de update wordt gestart vanuit de SaaS-service](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Onderbroken

Deze status geeft aan dat de betaling van een klant niet is ontvangen. Volgens het beleid bieden we de klant een respijtperiode voordat we het abonnement opzeggen. Wanneer een abonnement in deze staat is: 

- Als partner u ervoor kiezen om de toegang van de gebruiker tot de service te degraderen of te blokkeren.
- Het abonnement moet in een herstelbare status worden bewaard die de volledige functionaliteit kan herstellen zonder verlies van gegevens of instellingen. 
- Verwacht een verzoek tot herstel voor dit abonnement te krijgen via de afhandelings-API's of een de-provisioning-aanvraag aan het einde van de respijtperiode. 

#### <a name="unsubscribed"></a>Afgemeld 

Abonnementen bereiken deze status als reactie op een expliciet verzoek van de klant of het niet betalen van contributie. De verwachting van de partner is dat de gegevens van de klant worden bewaard voor herstel op verzoek voor een bepaald aantal dagen en vervolgens verwijderd. 


## <a name="api-reference"></a>API-verwijzing

In deze sectie worden *Subscription API* de API voor SaaS-abonnementen en *operations API document.*  De waarde `api-version` van de parameter voor `2018-08-31`versie 2 API's is .  


### <a name="parameter-and-entity-definitions"></a>Definities van parameters en entiteiten

In de volgende tabel worden de definities weergegeven voor algemene parameters en entiteiten die worden gebruikt door fulfillment-API's.

|     Entiteit/parameter     |     Definitie                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | De GUID-id voor een SaaS-bron.  |
| `name`                   | Een vriendelijke naam die voor deze bron door de klant wordt verstrekt. |
| `publisherId`            | Een unieke tekenreeks-id voor elke uitgever (bijvoorbeeld: 'contoso'). |
| `offerId`                | Een unieke tekenreeks-id voor elke aanbieding (bijvoorbeeld: 'aanbieding1').  |
| `planId`                 | Een unieke tekenreeks-id voor elk plan/SKU (bijvoorbeeld: 'zilver'). |
| `operationId`            | De GUID-id voor een bepaalde bewerking.  |
|  `action`                | De actie die op een `Unsubscribe` `Suspend`resource `Reinstate`wordt `ChangePlan` `ChangeQuantity`uitgevoerd, ofwel , , of , , `Transfer`. |
|   |   |

Wereldwijd unieke[id's (GUID's)](https://en.wikipedia.org/wiki/Universally_unique_identifier)zijn 128-bits (32-hexadecimale) getallen die meestal automatisch worden gegenereerd. 

#### <a name="resolve-a-subscription"></a>Een abonnement oplossen 

Met het endpoint voor het oplossen kan de uitgever een marketplace-token oplossen naar een permanente resource-id. De resource-id is de unieke id voor een SaaS-abonnement. Wanneer een gebruiker wordt doorgestuurd naar de website van een partner, bevat de URL een token in de queryparameters. Van de partner wordt verwacht dat hij dit token gebruikt en een verzoek indient om het op te lossen. Het antwoord bevat de unieke SaaS-abonnements-ID, naam, aanbiedings-ID en plan voor de resource. Dit token is slechts één uur geldig. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Plaatsen<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Queryparameters:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersie        |  De versie van de bewerking die voor dit verzoek moet worden gebruikt.  |

*Kopteksten aanvragen:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen. |
|  x-ms-correlatieid |  Een unieke tekenreekswaarde voor de bewerking op de client. Deze parameter correleert alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.  |
|  autorisatie     |  [Krijg JSON webtoken (JWT) toondertoken](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Bijvoorbeeld: "`Bearer <access_token>`". |
|  x-ms-marketplace-token  |  De parameter tokenquery in de URL wanneer de gebruiker vanuit Azure naar de website `https://contoso.com/signup?token=..`van de SaaS-partner wordt doorgestuurd (bijvoorbeeld: ). *Let op:* De URL decodeert de tokenwaarde van de browser voordat u deze gebruikt.  |

*Antwoordcodes:*

Code: 200<br>
Hiermee wordt het ondoorzichtige token opgelost voor een SaaS-abonnement. Reactieorgaan:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Code: 400<br>
Slecht verzoek. x-ms-marketplace-token ontbreekt, misvormd of verlopen.

Code: 403<br>
Onbevoegde. Het verificatietoken is niet opgegeven of ongeldig of is ongeldig of het verzoek probeert toegang te krijgen tot een acquisitie die niet van de huidige uitgever is.

Code: 404<br>
Niet gevonden.

Code: 500<br>
Interne serverfout.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>Abonnement-API

De abonnements-API ondersteunt de volgende HTTPS-bewerkingen: **Get**, **Post**, **Patch**en **Delete**.


#### <a name="list-subscriptions"></a>Abonnementen aanbieden

Hier vindt u alle SaaS-abonnementen voor een uitgever.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Ophalen<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Queryparameters:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersie  |  De versie van de bewerking die voor dit verzoek moet worden gebruikt.  |

*Kopteksten aanvragen:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen. |
| x-ms-correlatieid |  Een unieke tekenreekswaarde voor de bewerking op de client. Deze parameter correleert alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.  |
| autorisatie      |  [Krijg JSON webtoken (JWT) toondertoken](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Bijvoorbeeld: "`Bearer <access_token>`".  |

*Antwoordcodes:*

Code: 200 <br/>
Hiermee krijgt u de uitgever en de bijbehorende abonnementen voor alle aanbiedingen van de uitgever, op basis van het verificatietoken.

>[!Note]
>[Mock API's](#mock-apis) worden gebruikt wanneer u de aanbieding voor het eerst ontwikkelt, terwijl echte API's moeten worden gebruikt bij het daadwerkelijk publiceren van de aanbieding.  Echte API's en Mock API's verschillen per eerste regel van de code.  In de echte API `subscription` is er de sectie, terwijl deze sectie niet bestaat voor mock API.

Reactie payload voor mock API:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
En voor echte API: <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
Het vervolgtoken is alleen aanwezig als er extra "pagina's" van plannen zijn om op te halen. 

Code: 403 <br>
Onbevoegde. Het verificatietoken is niet opgegeven of ongeldig of is ongeldig of het verzoek probeert toegang te krijgen tot een acquisitie die niet van de huidige uitgever is. 

Code: 500<br>
Interne serverfout.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Abonnement krijgen

Krijgt het opgegeven SaaS-abonnement. Gebruik deze oproep om licentie-informatie en planinformatie op te halen.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Ophalen<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Queryparameters:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Een unieke id van het SaaS-abonnement dat is verkregen na het oplossen van het token via resolve API.   |
|  ApiVersie        |   De versie van de bewerking die voor dit verzoek moet worden gebruikt.   |

*Kopteksten aanvragen:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen. |
|  x-ms-correlatieid |  Een unieke tekenreekswaarde voor de bewerking op de client. Deze parameter correleert alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.  |
|  autorisatie     |  [Krijg JSON webtoken (JWT) toondertoken](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Bijvoorbeeld: "`Bearer <access_token>`".  |

*Antwoordcodes:*

Code: 200<br>
Haalt het SaaS-abonnement uit de id. Reactie payload:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Code: 403<br>
Onbevoegde. Het verificatietoken is niet opgegeven of ongeldig of is ongeldig of het verzoek probeert toegang te krijgen tot een acquisitie die niet van de huidige uitgever is.

Code: 404<br>
Niet gevonden.<br> 

Code: 500<br>
Interne serverfout.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Beschikbare plannen weergeven

Gebruik deze oproep om erachter te komen of er privé- of openbare aanbiedingen zijn voor de huidige uitgever.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Ophalen<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Queryparameters:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersie        |   De versie van de bewerking die voor dit verzoek moet worden gebruikt.  |

*Kopteksten aanvragen:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen. |
|  x-ms-correlatieid  | Een unieke tekenreekswaarde voor de bewerking op de client. Deze parameter correleert alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen. |
|  autorisatie     |  [Krijg JSON webtoken (JWT) toondertoken](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Bijvoorbeeld: "`Bearer <access_token>`". |

*Antwoordcodes:*

Code: 200<br>
Krijgt een lijst met beschikbare plannen voor een klant. Reactieorgaan:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Code: 404<br>
Niet gevonden.<br> 

Code: 403<br>
Onbevoegde. Het verificatietoken is niet opgegeven of ongeldig of is ongeldig of het verzoek probeert toegang te krijgen tot een acquisitie die niet van de huidige uitgever is. <br> 

Code: 500<br>
Interne serverfout.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Een abonnement activeren

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Plaatsen<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Queryparameters:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersie        |  De versie van de bewerking die voor dit verzoek moet worden gebruikt.  |
| subscriptionId     | Een unieke id van het SaaS-abonnement dat is verkregen na het oplossen van het token met behulp van de API Oplossen.  |

*Kopteksten aanvragen:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.  |
|  x-ms-correlatieid  | Een unieke tekenreekswaarde voor de bewerking op de client. Deze tekenreeks correleert alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.  |
|  autorisatie     |  [Krijg JSON webtoken (JWT) toondertoken](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Bijvoorbeeld: "`Bearer <access_token>`". |

*Verzoek payload:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Antwoordcodes:*

Code: 200<br>
Activeert het abonnement.<br>

Code: 400<br>
Slecht verzoek: validatiefouten.

Code: 403<br>
Onbevoegde. Het verificatietoken is niet opgegeven of ongeldig of is ongeldig of het verzoek probeert toegang te krijgen tot een acquisitie die niet van de huidige uitgever is.

Code: 404<br>
Niet gevonden.

Code: 500<br>
Interne serverfout.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Het abonnement op het abonnement wijzigen

Werk het abonnement bij op het abonnement.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Queryparameters:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersie        |  De versie van de bewerking die voor dit verzoek moet worden gebruikt.  |
| subscriptionId     | Een unieke id van het SaaS-abonnement dat is verkregen na het oplossen van het token met behulp van de API Oplossen.  |

*Kopteksten aanvragen:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.  |
|  x-ms-correlatieid  |  Een unieke tekenreekswaarde voor de bewerking op de client. Deze parameter correleert alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.    |
| autorisatie      |  [Krijg JSON webtoken (JWT) toondertoken](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Bijvoorbeeld: "`Bearer <access_token>`".  |

*Verzoek payload:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Kopteksten aanvragen:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operatielocatie | De koppeling naar een resource om de status van de bewerking te krijgen.   |

*Antwoordcodes:*

Code: 202<br>
Het verzoek om van plan te veranderen is geaccepteerd. Van de partner wordt verwacht dat hij de operatielocatie peilt om een succes of fout te bepalen. <br>

Code: 400<br>
Slecht verzoek: validatiefouten.

Code: 403<br>
Onbevoegde. Het verificatietoken is niet opgegeven of ongeldig of is ongeldig of het verzoek probeert toegang te krijgen tot een acquisitie die niet van de huidige uitgever is.

Code: 404<br>
Niet gevonden.

Code: 500<br>
Interne serverfout.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Alleen een plan of hoeveelheid kan in één keer worden gepatcht, niet beide. Bewerkingen op een **Update** abonnement met `allowedCustomerOperations`Update zijn niet in .

#### <a name="change-the-quantity-on-the-subscription"></a>Het aantal op het abonnement wijzigen

Werk de hoeveelheid op het abonnement bij.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Queryparameters:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersie        |  De versie van de bewerking die voor dit verzoek moet worden gebruikt.  |
| subscriptionId     | Een unieke id van het SaaS-abonnement dat is verkregen na het oplossen van het token met behulp van de API Oplossen.  |

*Kopteksten aanvragen:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.  |
|  x-ms-correlatieid  |  Een unieke tekenreekswaarde voor de bewerking op de client. Deze parameter correleert alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.    |
| autorisatie      |  [Krijg JSON webtoken (JWT) toondertoken](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Bijvoorbeeld: "`Bearer <access_token>`".  |

*Verzoek payload:*

```json
Request Body:
{
    "quantity": 5
}
```

*Kopteksten aanvragen:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operatielocatie | Koppeling naar een resource om de status van de bewerking te krijgen.   |

*Antwoordcodes:*

Code: 202<br>
Het verzoek om de hoeveelheid te wijzigen is geaccepteerd. Van de partner wordt verwacht dat hij de operatielocatie peilt om een succes of fout te bepalen. <br>

Code: 400<br>
Slecht verzoek: validatiefouten.


Code: 403<br>
Onbevoegde. Het verificatietoken is niet opgegeven of ongeldig of is ongeldig of het verzoek probeert toegang te krijgen tot een acquisitie die niet van de huidige uitgever is.

Code: 404<br>
Niet gevonden.

Code: 500<br>
Interne serverfout.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Alleen een plan of hoeveelheid kan in één keer worden gepatcht, niet beide. Bewerkingen op een **Update** abonnement met `allowedCustomerOperations`Update zijn niet in .

#### <a name="delete-a-subscription"></a>Een abonnement verwijderen

Meld u af en verwijder het opgegeven abonnement.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Verwijderen<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Queryparameters:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersie        |  De versie van de bewerking die voor dit verzoek moet worden gebruikt.  |
| subscriptionId     | Een unieke id van het SaaS-abonnement dat is verkregen na het oplossen van het token met behulp van de API Oplossen.  |

*Kopteksten aanvragen:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.   |
|  x-ms-correlatieid  |  Een unieke tekenreekswaarde voor de bewerking op de client. Deze parameter correleert alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.   |
|  autorisatie     |  [Krijg JSON webtoken (JWT) toondertoken](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Bijvoorbeeld: "`Bearer <access_token>`".  |

*Antwoordcodes:*

Code: 202<br>
De partner startte een oproep om een SaaS-abonnement af te melden.<br>

Code: 400<br>
Verwijderen bij een **Delete** abonnement `allowedCustomerOperations`met Verwijderen niet in .

Code: 403<br>
Onbevoegde. Het verificatietoken is niet opgegeven of ongeldig of is ongeldig of het verzoek probeert toegang te krijgen tot een acquisitie die niet van de huidige uitgever is.

Code: 404<br>
Niet gevonden.

Code: 500<br>
Interne serverfout.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>Operations API

De operations API ondersteunt de volgende Patch- en Get-bewerkingen.

#### <a name="list-outstanding-operations"></a>Lijst uitstaande bewerkingen 

Hiermee worden de uitstaande bewerkingen voor de huidige uitgever weergegeven. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Ophalen<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Queryparameters:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersie                |   De versie van de bewerking die voor dit verzoek moet worden gebruikt.                |
| subscriptionId     | Een unieke id van het SaaS-abonnement dat is verkregen na het oplossen van het token met behulp van de API Oplossen.  |

*Kopteksten aanvragen:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.  |
|  x-ms-correlatieid |  Een unieke tekenreekswaarde voor de bewerking op de client. Deze parameter correleert alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.  |
|  autorisatie     |  [Krijg JSON webtoken (JWT) toondertoken](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Bijvoorbeeld: "`Bearer <access_token>`".  |

*Antwoordcodes:*

Code: 200<br> Hier krijgt u de lijst met in behandeling zijnde bewerkingen op een abonnement. Reactie payload:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


Code: 400<br>
Slecht verzoek: validatiefouten.

Code: 403<br>
Onbevoegde. Het verificatietoken is niet opgegeven of ongeldig of is ongeldig of het verzoek probeert toegang te krijgen tot een acquisitie die niet van de huidige uitgever is.

Code: 404<br>
Niet gevonden.

Code: 500<br>
Interne serverfout.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>De bewerkingsstatus ophalen

Hiermee kan de uitgever de status van de opgegeven `Subscribe` `Unsubscribe`geactiveerde async-bewerking bijhouden (zoals , , `ChangePlan`of `ChangeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Ophalen<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Queryparameters:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersie        |  De versie van de bewerking die voor dit verzoek moet worden gebruikt.  |

*Kopteksten aanvragen:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.  |
|  x-ms-correlatieid |  Een unieke tekenreekswaarde voor de bewerking op de client. Deze parameter correleert alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen.  |
|  autorisatie     |  [Krijg JSON webtoken (JWT) toondertoken](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Bijvoorbeeld: "`Bearer <access_token>`".  |

*Antwoordcodes:*<br>

Code: 200<br> Hier wordt de opgegeven saas-bewerking uitgevoerd. Reactie payload:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Code: 400<br>
Slecht verzoek: validatiefouten.

Code: 403<br>
Onbevoegde. Het verificatietoken is niet opgegeven of ongeldig of is ongeldig of het verzoek probeert toegang te krijgen tot een acquisitie die niet van de huidige uitgever is.
 
Code: 404<br>
Niet gevonden.

Code: 500<br> Interne serverfout.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>De status van een bewerking bijwerken

Werk de status van een bewerking bij om succes of fout met de opgegeven waarden aan te geven.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Queryparameters:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersie       |  De versie van de bewerking die voor dit verzoek moet worden gebruikt.  |
| subscriptionId     | Een unieke id van het SaaS-abonnement dat is verkregen na het oplossen van het token met behulp van de API Oplossen.  |
|  operationId       | De operatie die wordt voltooid. |

*Kopteksten aanvragen:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Een unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen. |
|  x-ms-correlatieid |  Een unieke tekenreekswaarde voor de bewerking op de client. Deze parameter correleert alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen. |
|  autorisatie     |  [Krijg JSON webtoken (JWT) toondertoken](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Bijvoorbeeld: "`Bearer <access_token>`".  |

*Verzoek payload:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Antwoordcodes:*

Code: 200<br> Een oproep om te informeren over de voltooiing van een operatie aan de partnerkant. Deze reactie kan bijvoorbeeld de verandering van stoelen of plannen signaleren.

Code: 400<br>
Slecht verzoek: validatiefouten.

Code: 403<br>
Onbevoegde. Het verificatietoken is niet opgegeven of ongeldig of is ongeldig of het verzoek probeert toegang te krijgen tot een acquisitie die niet van de huidige uitgever is.

Code: 404<br>
Niet gevonden.

Code: 409<br>
Conflict. Een nieuwere transactie is bijvoorbeeld al uitgevoerd.

Code: 500<br> Interne serverfout.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Het implementeren van een webhook op de SaaS-service

De uitgever moet een webhook implementeren in deze SaaS-service om gebruikers proactief op de hoogte te stellen van wijzigingen in zijn service. De SaaS-service zal naar verwachting de operations API aanroepen om te valideren en te autoriseren voordat u een actie onderneemt op de webhook-melding.


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Wanneer de actie een van de volgende acties kan zijn: 
- `Unsubscribe`(wanneer de bron is verwijderd)
- `ChangePlan`(wanneer de bewerking van het wijzigingsplan is voltooid)
- `ChangeQuantity`(wanneer de wijzigingshoeveelheidsbewerking is voltooid)
- `Suspend`(wanneer de resource is opgeschort)
- `Reinstate`(wanneer de resource is hersteld na opschorting)

Wanneer de status een van de volgende statuskan zijn: 
- **Niet gestart** <br>
 - **InProgress** <br>
- **Geslaagd** <br>
- **Mislukt** <br>
- **Conflict** <br>

In een webhook-melding worden bruikbare statussen **geslaagd** en **mislukt.** De levenscyclus van een bewerking is van **NotStarted** naar een terminalstatus zoals **Geslaagd**, **Mislukt**of **Conflict**. Als u **NotStarted** of **InProgress**ontvangt, blijft u de status aanvragen via GET API totdat de bewerking een terminalstatus bereikt voordat u actie onderneemt. 

## <a name="mock-apis"></a>Mock API's

U onze mock API's gebruiken om u op weg te helpen met ontwikkeling, met name prototyping, evenals het testen van projecten. 

Host eindpunt: `https://marketplaceapi.microsoft.com/api` (geen verificatie vereist)<br/>
API-versie:`2018-09-15`<br/>
Voorbeeld URI:`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

De API-eindpuntpaden zijn hetzelfde voor zowel mock- als echte API's, maar de API-versies zijn verschillend. De versie `2018-09-15` is voor `2018-08-31` de mock versie en voor de productieversie. 

Elk van de API-aanroepen in dit artikel kan worden uitgevoerd naar het eindpunt van de mock host. In het algemeen, verwachten om mock gegevens terug te krijgen als een reactie. Oproepen naar de update-abonnementsmethoden op de mock-API keren altijd 500 terug. 

## <a name="next-steps"></a>Volgende stappen

Ontwikkelaars kunnen ook programmatisch workloads, aanbiedingen en uitgeversprofielen ophalen en manipuleren met behulp van de [API's van Cloud Partner Portal REST.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)
