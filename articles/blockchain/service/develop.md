---
title: Overzicht van de ontwikkeling van Azure Block Chain Service
description: Kennis Making met het ontwikkelen van oplossingen op de Azure Block Chain-service.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/11/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 2990917243507d93cb6a86da11cf771f3a42eebd
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298425"
---
# <a name="azure-blockchain-service-development-overview"></a>Overzicht van de ontwikkeling van Azure Block Chain Service

Met de Azure Block Chain-Service kunt u consortium Block chain-netwerken maken om bedrijfs scenario's zoals het bijhouden van activa, digitale tokens, loyaliteit en beloning, financiële toeleverings ketens en herkomst in te scha kelen. Dit artikel is een inleiding tot Azure Block Chain Service Development en belangrijkste onderwerpen voor het implementeren van Block chain voor Enter prise.

## <a name="client-connection-to-azure-blockchain-service"></a>Client verbinding met Azure Block Chain Service

Er zijn verschillende soorten clients voor Block chain-netwerken, waaronder volledige knoop punten, licht knooppunten en externe clients. Azure Block Chain service bouwt een Block chain-netwerk dat knoop punten bevat. U kunt verschillende clients gebruiken als uw gateway naar de Azure Block Chain-Service voor de ontwikkeling van Block chain. De Azure Block Chain-service biedt basis verificatie of toegangs sleutel als ontwikkel eindpunt. Hieronder vindt u populaire clients waarmee u verbinding kunt maken.

### <a name="metamask"></a>Het-gegevensmasker

Het-gegevensmask is een op een browser gebaseerde wallet (externe client), RPC-client en Basic contract Verkenner. In tegens telling tot andere browser-Wallet, injecteert het Web3-exemplaar een instantie in de Java script-context van de browser, die fungeert als een RPC-client die verbinding maakt met een groot aantal Ethereum blockchains (*mainnet*, *Ropsten testnet*, *Kovan testnet*, lokaal RPC-knoop punt, etc.). U kunt aangepaste RPC eenvoudig instellen om verbinding te maken met de Azure Block Chain-service en block Chain-ontwikkeling te starten met behulp van Remix.

### <a name="geth"></a>Geth

Geth is de opdracht regel interface voor het uitvoeren van een volledig Ethereum-knoop punt dat in go is geïmplementeerd. Het is niet nodig om een volledig knoop punt uit te voeren, maar u kunt ook de interactieve console van een Java Script-runtime omgeving openen om te communiceren met de Azure Block Chain-service.

## <a name="development-framework-configuration"></a>Configuratie van het ontwikkelings raamwerk

Voor het ontwikkelen van geavanceerde oplossingen voor Enter prise Block Chain is een ontwikkelings kader nodig om verbinding te maken met verschillende Block chain-netwerken, het beheren van de levens cyclus van Smart contracten, het automatiseren van tests, het implementeren van een slim contract met scripts en een interactieve console.

Truffle is een populair Block Chain Development Framework om gedecentraliseerde toepassingen op Ethereum blockchains te schrijven, te compileren, te implementeren en te testen. U kunt Truffle ook beschouwen als een framework dat ervoor probeert slimme contract ontwikkeling en traditionele webontwikkeling naadloos te integreren.

Zelfs het kleinste project communiceert met ten minste twee Block Chain-knoop punten: één op de computer van de ontwikkelaar en de andere die het netwerk aanduidt waar de ontwikkelaar zijn toepassing implementeert. Bijvoorbeeld het belangrijkste open bare Ethereum-netwerk of de Azure Block Chain-service. Truffle biedt een systeem voor het beheer van de compilatie-en implementatie-artefacten voor elk netwerk en biedt daarom een manier om de implementatie van het definitieve programma te vereenvoudigen. Voor meer informatie raadpleegt u [Quick Start: Truffle gebruiken om verbinding te maken met een Azure Block Chain service-netwerk](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Persoonlijke Ethereum-quorum transactie

Quorum is een op Ethereum gebaseerd, gedistribueerd grootboek protocol met trans acties en privacy voor contracten en nieuwe consensus mechanismen. De belangrijkste verbeteringen van Go-Ethereum zijn:

* Privacy-quorum ondersteunt persoonlijke trans acties en privé contracten via open bare en persoonlijke status scheiding en maakt gebruik van peer-to-peer versleutelde bericht uitwisselingen voor gerichte overdracht van privé gegevens naar netwerk deelnemers.
* Alternatieve consensus-mechanismen: zonder dat er een controle nodig is voor het gebruik van een bepaald netwerk of van een bewijs van staking. Quorum biedt meerdere consensus mechanismen die zijn ontworpen voor consortium ketens, zoals elk en IBFT.  Azure Block chain services maakt gebruik van het IBFT consensus-mechanisme.

* Machtigingen voor peer: machtigingen voor knoop punten en peering met behulp van slimme contracten, zodat alleen bekende partijen kunnen deel nemen aan het netwerk
* Hogere prestaties-quorum biedt hogere prestaties dan open bare Geth

Zie [zelf studie: een trans actie verzenden met behulp van de Azure Block Chain-Service](send-transaction.md) voor een voor beeld van een persoonlijke trans actie.

## <a name="block-explorers"></a>Verkenners blok keren

Blok keer verkenners zijn online Block Chain-browsers die afzonderlijke blok inhoud, transactie adres gegevens en geschiedenis weer geven. Basis informatie over blokken is beschikbaar via Azure Monitor in de Azure Block Chain-service. Als u echter meer gedetailleerde informatie tijdens de ontwikkeling nodig hebt, kan het zijn dat u de blok Verkenner kunt gebruiken.  Er zijn populaire open-source blok verkenners die u kunt gebruiken. Hier volgt een lijst met blok verkenners die werken met de Azure Block Chain-Service:

* [Epirus Azure Block Chain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) van Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TPS meting

Omdat Block Chain wordt gebruikt in meer bedrijfs scenario's, is de snelheid van trans acties per seconde (TPS) belang rijk om knel punten en inefficiënte systeem storingen te voor komen. Hoge transactie tarieven kunnen lastig zijn om binnen een gedecentraliseerde Block chain te onderhouden. Een nauw keurige meting op TPS kan worden beïnvloed door verschillende factoren zoals server thread, grootte van transactie wachtrij, netwerk latentie en beveiliging. Als u tijdens de ontwikkeling de snelheid van TPS moet meten, is een populair hulp programma voor open source [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Volgende stappen

[Snelstartgids: Truffle gebruiken om verbinding te maken met een Azure Block Chain service-netwerk](connect-truffle.md)
