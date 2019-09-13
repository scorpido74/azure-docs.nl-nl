---
title: Geth gebruiken om verbinding te maken met de Azure Block Chain-Service
description: Verbinding maken met een Azure Block Chain service-netwerk met behulp van Geth
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: a26899e291c4f44d3c9d91032b2ee191ba03133a
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931777"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>Quickstart: Geth gebruiken om verbinding te maken met een transactieknooppunt

Geth is een go Ethereum-client die u kunt gebruiken om te koppelen aan een Geth-exemplaar in een service transactie knooppunt van Azure Block chain.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Geth](https://github.com/ethereum/go-ethereum/wiki/geth) installeren
* Volledige [Snelstartgids: Maak een Block Chain-lid met behulp [van de Azure Portal](create-member.md) of Quick Start: Een Block Chain-lid van de Azure Block Chain-service maken met behulp van Azure CLI](create-member-cli.md)

## <a name="get-the-geth-connection-string"></a>De Geth-connection string ophalen

U kunt de Geth-connection string vinden in de Azure Portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer naar uw Azure Block Chain service-lid. Selecteer **transactie knooppunten** en de koppeling standaard transactie knooppunt.

    ![Standaard transactie knooppunt selecteren](./media/connect-geth/transaction-nodes.png)

1. Selecteer **verbindings reeksen**.
1. Kopieer de connection string van **https (toegangs sleutel 1)** . U hebt de opdracht voor de volgende sectie nodig.

    ![Verbindingsreeks](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Verbinding maken met Geth

1. Open een opdrachtprompt of de shell.
1. Gebruik de subopdracht Geth attach om aan het actieve Geth-exemplaar in uw transactie knooppunt te koppelen. Plak de connection string als een argument voor de subopdracht attach. Bijvoorbeeld:

    ```
    geth attach <connection string>
    ```

1. Wanneer u bent verbonden met de Ethereum-console van het transactie knooppunt, kunt u de Web3 java script Dapp API of de beheer-API aanroepen.

    Gebruik bijvoorbeeld de volgende API om de chainId te vinden.

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    In dit voor beeld is de chainId 297.

    ![Optie voor Azure Block Chain-Service](./media/connect-geth/geth-attach.png)

1. Als u de verbinding met de console `exit`wilt verbreken, typt u.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u de Geth-client gebruikt om een Geth-exemplaar te koppelen aan een Azure Block Chain Service-transactie knooppunt. Probeer de volgende zelf studie voor het gebruik van Azure Block Chain Development Kit voor Ethereum en Truffle voor het maken, bouwen, implementeren en uitvoeren van een slimme contract functie via een trans actie.

> [!div class="nextstepaction"]
> [Visual Studio code gebruiken om slimme contracten te maken, te bouwen en te implementeren](send-transaction.md)