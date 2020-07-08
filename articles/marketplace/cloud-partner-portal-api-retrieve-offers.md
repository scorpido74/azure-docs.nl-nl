---
title: API voor aanbiedingen ophalen-Azure Marketplace
description: API voor het ophalen van een overzicht van aanbiedingen onder een Publisher-naam ruimte.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ee8d0c773a5ec4d362eae66e289838b9646247e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85516205"
---
<a name="retrieve-offers"></a>Aanbiedingen ophalen
===============

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd met partner centrum en blijven werken nadat uw aanbiedingen zijn gemigreerd naar het partner centrum. De integratie introduceert kleine wijzigingen. Bekijk de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](./cloud-partner-portal-api-overview.md) om ervoor te zorgen dat uw code blijft werken na de migratie naar het partner centrum.

Hiermee wordt een overzicht van aanbiedingen onder een Publisher-naam ruimte opgehaald.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-para meters
--------------

| **Naam**         |  **Beschrijving**                         |  **Gegevenstype** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Uitgevers-id, bijvoorbeeld`contoso` |   Tekenreeks    |
|  api-versie     | Nieuwste versie van API                    |    Datum        |
|  |  |


<a name="header"></a>Koptekst
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

| **Code**  |  **Beschrijving**                                                                                                   |
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
