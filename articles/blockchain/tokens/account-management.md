---
title: Account beheer voor Azure Block Chain-tokens
description: Met Azure Block Chain tokens account management kunt u groepen maken en block Chain-accounts koppelen om de toegang tot Block Chain-acties te beheren.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74326105"
---
# <a name="azure-blockchain-tokens-account-management"></a>Account beheer voor Azure Block Chain-tokens

[!INCLUDE [Preview note](./includes/preview.md)]

Voor een Block Chain-oplossing kunnen gebruikers verschillende toegangs niveaus vereisen voor de tokens die zijn gemaakt met de Azure Block Chain tokens-service. In de meeste Block Chain-scenario's moet u verschillende Block Chain-accounts plannen en implementeren die in het groot boek bestaan. U moet ook de toegang tot de andere deel nemers beheren.Met Azure Block Chain tokens account management kunt u groepen maken en block Chain-accounts koppelen om de toegang tot Block Chain-acties te beheren.

## <a name="blockchain-networks"></a>Block chain-netwerken

Met Azure Block Chain-tokens kunt u tokens in een set block chain netwerken implementeren en beheren. U kunt één block Chain of verschillende Block Chain-groot boeken verbinden met de service.

## <a name="accounts"></a>Accounts

Voor Block chain-netwerken die zijn verbonden met Azure Block Chain-tokens, maakt en beheert de service het account persoonlijke-open bare sleutel paren en voert het ondertekenen en verzenden van trans acties uit. Azure Block Chain-tokens bieden ook identiteits toewijzing om accounts met de id van de open bare sleutel in het groot boek te vergelijken.

## <a name="groups"></a>Groepen

Met groepen kunt u een groot aantal Block Chain-accounts beheren in verbonden netwerken. U kunt bijhouden en controleren welke toepassingen en gebruikers in de Directory kunnen gebruikmaken van accounts via Azure Block Chain tokens-Api's. U kunt bijvoorbeeld een set accounts groeperen die verschillende bedrijfs regels of verschillende rollen en toegang tot Block Chain-tokens vertegenwoordigen.

U kunt een groep ook koppelen aan een Azure Active Directory gebruiker of Service-Principal en deze principal heeft machtigingen voor de groep en de bijbehorende accounts.  

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de beschik bare [Azure Block Chain-tokens-sjablonen](templates.md).
