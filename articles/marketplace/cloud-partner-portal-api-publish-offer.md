---
title: Een aanbieding publiceren-Azure Marketplace
description: API voor het publiceren van de opgegeven aanbieding.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.openlocfilehash: e3bc420a60c514e704a6caa38acee155b4981552
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115584"
---
# <a name="publish-an-offer"></a>Een aanbieding publiceren

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd met partner centrum en blijven werken nadat uw aanbiedingen zijn gemigreerd naar het partner centrum. De integratie introduceert kleine wijzigingen. Bekijk de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](./cloud-partner-portal-api-overview.md) om ervoor te zorgen dat uw code blijft werken na de migratie naar het partner centrum.

Start het publicatie proces voor de opgegeven aanbieding. Deze aanroep is een langlopende bewerking.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-para meters
--------------

|  **Naam**      |    **Beschrijving**                               |  **Gegevenstype** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Uitgevers-id, bijvoorbeeld`contoso`      |   Tekenreeks       |
|  offerId       | Aanbiedings-id                                 |   Tekenreeks       |
|  api-versie   | Nieuwste versie van de API                        |   Datum         |
|  |  |

## <a name="header"></a>Koptekst
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

| **Code** |  **Beschrijving**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`-De aanvraag is geaccepteerd. Het antwoord bevat een locatie die kan worden gebruikt voor het bijhouden van de bewerking die wordt gestart. |
| 400   | `Bad/Malformed request`-De hoofd tekst van het fout bericht bevat mogelijk meer informatie.                                                               |
| 422   | `Un-processable entity`-Geeft aan dat de validatie van de entiteit die moet worden gepubliceerd, is mislukt.                                                        |
| 404   | `Not found`-De entiteit die is opgegeven door de client, bestaat niet.                                                                              |
|  |  |
