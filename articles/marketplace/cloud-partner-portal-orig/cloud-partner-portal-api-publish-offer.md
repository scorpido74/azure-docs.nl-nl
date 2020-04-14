---
title: Een voorstel publiceren | Azure Marketplace
description: API om de opgegeven aanbieding te publiceren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255937"
---
# <a name="publish-an-offer"></a>Een aanbieding publiceren

> [!NOTE]
> De API's van cloudpartnerportalen zijn geïntegreerd met het Partner Center en blijven werken nadat uw aanbiedingen zijn gemigreerd naar partnercentrum. De integratie brengt kleine veranderingen met zich mee. Bekijk de wijzigingen in [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) om ervoor te zorgen dat uw code blijft werken na de migratie naar partnercentrum.

Hiermee wordt het publicatieproces voor de opgegeven aanbieding gestart. Deze oproep is een langlopende bewerking.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-parameters
--------------

|  **Naam**      |    **Beschrijving**                               |  **Gegevenstype** |
|  ------------- |  ------------------------------------            |   -----------  |
|  uitgeverId   | Publisher-id, bijvoorbeeld`contoso`      |   Tekenreeks       |
|  aanbiedingId       | Aanbiedings-id                                 |   Tekenreeks       |
|  api-versie   | Nieuwste versie van de API                        |   Date         |
|  |  |

## <a name="header"></a>Header
------

|  **Naam**        |    **Waarde**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autorisatie   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Voorbeeld van het lichaam
------------

### <a name="request"></a>Aanvraag

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Eigenschappen van hoofdtekst aanvragen

|  **Naam**               |   **Beschrijving**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  melding-e-mails    | Door komma's gescheiden lijst met e-mailadressen die op de hoogte moeten worden gesteld van de voortgang van de publicatiebewerking. |
|  |  |


### <a name="response"></a>Antwoord

#### <a name="migrated-offers"></a>Gemigreerde aanbiedingen

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Niet-gemigreerde aanbiedingen

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Reactieheader

|  **Naam**             |    **Waarde**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Locatie    | Het relatieve pad om de status van deze bewerking op te halen     |
|  |  |


### <a name="response-status-codes"></a>Statuscodes voor antwoord

| **Code** |  **Beschrijving**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`- Het verzoek is met succes ingewilligd. Het antwoord bevat een locatie die kan worden gebruikt om de bewerking te volgen die is gestart. |
| 400   | `Bad/Malformed request`- De instantie voor foutrespons kan meer informatie verstrekken.                                                               |
| 422   | `Un-processable entity`- Geeft aan dat de entiteit die moet worden gepubliceerd, de validatie heeft mislukt.                                                        |
| 404   | `Not found`- De entiteit die door de client is opgegeven, bestaat niet.                                                                              |
|  |  |
