---
title: Operation API annuleren | Azure Marketplace
description: Bewerkingen annuleren .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: f9e55ff2c581f9392a125f6dc3ec8d903e9876a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256430"
---
# <a name="cancel-operation"></a>Bewerking annuleren

> [!NOTE]
> De API's van cloudpartnerportalen zijn geïntegreerd met het Partner Center en blijven werken nadat uw aanbiedingen zijn gemigreerd naar partnercentrum. De integratie brengt kleine veranderingen met zich mee. Bekijk de wijzigingen in [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) om ervoor te zorgen dat uw code blijft werken na de migratie naar partnercentrum.

Deze API annuleert een bewerking die momenteel in uitvoering is op de aanbieding. Gebruik de [API Operations](./cloud-partner-portal-api-retrieve-operations.md) `operationId` ophalen om een informatie-informatie over te brengen aan deze API. Annulering is meestal een synchrone bewerking, maar in sommige complexe scenario's kan een nieuwe bewerking nodig zijn om een bestaande bewerking te annuleren. In dit geval bevat de HTTP-antwoordinstantie de locatie van de bewerking die moet worden gebruikt om de status op te vragen.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-parameters

--------------

|  **Naam**    |      **Beschrijving**                                  |    **Gegevenstype**  |
| ------------ |     ----------------                                  |     -----------   |
| uitgeverId  |  Publisher identifier, bijvoorbeeld,`contoso`         |   Tekenreeks          |
| aanbiedingId      |  Aanbiedings-id                                     |   Tekenreeks          |
| api-versie  |  Huidige versie van API                               |    Date           |
|  |  |  |

## <a name="header"></a>Header
------

|  **Naam**              |  **Waarde**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autorisatie         |  Toon UW TOKEN |
|  |  |

## <a name="body-example"></a>Voorbeeld van het lichaam
------------

### <a name="request"></a>Aanvraag

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Eigenschappen van hoofdtekst aanvragen

|  **Naam**                |  **Beschrijving**                                               |
|  --------                |  ---------------                                               |
|  melding-e-mails     | Komma gescheiden lijst van e-id's op de hoogte te worden gesteld van de voortgang van de publicatiebewerking. |
|  |  |

### <a name="response"></a>Antwoord

#### <a name="migrated-offers"></a>Gemigreerde aanbiedingen

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Niet-gemigreerde aanbiedingen

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Reactieheader

|  **Naam**             |    **Waarde**                       |
|  ---------            |    ----------                      |
| Locatie    | Het relatieve pad om de status van deze bewerking op te halen. |
|  |  |

### <a name="response-status-codes"></a>Statuscodes voor antwoord

| **Code**  |  **Beschrijving**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | OK. De aanvraag is verwerkt en de bewerking wordt synchroon geannuleerd. |
|  202      | Aanvaard. De aanvraag is verwerkt en de bewerking wordt momenteel geannuleerd. De locatie van de annuleringsbewerking wordt geretourneerd in de reactiekop. |
|  400      | Slecht/Misvormd verzoek. De instantie voor foutrespons kan meer informatie geven.  |
|  403      | Toegang verboden. De client heeft geen toegang tot de naamruimte die in de aanvraag is opgegeven. |
|  404      | Niet gevonden. De opgegeven entiteit bestaat niet. |
|  |  |
