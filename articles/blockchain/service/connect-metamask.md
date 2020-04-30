---
title: Een Block chain-netwerk verbinden met een Azure-service
description: Verbinding maken met een Azure Block Chain service-netwerk met behulp van het gebruik van een gemask en een slim contract implementeren.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: c0bad9efde44ce53f6b0656af3ac4af32ffe051d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79205099"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Snelstartgids: een slim masker gebruiken om verbinding te maken en een intelligent contract te implementeren

In deze Snelstartgids gebruikt u het block chain om verbinding te maken met een Azure-service netwerk en kunt u Remix gebruiken om een slim contract te implementeren. Het-opdracht masker is een browser uitbreiding voor het beheren van een ether wallet en het uitvoeren van actie-acties voor intelligente contracten.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Volledige [Snelstartgids: Maak een Block Chain-lid met behulp van de Azure Portal](create-member.md) of [Quick Start: een Azure Block Chain Service Block Chain-lid maken met behulp van Azure cli](create-member-cli.md)
* De [extensie van de Webmask-browser](https://metamask.io) installeren
* Een demaskeer [Wallet](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) genereren

## <a name="get-endpoint-address"></a>Eindpunt adres ophalen

U hebt het Azure Block Chain service-eindpunt adres nodig om verbinding te maken met het block chain-netwerk. Het eindpunt adres en de toegangs sleutels bevinden zich in de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Navigeer naar uw Azure Block Chain service-lid.
1. Selecteer **transactie knooppunten** en de koppeling standaard transactie knooppunt.

    ![Standaard transactie knooppunt selecteren](./media/connect-metamask/transaction-nodes.png)

1. Selecteer **verbindings reeksen > toegangs sleutels**.
1. Kopieer het eindpunt adres van **https (toegangs sleutel 1)**.

    ![Verbindingsreeks](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Het verbinden van het-bitmapmasker

1. Open de module voor het demaskeren van de browser en meld u aan.
1. Selecteer in de vervolg keuzelijst netwerk de optie **aangepaste RPC**.

    ![Aangepaste RPC](./media/connect-metamask/custom-rpc.png)

1. Plak in **nieuw netwerk > nieuwe RPC-URL**het eindpunt adres dat u hierboven hebt gekopieerd.
1. Selecteer **Opslaan**.

    Als de verbinding is geslaagd, wordt het particuliere netwerk weer gegeven in de vervolg keuzelijst netwerk.

    ![Nieuw netwerk](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Slim contract implementeren

Remix is een op een browser gebaseerde omgeving voor het ontwikkelen van volheid. U kunt met behulp van het gebruik van het Remix en de combi natie van intelligente contracten acties uitvoeren.

1. Ga in de browser naar `https://remix.ethereum.org`.
1. Selecteer in het tabblad **Start** onder **bestand**de optie **nieuw bestand** .

    Geef het nieuwe bestand `simple.sol`een naam.

    ![Bestand maken](./media/connect-metamask/create-file.png)

    Selecteer **OK**.
1. Plak in de Remix-editor de volgende **eenvoudige info contract** code.

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

    Het **eenvoudige contract** declareert een status variabele met de naam **saldo**. Er zijn twee functies gedefinieerd. Met de functie **toevoegen** wordt een getal toegevoegd om te **verdelen**. De functie **Get** retourneert de waarde **saldo**.
1. Als u het contract wilt compileren, selecteert u eerst het deel venster volheid compiler en selecteert u vervolgens **Simple. Sol compileren**.

    ![Verzamelen](./media/connect-metamask/compile.png)

1. Selecteer het deel venster **implementatie & uitvoeren** en stel vervolgens de **omgeving** in op **injected Web3** om verbinding te maken via het block Chain-lid.

    ![Tabblad uitvoeren](./media/connect-metamask/injected-web3.png)

1. Selecteer het **eenvoudige** contract en **Implementeer**het.

    ![Implementeren](./media/connect-metamask/deploy.png)


1. Een-bericht van een-tekst masker waarschuwt u over onvoldoende middelen om de trans actie uit te voeren.

    Voor een openbaar Block chain-netwerk moet u ether hebben om de transactie kosten te betalen. Aangezien dit een privé netwerk in een consortium is, kunt u de aardgas prijs instellen op nul.

1.  Selecteer de kosten voor het **gas > bewerk > Geavanceerd**en stel de **gas-prijs** in op 0.

    ![Benzine prijs](./media/connect-metamask/gas-price.png)

    Selecteer **Opslaan**.

1. Selecteer **bevestigen** om het slimme contract te implementeren op de Block chain.
1. Vouw in het gedeelte **geïmplementeerde contracten** het **eenvoudige** contract uit.

    ![Geïmplementeerd contract](./media/connect-metamask/deployed-contract.png)

    Twee acties, **toevoegen** en **ophalen**, toewijzen aan de functies die zijn gedefinieerd in het contract.

1. Als u een trans actie **toevoegen** wilt uitvoeren op de Block Chain, voert u een nummer in dat u wilt toevoegen en selecteert u vervolgens **toevoegen**. U ontvangt mogelijk een fout bericht over het oplossen van een gas van Remix: "u stuurt de trans actie naar een privé Block chain die geen gas vereist." Selecteer **trans actie verzenden** om de trans actie af te dwingen.
1. Net als bij het implementeren van het contract, wordt u met een informatie over een-Adresmask gewaarschuwd dat er onvoldoende middelen zijn om de trans actie uit te voeren.

    Aangezien dit een privé netwerk in een consortium is, kunnen we de prijs van een gas instellen op nul.

1. Selecteer de kosten voor het **gas > bewerk > Geavanceerd**, stel de **aardgas prijs** in op 0 en selecteer **Opslaan**.
1. Selecteer **bevestigen** om de trans actie naar het block chain te verzenden.
1. Selecteer actie **ophalen** . Dit is een aanroep voor het opvragen van knooppunt gegevens. Een trans actie is niet nodig.

Het deel venster fout opsporing van Remix toont details over de trans acties op de Block chain:

    ![Debug history](./media/connect-metamask/debug.png)

    You can see the **simple** contract creation, transaction for **simple.add**, and call to **simple.get**.

Als u de transactie geschiedenis in het deel venster wilt zien, opent u de module voor het indeel masker browser en zoekt u in het gedeelte **geschiedenis** naar een logboek van het geïmplementeerde contract en de gedistribueerde trans acties.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u de module voor het maken van een webmask-browser gebruikt voor verbinding met een Azure Block Chain-Service transactie knooppunt, een slim contract implementeren en een trans actie naar de Block Chain verzenden. Probeer de volgende zelf studie voor het gebruik van Azure Block Chain Development Kit voor Ethereum en Truffle voor het maken, bouwen, implementeren en uitvoeren van een slimme contract functie via een trans actie.

> [!div class="nextstepaction"]
> [Slimme contracten maken, bouwen en implementeren in azure Block Chain Service](send-transaction.md)