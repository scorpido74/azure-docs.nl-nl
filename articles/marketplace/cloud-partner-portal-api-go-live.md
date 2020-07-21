---
title: Go Live API-Azure Marketplace
description: De Go Live API initieert het Live-aanbiedings proces van de aanbieding.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: dsindona
ms.author: dsindona
ms.date: 07/14/2020
ms.openlocfilehash: 5c5448a853447a0eacc8d974a5b00d5d1e8f4be2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535940"
---
# <a name="go-live"></a>Live gaan

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd in en blijven werken in het partner centrum. De overgang introduceert kleine wijzigingen. Controleer de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](./cloud-partner-portal-api-overview.md) om ervoor te zorgen dat uw code blijft werken na het overstappen naar het partner centrum. CCP-Api's mogen alleen worden gebruikt voor bestaande producten die al zijn geïntegreerd vóór de overgang naar het partner centrum. nieuwe producten moeten de indienings-Api's van partner Center gebruiken.

Deze API start het proces voor het pushen van een app naar productie. Deze bewerking wordt meestal langdurig uitgevoerd. Deze aanroep gebruikt de lijst met e-mail meldingen van de bewerking [Publish](./cloud-partner-portal-api-publish-offer.md) API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI-para meters
--------------

|  **Naam**      |   **Beschrijving**                                                           | **Gegevens type** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | De uitgevers-id van de aanbieding die u wilt ophalen, bijvoorbeeld`contoso`       |  Tekenreeks       |
| offerId        | Id van de aanbieding die moet worden opgehaald                                   |  Tekenreeks       |
| api-versie    | Nieuwste versie van de API                                                   |  Datum         |
|  |  |  |

## <a name="header"></a>Header
------

|  **Naam**       |     **Waarde**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autorisatie   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Voor beeld van tekst

### <a name="response"></a>Reactie

#### <a name="migrated-offers"></a>Gemigreerde aanbiedingen

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Niet-gemigreerde aanbiedingen

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Reactieheader

|  **Naam**             |      **Waarde**                                                            |
|  --------             |      ----------                                                           |
| Locatie    |  Het relatieve pad om de status van deze bewerking op te halen            |
|  |  |

### <a name="response-status-codes"></a>Antwoord status codes

| **Code** |  **Beschrijving**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`-De aanvraag is geaccepteerd. Het antwoord bevat een locatie voor het bijhouden van de bewerkings status. |
|  400     | `Bad/Malformed request`-Er zijn aanvullende fout gegevens gevonden in de hoofd tekst van het antwoord. |
|  404     |  `Not found`-De opgegeven entiteit bestaat niet.                                       |
|  |  |
