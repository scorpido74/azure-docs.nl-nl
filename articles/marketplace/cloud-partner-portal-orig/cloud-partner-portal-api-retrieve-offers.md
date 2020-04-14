---
title: API ophalen aanbiedingen | Azure Marketplace
description: API haalt een samengevatte lijst met aanbiedingen op onder een naamruimte van een uitgever.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 0413bc71c113bf1eef9f761dbeed4c0d0afe735c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255954"
---
<a name="retrieve-offers"></a>Aanbiedingen ophalen
===============

> [!NOTE]
> De API's van cloudpartnerportalen zijn geïntegreerd met het Partner Center en blijven werken nadat uw aanbiedingen zijn gemigreerd naar partnercentrum. De integratie brengt kleine veranderingen met zich mee. Bekijk de wijzigingen in [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) om ervoor te zorgen dat uw code blijft werken na de migratie naar partnercentrum.

Hiermee haalt u een samengevatte lijst met aanbiedingen op onder een naamruimte van een uitgever.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parameters
--------------

| **Naam**         |  **Beschrijving**                         |  **Gegevenstype** |
| -------------    |  ------------------------------------    |  -----------   |
|  uitgeverId     | Publisher-id, bijvoorbeeld`contoso` |   Tekenreeks    |
|  api-versie     | Nieuwste versie van API                    |    Date        |
|  |  |


<a name="header"></a>Header
------

|  **Naam**        |         **Waarde**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Autorisatie   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Voorbeeld van het lichaam
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

### <a name="response-body-properties"></a>Eigenschappen van antwoordlichaam

|  **Naam**       |       **Beschrijving**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Identificeert het type aanbieding                                                                                           |
|  uitgeverId    | Id die de uitgever op unieke wijze identificeert                                                                      |
|  status         | Status van het aanbod. Zie [Aanbiedingsstatus](#offer-status) hieronder voor de lijst met mogelijke waarden.                         |
|  id             | GUID die het aanbod op unieke wijze identificeert in de naamruimte van de uitgever.                                                    |
|  versie        | Huidige versie van het aanbod. De eigenschap versie kan niet door de client worden gewijzigd. Het wordt verhoogd na elke publicatie. |
|  definitie     | Bevat een samengevatte weergave van de werkelijke definitie van de werkbelasting. Als u een gedetailleerde definitie wilt krijgen, gebruikt u de API [voor specifieke aanbieding ophalen.](./cloud-partner-portal-api-retrieve-specific-offer.md) |
|  changedTime    | UTC-tijd wanneer de aanbieding voor het laatst is gewijzigd                                                                              |
|  |  |


### <a name="response-status-codes"></a>Statuscodes voor antwoord

| **Code**  |  **Beschrijving**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`- Het verzoek is verwerkt en alle aanbiedingen onder de uitgever zijn teruggegeven aan de klant.  |
|  400      | `Bad/Malformed request`- De foutreactieinstantie kan meer informatie bevatten.                                    |
|  403      | `Forbidden`- De client heeft geen toegang tot de opgegeven naamruimte.                                          |
|  404      | `Not found`- De opgegeven entiteit bestaat niet.                                                                 |
|  |  |


### <a name="offer-status"></a>Aanbiedingsstatus

|  **Naam**                    | **Beschrijving**                                  |
|  ------------------------    | -----------------------------------------------  |
|  Nooit gepubliceerd              | Aanbieding is nooit gepubliceerd.                  |
|  Niet gestart                  | Aanbieding is nieuw, maar is niet gestart.                 |
|  WaitingForPublisherReview   | Aanbieding wacht op goedkeuring van de uitgever.         |
|  In uitvoering                     | Indiening van de aanbieding wordt verwerkt.             |
|  Geslaagd                   | Indiening van de aanbieding heeft de verwerking voltooid.       |
|  Geannuleerd                    | Indiening van de aanbieding is geannuleerd.                   |
|  Mislukt                      | Indiening van aanbiedingen is mislukt.                         |
|  |  |
