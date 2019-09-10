---
title: Preview-REST API van Azure Block Chain Workbench gebruiken
description: Scenario's voor het gebruik van de preview-versie van Azure Block Chain Workbench REST API
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 412b78363be70918969734cd8890a8ee940df431
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70843813"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>De preview-versie van Azure Block Chain Workbench gebruiken REST API

De preview-versie van Azure Block Chain Workbench REST API biedt ontwikkel aars en informatie werkers een manier om uitgebreide integraties te bouwen met block Chain-toepassingen. In dit document worden verschillende belangrijke methoden van de REST-API van Workbench beschreven. Stel dat een ontwikkelaar een aangepaste blockchain-client wil maken. Met deze blockchain-client kunnen ingelogde gebruikers hun toegewezen blockchain-applicaties bekijken en gebruiken. De client stelt gebruikers in de gelegenheid exemplaren van contracten te bekijken en actie te ondernemen op smart contracts. De client gebruikt de Workbench REST API in de context van de ingelogde gebruiker om de volgende acties uit te voeren:

* Lijst met toepassingen weergeven
* Lijst met werkstromen voor een toepassing weergeven
* Lijst met exemplaren van slimme contracten voor een werkstroom weergeven
* Lijst met beschikbare acties voor een contract weergeven
* Een actie voor een contract uitvoeren

De voorbeeld blockchain-toepassingen die in de scenario's kunnen worden gebruikt [gedownload uit GitHub](https://github.com/Azure-Samples/blockchain).

## <a name="list-applications"></a>Lijst met toepassingen weergeven

Zodra een gebruiker zich bij de blockchain-client heeft aangemeld, is de eerste taak om alle Blockchain Workbench-toepassingen voor de gebruiker op te halen. In dit scenario heeft de gebruiker toegang tot twee toepassingen:

1. [Asset overboeking](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2. [Koel transport](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

Gebruik de [GET-API voor toepassingen](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

In het antwoord wordt een lijst weer gegeven met alle Block Chain-toepassingen waartoe een gebruiker toegang heeft in Block Chain Workbench. Block Chain Workbench-beheerders krijgen elke Block Chain-toepassing. Beheerders van niet-Workbench krijgen alle blockchains waarvoor ze ten minste één gekoppelde toepassingsrol of een gekoppelde rol van een slimme contract instantie hebben.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Lijst met werkstromen voor een toepassing weergeven

Zodra een gebruiker de gewenste blockchain-toepassing selecteert (zoals **Asset Transfer**), haalt de blockchain-client alle werkstromen van de specifieke blockchain-toepassing op. Gebruikers kunnen vervolgens de toepasselijke werkstroom selecteren voordat alle slimme contractinstanties voor de werkstroom worden getoond. Elke blockchain-toepassing heeft een of meer werkstromen en elke werkstroom heeft nul of meer slimme contractinstanties. Voor een blockchain-clienttoepassing die slechts één werkstroom heeft, raden we aan de gebruikerservaringsstroom over te slaan, zodat gebruikers de juiste werkstroom kunnen selecteren. In dit geval heeft **Asset Transfer** slechts één werkstroom, die ook wel **Asset Transfer** wordt genoemd.

Gebruik de [GET-API voor werkstromen van toepassingen](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

Het antwoord geeft een overzicht van alle werkstromen van de gespecificeerde blockchain-toepassing waartoe een gebruiker toegang heeft in Blockchain Workbench. Blockchain Workbench-beheerders krijgen elke blockchain-werkstroom. Niet-Workbench-beheerders krijgen alle werkstromen waarvoor ze ten minste één bijbehorende toepassingsrol hebben of die zijn gekoppeld aan een instantierol van een slim contract.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Lijst met exemplaren van slimme contracten voor een werkstroom weergeven

Zodra een gebruiker de toepasselijke werkstroom selecteert, in dit geval **Asset Transfer**, haalt de blockchain-client alle slimme contractinstanties voor de opgegeven werkstroom op. U kunt deze informatie gebruiken om alle slimme contractinstanties voor de werkstroom te tonen. Of u kunt gebruikers toestaan om diep in een van de getoonde slimme contractinstanties te duiken. Overweeg in dit voorbeeld dat een gebruiker interactie wil hebben met een van de slimme contractinstanties om actie te ondernemen.

Gebruik de [GET-API voor contracten](/rest/api/azure-blockchain-workbench/contractsv2/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

Het antwoord vermeldt alle slimme contractinstanties van de opgegeven werkstroom. Workbench-beheerders krijgen alle slimme contractinstanties. Niet-Workbench-beheerders krijgen alle slimme contractexemplaren waarvoor ze ten minste één bijbehorende toepassingsrol hebben of die zijn gekoppeld aan een slimme contractinstantie-rol.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Lijst met beschikbare acties voor een contract weergeven

Zodra een gebruiker heeft besloten om een contract diep te vinden, kan de Block Chain-client vervolgens de beschik bare gebruikers acties weer geven op basis van de status van het contract. In dit voorbeeld kijkt de gebruiker naar alle beschikbare acties voor een nieuw slim contract die hij of zij heeft gemaakt:

* Wijzigen Hiermee kan de gebruiker de beschrijving en prijs van een Asset wijzigen.
* Tijdig Hiermee kan de gebruiker het contract van de Asset beëindigen.

Gebruik de [GET-API voor contractacties](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

Het antwoord geeft een overzicht van alle acties die een gebruiker kan ondernemen in de huidige staat van de opgegeven instantie van een slim contract. Gebruikers krijgen alle toepasselijke acties als de gebruiker een gekoppelde toepassingsrol heeft of is gekoppeld aan een slimme contractinstantie-rol voor de huidige status van de opgegeven instantie van een slim contract.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Een actie voor een contract uitvoeren

Een gebruiker kan vervolgens beslissen om actie te ondernemen voor de opgegeven instantie van een slim contract. Overweeg in dit geval het scenario waarbij een gebruiker de beschrijving en prijs van een asset wil wijzigen in de volgende actie:

* Beschrijving: ' Mijn bijgewerkte auto '
* Prijs: 54321

Gebruik de [POST-API voor contractacties](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost):

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Gebruikers kunnen de actie alleen uitvoeren op basis van de huidige status van de opgegeven instantie voor een slim contract en de bijbehorende toepassingsrol van de gebruiker of de rol van de slimme contractinstantie. Als het bericht succesvol is, wordt een HTTP 200 OK-antwoord geretourneerd zonder inhoudelijk antwoord.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over de REST-API van Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)