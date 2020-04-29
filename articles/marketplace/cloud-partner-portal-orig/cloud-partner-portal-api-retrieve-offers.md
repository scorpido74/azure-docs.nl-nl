---
title: API voor aanbiedingen ophalen | Azure Marketplace
description: API haalt een overzicht van de aanbiedingen onder een Publisher-naam ruimte op.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 0413bc71c113bf1eef9f761dbeed4c0d0afe735c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255954"
---
<a name="retrieve-offers"></a>Aanbiedingen ophalen
===============

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd met partner centrum en blijven werken nadat uw aanbiedingen zijn gemigreerd naar het partner centrum. De integratie introduceert kleine wijzigingen. Bekijk de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) om ervoor te zorgen dat uw code blijft werken na de migratie naar het partner centrum.

Hiermee wordt een overzicht van aanbiedingen onder een Publisher-naam ruimte opgehaald.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-para meters
--------------

| **Naam**         |  **Beschrijving**                         |  **Gegevens type** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Uitgevers-id, bijvoorbeeld`contoso` |   Tekenreeks    |
|  api-versie     | Nieuwste versie van API                    |    Date        |
|  |  |


<a name="header"></a>Header
------

|  **Naam**        |         **Waarde**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Autorisatie   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Voor beeld van tekst
------------

### <a name="response"></a>Antwoord

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Eigenschappen van antwoord tekst

|  **Naam**       |       **Beschrijving**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Hiermee wordt het type aanbieding aangegeven                                                                                           |
|  publisherId    | Id waarmee de uitgever uniek wordt geïdentificeerd                                                                      |
|  status         | De status van de aanbieding. Voor een lijst met mogelijke waarden, zie de status van de [aanbieding](#offer-status) hieronder.                         |
|  id             | GUID die de aanbieding uniek identificeert in de naam ruimte van de uitgever.                                                    |
|  versie        | Huidige versie van de aanbieding. De versie-eigenschap kan niet worden gewijzigd door de client. Deze wordt na elke publicatie verhoogd. |
|  definitie     | Bevat een overzicht van de werkelijke definitie van de werk belasting. Gebruik de [specifieke aanbiedings](./cloud-partner-portal-api-retrieve-specific-offer.md) -API ophalen voor een gedetailleerde definitie. |
|  changedTime    | UTC-tijd waarop de aanbieding voor het laatst is gewijzigd                                                                              |
|  |  |


### <a name="response-status-codes"></a>Antwoord status codes

| **Gecodeerd**  |  **Beschrijving**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`-De aanvraag is verwerkt en alle aanbiedingen onder de uitgever zijn geretourneerd naar de client.  |
|  400      | `Bad/Malformed request`-De hoofd tekst van het fout bericht bevat mogelijk meer informatie.                                    |
|  403      | `Forbidden`-De client heeft geen toegang tot de opgegeven naam ruimte.                                          |
|  404      | `Not found`-De opgegeven entiteit bestaat niet.                                                                 |
|  |  |


### <a name="offer-status"></a>Status van aanbieding

|  **Naam**                    | **Beschrijving**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | De aanbieding is nooit gepubliceerd.                  |
|  NotStarted                  | De aanbieding is nieuw, maar is niet gestart.                 |
|  WaitingForPublisherReview   | Aanbieding wacht op goed keuring van de uitgever.         |
|  In uitvoering                     | Verzen ding van aanbieding wordt verwerkt.             |
|  Geslaagd                   | Het verzenden van aanbiedingen is voltooid.       |
|  Geannuleerd                    | Het verzenden van het aanbod is geannuleerd.                   |
|  Mislukt                      | Kan de aanbieding niet verzenden.                         |
|  |  |
