---
title: Een aanbieding publiceren | Azure Marketplace
description: API voor het publiceren van de opgegeven aanbieding.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b7ad8086c417cf1f14d9116fa4abcb0a88030922
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819644"
---
<a name="publish-an-offer"></a>Een aanbieding publiceren
================

Start het publicatie proces voor de opgegeven aanbieding. Deze aanroep is een langlopende bewerking.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-para meters
--------------

|  **Naam**      |    **Beschrijving**                               |  **Gegevenstype** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Uitgevers-id, bijvoorbeeld `contoso`      |   Tekenreeks       |
|  OfferId       | Aanbiedings-id                                 |   Tekenreeks       |
|  API-versie   | Nieuwste versie van de API                        |   Date         |
|  |  |


<a name="header"></a>Header
------

|  **Naam**        |    **Waarde**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autorisatie   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Voor beeld van tekst
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

### <a name="request-body-properties"></a>Eigenschappen van aanvraag hoofdtekst

|  **Naam**               |   **Beschrijving**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  melding-e-mail berichten    | Een door komma's gescheiden lijst met e-mail adressen die moeten worden geïnformeerd over de voortgang van de publicatie bewerking. |
|  |  |


### <a name="response"></a>Antwoord

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Antwoordkoptekst

|  **Naam**             |    **Waarde**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Bewerkings locatie    | URL waarop een query kan worden uitgevoerd om de huidige status van de bewerking te bepalen.    |
|  |  |


### <a name="response-status-codes"></a>Antwoord status codes

| **Gecodeerd** |  **Beschrijving**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`-de aanvraag is geaccepteerd. Het antwoord bevat een locatie die kan worden gebruikt voor het bijhouden van de bewerking die wordt gestart. |
| 400   | `Bad/Malformed request`: de hoofd tekst van het fout bericht bevat mogelijk meer informatie.                                                               |
| 422   | `Un-processable entity`-geeft aan dat de validatie van de entiteit die moet worden gepubliceerd, is mislukt.                                                        |
| 404   | `Not found`-de entiteit die is opgegeven door de client, bestaat niet.                                                                              |
|  |  |
