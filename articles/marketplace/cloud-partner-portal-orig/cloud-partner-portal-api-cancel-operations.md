---
title: Bewerkings-API annuleren | Azure Marketplace
description: Annulerings bewerkingen.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 374425dbd2abacb2114b5792d7476bc341fa353a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819789"
---
# <a name="cancel-operation"></a>Bewerking annuleren 

Deze API annuleert een bewerking die momenteel wordt uitgevoerd op de aanbieding. Gebruik de [API retrieve Operations](./cloud-partner-portal-api-retrieve-operations.md) om een `operationId` op te halen die u wilt door geven aan deze API. Annulering is doorgaans een synchrone bewerking, maar in sommige complexe scenario's is het mogelijk dat er een nieuwe bewerking moet worden uitgevoerd om een bestaand item te annuleren. In dit geval bevat de HTTP-antwoord tekst de locatie van de bewerking die moet worden gebruikt voor het opvragen van de status.

U kunt een door komma's gescheiden lijst met e-mail adressen opgeven met de aanvraag, en de API meldt deze adressen over de voortgang van de bewerking.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-para meters
--------------

|  **Naam**    |      **Beschrijving**                                  |    **Gegevenstype**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Uitgevers-id, bijvoorbeeld `contoso`         |   Tekenreeks          |
| OfferId      |  Aanbiedings-id                                     |   Tekenreeks          |
| API-versie  |  Huidige versie van API                               |    Date           |
|  |  |  |


<a name="header"></a>Header
------

|  **Naam**              |  **Waarde**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autorisatie         |  Draagt uw TOKEN |
|  |  |


<a name="body-example"></a>Voor beeld van tekst
------------

### <a name="request"></a>Aanvraag

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Eigenschappen van aanvraag hoofdtekst

|  **Naam**                |  **Beschrijving**                                               |
|  --------                |  ---------------                                               |
|  melding-e-mail berichten     | Door komma's gescheiden lijst met e-mail-Id's die moeten worden geïnformeerd over de voortgang van de publicatie bewerking. |
|  |  |


### <a name="response"></a>Antwoord

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Antwoordkoptekst

|  **Naam**             |    **Waarde**                       |
|  ---------            |    ----------                      |
| Bewerkings locatie    | URL: er kan een query worden uitgevoerd om de huidige status van de bewerking te bepalen. |
|  |  |


### <a name="response-status-codes"></a>Antwoord status codes

| **Gecodeerd**  |  **Beschrijving**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | OK. De aanvraag is verwerkt en de bewerking is synchroon geannuleerd. |
|  202      | Afgewezen. De aanvraag is verwerkt en de bewerking wordt momenteel geannuleerd. De locatie van de annulerings bewerking wordt geretourneerd in de reactie header. |
|  400      | Ongeldige/onjuiste aanvraag. De hoofd tekst van het fout bericht kan meer informatie geven.  |
|  403      | Toegang verboden. De client heeft geen toegang tot de naam ruimte die in de aanvraag is opgegeven. |
|  404      | Niet gevonden. De opgegeven entiteit bestaat niet. |
|  |  |
