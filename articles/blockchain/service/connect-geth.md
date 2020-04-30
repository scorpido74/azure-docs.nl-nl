---
title: Geth gebruiken om aan de Azure Block Chain-service te koppelen
description: Koppelen aan een Geth-exemplaar in het Azure Block Chain Service-transactie knooppunt
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9da78eac1dc429bcc0ad52bb9cb2f1fb743a90d4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74455829"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Snelstartgids: Geth gebruiken om aan een Azure Block Chain Service-transactie knooppunt te koppelen

In deze Quick Start gebruikt u de Geth-client om te koppelen aan een Geth-exemplaar in een Azure Block Chain Service Trans Action-knoop punt. Na de koppeling gebruikt u de Geth java script-console om een Web3 java script Dapp-API aan te roepen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Geth](https://github.com/ethereum/go-ethereum/wiki/geth) installeren
* Volledige [Snelstartgids: Maak een Block Chain-lid met behulp van de Azure Portal](create-member.md) of [Quick Start: een Azure Block Chain Service Block Chain-lid maken met behulp van Azure cli](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Geth connection string ophalen

U kunt de Geth-connection string voor een Azure Block Chain Service-transactie knooppunt ophalen in de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Ga naar uw Azure Block Chain service-lid. Selecteer **transactie knooppunten** en de koppeling standaard transactie knooppunt.

    ![Standaard transactie knooppunt selecteren](./media/connect-geth/transaction-nodes.png)

1. Selecteer **verbindings reeksen**.
1. Kopieer de connection string van **https (toegangs sleutel 1)**. U hebt de teken reeks nodig voor de volgende sectie.

    ![Verbindingsreeks](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Verbinding maken met Geth

1. Open een opdracht prompt of shell.
1. Gebruik de subopdracht Geth attach om aan het actieve Geth-exemplaar in uw transactie knooppunt te koppelen. Plak de connection string als een argument voor de subopdracht attach. Bijvoorbeeld:

    ``` bash
    geth attach <connection string>
    ```

1. Wanneer u bent verbonden met de Ethereum-console van het transactie knooppunt, kunt u de Web3 java script Dapp API of de beheer-API aanroepen.

    Gebruik bijvoorbeeld de volgende API om de chainId te vinden.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    In dit voor beeld is de chainId 661.

    ![Optie voor Azure Block Chain-Service](./media/connect-geth/geth-attach.png)

1. Als u de verbinding met de console `exit`wilt verbreken, typt u.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u de Geth-client gebruikt om een Geth-exemplaar te koppelen aan een Azure Block Chain Service-transactie knooppunt. Probeer de volgende zelf studie voor het gebruik van Azure Block Chain Development Kit voor Ethereum voor het maken, bouwen, implementeren en uitvoeren van een slimme contract functie via een trans actie.

> [!div class="nextstepaction"]
> [Slimme contracten maken, bouwen en implementeren in azure Block Chain Service](send-transaction.md)
