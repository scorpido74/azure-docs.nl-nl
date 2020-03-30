---
title: Aanbiedingsstatus ophalen | Azure Marketplace
description: API haalt de huidige status van de aanbieding op.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2f5211716145d6c05bbfb0132c4a6ba2f9cceabe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280504"
---
<a name="retrieve-offer-status"></a>Aanbiedingsstatus ophalen 
=====================

Hiermee haalt u de huidige status van de aanbieding op.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parameters
--------------

|  **Naam**       |   **Beschrijving**                            |  **Gegevenstype** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  uitgeverId    | Publisher-id, bijvoorbeeld`Contoso`  |     Tekenreeks     |
|  aanbiedingId        | GUID die het aanbod op unieke wijze identificeert      |     Tekenreeks     |
|  api-versie    | Nieuwste versie van API                        |     Date       |
|  |  |


<a name="header"></a>Header
------

|  Name           |  Waarde               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Autorisatie  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Voorbeeld van het lichaam
------------

### <a name="response"></a>Antwoord

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
      "notificationEmails": "jdoe@contoso.com"
  } 
```


### <a name="response-body-properties"></a>Eigenschappen van antwoordlichaam

|  **Naam**             |    **Beschrijving**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | De status van het aanbod. Zie [Aanbiedingsstatus](#offer-status) hieronder voor de lijst met mogelijke waarden. |
|  berichten             | Reeks berichten die aan de aanbieding zijn gekoppeld                                                    |
|  stappen                | Array van de stappen die de aanbieding doormaakt tijdens het publiceren van een aanbieding                      |
|  geschatTijdsbestek   | Schatting van de tijd die nodig is om deze stap te voltooien, in vriendelijk formaat                       |
|  id                   | Identificatie van de stap                                                                         |
|  stepName             | Naam van de stap                                                                               |
|  description          | Beschrijving van de stap                                                                        |
|  status               | Status van de stap. Zie [De status Stap](#step-status) hieronder voor de lijst met mogelijke waarden.    |
|  berichten             | Reeks berichten met betrekking tot de stap                                                          |
|  procesPercentage    | Percentage voltooiing van de stap                                                              |
|  voorbeeldLinks         | *Momenteel niet geïmplementeerd*                                                                    |
|  liveLinks            | *Momenteel niet geïmplementeerd*                                                                    |
|  meldingE-mails   | Door komma's gescheiden lijst met e-mailadressen die op de hoogte moeten worden gebracht van de voortgang van de bewerking        |
|  |  |


### <a name="response-status-codes"></a>Statuscodes voor antwoord

| **Code** |   **Beschrijving**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK`- De aanvraag is verwerkt en de huidige status van de aanbieding is geretourneerd. |
|  400     | `Bad/Malformed request`- De foutreactieinstantie kan meer informatie bevatten.                 |
|  404     | `Not found`- De opgegeven entiteit bestaat niet.                                                |
|  |  |


### <a name="offer-status"></a>Aanbiedingsstatus

|  **Naam**                    |    **Beschrijving**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  Nooit gepubliceerd              | Aanbieding is nooit gepubliceerd.                          |
|  Niet gestart                  | Aanbieding is nieuw en niet gestart.                            |
|  WaitingForPublisherReview   | Aanbieding wacht op goedkeuring van de uitgever.                 |
|  In uitvoering                     | Indiening van de aanbieding wordt verwerkt.                     |
|  Geslaagd                   | Indiening van de aanbieding heeft de verwerking voltooid.               |
|  Geannuleerd                    | Indiening van de aanbieding is geannuleerd.                           |
|  Mislukt                      | Indiening van aanbiedingen is mislukt.                                 |
|  |  |


### <a name="step-status"></a>Stapstatus

|  **Naam**                    |    **Beschrijving**                           |
|  -------------------------   |  ------------------------------------------  |
|  Niet gestart                  | De stap is nog niet begonnen.                        |
|  InProgress                  | De stap loopt.                             |
|  WaitingForPublisherReview   | Stap wacht op goedkeuring van de uitgever.      |
|  Wachtenopgoedkeuring          | Stap wacht op procesgoedkeuring.        |
|  Geblokkeerd                     | Stap is geblokkeerd.                             |
|  Geweigerd                    | Stap wordt afgewezen.                            |
|  Voltooien                    | De stap is voltooid.                            |
|  Geannuleerd                    | De stap is geannuleerd.                           |
|  |  |
