---
title: Ga live | Azure Marketplace
description: De Go Live API initieert het proces van live vermelding van aanbiedingen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bf7bebf6e72e373811879a311d70255c29988ed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288577"
---
<a name="go-live"></a>Ga live
=======

Deze API start het proces voor het pushen van een app naar productie. Deze bewerking is meestal langlopend. Deze aanroep maakt gebruik van de e-maillijst van meldingen van de [bewerking Publish](./cloud-partner-portal-api-publish-offer.md) API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI-parameters
--------------

|  **Naam**      |   **Beschrijving**                                                           | **Gegevenstype** |
|  --------      |   ---------------                                                           | ------------- |
| uitgeverId    | Publisher-id voor de aanbieding die moet worden opgehaald, bijvoorbeeld`contoso`       |  Tekenreeks       |
| aanbiedingId        | Aanbiedingsid van de aanbieding om op te halen                                   |  Tekenreeks       |
| api-versie    | Nieuwste versie van de API                                                   |  Date         |
|  |  |  |


<a name="header"></a>Header
------

|  **Naam**       |     **Waarde**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autorisatie   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Voorbeeld van het lichaam
------------

### <a name="response"></a>Antwoord

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Reactieheader

|  **Naam**             |      **Waarde**                                                            |
|  --------             |      ----------                                                           |
| Operatielocatie    |  URL naar query om de huidige status van de bewerking te bepalen            |
|  |  |


### <a name="response-status-codes"></a>Statuscodes voor antwoord

| **Code** |  **Beschrijving**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`- Het verzoek is met succes ingewilligd. Het antwoord bevat een locatie om de bedrijfsstatus bij te houden. |
|  400     | `Bad/Malformed request`- Aanvullende foutinformatie wordt gevonden in de reactiebody. |
|  404     |  `Not found`- De opgegeven entiteit bestaat niet.                                       |
|  |  |
