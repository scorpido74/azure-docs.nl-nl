---
title: Zelf studie voor het maken, bouwen, & implementeren van Smart contracten-Azure Block Chain-Service
description: Zelf studie over het gebruik van de Azure Block Chain Development Kit voor Ethereum-extensie in Visual Studio code voor het maken, bouwen en implementeren van een slim contract op de Azure Block Chain-service.
ms.date: 12/06/2019
ms.topic: tutorial
ms.reviewer: chrisseg
ms.openlocfilehash: 5b901ab904425a22d2fe9643ffa75a4e978efa88
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972825"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>Zelf studie: slimme contracten maken, bouwen en implementeren in azure Block Chain Service

In deze zelf studie gebruikt u de Azure Block Chain Development Kit voor Ethereum-extensie in Visual Studio code voor het maken, bouwen en implementeren van een slim contract op de Azure Block Chain-service. U kunt ook de Development Kit gebruiken om een slimme contract functie uit te voeren via een trans actie.

U gebruikt Azure Block Chain Development Kit voor Ethereum voor het volgende:

> [!div class="checklist"]
> * Een slim contract maken
> * Een slim contract implementeren
> * Een slimme contract functie uitvoeren via een trans actie

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Volledige [Snelstartgids: Visual Studio code gebruiken om verbinding te maken met een Azure Block Chain Service consortium-netwerk](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure Block Chain Development Kit voor Ethereum-uitbrei ding](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node. js 10.15. x of hoger](https://nodejs.org/download)
* [Git 2.10. x of hoger](https://git-scm.com)
* [Python-2.7.15](https://www.python.org/downloads/release/python-2715/) Voeg python. exe toe aan het pad. Python-versie 2.7.15 in uw pad is vereist voor Azure Block Chain Development Kit.
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

In Windows is een geïnstalleerd C++ Compileer programma vereist voor de module node-Gyp. U kunt de MSBuild-hulpprogram ma's gebruiken:

* Als Visual Studio 2017 is geïnstalleerd, configureert u NPM voor het gebruik van de MSBuild-hulpprogram ma's met de opdracht `npm config set msvs_version 2017 -g`
* Als Visual Studio 2019 is geïnstalleerd, stelt u het pad voor MS build-hulpprogram ma's voor NPM in. Bijvoorbeeld: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Als dat niet het geval is, installeert u de zelfstandige hulp middelen voor het bouwen met behulp van `npm install --global windows-build-tools` in een opdracht shell met verhoogde bevoegdheden *uitvoeren als Administrator* .

Zie de [Gyp-opslag plaats op github](https://github.com/node-gyp)voor meer informatie over node-Gyp.

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

    ![Kies contracten maken menu ](./media/send-transaction/build-contracts.png)

Azure Block Chain Development Kit maakt gebruik van truffle om de slimme contracten te compileren.

![Uitvoer van truffle-compiler](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Een slim contract implementeren

Truffle maakt gebruik van migratie scripts om uw contracten te implementeren in een Ethereum-netwerk. Migraties zijn Java script-bestanden die zich in de map **migraties** van het project bevinden.

1. Als u uw slimme contract wilt implementeren, klikt u met de rechter muisknop op **HelloBlockchain. Sol** en kiest u **contracten implementeren** in het menu.
1. Kies uw Azure Block Chain consortium-netwerk in het opdracht palet. Het consortium Block chain-netwerk is toegevoegd aan het Truffle-configuratie bestand van het project wanneer u het project hebt gemaakt.
1. Kies **instructie genereren**. Kies een bestands naam en sla het bestand voor de instructie op in de projectmap. Bijvoorbeeld `myblockchainmember.env`. Het instructie bestand wordt gebruikt voor het genereren van een persoonlijke sleutel Ethereum voor uw Block Chain-lid.

Azure Block Chain Development Kit maakt gebruik van truffle om het migratie script uit te voeren om de contracten te implementeren op de Block chain.

![Het contract is geïmplementeerd](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Een contract functie aanroepen

De functie **SendRequest** van het **HelloBlockchain** -contract wijzigt de variabele **RequestMessage** -status. Het wijzigen van de status van een Block chain-netwerk wordt uitgevoerd via een trans actie. U kunt de Azure Block Chain Development Kit Smart contract-interactie pagina gebruiken om de functie **SendRequest** via een trans actie aan te roepen.

1. Als u met uw slimme contract wilt werken, klikt u met de rechter muisknop op **HelloBlockchain. Sol** en kiest u **pagina slimme contract interactie weer geven** in het menu.

    ![Kies pagina slimme contract interactie weer geven in het menu](./media/send-transaction/contract-interaction.png)

1. Op de pagina interactie kunt u een geïmplementeerde contract versie kiezen, functies aanroepen, de huidige status weer geven en meta gegevens weer geven.

    ![Voor beeld van een Smart contract-interactie pagina](./media/send-transaction/interaction-page.png)

1. Als u de functie Slim contract wilt aanroepen, selecteert u de actie contract en geeft u uw argumenten door. Kies **SendRequest** -contract actie en voer **Hallo in, Block Chain!** voor de para meter **requestMessage** . Selecteer **uitvoeren** om de functie **SendRequest** via een trans actie aan te roepen.

    ![SendRequest-actie uitvoeren](./media/send-transaction/sendrequest-action.png)

Zodra de trans actie is verwerkt, worden de status wijzigingen weer gegeven in de sectie interactie.

![Status wijzigingen contract](./media/send-transaction/contract-state.png)

De functie SendRequest stelt de velden **RequestMessage** en **status** in. De huidige status voor **RequestMessage** is het argument dat u hebt door gegeven als **Hello, Block Chain**. De waarde van het **status** veld blijft **aanvraag**.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de resources verwijderen door de `myResourceGroup` resource groep te verwijderen die u hebt gemaakt in de Snelstartgids *een Block Chain-lid maken* .

De resourcegroep verwijderen:

1. In de Azure-portal, gaat u naar **resourcegroep** in het navigatiedeelvenster links en selecteert u de resourcegroep die u wilt verwijderen.
1. Selecteer **Resourcegroep verwijderen**. Bevestig de verwijdering door de naam van de resourcegroep in te voeren en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een voor beeld van een volheid project gemaakt met behulp van de Azure Block Chain Development Kit. U hebt een slim contract gemaakt en geïmplementeerd. dit wordt een functie genoemd via een trans actie op een Block Chain consortium-netwerk dat wordt gehost op de Azure Block Chain-service.

> [!div class="nextstepaction"]
> [Block Chain-toepassingen ontwikkelen met behulp van de Azure Block Chain-Service](develop.md)
