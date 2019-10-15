---
title: Azure Block Chain Development Kit gebruiken voor Ethereum-Azure Block Chain-Service
description: Verbinding maken met een Azure Block Chain Service consortium-netwerk met behulp van de Azure Block Chain Development Kit for Ethereum-extensie in Visual Studio code
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 6364e887c699219d80974d592a8ff7c77cca2621
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329301"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Snelstartgids: Visual Studio code gebruiken om verbinding te maken met een Azure Block Chain Service consortium-netwerk

In deze Quick Start installeert en gebruikt u de Azure Block Chain Development Kit voor Ethereum Visual Studio code extension om te koppelen aan een consortium op Azure Block Chain service. De Azure Block Chain Development Kit vereenvoudigt het maken, verbinden, bouwen en implementeren van slimme contracten op Ethereum-grootten. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Volledige [Snelstartgids: Maak een Block Chain-lid met behulp van de Azure Portal](create-member.md) of [Quick Start: een Azure Block Chain Service Block Chain-lid maken met behulp van Azure cli](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure Block Chain Development Kit voor Ethereum-uitbrei ding](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js](https://nodejs.org)
* [Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/). Voeg python. exe toe aan het pad. Python in het pad is vereist voor Azure Block Chain Development Kit.
* [Truffle](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI](https://github.com/trufflesuite/ganache-cli)

### <a name="verify-azure-blockchain-development-kit-environment"></a>De Azure Block Chain Development Kit-omgeving controleren

In azure Block Chain Development Kit wordt gecontroleerd of aan de vereisten van uw ontwikkel omgeving is voldaan. Controleren of uw ontwikkel omgeving:

Kies in het palet met de VS code-opdracht **Azure Block chain: Welkomst pagina weer geven**.

Azure Block Chain Development Kit voert een validatie script uit dat ongeveer een minuut in beslag neemt. U kunt de uitvoer weer geven door **Terminal > New Terminal**te selecteren. Selecteer in de menu balk van de Terminal het tabblad **uitvoer** en **Azure Block Chain** in de vervolg keuzelijst. Geslaagde validatie ziet eruit als in de volgende afbeelding:

![Geldige ontwikkel omgeving](./media/connect-vscode/valid-environment.png)

 Als u een vereist hulp programma mist, wordt een nieuw tabblad met de naam **Azure Block Chain Development Kit** weer gegeven met de vereiste apps die moeten worden geïnstalleerd en koppelingen voor het downloaden van de hulpprogram ma's.

![Ontwikkel Kit vereiste apps](./media/connect-vscode/required-apps.png)

Installeer de ontbrekende vereiste onderdelen voordat u doorgaat met de Snelstartgids.

## <a name="connect-to-consortium-member"></a>Verbinding maken met het consortium

U kunt verbinding maken met consortium leden met behulp van de Azure Block Chain Development Kit VS code extension. Wanneer u bent verbonden met een consortium, kunt u slimme contracten compileren, bouwen en implementeren in een Azure Block Chain Service consortium-lid.

Als u geen toegang hebt tot een lid van een Azure Block Chain Service consortium, voltooit u de vereiste [Snelstartgids: een Block Chain-lid maken met behulp van de Azure Portal](create-member.md) of [Quick Start: een Azure Block Chain-Service Block Chain-lid maken met behulp van Azure cli ](create-member-cli.md).

1. Vouw in het deel venster Visual Studio code (VS code) Explorer de **Azure Block Chain** -extensie uit.
1. Selecteer **verbinding maken met consortium**.

   ![Verbinding maken met consortium](./media/connect-vscode/connect-consortium.png)

    Als u wordt gevraagd om Azure-verificatie, volgt u de prompts voor verificatie met behulp van een browser.
1. Kies **verbinding maken met Azure Block Chain Service consortium** in de vervolg keuzelijst voor het opdracht palet.
1. Kies het abonnement en de resource groep die aan uw Azure Block Chain Service consortium-lid zijn gekoppeld.
1. Kies uw consortium in de lijst.

De consortium-en block Chain-leden worden weer gegeven in de Visual Studio Explorer-balk.

![Consortium weer gegeven in Verkenner](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u Azure Block Chain Development Kit voor Ethereum Visual Studio code extension gebruikt om te koppelen aan een consortium op Azure Block Chain-service. Probeer de volgende zelf studie voor het gebruik van Azure Block Chain Development Kit voor Ethereum en Truffle voor het maken, bouwen, implementeren en uitvoeren van een slimme contract functie via een trans actie.

> [!div class="nextstepaction"]
> [Visual Studio code gebruiken om slimme contracten te maken, te bouwen en te implementeren](send-transaction.md)