---
title: Visual Studio code-Azure Block Chain service gebruiken
description: Zelf studie over het gebruik van de Azure Block Chain Development Kit voor Ethereum-extensie in Visual Studio code voor het maken, bouwen en implementeren van een slim contract op de Azure Block Chain-service.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
ms.openlocfilehash: 13a5993a14e386dc7d24c7464610bbf1ace4b9cb
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329248"
---
# <a name="tutorial-usevisual-studio-code-to-create-buildanddeploysmartcontracts"></a>Zelf studie: Visual Studio code gebruiken om slimme contracten te maken, te bouwen en te implementeren

In deze zelf studie gebruikt u de Azure Block Chain Development Kit voor Ethereum-extensie in Visual Studio code voor het maken, bouwen en implementeren van een slim contract op de Azure Block Chain-service. U kunt Truffle ook gebruiken om een slimme contract functie uit te voeren via een trans actie.

U gebruikt Azure Block Chain Development Kit voor Ethereum voor het volgende:

> [!div class="checklist"]
> * Een slim contract maken
> * Een slim contract implementeren
> * Een slimme contract functie uitvoeren via een trans actie
> * Query contract status

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Volledige [Snelstartgids: Visual Studio code gebruiken om verbinding te maken met een Azure Block Chain Service consortium-netwerk](connect-vscode.md)

## <a name="create-a-smart-contract"></a>Een slim contract maken

De Azure Block Chain Development Kit voor Ethereum gebruikt Project sjablonen en Truffle-hulpprogram ma's om contracten te helpen, te bouwen en te implementeren. Voordat u begint, voltooit u de vereiste [Snelstartgids: gebruik Visual Studio code om verbinding te maken met een Azure Block Chain Service consortium-netwerk](connect-vscode.md). De Snelstartgids helpt u bij de installatie en configuratie van de Azure Block Chain Development Kit voor Ethereum.

1. Kies in het palet met de VS code-opdracht **Azure Block chain: New volheid project**.
1. Kies **basis project maken**.
1. Maak een nieuwe map met de naam `HelloBlockchain` en **Selecteer Nieuw pad**voor het project.

De Azure Block Chain Development Kit maakt en initialiseert een nieuw solide project voor u. Het basis project bevat een voor beeld van een **HelloBlockchain** slim contract en alle benodigde bestanden voor het bouwen en implementeren van uw consortium in de Azure Block Chain-service. Het kan enkele minuten duren voordat het project is gemaakt. U kunt de voortgang in het Terminal paneel van VS code controleren door de uitvoer voor Azure Block chain te selecteren.

