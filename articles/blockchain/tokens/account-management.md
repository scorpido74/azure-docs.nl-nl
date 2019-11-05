---
title: Account beheer voor Azure Block Chain-tokens
description: Met Azure Block Chain tokens account management kunt u groepen maken en block Chain-accounts koppelen om de toegang tot Block Chain-acties te beheren.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: b396b359585c8865c3b08284f4398076f37c7a6e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512962"
---
# <a name="azure-blockchain-tokens-account-management"></a>Account beheer voor Azure Block Chain-tokens

[!INCLUDE [Preview note](./includes/preview.md)]

Voor een Block Chain-oplossing kunnen gebruikers verschillende toegangs niveaus vereisen voor de tokens die zijn gemaakt met de Azure Block Chain tokens-service. In de meeste Block Chain-scenario's moet u verschillende Block Chain-accounts plannen en implementeren die in het groot boek bestaan. U moet ook de toegang tot de andere deel nemers beheren. Met Azure Block Chain tokens account management kunt u groepen maken en block Chain-accounts koppelen om de toegang tot Block Chain-acties te beheren.

## <a name="blockchain-networks"></a>Block chain-netwerken

Met Azure Block Chain-tokens kunt u tokens in een set block chain netwerken implementeren en beheren. U kunt één block Chain of verschillende Block Chain-groot boeken verbinden met de service.

## <a name="accounts"></a>Accounts

Voor Block chain-netwerken die zijn verbonden met Azure Block Chain-tokens, maakt en beheert de service het account persoonlijke-open bare sleutel paren en voert het ondertekenen en verzenden van trans acties uit. Azure Block Chain-tokens bieden ook identiteits toewijzing om accounts met de id van de open bare sleutel in het groot boek te vergelijken.

## <a name="groups"></a>Groepen

Met groepen kunt u een groot aantal Block Chain-accounts beheren in verbonden netwerken. U kunt bijhouden en controleren welke toepassingen en gebruikers in de Directory kunnen gebruikmaken van accounts via Azure Block Chain tokens-Api's. U kunt bijvoorbeeld een set accounts groeperen die verschillende bedrijfs regels of verschillende rollen en toegang tot Block Chain-tokens vertegenwoordigen.

U kunt een groep ook koppelen aan een Azure Active Directory gebruiker of Service-Principal en deze principal heeft machtigingen voor de groep en de bijbehorende accounts.  

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de beschik bare [Azure Block Chain-tokens-sjablonen](templates.md).
