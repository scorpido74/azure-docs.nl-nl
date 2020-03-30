---
title: Operation API annuleren | Azure Marketplace
description: Bewerkingen annuleren .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d4c1f52f0f3b1e05ec06f5a66a36323f346d4eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280538"
---
# <a name="cancel-operation"></a>Bewerking annuleren 

Deze API annuleert een bewerking die momenteel in uitvoering is op de aanbieding. Gebruik de [API Operations](./cloud-partner-portal-api-retrieve-operations.md) `operationId` ophalen om een informatie-informatie over te brengen aan deze API. Annulering is meestal een synchrone bewerking, maar in sommige complexe scenario's kan een nieuwe bewerking nodig zijn om een bestaande bewerking te annuleren. In dit geval bevat de HTTP-antwoordinstantie de locatie van de bewerking die moet worden gebruikt om de status op te vragen.

U een door komma's gescheiden lijst met e-mailadressen bij de aanvraag opnemen en de API zal deze adressen op de hoogte brengen van de voortgang van de bewerking.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-parameters
--------------

|  **Naam**    |      **Beschrijving**                                  |    **Gegevenstype**  |
| ------------ |     ----------------                                  |     -----------   |
| uitgeverId  |  Publisher identifier, bijvoorbeeld,`contoso`         |   Tekenreeks          |
| aanbiedingId      |  Aanbiedings-id                                     |   Tekenreeks          |
| api-versie  |  Huidige versie van API                               |    Date           |
|  |  |  |


<a name="header"></a>Header
------

|  **Naam**              |  **Waarde**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autorisatie         |  Toon UW TOKEN |
|  |  |


<a name="body-example"></a>Voorbeeld van het lichaam
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

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Reactieheader

|  **Naam**             |    **Waarde**                       |
|  ---------            |    ----------                      |
| Operatielocatie    | URL, die kan worden opgevraagd om de huidige status van de bewerking te bepalen. |
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
