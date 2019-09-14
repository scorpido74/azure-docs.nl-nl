---
title: Een Block chain-netwerk verbinden met een Azure-service
description: Verbinding maken met een Azure Block Chain service-netwerk met behulp van het gebruik van een gemask en een slim contract implementeren.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/12/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: b89e75d406a738fb685bb3294dca8d79a2b9170c
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966553"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Quickstart: Een slim contract gebruiken om verbinding te maken en te implementeren

In deze Quick Start gebruikt u het block chain om verbinding te maken met een Azure-service netwerk en kunt u Remix gebruiken om een slim contract te implementeren. Het-opdracht masker is een browser uitbreiding voor het beheren van een ether wallet en het uitvoeren van actie-acties voor intelligente contracten.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Volledige [Snelstartgids: Maak een Block Chain-lid met behulp [van de Azure Portal](create-member.md) of Quick Start: Een Block Chain-lid van de Azure Block Chain-service maken met behulp van Azure CLI](create-member-cli.md)
* De [extensie van de Webmask-browser](https://metamask.io) installeren
* Een demaskeer [Wallet](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) genereren

## <a name="get-endpoint-address"></a>Eindpunt adres ophalen

U hebt het Azure Block Chain service-eindpunt adres nodig om verbinding te maken met het block chain-netwerk. U vindt het eindpunt adres en toegangs sleutels in de Azure Portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer naar uw Azure Block Chain service-lid. Selecteer **transactie knooppunten** en de koppeling standaard transactie knooppunt.

    ![Standaard transactie knooppunt selecteren](./media/connect-metamask/transaction-nodes.png)

1. Selecteer **verbindings reeksen > toegangs sleutels**.
1. Kopieer het eindpunt adres van **https (toegangs sleutel 1)** . U hebt het adres voor de volgende sectie nodig.

    ![Verbindingsreeks](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Het verbinden van het-bitmapmasker

1. Open de module voor het demaskeren van de browser en meld u aan.
1. Selecteer in de vervolg keuzelijst netwerk de optie **aangepaste RPC**.

    ![Aangepaste RPC](./media/connect-metamask/custom-rpc.png)

1. Voer in **nieuw netwerk > nieuwe RPC-URL**het adres van het eind punt in dat u uit de vorige sectie hebt gekopieerd.
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


1. Er wordt een melding over een gemaskeerde waarschuwing weer gegeven met de melding dat er onvoldoende middelen zijn om de trans actie uit te voeren.

    Voor een openbaar Block chain-netwerk moet u ether hebben om de transactie kosten te betalen. Aangezien dit een privé netwerk in een consortium is, kunt u de aardgas prijs instellen op nul.

1.  Selecteer de kosten voor het **gas > bewerk > Geavanceerd**en stel de **gas-prijs** in op 0.

    ![Benzine prijs](./media/connect-metamask/gas-price.png)

    Selecteer **Opslaan**.

1. Selecteer **bevestigen** om het slimme contract te implementeren op de Block chain.
1. Vouw in het gedeelte **geïmplementeerde contracten** het **eenvoudige** contract uit.

    ![Geïmplementeerd contract](./media/connect-metamask/deployed-contract.png)

    Er zijn twee **acties waarmee u** deze kaart kunt **toevoegen** aan de functies die in het contract zijn gedefinieerd.

1. Als u een trans actie **toevoegen** wilt uitvoeren op de Block Chain, voert u een nummer in dat u wilt toevoegen en selecteert u vervolgens **toevoegen**. U ontvangt mogelijk een fout bericht over een gas-schatting van Remix. U stuurt de trans actie naar een persoonlijke Block chain die geen gas vereist. Selecteer **trans actie verzenden** om de trans actie af te dwingen.
1. Net als bij het implementeren van het contract wordt er een melding over een gemaskeerde waarschuwing weer gegeven waarin u wordt gewaarschuwd dat er onvoldoende middelen zijn om de trans actie uit te voeren.

    Aangezien dit een privé netwerk in een consortium is, kunnen we de prijs van een gas instellen op nul.

1.  Selecteer de kosten voor het **gas > bewerk > Geavanceerd**, stel de **aardgas prijs** in op 0 en selecteer **Opslaan**.
1. Selecteer **bevestigen** om de trans actie naar het block chain te verzenden.
1. Selecteer actie **ophalen** . Dit is een aanroep voor het opvragen van knooppunt gegevens. Een trans actie is niet nodig.
1. In het deel venster fout opsporing van Remix ziet u details over de trans acties in de Block chain.

    ![Geschiedenis van fout opsporing](./media/connect-metamask/debug.png)

    U kunt het **eenvoudige** contract maken, de trans actie **eenvoudig. toevoegen**en aanroepen naar **Simple. Get**.

1. U kunt ook de transactie geschiedenis bekijken in het gemask. Open de module voor de webmasker browser.
1. In het gedeelte **geschiedenis** ziet u een logboek van het geïmplementeerde contract en de gedistribueerde trans acties.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u de module voor het maken van een webmask-browser gebruikt voor verbinding met een Azure Block Chain-Service transactie knooppunt, een slim contract implementeren en een trans actie naar de Block Chain verzenden. Probeer de volgende zelf studie voor het gebruik van Azure Block Chain Development Kit voor Ethereum en Truffle voor het maken, bouwen, implementeren en uitvoeren van een slimme contract functie via een trans actie.

> [!div class="nextstepaction"]
> [Visual Studio code gebruiken om slimme contracten te maken, te bouwen en te implementeren](send-transaction.md)