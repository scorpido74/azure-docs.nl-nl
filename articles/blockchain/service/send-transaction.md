---
title: Maak, bouw, & implementeer slimme contracts tutorial - Azure Blockchain Service
description: Zelfstudie over het gebruik van de Azure Blockchain Development Kit voor Ethereum-extensie in Visual Studio Code om een slim contract op Azure Blockchain Service te maken, te bouwen en te implementeren.
ms.date: 04/22/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: dc23c680dfb2ed33cae2a251af16e1b1f25c6ac7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086654"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>Zelfstudie: Slimme contracten maken, bouwen en implementeren op Azure Blockchain Service

Gebruik in deze zelfstudie de Azure Blockchain Development Kit voor Ethereum-extensie in Visual Studio Code om een slim contract op Azure Blockchain Service te maken, bouwen en implementeren. U gebruikt de ontwikkelkit ook om via een transactie een slimme contractfunctie uit te voeren.

U gebruikt Azure Blockchain Development Kit voor Ethereum om:

> [!div class="checklist"]
> * Een slim contract maken
> * Een slim contract implementeren
> * Een slimme contractfunctie uitvoeren via een transactie

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Snelstart [voltooien: Visual Studio-code gebruiken om verbinding te maken met een Consortiumnetwerk van Azure Blockchain Service](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure Blockchain Development Kit voor Ethereum-extensie](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x of hoger](https://nodejs.org/download)
* [Git 2.10.x of hoger](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Voeg python.exe toe aan je pad. Python-versie 2.7.15 op uw pad is vereist voor Azure Blockchain Development Kit.
* [Truffel 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

Voor Windows is een geïnstalleerde C++ compiler vereist voor de node-gyp module. U de MSBuild-tools gebruiken:

* Als Visual Studio 2017 is geïnstalleerd, configureert u npm om de MSBuild-hulpprogramma's met de opdracht te gebruiken`npm config set msvs_version 2017 -g`
* Als Visual Studio 2019 is geïnstalleerd, stelt u het pad voor MS-buildtools in voor npm. Bijvoorbeeld: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Installeer anders de stand-alone VS `npm install --global windows-build-tools` Build-gereedschappen met behulp van een verhoogde *run als opdrachtshell voor beheerders.*

Voor meer informatie over node-gyp, zie de [node-gyp repository op GitHub](https://github.com/nodejs/node-gyp).

## <a name="create-a-smart-contract"></a>Een slim contract maken

De Azure Blockchain Development Kit voor Ethereum maakt gebruik van projectsjablonen en Truffel-tools om steigers te helpen bij het schavot, bouwen en implementeren van contracten. Voordat u begint, voltooit u de vereiste [Quickstart: Visual Studio Code gebruiken om verbinding te maken met een Azure Blockchain Service consortiumnetwerk.](connect-vscode.md) De quickstart begeleidt u bij de installatie en configuratie van de Azure Blockchain Development Kit voor Ethereum.

1. Kies azure **blockchain: nieuw soliditeitsproject**in het opdrachtpalet VS-code.
1. Kies **Basisproject maken**.
1. Maak een nieuwe `HelloBlockchain` map met de naam en **Selecteer nieuw projectpad**.

De Azure Blockchain Development Kit maakt en initialiseert een nieuw Solidity-project voor u. Het basisproject bevat een voorbeeld **van helloblockchain-smart** contract en alle benodigde bestanden om te bouwen en te implementeren voor uw consortiumlid in Azure Blockchain Service. Het kan enkele minuten duren voordat het project is gemaakt. U de voortgang in het eindpaneel van VS-code controleren door de uitvoer voor Azure Blockchain te selecteren.

De projectstructuur ziet eruit als het volgende voorbeeld:

   ![Soliditeitsproject](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Een slim contract opbouwen

Slimme contracten bevinden zich in de **contractenmap** van het project. Je stelt slimme contracten samen voordat je ze implementeert in een blockchain. Gebruik de opdracht **Contracten bouwen** om alle slimme contracten in uw project samen te stellen.

1. Vouw in de zijbalk van de VS Code explorer de **map contracten** in uw project uit.
1. Klik met de rechtermuisknop op **HelloBlockchain.sol** en kies **Contracten bouwen** in het menu.

    ![Menu Contracten bouwen kiezen ](./media/send-transaction/build-contracts.png)

Azure Blockchain Development Kit gebruikt Truffel om de slimme contracten samen te stellen.

![Truffelcompileruitvoer](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Een slim contract implementeren

Truffel gebruikt migratiescripts om uw contracten te implementeren op een Ethereum-netwerk. Migraties zijn JavaScript-bestanden in de **migratiemap van** het project.

1. Als u uw slimme contract wilt implementeren, klikt u met de rechtermuisknop op **HelloBlockchain.sol** en kiest **u Contracten implementeren** in het menu.
1. Kies uw Azure Blockchain-consortiumnetwerk in het opdrachtpalet. Het blockchain-netwerk van het consortium is toegevoegd aan het Truffel-configuratiebestand van het project toen u het project maakte.
1. Kies **Genereren ezelsbruggetje**. Kies een bestandsnaam en sla het ezelsbruggetje bestand op in de projectmap. Bijvoorbeeld `myblockchainmember.env`. Het ezelsbruggetje wordt gebruikt om een Ethereum-privésleutel voor uw blockchain-lid te genereren.

Azure Blockchain Development Kit gebruikt Truffel om het migratiescript uit te voeren om de contracten naar de blockchain te implementeren.

![Contract met succes geïmplementeerd](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Een contractfunctie aanroepen

De functie **SendRequest van** het **HelloBlockchain-contract** wijzigt de statusvariabele **RequestMessage.** Het veranderen van de status van een blockchain-netwerk gebeurt via een transactie. U de pagina slimme contractinteractie van Azure Blockchain Development Kit gebruiken om de functie **SendRequest** via een transactie aan te roepen.

1. Als u wilt communiceren met uw slimme contract, klikt u met de rechtermuisknop op **HelloBlockchain.sol** en kiest **u Smart Contract Interaction Page weergeven** in het menu.

    ![Kies Pagina voor slimme contractinteractie weergeven in het menu](./media/send-transaction/contract-interaction.png)

1. Op de interactiepagina u een geïmplementeerde contractversie kiezen, oproepfuncties, huidige status weergeven en metagegevens weergeven.

    ![Voorbeeld pagina voor slimme contractinteractie](./media/send-transaction/interaction-page.png)

1. Als u de functie slim contract wilt aanroepen, selecteert u de contractactie en geeft u uw argumenten door. Kies **Contractactie SendRequest** en voer **Hello, Blockchain in!** voor de parameter **requestMessage.** Selecteer **Uitvoeren** om de functie **SendRequest** aan te roepen via een transactie.

    ![Actie SendRequest uitvoeren](./media/send-transaction/sendrequest-action.png)

Zodra de transactie is verwerkt, geeft de interactiesectie de statuswijzigingen weer.

![Wijzigingen in contractstatus](./media/send-transaction/contract-state.png)

Met de functie SendRequest worden de velden **RequestMessage** en **State** ingesteld. De huidige status voor **RequestMessage** is het argument dat u voorbij **Hello, Blockchain**. De **veldwaarde van de staat** blijft **Request**.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet langer nodig bent, `myResourceGroup` u de resources verwijderen door de resourcegroep die u hebt gemaakt in de vereiste *voor een blockchain-lid* maken snel te verwijderen.

De resourcegroep verwijderen:

1. Navigeer in de Azure-portal naar **de groep Resource** in het linkernavigatiedeelvenster en selecteer de resourcegroep die u wilt verwijderen.
1. Selecteer **Resourcegroep verwijderen**. Verwijder het verwijderen door de naam van de brongroep in te voeren en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een voorbeeld van Solidity-project gemaakt met Azure Blockchain Development Kit. U hebt een slim contract gebouwd en geïmplementeerd, dat vervolgens een functie werd genoemd via een transactie op een blockchain-consortiumnetwerk dat wordt gehost op Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Blockchain-toepassingen ontwikkelen met Azure Blockchain Service](develop.md)
