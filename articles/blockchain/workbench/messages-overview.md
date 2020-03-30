---
title: Berichten gebruiken om te integreren met Azure Blockchain Workbench
description: Overzicht van het gebruik van berichten om Azure Blockchain Workbench Preview te integreren met andere systemen.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 14bd0f84bc9490d95d3dbe0b9f122882f0d2059d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324512"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Azure Blockchain Workbench-berichten-integratie

Azure Blockchain Workbench biedt niet alleen een REST API, maar biedt ook integratie op basis van berichten. Workbench publiceert grootboekgerichte gebeurtenissen via Azure Event Grid, waardoor downstreamconsumenten gegevens kunnen opnemen of actie kunnen ondernemen op basis van deze gebeurtenissen. Voor clients die betrouwbare berichten nodig hebben, levert Azure Blockchain Workbench ook berichten aan een Azure Service Bus-eindpunt.

## <a name="input-apis"></a>Invoer-API's

Als u transacties van externe systemen wilt starten om gebruikers te maken, contracten te maken en contracten bij te werken, u API's voor berichteninvoer gebruiken om transacties uit te voeren op een grootboek. Zie [voorbeelden van berichtenintegratie](https://aka.ms/blockchain-workbench-integration-sample) voor een voorbeeld dat invoer-API's aantoont.

Hieronder volgen de momenteel beschikbare invoer-API's.

### <a name="create-user"></a>Gebruiker maken

Hiermee maakt u een nieuwe gebruiker.

De aanvraag vereist de volgende velden:

| **Naam**             | **Beschrijving**                                      |
|----------------------|------------------------------------------------------|
| requestId            | Client geleverd GUID                                |
| voornaam            | Voornaam van de gebruiker                              |
| achternaam             | Achternaam van de gebruiker                               |
| Emailaddress         | Het e-mailadres van de gebruiker                           |
| extern id           | Azure AD-object-id van de gebruiker                      |
| verbindingId         | Unieke id voor de blockchain-verbinding |
| messageSchemaVersie | Versie van het berichtenschema                            |
| berichtNaam          | **UserRequest maken**                               |

Voorbeeld:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench retourneert een antwoord met de volgende velden:

| **Naam**              | **Beschrijving**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | Client geleverd GUID |
| userId                | ID van de gebruiker die is gemaakt |
| userChainIdentifier   | Adres van de gebruiker die is gemaakt op het blockchain-netwerk. In Ethereum is het adres het **adres van** de gebruiker. |
| verbindingId          | Unieke id voor de blockchain-verbinding|
| messageSchemaVersie  | Versie van het berichtenschema |
| berichtNaam           | **UserUpdate maken** |
| status                | Status van het verzoek voor het maken van de gebruiker.  Als dit lukt, is waarde **succes.** Bij falen is waarde **mislukt.**     |
| aanvullendeinformatie | Aanvullende informatie op basis van de status |

Voorbeeld succesvol maken reactie van **de gebruiker** van Blockchain Workbench:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Als het verzoek niet is mislukt, worden details over de fout opgenomen in aanvullende informatie.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Contract maken

Hiermee maakt u een nieuw contract.

De aanvraag vereist de volgende velden:

| **Naam**             | **Beschrijving**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | Client geleverd GUID |
| userChainIdentifier  | Adres van de gebruiker die is gemaakt op het blockchain-netwerk. In Ethereum is dit adres het adres van de gebruiker **op de ketting.** |
| applicationName      | Naam van de aanvraag |
| versie              | Versie van de toepassing. Vereist als u meerdere versies van de toepassing hebt ingeschakeld. Anders is de versie optioneel. Zie [Azure Blockchain Workbench-toepassingsversieing](version-app.md)voor meer informatie over het versiewerk van toepassingen in Azure Blockchain. |
| werkstroomNaam         | Naam van de werkstroom |
| parameters           | Parameters invoer voor het maken van contracten |
| verbindingId         | Unieke id voor de blockchain-verbinding |
| messageSchemaVersie | Versie van het berichtenschema |
| berichtNaam          | **Contractrequest maken** |

