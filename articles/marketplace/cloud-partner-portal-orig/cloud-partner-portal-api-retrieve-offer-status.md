---
title: Aanbiedings status ophalen | Azure Marketplace
description: API haalt de huidige status van de aanbieding op.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5ce546d79497f462f6c262de738036d7e3a30226
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819669"
---
<a name="retrieve-offer-status"></a>Aanbiedingsstatus ophalen 
=====================

Hiermee wordt de huidige status van de aanbieding opgehaald.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-para meters
--------------

|  **Naam**       |   **Beschrijving**                            |  **Gegevenstype** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Uitgevers-id, bijvoorbeeld `Contoso`  |     Tekenreeks     |
|  OfferId        | GUID die de aanbieding uniek identificeert      |     Tekenreeks     |
|  API-versie    | Nieuwste versie van API                        |     Date       |
|  |  |


<a name="header"></a>Header
------

|  Naam           |  Waarde               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Autorisatie  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Voor beeld van tekst
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


### <a name="response-body-properties"></a>Eigenschappen van antwoord tekst

|  **Naam**             |    **Beschrijving**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | De status van de aanbieding. Voor een lijst met mogelijke waarden, zie de status van de [aanbieding](#offer-status) hieronder. |
|  berichten             | Matrix van berichten die aan de aanbieding zijn gekoppeld                                                    |
|  stappen                | Matrix van de stappen die de aanbieding doorloopt tijdens een aanbiedings publicatie                      |
|  estimatedTimeFrame   | Schatting van de tijd die nodig is om deze stap te volt ooien, met een beschrijvende notatie                       |
|  id                   | Id van de stap                                                                         |
|  Stapnaam             | De naam van de stap                                                                               |
|  description          | Beschrijving van de stap                                                                        |
|  status               | De status van de stap. Zie [stap status](#step-status) hieronder voor een lijst met mogelijke waarden.    |
|  berichten             | Matrix van berichten met betrekking tot de stap                                                          |
|  processPercentage    | Voltooiings percentage van de stap                                                              |
|  previewLinks         | *Momenteel niet geïmplementeerd*                                                                    |
|  liveLinks            | *Momenteel niet geïmplementeerd*                                                                    |
|  notificationEmails   | Een door komma's gescheiden lijst met e-mail adressen die moeten worden geïnformeerd over de voortgang van de bewerking        |
|  |  |


### <a name="response-status-codes"></a>Antwoord status codes

| **Gecodeerd** |   **Beschrijving**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK`-de aanvraag is verwerkt en de huidige status van de aanbieding is geretourneerd. |
|  400     | `Bad/Malformed request`: de hoofd tekst van het fout bericht bevat mogelijk meer informatie.                 |
|  404     | `Not found`-de opgegeven entiteit bestaat niet.                                                |
|  |  |


### <a name="offer-status"></a>Status van aanbieding

|  **Naam**                    |    **Beschrijving**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | De aanbieding is nooit gepubliceerd.                          |
|  NotStarted                  | De aanbieding is nieuw en niet gestart.                            |
|  WaitingForPublisherReview   | Aanbieding wacht op goed keuring van de uitgever.                 |
|  In uitvoering                     | Verzen ding van aanbieding wordt verwerkt.                     |
|  Geslaagd                   | Het verzenden van aanbiedingen is voltooid.               |
|  Geannuleerd                    | Het verzenden van het aanbod is geannuleerd.                           |
|  Mislukt                      | Kan de aanbieding niet verzenden.                                 |
|  |  |


### <a name="step-status"></a>Stap status

|  **Naam**                    |    **Beschrijving**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | De stap is niet gestart.                        |
|  InProgress                  | Stap wordt uitgevoerd.                             |
|  WaitingForPublisherReview   | Stap wacht op goed keuring van uitgever.      |
|  WaitingForApproval          | De stap wacht op goed keuring van het proces.        |
|  #                     | De stap is geblokkeerd.                             |
|  Afgekeurd                    | De stap is geweigerd.                            |
|  Voltooien                    | Stap is voltooid.                            |
|  Geannuleerd                    | De stap is geannuleerd.                           |
|  |  |
