---
title: Azure Blockchain Workbench REST API's gebruiken
description: Scenario's voor het gebruik van de REST API voor Azure Blockchain Workbench Preview
ms.date: 03/05/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 3084fcf343bc42fe01bf352b6791916d62f63540
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672738"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>De API voor de proefversie van Azure Blockchain Workbench Preview gebruiken

Azure Blockchain Workbench Preview REST API biedt ontwikkelaars en informatiewerkers een manier om rijke integraties te bouwen in blockchain-toepassingen. In dit artikel worden verschillende scenario's belicht voor het gebruik van de Workbench REST API. Stel dat u een aangepaste blockchain-client wilt maken waarmee aangemelde gebruikers hun toegewezen blockchain-toepassingen kunnen bekijken en ermee kunnen communiceren. De client kan de Blockchain Workbench API gebruiken om contractinstanties te bekijken en acties te ondernemen op basis van slimme contracten.

## <a name="blockchain-workbench-api-endpoint"></a>Blockchain Workbench API-eindpunt

Blockchain Workbench API's zijn toegankelijk via een eindpunt voor uw implementatie. Ga als eerste over de URL van api-eindpunt voor uw implementatie:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen**in het navigatiedeelvenster aan de linkerkant .
1. Kies de naam van de resourcegroep van uw geïmplementeerde Blockchain Workbench.
1. Selecteer de kolomkop **TYPE** om de lijst alfabetisch te sorteren op type.
1. Er zijn twee bronnen met type **App Service**. Selecteer de bron van het type **App-service** *met* het achtervoegsel 'api'.
1. Kopieer in het **Overzicht**van de App-service de **URL-waarde,** die de URL van het API-eindpunt vertegenwoordigt naar uw geïmplementeerde Blockchain Workbench.

    ![URL van het eindpunt van app-service-API](media/use-api/app-service-api.png)

## <a name="authentication"></a>Authentication

Aanvragen voor de Blockchain Workbench REST API worden beveiligd met Azure Active Directory (Azure AD).

Als u een geverifieerd verzoek wilt indienen bij de REST-API's, vereist clientcode verificatie met geldige referenties voordat u de API aanroepen. Verificatie wordt gecoördineerd tussen de verschillende actoren door Azure AD en biedt uw client een [toegangstoken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#access-token) als bewijs van de verificatie. Het token wordt vervolgens verzonden in de HTTP Authorization-header van REST API-aanvragen. Zie [Azure Active Directory voor ontwikkelaars voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)meer informatie over Azure AD-verificatie.

Zie [REST API-voorbeelden](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) voor voorbeelden van hoe u deze verifiëren.

## <a name="using-postman"></a>Met Postman

Als u Workbench API's wilt testen of experimenteren, u [Postman](https://www.postman.com) gebruiken om API-aanroepen naar uw implementatie uit te voeren. [Download een voorbeeld Postman verzameling van Workbench API aanvragen](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) van GitHub. Zie het README-bestand voor meer informatie over het verifiëren en gebruiken van de voorbeeld-API-aanvragen.

## <a name="create-an-application"></a>Een app maken

U gebruikt twee API-aanroepen om een Blockchain Workbench-toepassing te maken. Deze methode kan alleen worden uitgevoerd door gebruikers die Workbench-beheerders zijn.

Gebruik de [API Toepassingen POST](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationspost) om het JSON-bestand van de toepassing te uploaden en een toepassings-id te krijgen.

### <a name="applications-post-request"></a>Aanvragen POST-aanvraag

Gebruik de parameter **appFile** om het configuratiebestand te verzenden als onderdeel van de aanvraaginstantie.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Reactie van toepassingen POST

De gemaakte toepassings-ID wordt geretourneerd in het antwoord. U hebt de toepassings-id nodig om het configuratiebestand te koppelen aan het codebestand wanneer u de volgende API aanroept.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Aanvraag van postcode

