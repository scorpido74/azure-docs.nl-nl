---
title: Een aanbieding publiceren | Azure Marketplace
description: API voor het publiceren van de opgegeven aanbieding.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255937"
---
# <a name="publish-an-offer"></a>Een aanbieding publiceren

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd met partner centrum en blijven werken nadat uw aanbiedingen zijn gemigreerd naar het partner centrum. De integratie introduceert kleine wijzigingen. Bekijk de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) om ervoor te zorgen dat uw code blijft werken na de migratie naar het partner centrum.

Start het publicatie proces voor de opgegeven aanbieding. Deze aanroep is een langlopende bewerking.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-para meters
--------------

|  **Naam**      |    **Beschrijving**                               |  **Gegevens type** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Uitgevers-id, bijvoorbeeld`contoso`      |   Tekenreeks       |
|  offerId       | Aanbiedings-id                                 |   Tekenreeks       |
|  api-versie   | Nieuwste versie van de API                        |   Date         |
|  |  |

## <a name="header"></a>Header
------

|  **Naam**        |    **Waarde**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autorisatie   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Voor beeld van tekst
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

#### <a name="migrated-offers"></a>Gemigreerde aanbiedingen

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Niet-gemigreerde aanbiedingen

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Reactieheader

|  **Naam**             |    **Waarde**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Locatie    | Het relatieve pad om de status van deze bewerking op te halen     |
|  |  |


### <a name="response-status-codes"></a>Antwoord status codes

| **Gecodeerd** |  **Beschrijving**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`-De aanvraag is geaccepteerd. Het antwoord bevat een locatie die kan worden gebruikt voor het bijhouden van de bewerking die wordt gestart. |
| 400   | `Bad/Malformed request`-De hoofd tekst van het fout bericht bevat mogelijk meer informatie.                                                               |
| 422   | `Un-processable entity`-Geeft aan dat de validatie van de entiteit die moet worden gepubliceerd, is mislukt.                                                        |
| 404   | `Not found`-De entiteit die is opgegeven door de client, bestaat niet.                                                                              |
|  |  |
