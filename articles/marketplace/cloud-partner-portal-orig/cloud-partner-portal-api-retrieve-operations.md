---
title: De operations-API ophalen | Azure Marketplace
description: Hiermee haalt u alle bewerkingen op de aanbieding op of krijgt u een bepaalde bewerking voor de opgegeven operationId.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: c3eb77744d61322ca0aed20bb2b3f486cc02ac70
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819597"
---
<a name="retrieve-operations"></a>Bewerkingen ophalen
===================

Hiermee haalt u alle bewerkingen op de aanbieding op of krijgt u een bepaalde bewerking voor de opgegeven operationId. De client kan query parameters gebruiken om te filteren op actieve bewerkingen.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-para meters
--------------

|  **Naam**          |      **Beschrijving**                                                                                           | **Gegevenstype** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Uitgevers-id, bijvoorbeeld `Contoso`                                                                   |  Tekenreeks       |
|  OfferId           |  Aanbiedings-id                                                                                              |  Tekenreeks       |
|  operationId       |  GUID waarmee de bewerking op de aanbieding uniek wordt geïdentificeerd. De operationId kan worden opgehaald met behulp van deze API en wordt ook geretourneerd in de HTTP-header van het antwoord op een langlopende bewerking, zoals de API voor het [publiceren van aanbiedingen](./cloud-partner-portal-api-publish-offer.md) .  |   GUID   |
|  filteredStatus    | Optionele query parameter die wordt gebruikt om te filteren op status (bijvoorbeeld `running`) voor de verzameling die wordt geretourneerd door deze API.  |   Tekenreeks |
|  API-versie       | Nieuwste versie van API                                                                                           |    Date      |
|  |  |  |


<a name="header"></a>Header
------

|  **Naam**          |  **Waarde**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Autorisatie     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Voor beeld van tekst
------------

### <a name="response"></a>Antwoord

#### <a name="get-operations"></a>GET-bewerkingen

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>GET-bewerking

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 day",
                            "id": "displayprovision",
                            "stepName": "Provisioning",
                            "description": "Your virtual machine is being replicated in our production systems.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 hour",
                            "id": "displaypackage",
                            "stepName": "Packaging and Lead Generation Registration",
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
                "notificationEmails": "jondoe@contoso.com"
            } 
        }
    ]
```


### <a name="response-body-properties"></a>Eigenschappen van antwoord tekst

|  **Naam**                    |  **Beschrijving**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | GUID die de bewerking uniek identificeert                                                       |
|  submissionType              | Hiermee wordt het type bewerking aangegeven dat wordt gerapporteerd voor de aanbieding, bijvoorbeeld `Publish/GGoLive`      |
|  createdDateTime             | UTC-datum/tijd waarop de bewerking is gemaakt                                                       |
|  lastActionDateTime          | UTC-datum/tijd waarop de laatste update is uitgevoerd voor de bewerking                                       |
|  status                      | De status van de bewerking, `not started` \| `running` \| `failed` \|.`completed` Er kan slechts één bewerking per keer de status `running`. |
|  error                       | Fout bericht voor mislukte bewerkingen                                                               |
|  |  |


### <a name="response-status-codes"></a>Antwoord status codes

| **Gecodeerd**  |   **Beschrijving**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK`-de aanvraag is verwerkt en de aangevraagde bewerking (en) zijn geretourneerd.        |
|  400      | `Bad/Malformed request`: de hoofd tekst van het fout bericht bevat mogelijk meer informatie.                    |
|  403      | `Forbidden`-de client heeft geen toegang tot de opgegeven naam ruimte.                          |
|  404      | `Not found`-de opgegeven entiteit bestaat niet.                                                 |
|  |  |