Gebruik de [POST-API voor toepassingen-contractcode](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/contractcodepost) door de toepassings-id door te geven om het soliditeitscodebestand van de toepassing te uploaden. De payload kan een enkel Solidity-bestand zijn of een bestand met rits met Solidity-bestanden.

Vervang de volgende waarden:

| Parameter | Waarde |
|-----------|-------|
| {applicationId} | Retourwaarde uit de API voor toepassingen POST. |
| {ledgerId} | Index van het grootboek. De waarde is meestal 1. U ook de [tabel Grootboek](data-sql-management-studio.md) controleren op de waarde. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Antwoord van postcode

Als dit is gelukt, bevat het antwoord de gemaakte contractcode-id uit de [tabel ContractCode.](data-sql-management-studio.md)

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Rollen toewijzen aan gebruikers

Gebruik de [post-api voor toepassingen-toewijzingen](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) door de toepassings-id, gebruikers-id en toepassingsrol-id door te geven om een user-to-role-toewijzing te maken in de opgegeven blockchain-toepassing. Deze methode kan alleen worden uitgevoerd door gebruikers die Workbench-beheerders zijn.

### <a name="role-assignments-post-request"></a>Functietoewijzingen POST-aanvraag

Vervang de volgende waarden:

| Parameter | Waarde |
|-----------|-------|
| {applicationId} | Retourwaarde uit de API Voor toepassingen post. |
| {userId} | De waarde van de gebruikersnaam van [de gebruikerstabel](data-sql-management-studio.md). |
| {applicationRoleId} | De waarde van toepassingsrol-id's die is gekoppeld aan de toepassings-id in de [tabel ApplicationRole](data-sql-management-studio.md). |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Functietoewijzingen POST-respons