Voorbeeld:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench retourneert een antwoord met de volgende velden:

| **Naam**                 | **Beschrijving**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | Client geleverd GUID                                                             |
| contractId               | Unieke id voor het contract in Azure Blockchain Workbench |
| contractLedgerIdentifier | Adres van het contract op het grootboek                                            |
| verbindingId             | Unieke id voor de blockchain-verbinding                               |
| messageSchemaVersie     | Versie van het berichtenschema                                                         |
| berichtNaam              | **Contractupdate maken**                                                      |
| status                   | Status van het aanvraag voor het maken van contracten.  Mogelijke waarden: **Ingediend**, **Vastgelegd**, **Mislukt**.  |
| aanvullendeinformatie    | Aanvullende informatie op basis van de status                              |

Voorbeeld van een ingediende **contractrespons** maken vanuit Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Voorbeeld van een **vastgelegde contractrespons** maken vanuit Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Als het verzoek niet is mislukt, worden details over de fout opgenomen in aanvullende informatie.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Contractactie maken

Hiermee maakt u een nieuwe contractactie.

De aanvraag vereist de volgende velden:

| **Naam**                 | **Beschrijving**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | Client geleverd GUID |
| userChainIdentifier      | Adres van de gebruiker die is gemaakt op het blockchain-netwerk. In Ethereum is dit adres het adres van de gebruiker **op de ketting.** |
| contractLedgerIdentifier | Adres van het contract op het grootboek |
| versie                  | Versie van de toepassing. Vereist als u meerdere versies van de toepassing hebt ingeschakeld. Anders is de versie optioneel. Zie [Azure Blockchain Workbench-toepassingsversieing](version-app.md)voor meer informatie over het versiewerk van toepassingen in Azure Blockchain. |
| werkstroomFunctieNaam     | Naam van de werkstroomfunctie |
| parameters               | Parameters invoer voor het maken van contracten |
| verbindingId             | Unieke id voor de blockchain-verbinding |
| messageSchemaVersie     | Versie van het berichtenschema |
| berichtNaam              | **Contractactierequest maken** |

Voorbeeld:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench retourneert een antwoord met de volgende velden:

| **Naam**              | **Beschrijving**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | Client geleverd GUID|
| contractId            | Unieke id voor het contract in Azure Blockchain Workbench |
| verbindingId          | Unieke id voor de blockchain-verbinding |
| messageSchemaVersie  | Versie van het berichtenschema |
| berichtNaam           | **ContractactionUpdate maken** |
| status                | Status van het contractactieverzoek. Mogelijke waarden: **Ingediend**, **Vastgelegd**, **Mislukt**.                         |
| aanvullendeinformatie | Aanvullende informatie op basis van de status |

Voorbeeld van een ingediende **contractactiereactie** van Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Voorbeeld van een **geëngageerde contractactierespons** van Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Als het verzoek niet is mislukt, worden details over de fout opgenomen in aanvullende informatie.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Foutcodes en berichten van invoer-API

**Foutcode 4000: fout in slechte aanvragen**
- Ongeldige verbindingId
- Deserialisatie van CreateUserRequest is mislukt
- Deserialisatie van CreateContractRequest is mislukt
- Deserialisatie van CreateContractActionRequest is mislukt
- Toepassing {geïdentificeerd op naam van de toepassing} bestaat niet
- Toepassing {geïdentificeerd op toepassingsnaam} heeft geen werkstroom
- UserChainIdentifier bestaat niet
- Contract {geïdentificeerd door grootboek-id} bestaat niet
- Contract {geïdentificeerd door grootboek-id} heeft geen functie {werkstroomfunctienaam}
- UserChainIdentifier bestaat niet

**Foutcode 4090: conflictfout**
- Gebruiker bestaat al
- Contract bestaat al
- Er bestaat al een contractactie

