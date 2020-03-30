---
title: Overzicht van Azure Blockchain Service-ontwikkeling
description: Inleiding over het ontwikkelen van oplossingen op Azure Blockchain Service.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348383"
---
# <a name="azure-blockchain-service-development-overview"></a>Overzicht van Azure Blockchain Service-ontwikkeling

Met Azure Blockchain Service u blockchain-netwerken van meerdere netwerken maken om bedrijfsscenario's mogelijk te maken, zoals asset tracking, digitale token, loyaliteit en beloning, financiële supply chain en herkomst. De volgende secties introduceren Azure Blockchain Service-ontwikkeling voor het implementeren van enterprise blockchain-oplossingen.

## <a name="connecting-to-azure-blockchain-service"></a>Verbinding maken met Azure Blockchain-service

Er zijn verschillende soorten clients voor blockchain-netwerken, waaronder volledige knooppunten, lichte knooppunten en externe clients. Azure Blockchain Service bouwt een blockchain-netwerk met knooppunten. U verschillende clients gebruiken als uw toegangspoort tot Azure Blockchain Service voor blockchain-ontwikkeling. Azure Blockchain Service biedt basisverificatie of toegangssleutel als eindpunt voor ontwikkeling. De volgende zijn populaire clients die u gebruiken connect.

### <a name="visual-studio-code"></a>Visual Studio Code

U verbinding maken met consortiumleden via de Azure Blockchain Development Kit Visual Studio Code-extensie. Zodra u bent verbonden met een consortium, u slimme contracten compileren, bouwen en implementeren voor een Consortiumlid van Azure Blockchain Service.

Om geavanceerde enterprise blockchain-oplossingen te ontwikkelen, is een ontwikkelingskader nodig om verbinding te maken met verschillende blockchain-netwerken en slimme contractcycli te beheren. De meeste projecten werken samen met ten minste twee blockchain-knooppunten. Ontwikkelaars gebruiken een lokale blockchain tijdens de ontwikkeling. Wanneer de toepassing klaar is voor test of release, implementeert de ontwikkelaar naar een blockchain-netwerk. Bijvoorbeeld het belangrijkste openbare Ethereum-netwerk of Azure Blockchain-service. Azure Blockchain Development Kit voor Ethereum-extensie in Visual Studio Code maakt gebruik van Truffel. Truffel is een populair blockchain-ontwikkelingskader om gedecentraliseerde toepassingen op Ethereum-blockchains te schrijven, compileren, implementeren en testen. U truffel ook zien als een raamwerk dat probeert slimme contractontwikkeling en traditionele webontwikkeling naadloos te integreren.

Zie [Quickstart: Visual Studio Code gebruiken om verbinding te maken met een Consortiumnetwerk van Azure Blockchain Service](connect-vscode.md)voor meer informatie.

### <a name="metamask"></a>MetaMask MetaMask

MetaMask is een browsergebaseerde portemonnee (externe client), RPC-client en basiscontractverkenner. In tegenstelling tot andere browserportefeuilles injecteert MetaMask een web3-exemplaar in de JavaScript-context van de browser, die fungeert als een RPC-client die verbinding maakt met een verscheidenheid aan Ethereum-blockchains *(mainnet*, *Ropsten testnet*, *Kovan testnet*, lokaal RPC-knooppunt, enz.). U aangepaste RPC eenvoudig instellen om verbinding te maken met Azure Blockchain Service en blockchain-ontwikkeling te starten met Remix.

Zie [Snelstart: MetaMask gebruiken om verbinding te maken en een slim contract te implementeren](connect-metamask.md) voor meer informatie.

### <a name="geth"></a>Geth Geth

Geth is de command-line interface voor het uitvoeren van een volledige Ethereum node geïmplementeerd in Go. U hoeft geen volledig knooppunt uit te voeren, maar de interactieve console te starten die een JavaScript-runtime-omgeving biedt die een JavaScript-API blootstelt om te communiceren met Azure Blockchain Service.

Zie [Snelstart: Geth gebruiken om te koppelen aan een Azure Blockchain Service-transactieknooppunt](connect-geth.md).

## <a name="ethereum-quorum-private-transactions"></a>Particuliere transacties van het Ethereum Quorum

Quorum is een ethereum-gebaseerd gedistribueerd grootboekprotocol met transactie plus contractprivacy en nieuwe consensusmechanismen. Belangrijke verbeteringen ten opzichte van Go-Ethereum zijn:

* **Privacy** - Quorum ondersteunt particuliere transacties en privé-contracten via scheiding van openbare en particuliere staten en maakt gebruik van peer-to-peer versleutelde berichtenuitwisselingen voor gerichte overdracht van privégegevens aan netwerkdeelnemers.
* **Alternatieve consensusmechanismen** - proof-of-work of proof-of-stake consensus is niet nodig voor een gepered isnetwerk. Quorum biedt meerdere consensusmechanismen die zijn ontworpen voor consortiumketens zoals RAFT en IBFT.Azure Blockchain Service maakt gebruik van het IBFT-consensusmechanisme.
* **Peer permissioning** - node en peer permissioning using smart contracts zorgt ervoor dat alleen bekende partijen lid kunnen worden van het netwerk.
* **Hogere prestaties** - Quorum biedt hogere prestaties dan openbare Geth.

## <a name="block-explorers"></a>Verkenners blokkeren

Block explorers zijn online blockchain browsers die individuele blokinhoud, transactieadresgegevens en geschiedenis weergeven. Basisblokgegevens zijn beschikbaar via Azure Monitor in Azure Blockchain Service. Als u echter meer gedetailleerde informatie nodig hebt tijdens de ontwikkeling, kunnen blokverkenners nuttig zijn.  De volgende blokverkenners werken met Azure Blockchain Service:

* [Epirus Azure Blockchain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) van Web3 Labs
* [BlockScout BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

U ook uw eigen block explorer bouwen met Blockchain Data Manager en Azure Cosmos DB, zie [Zelfstudie: Blockchain Data Manager gebruiken om gegevens naar Azure Cosmos DB te verzenden.](data-manager-cosmosdb.md)

## <a name="tps-measurement"></a>TPS-meting

Aangezien blockchain wordt gebruikt in meer bedrijfsscenario's, is transacties per seconde (TPS)-snelheid belangrijk om knelpunten en systeeminefficiënties te voorkomen. Hoge transactietarieven kunnen moeilijk te handhaven zijn binnen een gedecentraliseerde blockchain. Een nauwkeurige TPS-meting kan worden beïnvloed door verschillende factoren, zoals serverthread, transactiewachtrijgrootte, netwerklatentie en beveiliging. Als u tps snelheid te meten tijdens de ontwikkeling, een populaire open-source tool is [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Volgende stappen

Probeer een snelle start met Azure Blockchain Development Kit voor Ethereum om te koppelen aan een consortium op Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Visual Studio-code gebruiken om verbinding te maken met Azure Blockchain-service](connect-vscode.md)