Als dit is gelukt, bevat het antwoord de gemaakte functietoewijzings-id in de [tabel RoleAssignment](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Lijst met toepassingen weergeven

Gebruik de [API Voor toepassingen GET](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget) om alle Blockchain Workbench-toepassingen voor de gebruiker op te halen. In dit voorbeeld heeft de aangemelde gebruiker toegang tot twee toepassingen:

- [Overdracht van activa](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Gekoeld transport](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Aanvragen GET-aanvraag

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Toepassingen GET-respons

De respons bevat alle blockchain-toepassingen waartoe een gebruiker toegang heeft in Blockchain Workbench. Blockchain Workbench-beheerders krijgen elke blockchain-toepassing. Niet-Workbench-beheerders krijgen alle blockchain-toepassingen waarvoor ze ten minste één bijbehorende toepassingsrol of een bijbehorende rol van slimme contractinstantie hebben.

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

Gebruik [Applications Workflows GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget) om alle workflows van een bepaalde blockchain-toepassing te vermelden waartoe een gebruiker toegang heeft in Blockchain Workbench. Elke blockchain-toepassing heeft een of meer werkstromen en elke werkstroom heeft nul of meer exemplaren van slimme contracten. Voor een blockchain-clienttoepassing met slechts één workflow raden we aan de gebruikerservaringsstroom over te slaan waarmee gebruikers de juiste workflow kunnen selecteren.

### <a name="application-workflows-request"></a>Aanvraag voor toepassingswerkstromen

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Antwoord op toepassingswerkstromen

Blockchain Workbench-beheerders krijgen elke blockchain-workflow. Niet-Workbench-beheerders krijgen alle werkstromen waarvoor ze ten minste één bijbehorende toepassingsrol hebben of die zijn gekoppeld aan een functie voor slimme contractinstanties.

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

## <a name="create-a-contract-instance"></a>Een contractinstantie maken

Gebruik [Contracts V2 POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contractsv2/contractpost) om een nieuwe slimme contractinstantie voor een workflow te maken. Gebruikers kunnen alleen een nieuwe smart contract-instantie maken als de gebruiker is gekoppeld aan een toepassingsrol, waarmee een slim contractexemplaar voor de werkstroom kan worden gestart.

> [!NOTE]
> In dit voorbeeld wordt versie 2 van de API gebruikt. Versie 2 contract API's bieden meer granulariteit voor de bijbehorende provisioningstatus velden.

### <a name="contracts-post-request"></a>Contracten POST aanvraag

Vervang de volgende waarden:

| Parameter | Waarde |
|-----------|-------|
| {workflowId} | De waarde van de werkstroom-id is de constructorid van het contract in de [tabel Werkstroom.](data-sql-management-studio.md) |
| {contractCodeId} | Waarde van de contractcode-id in de [tabel ContractCode](data-sql-management-studio.md). Correleer de toepassings-id en grootboek-id voor de contractinstantie die u wilt maken. |
| {connectionId} | Verbindings-ID-waarde in de [tabel Verbinding](data-sql-management-studio.md). |

Stel voor de aanvraaginstantie waarden in met de volgende gegevens:

| Parameter | Waarde |
|-----------|-------|
| werkstroomFunctie-ID | ID van de [tabel Werkstroomfunctie](data-sql-management-studio.md). |
| werkstroomActionParameters | Naamwaardeparen van parameters die aan de constructor worden doorgegeven. Gebruik voor elke parameter de waarde van de werkstroomFunctieParameterID in de tabel [WorkflowFunctionParameter.](data-sql-management-studio.md) |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Contracten POST reactie

Als deze is geslaagd, retourneert de API contractactionid van de [tabel ContractActionParameter](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Lijst met exemplaren van slimme contracten voor een werkstroom weergeven

Gebruik [Contracts GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) om alle slimme contractinstanties voor een werkstroom weer te geven. Of u gebruikers toestaan om diep te duiken in een van de getoonde slimme contract exemplaren.

### <a name="contracts-request"></a>Contractaanvraag

Stel dat een gebruiker in dit voorbeeld interactie wil hebben met een van de exemplaren van een slim contract om er vervolgens een bepaalde actie op uit te voeren.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Antwoord op contracten

Het antwoord bevat alle slimme contractexemplaren van de opgegeven werkstroom. Workbench-beheerders krijgen alle slimme contractexemplaren. Niet-Workbench-beheerders krijgen elk slim contractexemplaar waarvoor ze ten minste één bijbehorende toepassingsrol hebben of is gekoppeld aan een functie voor slimme contractinstanties.

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

Gebruik [de API voor contractactie get](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) om de beschikbare gebruikersacties weer te geven, gezien de status van het contract. 

### <a name="contract-action-request"></a>Contractactieaanvraag

In dit voorbeeld bekijkt de gebruiker alle beschikbare acties voor een nieuw slim contract dat ze hebben gemaakt.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Reactie van contractactie

Het antwoord bestaat uit een lijst met alle acties die een gebruiker kan uitvoeren op basis van de huidige status van het opgegeven exemplaar van het slimme contract.

* Modify: hiermee kan de gebruiker de beschrijving en prijs van een asset aanpassen.
* Einde: hiermee kan de gebruiker het contract van het actief beëindigen.

Gebruikers zien alle toepasselijke acties als de gebruiker een bijbehorende toepassingsrol heeft of een rol voor het exemplaar van het slimme contract voor de huidige status van het opgegeven exemplaar van het slimme contract.

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

Gebruik [de API voor contractactie post](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) om actie te ondernemen voor de opgegeven slimme contractinstantie.

### <a name="contract-action-post-request"></a>Contractactie POST-aanvraag

Houd in dit geval rekening met het scenario waarin een gebruiker de beschrijving en prijs van een actief wil wijzigen.

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

Gebruikers kunnen alleen de actie uitvoeren die mogelijk is op basis van de huidige status van het opgegeven exemplaar van het slimme contract en de bijbehorende toepassingsrol van de gebruiker of de rol van het exemplaar van het slimme contract.

### <a name="contract-action-post-response"></a>Reactie van contractactie POST

Als het posten is gelukt, wordt dit aangegeven met een HTTP 200 OK-antwoord zonder verdere toelichting.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Volgende stappen

Zie de [Azure Blockchain Workbench REST API-referentie](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)voor referentieinformatie over Blockchain Workbench API's.
