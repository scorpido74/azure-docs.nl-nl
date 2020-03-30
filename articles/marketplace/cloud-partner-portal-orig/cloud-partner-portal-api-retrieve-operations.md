---
title: API ophalen | Azure Marketplace
description: Hiermee haalt u alle bewerkingen op de aanbieding op of om een bepaalde bewerking voor de opgegeven operationId te krijgen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 4fc77407ae1c5854d3fe977da5a81f4226bf5305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280470"
---
<a name="retrieve-operations"></a>Bewerkingen ophalen
===================

Hiermee haalt u alle bewerkingen op de aanbieding op of om een bepaalde bewerking voor de opgegeven operationId te krijgen. De client kan queryparameters gebruiken om te filteren op bewerkingen.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-parameters
--------------

|  **Naam**          |      **Beschrijving**                                                                                           | **Gegevenstype** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  uitgeverId       |  Publisher-id, bijvoorbeeld`Contoso`                                                                   |  Tekenreeks       |
|  aanbiedingId           |  Aanbiedings-id                                                                                              |  Tekenreeks       |
|  operationId       |  GUID die de bewerking op het aanbod op een unieke basis identificeert. De operationId kan worden opgehaald met behulp van deze API en wordt ook geretourneerd in de HTTP-header van het antwoord voor een langdurige bewerking, zoals de [API voor publicatieaanbieding.](./cloud-partner-portal-api-publish-offer.md)  |   GUID   |
|  gefilterdestatus    | Optionele queryparameter die wordt gebruikt `running`om te filteren op status (bijvoorbeeld) op de verzameling die door deze API wordt geretourneerd.  |   Tekenreeks |
|  api-versie       | Nieuwste versie van API                                                                                           |    Date      |
|  |  |  |


<a name="header"></a>Header
------

|  **Naam**          |  **Waarde**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Autorisatie     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Voorbeeld van het lichaam
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


### <a name="response-body-properties"></a>Eigenschappen van antwoordlichaam

|  **Naam**                    |  **Beschrijving**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | GUID die de bewerking op unieke wijze identificeert                                                       |
|  submissionType              | Hiermee wordt aangegeven welk type bewerking wordt gerapporteerd voor de aanbieding, bijvoorbeeld`Publish/GGoLive`      |
|  createdDateTime             | UTC-datum tijd toen de bewerking werd gemaakt                                                       |
|  laatsteActionDateTime          | UTC-datum waarop de laatste update op de bewerking is uitgevoerd                                       |
|  status                      | Status van de `not started` \| `running` \| `failed` \| `completed`bewerking, ofwel . Slechts één bewerking `running` kan status tegelijk hebben. |
|  error                       | Foutbericht voor mislukte bewerkingen                                                               |
|  |  |


### <a name="response-status-codes"></a>Statuscodes voor antwoord

| **Code**  |   **Beschrijving**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK`- De aanvraag is verwerkt en de gevraagde bewerking(en) is teruggestuurd.        |
|  400      | `Bad/Malformed request`- De foutreactieinstantie kan meer informatie bevatten.                    |
|  403      | `Forbidden`- De client heeft geen toegang tot de opgegeven naamruimte.                          |
|  404      | `Not found`- De opgegeven entiteit bestaat niet.                                                 |
|  |  |