De project structuur ziet eruit als in het volgende voor beeld:

   ![Volheid project](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Een slim contract bouwen

Slimme contracten bevinden zich in de map **contracten** van het project. U kunt slimme contracten compileren voordat u ze implementeert in een Block chain. Gebruik de opdracht **contracten bouwen** om alle slimme contracten in uw project te compileren.

1. Vouw in de VS code Explorer-zijbalk de map **contracten** in uw project uit.
1. Klik met de rechter muisknop op **HelloBlockchain. Sol** en kies **contracten maken** in het menu.

    ![Contracten samen stellen](./media/send-transaction/build-contracts.png)

Azure Block Chain Development Kit maakt gebruik van truffle om de slimme contracten te compileren.

![Uitvoer compileren](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Een slim contract implementeren

Truffle maakt gebruik van migratie scripts om uw contracten te implementeren in een Ethereum-netwerk. Migraties zijn Java script-bestanden die zich in de map **migraties** van het project bevinden.

1. Als u uw slimme contract wilt implementeren, klikt u met de rechter muisknop op **HelloBlockchain. Sol** en kiest u **contracten implementeren** in het menu.
1. Kies uw Azure Block Chain consortium-netwerk in het opdracht palet. Het consortium Block chain-netwerk is toegevoegd aan het Truffle-configuratie bestand van het project wanneer u het project hebt gemaakt.
1. Kies **instructie genereren**. Kies een bestands naam en sla het bestand voor de instructie op in de projectmap. Bijvoorbeeld `myblockchainmember.env`. Het instructie bestand wordt gebruikt voor het genereren van een persoonlijke sleutel Ethereum voor uw Block Chain-lid.

Azure Block Chain Development Kit maakt gebruik van truffle om het migratie script uit te voeren om de contracten te implementeren op de Block chain.

![Het contract is geïmplementeerd](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Een contract functie aanroepen

De functie **SendRequest** van het **HelloBlockchain** -contract wijzigt de variabele **RequestMessage** -status. Het wijzigen van de status van een Block chain-netwerk wordt uitgevoerd via een trans actie. U kunt een script maken om de functie **SendRequest** uit te voeren via een trans actie.

1. Maak een nieuw bestand in de hoofdmap van uw Truffle-project en geef het de naam `sendrequest.js`. Voeg de volgende Web3 java script-code toe aan het bestand.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Wanneer Azure Block Chain Development Kit een project maakt, wordt het Truffle-configuratie bestand gegenereerd met de details van uw consortium Block chain-netwerk eindpunt. Open **Truffle-config. js** in uw project. In het configuratie bestand worden twee netwerken weer gegeven: een met de naam ontwikkeling en een met dezelfde namen als het consortium.
1. Gebruik in het Terminal venster van VS code Truffle om het script uit te voeren op uw consortium Block chain-netwerk. Selecteer in de menu balk van het Terminal venster het tabblad **Terminal** en **Power shell** in de vervolg keuzelijst.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Vervang \<blockchain Network @ no__t-1 door de naam van het block chain-netwerk dat is gedefinieerd in **Truffle-config. js**.

Truffle voert het script uit op uw Block chain-netwerk.

![Script uitvoer](./media/send-transaction/execute-transaction.png)

Wanneer u de functie van een contract uitvoert via een trans actie, wordt de trans actie niet verwerkt totdat er een blok is gemaakt. Functies die via een trans actie moeten worden uitgevoerd, retour neren een trans actie-ID in plaats van een retour waarde.

## <a name="query-contract-state"></a>Query contract status

Met de functies van een slimme opdracht kan de huidige waarde van de status variabelen worden geretourneerd. We gaan een functie toevoegen om de waarde van een status variabele te retour neren.

1. Voeg in **HelloBlockchain. Sol**een **GetMessage** -functie toe aan het slimme **HelloBlockchain** -contract.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    De functie retourneert het bericht dat is opgeslagen in een status variabele op basis van de huidige status van het contract.

1. Klik met de rechter muisknop op **HelloBlockchain. Sol** en kies **contracten maken** in het menu om de wijzigingen in het slimme contract te compileren.
1. Als u wilt implementeren, klikt u met de rechter muisknop op **HelloBlockchain. Sol** en kiest u **contracten implementeren** in het menu. Kies uw Azure Block Chain consortium-netwerk in het opdracht palet wanneer u hierom wordt gevraagd.
1. Maak vervolgens een script dat wordt gebruikt om de functie **GetMessage** aan te roepen. Maak een nieuw bestand in de hoofdmap van uw Truffle-project en geef het de naam `getmessage.js`. Voeg de volgende Web3 java script-code toe aan het bestand.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Gebruik in het Terminal venster van VS code Truffle om het script uit te voeren op uw Block chain-netwerk. Selecteer in de menu balk van het Terminal venster het tabblad **Terminal** en **Power shell** in de vervolg keuzelijst.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Vervang \<blockchain Network @ no__t-1 door de naam van het block chain-netwerk dat is gedefinieerd in **Truffle-config. js**.

Het script voert een query uit op het slimme contract door de getMessage-functie aan te roepen. De huidige waarde van de status variabele **RequestMessage** wordt geretourneerd.

![Script uitvoer](./media/send-transaction/execute-get.png)

Let op: de waarde is niet **Hello, Block Chain!** . In plaats daarvan is de geretourneerde waarde een tijdelijke aanduiding. Wanneer u het contract wijzigt en implementeert, haalt het contract een nieuw contract adres op en worden aan de status variabelen waarden toegewezen in de Smart contract-constructor. Het Truffle-voor beeld- **2_deploy_contracts. js** -migratie script implementeert het slimme contract en geeft een tijdelijke aanduiding door als argument. De constructor stelt de **RequestMessage** -status variabele in op de waarde van de tijdelijke aanduiding en dat is wat er wordt geretourneerd.

1. Als u de **RequestMessage** -status variabele wilt instellen en de waarde wilt opvragen, voert u de scripts **SendRequest. js** en **GetMessage. js** opnieuw uit.

    ![Script uitvoer](./media/send-transaction/execute-set-get.png)

    **SendRequest. js** stelt de **RequestMessage** -status variabele in op **Hello, Block Chain!** en **GetMessage. js** vraagt het contract om de waarde van de **RequestMessage** -status variabele en retourneert **Hello, Block Chain!** .

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de resources verwijderen door de `myResourceGroup`-Resource groep te verwijderen die u hebt gemaakt in de Snelstartgids *een Block Chain-lid maken* .

De resource groep verwijderen:

1. Ga in het Azure Portal naar de **resource groep** in het navigatie deel venster links en selecteer de resource groep die u wilt verwijderen.
1. Selecteer **Resourcegroep verwijderen**. Controleer het verwijderen door de naam van de resource groep in te voeren en **verwijderen**te selecteren.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een voor beeld van een volheid project gemaakt met behulp van de Azure Block Chain Development Kit. U hebt een slim contract gemaakt en geïmplementeerd. dit wordt een functie genoemd via een trans actie op een Block Chain consortium-netwerk dat wordt gehost op de Azure Block Chain-service.

> [!div class="nextstepaction"]
> [Block Chain-toepassingen ontwikkelen met behulp van de Azure Block Chain-Service](develop.md)
