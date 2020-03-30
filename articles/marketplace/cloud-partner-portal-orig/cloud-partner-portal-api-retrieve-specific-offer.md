---
title: Een specifieke aanbiedings-API ophalen | Azure Marketplace
description: API haalt de opgegeven aanbieding op in de naamruimte van de uitgever.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: a83b664bb770a88f3c4c13a672655e736a46ca75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280453"
---
<a name="retrieve-a-specific-offer"></a>Een specifieke aanbieding ophalen
=========================

Hiermee haalt u de opgegeven aanbieding op in de naamruimte van de uitgever.  

U ook een bepaalde versie van de aanbieding ophalen of de aanbieding ophalen in concept-, weergave- of productieruimten. Als een sleuf niet is `draft`opgegeven, is de standaardinstelling . Als u een aanbieding probeert op te halen die `404 Not Found` niet is bekeken of gepubliceerd, wordt er een fout opgetreden.

> [!WARNING]
> De geheime waarden voor geheime tekstvelden worden niet opgehaald door deze API.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-parameters
--------------


| **Naam**    | **Beschrijving**                                                                          | **Gegevenstype** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| uitgeverId | uitgeverId. Contoso bijvoorbeeld                                                        | Tekenreeks        |
| aanbiedingId     | Guid dat uniek identificeert het aanbod.                                                 | Tekenreeks        |
| versie     | Versie van het aanbod dat wordt opgehaald. Standaard wordt de nieuwste aanbiedingsversie opgehaald. | Geheel getal       |
| slotId      | De sleuf waaruit het aanbod moet worden opgehaald, kan een van:      <br/>  - `Draft`(standaard) haalt de aanbiedingsversie op die momenteel in concept is.  <br/>  -  `Preview`haalt de aanbiedingsversie op die momenteel in preview wordt.     <br/>  -  `Production`haalt de aanbiedingsversie op die momenteel in productie is.          |      Enum |
| api-versie | Nieuwste versie van API                                                                    | Date          |
|  |  |  |


<a name="header"></a>Header
------

|  **Naam**          |   **Waarde**            |
|  ---------------   |  --------------        |
|  Content-Type      | `application/json`     |
|  Autorisatie     | `Bearer YOUR_TOKEN`    |
|  |  |


<a name="body-example"></a>Voorbeeld van het lichaam
------------

### <a name="response"></a>Antwoord

``` json
{
    "offerTypeId": "microsoft-azure-virtualmachines",
    "publisherId": "contoso",
    "status": "failed",
    "id": "059afc24-07de-4126-b004-4e42a51816fe",
    "version": 5,
    "definition": {
        "displayText": "Contoso Virtual Machine Offer",
        "offer": {
            "microsoft-azure-marketplace-testdrive.enabled": false,
            "microsoft-azure-marketplace-testdrive.videos": [],
            "microsoft-azure-marketplace.title": "Contoso App",
            "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
            "microsoft-azure-marketplace.allowedSubscriptions": [
                "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
            ],
            "microsoft-azure-marketplace.usefulLinks": [
            {
                "linkTitle": "Contoso App for Azure",
                "linkUrl": "https://azuremarketplace.microsoft.com"
            }
            ],
            "microsoft-azure-marketplace.categories": [
                "devService",
                "networking",
                "database",
                "cache",
                "security"
            ],
            "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
            "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.screenshots": [],
            "microsoft-azure-marketplace.videos": [],
            "microsoft-azure-marketplace.leadDestination": "None",
            "microsoft-azure-marketplace.tableLeadConfiguration": {},
            "microsoft-azure-marketplace.blobLeadConfiguration": {},
            "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
            "microsoft-azure-marketplace.crmLeadConfiguration": {},
            "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
            "microsoft-azure-marketplace.marketoLeadConfiguration": {},
            "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
            "microsoft-azure-marketplace.termsOfUse": "Terms of use",
            "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
            "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.supportContactName": "Jon Doe",
            "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.publicAzureSupportUrl": "",
            "microsoft-azure-marketplace.fairfaxSupportUrl": ""
            },
            "plans": [
            {
                "planId": "contososkuidentifier",
                "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
                "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                "microsoft-azure-virtualmachines.cloudAvailability": [
                    "PublicAzure"
                ],
                "microsoft-azure-virtualmachines.certificationsFairfax": [],
                "virtualMachinePricing": {
                    "isByol": true,
                    "freeTrialDurationInMonths": 0
                },
                "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                "microsoft-azure-virtualmachines.windowsOSType": "Other",
                "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                "microsoft-azure-virtualmachines.recommendedVMSizes": [
                    "a0-basic",
                    "a0-standard",
                    "a1-basic",
                    "a1-standard",
                    "a2-basic",
                    "a2-standard"
                ],
                "microsoft-azure-virtualmachines.openPorts": [],
                "microsoft-azure-virtualmachines.vmImages": {
                    "1.0.1": {
                    "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                    "lunVhdDetails": []
                    }
                },
                "regions": [
                    "DZ",
                    "AR"
                ]
            }
            ]
        },
        "changedTime": "2017-06-07T06:15:39.7349221Z"
    }
}
```


### <a name="response-body-properties"></a>Eigenschappen van antwoordlichaam

|  **Naam**       |   **Beschrijving**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Identificeert het type aanbieding                                                                                                    |
|  uitgeverId    | Unieke id van de uitgever                                                                                              |
|  status         | Status van het aanbod. Zie [Aanbiedingsstatus](#offer-status) hieronder voor de lijst met mogelijke waarden.                                  |
|  Id             | GUID die het aanbod op unieke wijze identificeert                                                                                         |
|  versie        | Huidige versie van het aanbod. De eigenschap versie kan niet door de client worden gewijzigd. Het wordt verhoogd na elke publicatie.    |
|  definitie     | Werkelijke definitie van de werklast                                                                                               |
|  changedTime    | UTC-datum toen de aanbieding voor het laatst is gewijzigd                                                                                   |
|  |  |


### <a name="response-status-codes"></a>Statuscodes voor antwoord

| **Code**  | **Beschrijving**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`- Het verzoek is verwerkt en alle aanbiedingen onder de uitgever zijn teruggegeven aan de klant.               |
|  400      | `Bad/Malformed request`- De foutreactieinstantie kan meer informatie bevatten.                                                 |
|  403      | `Forbidden`- De client heeft geen toegang tot de opgegeven naamruimte.                                                        |
|  404      | `Not found`- De opgegeven entiteit bestaat niet. Client moet de publisherId, offerId en versie controleren (indien opgegeven).      |
|  |  |


### <a name="offer-status"></a>Aanbiedingsstatus

|  **Naam**                   |   **Beschrijving**                             |
| --------------------------- |  -------------------------------------------- |
|  Nooit gepubliceerd             | Aanbieding is nooit gepubliceerd.               |
|  Niet gestart                 | Aanbieding is nieuw, maar is niet gestart.              |
|  WaitingForPublisherReview  | Aanbieding wacht op goedkeuring van de uitgever.      |
|  In uitvoering                    | Indiening van de aanbieding wordt verwerkt.          |
|  Geslaagd                  | Indiening van de aanbieding heeft de verwerking voltooid.    |
|  Geannuleerd                   | Indiening van de aanbieding is geannuleerd.                |
|  Mislukt                     | Indiening van aanbiedingen is mislukt.                      |
|  |  |
