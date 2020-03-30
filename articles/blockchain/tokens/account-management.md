---
title: Azure Blockchain Tokens-accountbeheer
description: Met Azure Blockchain Tokens-accountbeheer u groepen maken en blockchain-accounts koppelen om de toegang tot blockchain-acties te beheren.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74326105"
---
# <a name="azure-blockchain-tokens-account-management"></a>Azure Blockchain Tokens-accountbeheer

[!INCLUDE [Preview note](./includes/preview.md)]

Voor een blockchain-oplossing hebben gebruikers mogelijk verschillende toegangsniveaus nodig tot de tokens die zijn gemaakt met de Azure Blockchain Tokens-service. In de meeste blockchain-scenario's moet u verschillende blockchain-accounts plannen en implementeren die op het grootboek bestaan. U moet ook de toegang tussen deelnemers beheren.Met Azure Blockchain Tokens-accountbeheer u groepen maken en blockchain-accounts koppelen om de toegang tot blockchain-acties te beheren.

## <a name="blockchain-networks"></a>Blockchain-netwerken

Azure Blockchain Tokens maakt implementatie en beheer van tokens mogelijk in een reeks blockchain-netwerken. U één blockchain-grootboek of meerdere blockchain-grootboeken met de service verbinden.

## <a name="accounts"></a>Accounts

Voor blockchain-netwerken die zijn verbonden met Azure Blockchain Tokens, maakt en beheert de service de privé-publieke sleutelparen van het account en voert de ondertekening en indiening van transacties uit. Azure Blockchain Tokens biedt ook identiteitstoewijzing om accounts te koppelen aan de openbare sleutelidentiteit op het grootboek.

## <a name="groups"></a>Groepen

Met groepen u een groot aantal blockchain-accounts beheren in verbonden netwerken. U bijhouden en controleren welke toepassingen en gebruikers in de directory de mogelijkheid hebben om accounts te gebruiken via Azure Blockchain Tokens API's. U bijvoorbeeld een set accounts groeperen die verschillende bedrijfsonderdelen of verschillende rollen vertegenwoordigen en toegang tot blockchain-tokens.

U een groep ook koppelen aan een Azure Active Directory-gebruiker of serviceprincipal en deze principal heeft machtigingen voor de groep en de bijbehorende accounts.  

## <a name="next-steps"></a>Volgende stappen

Meer informatie over beschikbare [Azure Blockchain-tokenssjablonen](templates.md).
