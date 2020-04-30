---
title: Visual Studio code gebruiken om verbinding te maken met de Azure Block Chain-Service
description: Verbinding maken met een Azure Block Chain Service consortium-netwerk met behulp van de Azure Block Chain Development Kit for Ethereum-extensie in Visual Studio code
ms.date: 04/22/2020
ms.topic: quickstart
ms.reviewer: caleteet
ms.openlocfilehash: 8b502966317c5d07e89de4ae70ff72b899e963e6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82084835"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Snelstartgids: Visual Studio code gebruiken om verbinding te maken met een Azure Block Chain Service consortium-netwerk

In deze Snelstartgids installeert en gebruikt u de extensie Azure Block Chain Development Kit voor Ethereum Visual Studio code (VS code) om aan een consortium te koppelen op de Azure Block Chain-service. De Azure Block Chain Development Kit vereenvoudigt het maken, verbinden, bouwen en implementeren van slimme contracten op Ethereum Block Chain-boekingen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Volledige [Snelstartgids: Maak een Block Chain-lid met behulp van de Azure Portal](create-member.md) of [Quick Start: een Azure Block Chain Service Block Chain-lid maken met behulp van Azure cli](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure Block Chain Development Kit voor Ethereum-uitbrei ding](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node. js 10.15. x of hoger](https://nodejs.org)
* [Git 2.10. x of hoger](https://git-scm.com)
* [Python-2.7.15](https://www.python.org/downloads/release/python-2715/) Voeg python. exe toe aan het pad. Een python-versie 2.7.15 in uw pad is vereist voor Azure Block Chain Development Kit.
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

In Windows is een geïnstalleerde C++-compiler vereist voor de module node-Gyp. U kunt de MSBuild-hulpprogram ma's gebruiken:

* Als Visual Studio 2017 is geïnstalleerd, configureert u NPM voor het gebruik van de MSBuild-hulpprogram ma's met de opdracht`npm config set msvs_version 2017 -g`
* Als Visual Studio 2019 is geïnstalleerd, stelt u het pad voor MS build-hulpprogram ma's voor NPM in. Bijvoorbeeld: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Als dat niet het geval is, installeert u de zelfstandige `npm install --global windows-build-tools` hulp middelen en hulpprogram ma's die worden gebruikt in een verhoogde *uitvoeren als Administrator* -opdracht shell.

Zie de [Gyp-opslag plaats op github](https://github.com/nodejs/node-gyp)voor meer informatie over node-Gyp.

### <a name="verify-azure-blockchain-development-kit-environment"></a>De Azure Block Chain Development Kit-omgeving controleren

In azure Block Chain Development Kit wordt gecontroleerd of aan de vereisten van uw ontwikkel omgeving is voldaan. Controleren of uw ontwikkel omgeving:

Kies in het palet met de VS code-opdracht **Azure Block chain: Welkomst pagina weer geven**.

Azure Block Chain Development Kit voert een validatie script uit dat ongeveer een minuut in beslag neemt. U kunt de uitvoer weer geven door **Terminal > New Terminal**te selecteren. Selecteer in de menu balk van de Terminal het tabblad **uitvoer** en **Azure Block Chain** in de vervolg keuzelijst. Geslaagde validatie ziet eruit als in de volgende afbeelding:

![Geldige ontwikkel omgeving](./media/connect-vscode/valid-environment.png)

 Als u een vereist hulp programma mist, wordt een nieuw tabblad met de naam **Azure Block Chain Development Kit** weer gegeven met de vereiste hulpprogram ma's met Download koppelingen.

![Ontwikkel Kit vereiste apps](./media/connect-vscode/required-apps.png)

Installeer de ontbrekende vereiste onderdelen voordat u doorgaat met de Snelstartgids.

## <a name="connect-to-consortium-member"></a>Verbinding maken met het consortium

U kunt verbinding maken met consortium leden met behulp van de Azure Block Chain Development Kit VS code extension. Wanneer u bent verbonden met een consortium, kunt u slimme contracten compileren, bouwen en implementeren in een Azure Block Chain Service consortium-lid.

Als u geen toegang hebt tot een lid van een Azure Block Chain Service consortium, voltooit u de vereiste [Snelstartgids: een Block Chain-lid maken met behulp van de Azure Portal](create-member.md) of [Quick Start: een Azure Block Chain Service Block Chain-lid maken met behulp van Azure cli](create-member-cli.md).

1. Vouw in het deel venster VS code Explorer de **Azure Block Chain** -extensie uit.
1. Selecteer **verbinding maken met netwerk**.

   ![Verbinding maken met netwerk](./media/connect-vscode/connect-consortium.png)

    Als u wordt gevraagd om Azure-verificatie, volgt u de prompts voor verificatie met behulp van een browser.
1. Kies **Azure Block Chain Service** in het opdracht palet vervolg keuzelijst.
1. Kies het abonnement en de resource groep die aan uw Azure Block Chain Service consortium-lid zijn gekoppeld.
1. Kies uw consortium in de lijst.

De consortium-en block Chain-leden worden weer gegeven in de VS code Explorer-balk aan de zijkant.

![Consortium weer gegeven in Verkenner](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u Azure Block Chain Development Kit voor Ethereum VS code-extensie gebruikt om aan een consortium te koppelen op de Azure Block Chain-service. Probeer de volgende zelf studie voor het gebruik van Azure Block Chain Development Kit voor Ethereum voor het maken, bouwen, implementeren en uitvoeren van een slimme contract functie via een trans actie.

> [!div class="nextstepaction"]
> [Slimme contracten maken, bouwen en implementeren in azure Block Chain Service](send-transaction.md)