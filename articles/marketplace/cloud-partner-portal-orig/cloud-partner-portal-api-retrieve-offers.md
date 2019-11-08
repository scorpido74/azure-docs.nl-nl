---
title: API voor aanbiedingen ophalen | Azure Marketplace
description: API haalt een overzicht van de aanbiedingen onder een Publisher-naam ruimte op.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5c94c03a63936be2b086085a1e52064dedf214b0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819631"
---
<a name="retrieve-offers"></a>Aanbiedingen ophalen
===============

Hiermee wordt een overzicht van aanbiedingen onder een Publisher-naam ruimte opgehaald.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>URI-para meters
--------------

| **Naam**         |  **Beschrijving**                         |  **Gegevenstype** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Uitgevers-id, bijvoorbeeld `contoso` |   Tekenreeks    |
|  API-versie     | Nieuwste versie van API                    |    Date        |
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
|  Inhoudsindexdefinitie     | Bevat een overzicht van de werkelijke definitie van de werk belasting. Gebruik de [specifieke aanbiedings](./cloud-partner-portal-api-retrieve-specific-offer.md) -API ophalen voor een gedetailleerde definitie. |
|  changedTime    | UTC-tijd waarop de aanbieding voor het laatst is gewijzigd                                                                              |
|  |  |


### <a name="response-status-codes"></a>Antwoord status codes

| **Gecodeerd**  |  **Beschrijving**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`-de aanvraag is verwerkt en alle aanbiedingen onder de uitgever zijn geretourneerd naar de client.  |
|  400      | `Bad/Malformed request`: de hoofd tekst van het fout bericht bevat mogelijk meer informatie.                                    |
|  403      | `Forbidden`-de client heeft geen toegang tot de opgegeven naam ruimte.                                          |
|  404      | `Not found`-de opgegeven entiteit bestaat niet.                                                                 |
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
