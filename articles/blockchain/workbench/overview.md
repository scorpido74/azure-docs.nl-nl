---
title: Overzicht van Azure Block Chain Workbench preview
description: Overzicht van de preview-versie van Azure Block Chain Workbench en de mogelijkheden ervan.
ms.date: 09/05/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: 9c40f95aa1490362b9232ec8d87a199d7a41c731
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324551"
---
# <a name="what-is-azure-blockchain-workbench"></a>Wat is Azure Blockchain Workbench?

Azure Block Chain Workbench preview is een verzameling Azure-Services en-functies die zijn ontworpen om u te helpen bij het maken en implementeren van Block Chain-toepassingen voor het delen van bedrijfs processen en-gegevens met andere organisaties. Azure Blockchain Workbench biedt de infrastructuur-scaffolding voor het bouwen van blockchain-toepassingen, zodat ontwikkelaars zich kunnen richten op het maken van bedrijfslogica en slimme contracten. Met Blockchain Workbench wordt het ook eenvoudiger om blockchain-toepassingen te maken door verschillende Azure-services en -functies te integreren om zo algemene ontwikkeltaken te automatiseren.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>Blockchain-toepassingen maken

U kunt met Blockchain Workbench blockchain-toepassingen definiëren met behulp van configuratie en door code voor slimme contracten te schrijven. U kunt de ontwikkeling van blockchains een boost geven en richten op het definiëren van contracten en het schrijven van bedrijfslogica, in plaats van scaffolding te bouwen en ondersteunende services in te stellen.

## <a name="manage-applications-and-users"></a>Toepassingen en gebruikers beheren

Azure Blockchain Workbench biedt een webtoepassing en REST-API's voor het beheren van blockchain-toepassingen en -gebruikers. Beheerders van Blockchain Workbench kunnen de toegang tot toepassingen beheren en toepassingsrollen toewijzen aan uw gebruikers. Gebruikers van Azure AD worden automatisch toegewezen aan leden in de toepassing.

## <a name="integrate-blockchain-with-applications"></a>Blockchain integreren met toepassingen

U kunt de REST-API's van Blockchain Workbench en API's op berichtbasis integreren met bestaande systemen. De API's bieden een interface die het mogelijk maakt om meerdere technologieën voor een gedistribueerd grootboek, opslag en databaseaanbiedingen te vervangen of gebruiken.

Blockchain Workbench kan berichten die zijn verzonden naar de API op berichtbasis, transformeren in transacties met een indeling die wordt verwacht door de native API van die blockchain.  Workbench kan transacties ondertekenen en naar de juiste blockchain routeren. 

Gebeurtenissen worden door Workbench automatisch afgeleverd aan Service Bus en Event Grid om berichten te verzenden naar downstream-consumenten. Ontwikkelaars kunnen hun code integreren met een van deze berichtsystemen om transacties te faciliteren en de resultaten te bekijken.

## <a name="deploy-a-blockchain-network"></a>Een blockchain-netwerk implementeren

Azure Blockchain Workbench vereenvoudigt het instellen van een blockchain-netwerk voor een consortium door een vooraf geconfigureerde oplossing aan te bieden in de vorm van een Azure Resource Manager-oplossingssjabloon. De sjabloon biedt vereenvoudigde implementatie van alle onderdelen die nodig zijn voor het exploiteren van een consortium. Op dit moment wordt alleen Ethereum ondersteund door Blockchain Workbench.

## <a name="use-active-directory"></a>Active Directory gebruiken

Bij bestaande blockchain-protocollen worden blockchain-identiteiten voorgesteld als een adres in het netwerk. Azure Blockchain Workbench abstraheert de blockchain-identiteit door deze te koppelen aan een Active Directory-identiteit, zodat het eenvoudiger wordt om bedrijfstoepassingen met Active Directory-identiteiten te bouwen.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Gegevens in de blockchain synchroniseren met opslag buiten de blockchain

Met Azure Blockchain Workbench is het eenvoudiger om blockchain-gebeurtenissen en -gegevens te analyseren doordat gegevens in de blockchain automatisch worden gesynchroniseerd naar opslag buiten de blockchain. In plaats van de gegevens rechtstreeks uit de blockchain te extraheren, kunt u query's uitvoeren op databasesystemen buiten de blockchain, zoals SQL Server. Block Chain-expertise is niet vereist voor eind gebruikers die taken voor gegevens analyse uitvoeren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Architectuur van Azure Blockchain Workbench](architecture.md)