**Foutcode 5000: interne serverfout**
- Uitzonderingsberichten

## <a name="event-notifications"></a>Gebeurtenismeldingen

Gebeurtenismeldingen kunnen worden gebruikt om gebruikers en downstreamsystemen op de hoogte te stellen van gebeurtenissen die plaatsvinden in Blockchain Workbench en het blockchain-netwerk waarmee het is verbonden. Gebeurtenismeldingen kunnen rechtstreeks in code worden verbruikt of worden gebruikt met hulpprogramma's zoals Logic Apps en Flow om de stroom van gegevens naar downstreamsystemen te activeren.

Zie [Berichtverwijzing voor meldingen](#notification-message-reference) voor informatie over verschillende berichten die kunnen worden ontvangen.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Gebeurtenisrastergebeurtenissen gebruiken met Azure-functies

Als een gebruiker Event Grid wil gebruiken om op de hoogte te worden gesteld van gebeurtenissen die plaatsvinden in Blockchain Workbench, u gebeurtenissen uit gebeurtenisraster gebruiken met Azure-functies.

1. Maak een **Azure Function App** in de Azure-portal.
2. Een nieuwe functie maken.
3. Zoek de sjabloon voor gebeurtenisraster. Basissjablooncode voor het lezen van het bericht wordt weergegeven. Wijzig de code indien nodig.
4. Sla de functie op. 
5. Selecteer het gebeurtenisraster uit de resourcegroep van Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Gebeurtenisrastergebeurtenissen consumeren met Logische apps

1. Maak een nieuwe **Azure Logic App** in de Azure-portal.
2. Wanneer u de Azure Logic App in de portal opent, wordt u gevraagd een trigger te selecteren. Selecteer **Azure Event Grid - Wanneer een resourcegebeurtenis plaatsvindt**.
3. Wanneer de werkstroomontwerper wordt weergegeven, wordt u gevraagd zich aan te melden.
4. Selecteer het abonnement. Resource als **Microsoft.EventGrid.Topics**. Selecteer de **resourcenaam** uit de naam van de bron uit de azure blockchain-werkbankbrongroep.
5. Selecteer het gebeurtenisraster uit de resourcegroep van Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Servicebusonderwerpen gebruiken voor meldingen

ServiceBus-onderwerpen kunnen worden gebruikt om gebruikers op de hoogte te stellen van gebeurtenissen die plaatsvinden in Blockchain Workbench. 

1. Blader naar de servicebus in de resourcegroep van de Workbench.
2. Selecteer **Onderwerpen**.
3. Selecteer **het uitgangsonderwerp**.
4. Maak een nieuw abonnement op dit onderwerp. Verkrijg er een sleutel voor.
5. Maak een programma dat zich abonneert op evenementen van dit abonnement.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Servicebusberichten consumeren met Logische apps

1. Maak een nieuwe **Azure Logic App** in de Azure-portal.
2. Wanneer u de Azure Logic App in de portal opent, wordt u gevraagd een trigger te selecteren. Typ **Servicebus** in het zoekvak en selecteer de trigger die geschikt is voor het type interactie dat u wilt hebben met de ServiceBus. Servicebus bijvoorbeeld **- Wanneer een bericht wordt ontvangen in een onderwerpabonnement (automatisch aanvullen).**
3. Wanneer de werkstroomontwerper wordt weergegeven, geeft u de verbindingsgegevens voor de servicebus op.
4. Selecteer uw abonnement en geef het onderwerp **werkbank-extern op.**
5. Ontwikkel de logica voor uw toepassing die gebruik maakt van het bericht van deze trigger.

## <a name="notification-message-reference"></a>Naslaginformatie over meldingsbericht

Afhankelijk van de **messageName**hebben de meldingsberichten een van de volgende berichttypen.

### <a name="block-message"></a>Bericht blokkeren

Bevat informatie over afzonderlijke blokken. De *BlockMessage* bevat een sectie met blokniveauinformatie en een sectie met transactiegegevens.

| Name | Beschrijving |
|------|-------------|
| blokkeren | Bevat [blokgegevens](#block-information) |
| transacties | Bevat [transactiegegevens](#transaction-information) voor het blok |
| verbindingId | Unieke id voor de verbinding |
| messageSchemaVersie | Versie van het berichtenschema |
| berichtNaam | **BlockMessage (Blokbericht)** |
| aanvullendeinformatie | Aanvullende informatie |

#### <a name="block-information"></a>Informatie blokkeren

| Name              | Beschrijving |
|-------------------|-------------|
| blockId blockId           | Unieke id voor het blok in Azure Blockchain Workbench |
| blokAantal       | Unieke id voor een blok op het grootboek |
| blockHash blockHash blockHash blockHash         | De hash van het blok |
| vorigeBlockHash | De hash van het vorige blok |
| blockTimestamp    | De tijdstempel van het blok |

#### <a name="transaction-information"></a>Transactiegegevens

| Name               | Beschrijving |
|--------------------|-------------|
| transactionId      | Unieke id voor de transactie in Azure Blockchain Workbench |
| transactionHash    | De hash van de transactie op het grootboek |
| from               | Unieke id op het grootboek voor de transactieoorsprong |
| tot                 | Unieke id op het grootboek voor de transactiebestemming |
| provisioningStatus | Hiermee wordt de huidige status van het inrichtingsproces voor de transactie aangegeven. Mogelijke waarden zijn: </br>0 – De transactie is gemaakt door de API in de database</br>1 – De transactie is verzonden naar het grootboek</br>2 – De transactie is met succes vastgelegd in het grootboek</br>3 of 4 - De transactie is niet vastgelegd in het grootboek</br>5 - De transactie is met succes vastgelegd in het grootboek |

Voorbeeld van een *BlockMessage* van Blockchain Workbench:

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>Contractbericht

Bevat informatie over een contract. Het bericht bevat een sectie met contracteigenschappen en een sectie met transactiegegevens. Alle transacties die het contract voor het betreffende blok hebben gewijzigd, zijn opgenomen in de transactiesectie.

| Name | Beschrijving |
|------|-------------|
| blockId blockId | Unieke id voor het blok in Azure Blockchain Workbench |
| blockHash blockHash blockHash blockHash | Hash van het blok |
| WijzigenTransacties wijzigen | [Transacties die](#modifying-transaction-information) het contract hebben gewijzigd |
| contractId | Unieke id voor het contract in Azure Blockchain Workbench |
| contractLedgerIdentifier | Unieke id voor het contract op het grootboek |
| contractEigenschappen | [Eigenschappen van het contract](#contract-properties) |
| isNewContract | Geeft aan of dit contract nieuw is gemaakt. Mogelijke waarden zijn: waar: dit contract was een nieuw contract gemaakt. false: dit contract is een contractupdate. |
| verbindingId | Unieke id voor de verbinding |
| messageSchemaVersie | Versie van het berichtenschema |
| berichtNaam | **ContractMessage** |
| aanvullendeinformatie | Aanvullende informatie |

#### <a name="modifying-transaction-information"></a>Transactiegegevens wijzigen

| Name               | Beschrijving |
|--------------------|-------------|
| transactionId | Unieke id voor de transactie in Azure Blockchain Workbench |
| transactionHash | De hash van de transactie op het grootboek |
| from | Unieke id op het grootboek voor de transactieoorsprong |
| tot | Unieke id op het grootboek voor de transactiebestemming |

#### <a name="contract-properties"></a>Contracteigenschappen

| Name               | Beschrijving |
|--------------------|-------------|
| werkstroomPropertyId | Unieke id voor de werkstroomeigenschap in Azure Blockchain Workbench |
| name | Naam van de eigenschap werkstroom |
| waarde | Waarde van de eigenschap werkstroom |

Voorbeeld van een *ContractMessage* van Blockchain Workbench:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>Gebeurtenisbericht: Aanroep van de functie Contract

Bevat informatie wanneer een contractfunctie wordt aangeroepen, zoals de functienaam, parametersinvoer en de beller van de functie.

| Name | Beschrijving |
|------|-------------|
| gebeurtenisNaam                   | **ContractfunctieInvocation** |
| Beller                      | [Bellergegevens](#caller-information) |
| contractId                  | Unieke id voor het contract in Azure Blockchain Workbench |
| contractLedgerIdentifier    | Unieke id voor het contract op het grootboek |
| functieNaam                | Naam van de functie |
| parameters                  | [Parameterinformatie](#parameter-information) |
| Transactie                 | Transactiegegevens |
| inTransactionSequenceNumber | Het volgnummer van de transactie in het blok |
| verbindingId                | Unieke id voor de verbinding |
| messageSchemaVersie        | Versie van het berichtenschema |
| berichtNaam                 | **EventMessage (EventMessage)** |
| aanvullendeinformatie       | Aanvullende informatie |

#### <a name="caller-information"></a>Bellergegevens

| Name | Beschrijving |
|------|-------------|
| type | Type van de beller, zoals een gebruiker of een contract |
| id | Unieke id voor de beller in Azure Blockchain Workbench |
| grootboekId | Unieke id voor de beller op het grootboek |

#### <a name="parameter-information"></a>Parameterinformatie

| Name | Beschrijving |
|------|-------------|
| name | Parameternaam |
| waarde | Parameterwaarde |

#### <a name="event-message-transaction-information"></a>Transactiegegevens van gebeurtenisberichten

| Name               | Beschrijving |
|--------------------|-------------|
| transactionId      | Unieke id voor de transactie in Azure Blockchain Workbench |
| transactionHash    | De hash van de transactie op het grootboek |
| from               | Unieke id op het grootboek voor de transactieoorsprong |
| tot                 | Unieke id op het grootboek voor de transactiebestemming |

Voorbeeld van een *EventMessage-contractFunctieInvocation* van Blockchain Workbench:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>Gebeurtenisbericht: Inname van toepassingen

Bevat informatie wanneer een toepassing wordt geüpload naar Workbench, zoals de naam en versie van de geüploade toepassing.

| Name | Beschrijving |
|------|-------------|
| gebeurtenisNaam | **ToepassingOpname** |
| applicationId | Unieke id voor de toepassing in Azure Blockchain Workbench |
| applicationName | De naam van de toepassing |
| toepassingsWeergavenaam | Naam van de weergave van toepassingen |
| applicationVersion | Toepassingsversie |
| applicationDefinitionLocatie | URL waar het configuratiebestand van de toepassing zich bevindt |
| contractCodes | Verzameling van [contractcodes](#contract-code-information) voor de aanvraag |
| toepassingsrollen | Verzameling van [toepassingsrollen](#application-role-information) voor de toepassing |
| toepassingsWerkstromen | Verzameling van [toepassingswerkstromen](#application-workflow-information) voor de toepassing |
| verbindingId | Unieke id voor de verbinding |
| messageSchemaVersie | Versie van het berichtenschema |
| berichtNaam | **EventMessage (EventMessage)** |
| aanvullendeinformatie | Aanvullende informatie die hier wordt verstrekt, omvat de statussen van de toepassingswerkstroom en overgangsinformatie. |

#### <a name="contract-code-information"></a>Contractcode-informatie

| Name | Beschrijving |
|------|-------------|
| id | Unieke id voor het contractcodebestand in Azure Blockchain Workbench |
| ledgerId | Unieke id voor het grootboek in Azure Blockchain Workbench |
| location | URL waar het bestand met contractcode zich bevindt |

#### <a name="application-role-information"></a>Informatie over toepassingsrol

| Name | Beschrijving |
|------|-------------|
| id | Unieke id voor de toepassingsrol in Azure Blockchain Workbench |
| name | Naam van de toepassingsrol |

#### <a name="application-workflow-information"></a>Gegevens over de werkstroom van toepassingen

| Name | Beschrijving |
|------|-------------|
| id | Unieke id voor de toepassingswerkstroom in Azure Blockchain Workbench |
| name | Naam van de werkstroom van toepassingen |
| displayName | Weergavenaam van toepassingswerkwerkstroom |
| Functies | Verzameling van [functies voor de toepassingswerkstroom](#workflow-function-information)|
| Staten | Verzameling van [statussen voor de toepassingswerkstroom](#workflow-state-information) |
| properties | [Informatie over eigenschappen van toepassingswerkstroom](#workflow-property-information) |

##### <a name="workflow-function-information"></a>Informatie over de werkstroomfunctie

| Name | Beschrijving |
|------|-------------|
| id | Unieke id voor de toepassingswerkstroomfunctie in Azure Blockchain Workbench |
| name | Functienaam |
| parameters | Parameters voor de functie |

##### <a name="workflow-state-information"></a>Gegevens over werkstroomstatus

| Name | Beschrijving |
|------|-------------|
| name | Staatsnaam |
| displayName | Naam van de statusweergave |
| stijl | Staatsstijl (succes of mislukking) |

##### <a name="workflow-property-information"></a>Informatie over de eigenschap Workflow

| Name | Beschrijving |
|------|-------------|
| id | Unieke id voor de eigenschap toepassingswerkstroom in Azure Blockchain Workbench |
| name | Naam van eigenschap |
| type | Eigenschappentype |

Voorbeeld van een *EventMessage ApplicationIngestion* van Blockchain Workbench:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationDefinitionLocation": "http://url",
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                        "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                        "name": "int"
                     }
                }
            ]
        }
    ],
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept",
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>Gebeurtenisbericht: Roltoewijzing

Bevat informatie wanneer een gebruiker een rol in Workbench krijgt toegewezen, zoals wie de roltoewijzing heeft uitgevoerd en de naam van de rol en de bijbehorende toepassing.

| Name | Beschrijving |
|------|-------------|
| gebeurtenisNaam | **RoleAssignment** |
| applicationId | Unieke id voor de toepassing in Azure Blockchain Workbench |
| applicationName | De naam van de toepassing |
| toepassingsWeergavenaam | Naam van de weergave van toepassingen |
| applicationVersion | Toepassingsversie |
| toepassingRol        | Informatie over de [toepassingsrol](#roleassignment-application-role) |
| assigner               | Informatie over de [toewijser](#roleassignment-assigner) |
| Gevolmachtigde               | Informatie over de [cessionaris](#roleassignment-assignee) |
| verbindingId           | Unieke id voor de verbinding |
| messageSchemaVersie   | Versie van het berichtenschema |
| berichtNaam            | **EventMessage (EventMessage)** |
| aanvullendeinformatie  | Aanvullende informatie |

#### <a name="roleassignment-application-role"></a>Rol RoleAssignment-toepassing

| Name | Beschrijving |
|------|-------------|
| id | Unieke id voor de toepassingsrol in Azure Blockchain Workbench |
| name | Naam van de toepassingsrol |

#### <a name="roleassignment-assigner"></a>Toewijzingstoewijser RoleAssignment

| Name | Beschrijving |
|------|-------------|
| id | Unieke id van de gebruiker in Azure Blockchain Workbench |
| type | Type van de toewijssmachine |
| chainIdentifier | Unieke id van de gebruiker op het grootboek |

#### <a name="roleassignment-assignee"></a>Toegewezen opdracht voor RoleAssignment

| Name | Beschrijving |
|------|-------------|
| id | Unieke id van de gebruiker in Azure Blockchain Workbench |
| type | Type van de cessionaris |
| chainIdentifier | Unieke id van de gebruiker op het grootboek |

Voorbeeld van een *EventMessage RoleAssignment* van Blockchain Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>Volgende stappen

- [Integratiepatronen voor slimme contracten](integration-patterns.md)
