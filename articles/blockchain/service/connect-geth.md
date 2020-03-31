---
title: Geth gebruiken om te koppelen aan Azure Blockchain-service
description: Koppelen aan een Geth-exemplaar op het transactieknooppunt van Azure Blockchain Service
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9da78eac1dc429bcc0ad52bb9cb2f1fb743a90d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74455829"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Snelstart: Gebruik Geth om te koppelen aan een Azure Blockchain Service-transactieknooppunt

In deze quickstart gebruikt u de Geth-client om een Geth-exemplaar op een Azure Blockchain Service-transactieknooppunt te koppelen. Eenmaal aangesloten, gebruikt u de Geth JavaScript-console om een web3 JavaScript Dapp API aan te roepen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Geth](https://github.com/ethereum/go-ethereum/wiki/geth) installeren
* Snelstart [voltooien: een blockchain-lid maken met behulp van de Azure-portal](create-member.md) of [Quickstart: een Azure Blockchain Service-blockchain-lid maken met Azure CLI](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Geth-verbindingstekenreeks oppakken

U de geth-verbindingstekenreeks voor een Azure Blockchain Service-transactieknooppunt in de Azure-portal krijgen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar uw Azure Blockchain Service-lid. Selecteer **Transactieknooppunten** en de standaardkoppeling transactieknooppunt.

    ![Standaardtransactieknooppunt selecteren](./media/connect-geth/transaction-nodes.png)

1. Selecteer **Verbindingstekenreeksen**.
1. Kopieer de verbindingstekenreeks vanuit **HTTPS (Access-toets 1)**. Je hebt het touwtje nodig voor de volgende sectie.

    ![Verbindingsreeks](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Verbinding maken met Geth

1. Open een opdrachtprompt of shell.
1. Gebruik de subcommand Geth attachattach om te koppelen aan de runh-instantie op uw transactieknooppunt. Plak de verbindingstekenreeks als argument voor de subopdracht Koppelen. Bijvoorbeeld:

    ``` bash
    geth attach <connection string>
    ```

1. Eenmaal verbonden met de Ethereum-console van het transactieknooppunt, u de web3 JavaScript Dapp API of de admin-API aanroepen.

    Gebruik bijvoorbeeld de volgende API om de chainId te achterhalen.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    In dit voorbeeld is de chainId 661.

    ![Azure Blockchain-service, optie](./media/connect-geth/geth-attach.png)

1. Als u de verbinding `exit`met de console wilt verbreken, typt u .

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de Geth-client gebruikt om een Geth-exemplaar op een Azure Blockchain Service-transactieknooppunt te koppelen. Probeer de volgende zelfstudie om Azure Blockchain Development Kit voor Ethereum te gebruiken om een slimme contractfunctie te maken, te bouwen, implementeren en uitvoeren via een transactie.

> [!div class="nextstepaction"]
> [Slimme contracten maken, bouwen en implementeren op Azure Blockchain Service](send-transaction.md)
