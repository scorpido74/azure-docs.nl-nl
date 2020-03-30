---
title: Een voorstel publiceren | Azure Marketplace
description: API om de opgegeven aanbieding te publiceren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 4163bf5727c327d559b81db42f99684aa0cc8d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280521"
---
<a name="publish-an-offer"></a>Een aanbieding publiceren
================

Hiermee wordt het publicatieproces voor de opgegeven aanbieding gestart. Deze oproep is een langlopende bewerking.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parameters
--------------

|  **Naam**      |    **Beschrijving**                               |  **Gegevenstype** |
|  ------------- |  ------------------------------------            |   -----------  |
|  uitgeverId   | Publisher-id, bijvoorbeeld`contoso`      |   Tekenreeks       |
|  aanbiedingId       | Aanbiedings-id                                 |   Tekenreeks       |
|  api-versie   | Nieuwste versie van de API                        |   Date         |
|  |  |


<a name="header"></a>Header
------

|  **Naam**        |    **Waarde**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autorisatie   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Voorbeeld van het lichaam
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

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Reactieheader

|  **Naam**             |    **Waarde**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operatielocatie    | URL die kan worden opgevraagd om de huidige status van de bewerking te bepalen.    |
|  |  |


### <a name="response-status-codes"></a>Statuscodes voor antwoord

| **Code** |  **Beschrijving**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`- Het verzoek is met succes ingewilligd. Het antwoord bevat een locatie die kan worden gebruikt om de bewerking te volgen die is gestart. |
| 400   | `Bad/Malformed request`- De instantie voor foutrespons kan meer informatie verstrekken.                                                               |
| 422   | `Un-processable entity`- Geeft aan dat de entiteit die moet worden gepubliceerd, de validatie heeft mislukt.                                                        |
| 404   | `Not found`- De entiteit die door de client is opgegeven, bestaat niet.                                                                              |
|  |  |
