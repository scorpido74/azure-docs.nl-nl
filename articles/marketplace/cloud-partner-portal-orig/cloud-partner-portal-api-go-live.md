---
title: Ga live | Azure Marketplace
description: De Go Live API initieert het proces van live vermelding van aanbiedingen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ef22f7720a4af2239c55d1a01f9d3f11c878d66e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256311"
---
# <a name="go-live"></a>Ga live

> [!NOTE]
> De API's van cloudpartnerportalen zijn geïntegreerd met het Partner Center en blijven werken nadat uw aanbiedingen zijn gemigreerd naar partnercentrum. De integratie brengt kleine veranderingen met zich mee. Bekijk de wijzigingen in [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) om ervoor te zorgen dat uw code blijft werken na de migratie naar partnercentrum.

Deze API start het proces voor het pushen van een app naar productie. Deze bewerking is meestal langlopend. Deze aanroep maakt gebruik van de e-maillijst van meldingen van de [bewerking Publish](./cloud-partner-portal-api-publish-offer.md) API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI-parameters
--------------

|  **Naam**      |   **Beschrijving**                                                           | **Gegevenstype** |
|  --------      |   ---------------                                                           | ------------- |
| uitgeverId    | Publisher-id voor de aanbieding die moet worden opgehaald, bijvoorbeeld`contoso`       |  Tekenreeks       |
| aanbiedingId        | Aanbiedingsid van de aanbieding om op te halen                                   |  Tekenreeks       |
| api-versie    | Nieuwste versie van de API                                                   |  Date         |
|  |  |  |

## <a name="header"></a>Header
------

|  **Naam**       |     **Waarde**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autorisatie   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Voorbeeld van het lichaam

### <a name="response"></a>Antwoord

#### <a name="migrated-offers"></a>Gemigreerde aanbiedingen

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Niet-gemigreerde aanbiedingen

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Reactieheader

|  **Naam**             |      **Waarde**                                                            |
|  --------             |      ----------                                                           |
| Locatie    |  Het relatieve pad om de status van deze bewerking op te halen            |
|  |  |

### <a name="response-status-codes"></a>Statuscodes voor antwoord

| **Code** |  **Beschrijving**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`- Het verzoek is met succes ingewilligd. Het antwoord bevat een locatie om de bedrijfsstatus bij te houden. |
|  400     | `Bad/Malformed request`- Aanvullende foutinformatie wordt gevonden in de reactiebody. |
|  404     |  `Not found`- De opgegeven entiteit bestaat niet.                                       |
|  |  |
