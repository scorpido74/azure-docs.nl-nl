---
title: Truffle gebruiken om verbinding te maken met de Azure Block Chain-Service
description: Verbinding maken met een Azure Block Chain service-netwerk met behulp van truffle
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9ea96aa32072775fe6fb9563442f5e3564d37ea5
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455794"
---
# <a name="quickstart-use-truffle-to-connect-to-azure-blockchain-service"></a>Snelstartgids: Truffle gebruiken om verbinding te maken met de Azure Block Chain-Service

In deze Quick Start gebruikt u Truffle verbinding maken met een Azure Block Chain Service-transactie knooppunt. Vervolgens gebruikt u de interactieve console van truffle om **Web3** -methoden aan te roepen om te communiceren met uw Block chain-netwerk.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Volledige [Snelstartgids: Maak een Block Chain-lid met behulp van de Azure Portal](create-member.md) of [Quick Start: een Azure Block Chain Service Block Chain-lid maken met behulp van Azure cli](create-member-cli.md)
* Installeer [Truffle](https://github.com/trufflesuite/truffle). Truffle vereist verschillende hulpprogram ma's die moeten worden geïnstalleerd, waaronder [node. js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/)installeren. Python is vereist voor web3.

## <a name="create-truffle-project"></a>Truffle-project maken

1. Open een node. js-opdracht prompt of-shell.
1. Wijzig de Directory in de map waar u de Truffle-projectmap wilt maken.
1. Maak een map voor het project en wijzig het pad naar de nieuwe map. Bijvoorbeeld:

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Initialiseer het Truffle-project.

    ``` bash
    truffle init
    ```

1. Installeer Ethereum java script API Web3 in de projectmap. Momenteel versie Web3 versie 1.0.0-Beta. 37 is vereist.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    Mogelijk ontvangt u NPM waarschuwingen tijdens de installatie.
    
## <a name="configure-truffle-project"></a>Truffle-project configureren

Als u het Truffle-project wilt configureren, hebt u enkele informatie over het transactie knooppunt nodig van de Azure Portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar uw Azure Block Chain service-lid. Selecteer **transactie knooppunten** en de koppeling standaard transactie knooppunt.

    ![Standaard transactie knooppunt selecteren](./media/connect-truffle/transaction-nodes.png)

1. Selecteer **verbindings reeksen**.
1. Kopieer de connection string van **https (toegangs sleutel 1)** . U hebt de teken reeks nodig voor de volgende sectie.

    ![Verbindingsreeks](./media/connect-truffle/connection-string.png)

### <a name="edit-configuration-file"></a>Configuratie bestand bewerken

Vervolgens moet u het Truffle-configuratie bestand bijwerken met het eind punt van het trans actie knooppunt.

1. Open in de projectmap **truffledemo** het Truffle-configuratie bestand `truffle-config.js` in een editor.
1. Vervang de inhoud van het bestand door de volgende configuratie gegevens. Voeg een variabele met het eindpunt adres toe. Vervang de punt haak door de waarden die u hebt verzameld uit de vorige sectie.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";   
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider: new Web3.providers.HttpProvider(defaultnode),
          network_id: "*"
        }
      }
    }
    ```

1. Sla de wijzigingen in `truffle-config.js`op.

## <a name="connect-to-transaction-node"></a>Verbinding maken met transactieknooppunt

Gebruik *Web3* om verbinding te maken met het transactie knooppunt.

1. Gebruik de Truffle-console om verbinding te maken met het standaard transactie knooppunt. Voer bij een opdracht prompt of shell de volgende opdracht uit:

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle maakt verbinding met het standaard transactie knooppunt en biedt een interactieve console.

    U kunt methoden op het object **Web3** aanroepen om te communiceren met uw Block chain-netwerk.

1. Roep de methode **getBlockNumber** aan om het huidige blok nummer te retour neren.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Voorbeelduitvoer:

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Sluit de Truffle-console af.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u Truffle gebruikt om verbinding te maken met een Azure Block Chain service-standaard transactie knooppunt en de interactieve console te gebruiken om het huidige Block Chain-blok nummer te retour neren.

Probeer de volgende zelf studie voor het gebruik van Azure Block Chain Development Kit voor Ethereum voor het maken, bouwen, implementeren en uitvoeren van een slimme contract functie via een trans actie.

> [!div class="nextstepaction"]
> [Slimme contracten maken, bouwen en implementeren in azure Block Chain Service](send-transaction.md)
