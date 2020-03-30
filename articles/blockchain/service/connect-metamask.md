---
title: MetaMask verbinden met een Azure Blockchain-servicenetwerk
description: Maak verbinding met een Azure Blockchain Service-netwerk met MetaMask en implementeer een slim contract.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: c0bad9efde44ce53f6b0656af3ac4af32ffe051d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79205099"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Snelstart: MetaMask gebruiken om een slim contract aan te sluiten en te implementeren

In deze quickstart gebruikt u MetaMask om verbinding te maken met een Azure Blockchain Service-netwerk en remix te gebruiken om een slim contract te implementeren. Metamask is een browserextensie om een Ether-portemonnee te beheren en slimme contractacties uit te voeren.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Snelstart [voltooien: een blockchain-lid maken met behulp van de Azure-portal](create-member.md) of [Quickstart: een Azure Blockchain Service-blockchain-lid maken met Azure CLI](create-member-cli.md)
* [MetaMask-browserextensie installeren](https://metamask.io)
* Een MetaMask-portemonnee [genereren](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time)

## <a name="get-endpoint-address"></a>Eindpuntadres opvragen

U hebt het Azure Blockchain Service-eindpuntadres nodig om verbinding te maken met het blockchain-netwerk. Het eindpuntadres en de toegangssleutels bevinden zich in de Azure-portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer naar uw Azure Blockchain Service-lid.
1. Selecteer **Transactieknooppunten** en de standaardkoppeling transactieknooppunt.

    ![Standaardtransactieknooppunt selecteren](./media/connect-metamask/transaction-nodes.png)

1. Selecteer **Verbindingstekenreeksen > Toegangstoetsen**.
1. Kopieer het eindpuntadres vanuit **HTTPS (Access-toets 1)**.

    ![Verbindingsreeks](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Maak metamasker verbinding

1. Open metamask-browserextensie en meld u aan.
1. Selecteer in de vervolgkeuzelijst van het netwerk **Aangepaste RPC**.

    ![Aangepaste RPC](./media/connect-metamask/custom-rpc.png)

1. Plak in **Nieuwe netwerk> Nieuwe RPC-URL**het eindpuntadres dat u hierboven hebt gekopieerd.
1. Selecteer **Opslaan**.

    Als de verbinding is gelukt, wordt het privénetwerk weergegeven in de vervolgkeuzelijst van het netwerk.

    ![Nieuw netwerk](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Slim contract implementeren

Remix is een browser-gebaseerde Solidity ontwikkelomgeving. Met Behulp van MetaMask en Remix samen, u implementeren en acties ondernemen op slimme contracten.

1. Ga in de browser naar `https://remix.ethereum.org`.
1. Selecteer **Nieuw bestand** op het tabblad **Start** onder **Bestand**.

    Geef het `simple.sol`nieuwe bestand een naam.

    ![Bestand maken](./media/connect-metamask/create-file.png)

    Selecteer **OK**.
1. Plak in de Remix-editor de volgende **eenvoudige slimme contractcode** in.

    ```solidity
    pragma solidity ^0.5.0;
             
    contract simple {
        uint balance;
                 
        constructor() public{
            balance = 0;
        }
                 
        function add(uint _num) public {
            balance += _num;
        }
                 
        function get() public view returns (uint){
            return balance;
        }
    }
    ```

    Het **eenvoudige contract** verklaart een staatvariabele met de naam **saldo**. Er zijn twee functies gedefinieerd. De functie **Toevoegen** voegt een getal toe om **in evenwicht**te brengen . De functie **Opdoen,** als resultaat de waarde van **het saldo**.
1. Als u het contract wilt compileren, selecteert u eerst het deelvenster Soliditeitcompiler en selecteert u vervolgens de **compiler simple.sol compileren.**

    ![Compileren](./media/connect-metamask/compile.png)

1. Selecteer het deelvenster **Implementeren & Uitvoeren** en stel de **omgeving** in op **Injected Web3** om verbinding te maken via MetaMask met uw blockchain-lid.

    ![Tabblad Uitvoeren](./media/connect-metamask/injected-web3.png)

1. Selecteer het **eenvoudige** contract en **vervolgens Implementeren**.

    ![Implementeren](./media/connect-metamask/deploy.png)


1. Een MetaMask-melding waarschuwt u van onvoldoende middelen om de transactie uit te voeren.

    Voor een openbaar blockchain-netwerk hebt u Ether nodig om de transactiekosten te betalen. Aangezien dit een privénetwerk in een consortium is, u de gasprijs op nul instellen.

1.  Selecteer **Gasfee > Bewerk > Advanced**, stel de **gasprijs in** op 0.

    ![Gasprijs](./media/connect-metamask/gas-price.png)

    Selecteer **Opslaan**.

1. Selecteer **Bevestigen** om het slimme contract te implementeren in de blockchain.
1. Vouw in de sectie **Geïmplementeerde contracten** het **eenvoudige** contract uit.

    ![Geïmplementeerd contract](./media/connect-metamask/deployed-contract.png)

    Twee acties, **toevoegen** en **krijgen,** toewijzen aan de functies gedefinieerd in het contract.

1. Als u een **add-transactie** op de blockchain wilt uitvoeren, voert u een getal in om toe te voegen en selecteert u **Toevoegen**. U een gasschatting mislukking bericht van Remix: U stuurt de transactie naar een prive-blockchain die geen gas nodig heeft. Selecteer **Transactie verzenden** om de transactie te forceren.
1. Net als toen u het contract hebt geïmplementeerd, waarschuwt een MetaMask-melding u van onvoldoende middelen om de transactie uit te voeren.

    Aangezien dit een particulier netwerk in een consortium is, kunnen we de gasprijs op nul zetten.

1. Selecteer **Gasfee > Bewerk > Geavanceerd,** stel de **gasprijs** in op 0 en selecteer **Opslaan**.
1. Selecteer **Bevestigen** om de transactie naar de blockchain te verzenden.
1. Selecteer **actie uitvoeren.** Dit is een oproep om knooppuntgegevens op te vragen. Een transactie is niet nodig.

Het foutopsporingsvenster van Remix toont details over de transacties op de blockchain:

    ![Debug history](./media/connect-metamask/debug.png)

    You can see the **simple** contract creation, transaction for **simple.add**, and call to **simple.get**.

Als u de transactiegeschiedenis in MetaMask wilt bekijken, opent u de metamask-browserextensie en zoekt u in de sectie **Geschiedenis** naar een logboek van het geïmplementeerde contract en transacties.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de MetaMask-browserextensie gebruikt om verbinding te maken met een Azure Blockchain Service-transactieknooppunt, een slim contract te implementeren en een transactie naar de blockchain te verzenden. Probeer de volgende zelfstudie om Azure Blockchain Development Kit voor Ethereum en Truffle te gebruiken om een slimme contractfunctie te maken, te bouwen, implementeren en uitvoeren via een transactie.

> [!div class="nextstepaction"]
> [Slimme contracten maken, bouwen en implementeren op Azure Blockchain Service](send-transaction.md)