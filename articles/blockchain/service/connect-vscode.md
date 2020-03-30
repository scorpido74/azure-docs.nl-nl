---
title: Visual Studio-code gebruiken om verbinding te maken met Azure Blockchain-service
description: Verbinding maken met een Consortiumnetwerk van Azure Blockchain Service met behulp van de Azure Blockchain Development Kit voor Ethereum-extensie in Visual Studio Code
ms.date: 03/26/2020
ms.topic: quickstart
ms.reviewer: caleteet
ms.openlocfilehash: 269a3e419186daddf1e6a41a54e52e688f4bc7e3
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348586"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Snelstart: Visual Studio-code gebruiken om verbinding te maken met een Consortiumnetwerk van Azure Blockchain Service

In deze quickstart installeert en gebruikt u de Azure Blockchain Development Kit voor Ethereum Visual Studio Code (VS Code) extensie om te koppelen aan een consortium op Azure Blockchain Service. De Azure Blockchain Development Kit vereenvoudigt de manier waarop u slimme contracten maakt, verbindt, bouwt en implementeert op Ethereum blockchain-grootboeken.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Snelstart [voltooien: een blockchain-lid maken met behulp van de Azure-portal](create-member.md) of [Quickstart: een Azure Blockchain Service-blockchain-lid maken met Azure CLI](create-member-cli.md)
* [Visual Studio-code](https://code.visualstudio.com/Download)
* [Azure Blockchain Development Kit voor Ethereum-extensie](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x of hoger](https://nodejs.org)
* [Git 2.10.x of hoger](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Voeg python.exe toe aan je pad. Python-versie 2.7.15 op uw pad is vereist voor Azure Blockchain Development Kit.
* [Truffel 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

Voor Windows is een geïnstalleerde C++ compiler vereist voor de node-gyp module. U de MSBuild-tools gebruiken:

* Als Visual Studio 2017 is geïnstalleerd, configureert u npm om de MSBuild-hulpprogramma's met de opdracht te gebruiken`npm config set msvs_version 2017 -g`
* Als Visual Studio 2019 is geïnstalleerd, stelt u het pad voor MS-buildtools in voor npm. Bijvoorbeeld: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Installeer anders de stand-alone VS `npm install --global windows-build-tools` Build-gereedschappen met behulp van een verhoogde *run als opdrachtshell voor beheerders.*

Voor meer informatie over node-gyp, zie de [node-gyp repository op GitHub](https://github.com/node-gyp).

### <a name="verify-azure-blockchain-development-kit-environment"></a>Azure Blockchain Development Kit-omgeving verifiëren

Azure Blockchain Development Kit controleert of aan de vereisten voor uw ontwikkelomgeving is voldaan. Ga als volgt te werk om uw ontwikkelomgeving te verifiëren:

Kies in het opdrachtpalet VS-code **Azure Blockchain: Welkomstpagina weergeven**.

Azure Blockchain Development Kit voert een validatiescript uit dat ongeveer een minuut in beslag neemt. U de uitvoer bekijken door **Terminal > Nieuwe Terminal te**selecteren. Selecteer in de menubalk van de terminal het tabblad **Uitvoer** en **Azure Blockchain** in de vervolgkeuzelijst. Succesvolle validatie ziet eruit als de volgende afbeelding:

![Geldige ontwikkelomgeving](./media/connect-vscode/valid-environment.png)

 Als u een vereist hulpmiddel mist, wordt in een nieuw tabblad met de naam **Azure Blockchain Development Kit - Preview** de vereiste hulpprogramma's met downloadkoppelingen weergegeven.

![Dev kit vereist apps](./media/connect-vscode/required-apps.png)

Installeer ontbrekende vereisten voordat u verdergaat met de quickstart.

## <a name="connect-to-consortium-member"></a>Verbinding maken met consortiumlid

U verbinding maken met consortiumleden via de Azure Blockchain Development Kit VS Code-extensie. Zodra u bent verbonden met een consortium, u slimme contracten compileren, bouwen en implementeren voor een Consortiumlid van Azure Blockchain Service.

Als u geen toegang hebt tot een Azure Blockchain Service-consortiumlid, vult u de vereiste [Quickstart in: Maak een blockchain-lid met behulp van de Azure-portal](create-member.md) of [Quickstart: Maak een Azure Blockchain Service-blockchain-lid met Azure CLI](create-member-cli.md).

1. Vouw in het deelvenster VS Code explorer de **Azure Blockchain-extensie** uit.
1. Selecteer **Verbinding maken met het netwerk**.

   ![Verbinding maken met het netwerk](./media/connect-vscode/connect-consortium.png)

    Volg de aanwijzingen om te verifiëren met een browser als u wordt gevraagd om Azure-verificatie.
1. Kies **Azure Blockchain Service** in de vervolgkeuzelijst van het opdrachtpalet.
1. Kies de abonnements- en brongroep die is gekoppeld aan uw Azure Blockchain Service-consortiumlid.
1. Kies uw consortium uit de lijst.

Het consortium en blockchain-leden worden opgenomen in de VS Code explorer-zijbalk.

![Consortium weergegeven in explorer](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u Azure Blockchain Development Kit voor Ethereum VS Code-extensie gebruikt om te koppelen aan een consortium op Azure Blockchain Service. Probeer de volgende zelfstudie om Azure Blockchain Development Kit voor Ethereum te gebruiken om een slimme contractfunctie te maken, te bouwen, implementeren en uitvoeren via een transactie.

> [!div class="nextstepaction"]
> [Slimme contracten maken, bouwen en implementeren op Azure Blockchain Service](send-transaction.md)