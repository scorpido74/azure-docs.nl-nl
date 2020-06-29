---
title: Go Live API-Azure Marketplace
description: De Go Live API initieert het Live-aanbiedings proces van de aanbieding.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 5d550f576108447a88660321899f2f55ffeb3c77
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/28/2020
ms.locfileid: "85516220"
---
# <a name="go-live"></a>Live gaan

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd met partner centrum en blijven werken nadat uw aanbiedingen zijn gemigreerd naar het partner centrum. De integratie introduceert kleine wijzigingen. Bekijk de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](./cloud-partner-portal-api-overview.md) om ervoor te zorgen dat uw code blijft werken na de migratie naar het partner centrum.

Deze API start het proces voor het pushen van een app naar productie. Deze bewerking wordt meestal langdurig uitgevoerd. Deze aanroep gebruikt de lijst met e-mail meldingen van de bewerking [Publish](./cloud-partner-portal-api-publish-offer.md) API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI-para meters
--------------

|  **Naam**      |   **Beschrijving**                                                           | **Gegevenstype** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | De uitgevers-id van de aanbieding die u wilt ophalen, bijvoorbeeld`contoso`       |  Tekenreeks       |
| offerId        | Id van de aanbieding die moet worden opgehaald                                   |  Tekenreeks       |
| api-versie    | Nieuwste versie van de API                                                   |  Date         |
|  |  |  |

## <a name="header"></a>Koptekst
------

|  **Naam**       |     **Waarde**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autorisatie   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Voor beeld van tekst

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

### <a name="response-status-codes"></a>Antwoord status codes

| **Code** |  **Beschrijving**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`-De aanvraag is geaccepteerd. Het antwoord bevat een locatie voor het bijhouden van de bewerkings status. |
|  400     | `Bad/Malformed request`-Er zijn aanvullende fout gegevens gevonden in de hoofd tekst van het antwoord. |
|  404     |  `Not found`-De opgegeven entiteit bestaat niet.                                       |
|  |  |
