---
title: Live gaan | Azure Marketplace
description: De Go Live API initieert het Live-aanbiedings proces van de aanbieding.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 30500e9dfae9411563fc727290d0569998ba3550
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819688"
---
<a name="go-live"></a>Live gaan
=======

Deze API start het proces voor het pushen van een app naar productie. Deze bewerking wordt meestal langdurig uitgevoerd. Deze aanroep gebruikt de lijst met e-mail meldingen van de bewerking [Publish](./cloud-partner-portal-api-publish-offer.md) API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI-para meters
--------------

|  **Naam**      |   **Beschrijving**                                                           | **Gegevenstype** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | De uitgevers-id van de aanbieding die u wilt ophalen, bijvoorbeeld `contoso`       |  Tekenreeks       |
| OfferId        | Id van de aanbieding die moet worden opgehaald                                   |  Tekenreeks       |
| API-versie    | Nieuwste versie van de API                                                   |  Date         |
|  |  |  |


<a name="header"></a>Header
------

|  **Naam**       |     **Waarde**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autorisatie   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Voor beeld van tekst
------------

### <a name="response"></a>Antwoord

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Antwoordkoptekst

|  **Naam**             |      **Waarde**                                                            |
|  --------             |      ----------                                                           |
| Bewerkings locatie    |  URL die moet worden opgevraagd om de huidige status van de bewerking te bepalen            |
|  |  |


### <a name="response-status-codes"></a>Antwoord status codes

| **Gecodeerd** |  **Beschrijving**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`-de aanvraag is geaccepteerd. Het antwoord bevat een locatie voor het bijhouden van de bewerkings status. |
|  400     | `Bad/Malformed request`: er zijn aanvullende fout gegevens gevonden in de hoofd tekst van het antwoord. |
|  404     |  `Not found`-de opgegeven entiteit bestaat niet.                                       |
|  |  |
