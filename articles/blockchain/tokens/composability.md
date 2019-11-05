---
title: Azure Block Chain-tokens opstellen
description: Azure Block Chain tokens samenstel baarheid biedt flexibiliteit voor het maken van tokens voor geavanceerde scenario's.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: fe932d3ae1874e7a35abb9729a0b80e4fa3512eb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512299"
---
# <a name="azure-blockchain-tokens-composability"></a>Azure Block Chain-tokens opstellen

[!INCLUDE [Preview note](./includes/preview.md)]

Token samenstel baarheid biedt flexibiliteit voor het maken van tokens voor geavanceerde scenario's. Mogelijk hebt u een complex scenario dat niet kan worden geïmplementeerd met behulp van de [vier vooraf samengestelde token sjablonen](templates.md#base-token-types). Met token opstellen kunt u uw eigen token sjablonen ontwerpen door gedefinieerde gedragingen toe te voegen of te verwijderen om uw eigen token sjabloon te maken. Bij het maken van een nieuwe token sjabloon verifieert Azure Block Chain-tokens alle token-grammatica regels. Samengestelde sjablonen worden opgeslagen in de Azure Block Chain tokens-service voor uitgifte op verbonden Block chain netwerken.

U kunt het [token gedrag](templates.md#token-behaviors) in de volgende secties gebruiken om uw token sjabloon te ontwerpen.

## <a name="burnable-b"></a>Branden (b)

De mogelijkheid om de tokens uit de levering te verwijderen.

Wanneer u bijvoorbeeld online creditcard punten inwisselt voor een geschenkbon, worden de creditcard punten gebrand.

## <a name="delegable-g"></a>Delegeer bare (g)

De mogelijkheid voor het delegeren van de acties die zijn uitgevoerd op het token waarvan u de eigenaar bent.

De gemachtigde kan acties uitvoeren als de eigenaar van het token. U kunt bijvoorbeeld een Delegeer bare-token gebruiken om een stem te implementeren. Met een Delegeer bare-token kan de eigenaar van het stem token iemand anders in hun naam laten stemmen.

## <a name="logable-l"></a>Logable (l)

De mogelijkheid om te registreren.

U kunt bijvoorbeeld een logable-token uitgeven voor een film distributie naar elke theater met een specifieke film. De weer gave van de film moet een trans actie registreren voor elke uitzending, omdat royalty-uitbetalingen per uitzending worden weer gegeven tijdens de release van de uitvoering van de film. De bouwers kunnen de film tokens gebruiken voor het valideren van uitbetalingen per film per theater in de distributie.

## <a name="mint-able-m"></a>Licht-mogelijk (m)

De mogelijkheid om extra tokens te licht voor de token klasse. De rol Minter omvat het gedrag mintable.

Een retail bedrijf dat bijvoorbeeld een getrouwheids programma wil implementeren, kan mintable-tokens gebruiken voor hun loyaliteits programma. Ze kunnen extra loyaliteits punten voor hun klanten lichten wanneer hun klanten database groeit.  

## <a name="non-subdividable-or-whole-d"></a>Niet-verdeelbaar of geheel (~ d)

Beperking om te voor komen dat een token wordt onderverdeeld in kleinere delen.

Een tekening met één illustratie kan bijvoorbeeld niet worden onderverdeeld in meerdere kleinere delen. 

## <a name="non-transferable-t"></a>Niet-overdraag bare (~ t)

Beperking om te voor komen dat het eigendom van de oorspronkelijke token eigenaar wordt gewijzigd.

Een universiteit diploma is bijvoorbeeld een token dat niet kan worden overgedragen. Zodra een diploma aan een afgestudeerde is gegeven, kan deze niet worden overgedragen van de afstuderen naar een andere persoon.

## <a name="roles-r"></a>Rollen (r)

De mogelijkheid om rollen te definiëren in de token sjabloon klasse voor specifiek gedrag.

U kunt een lijst opgeven met namen van rollen die door een token worden ondersteund bij de aanmaak tijd van het token. Wanneer rollen zijn opgegeven, kan de gebruiker rollen toewijzen aan deze gedragingen. Op dit moment wordt alleen de rol Minter ondersteund.

## <a name="singleton-s"></a>Singleton (s)

Beperking voor het toestaan van een-aanbod van één token.

Een museum artefact is bijvoorbeeld een singleton-token. Museum artefacten zijn uniek. Een token dat een artefact vertegenwoordigt, heeft slechts één item in de levering.

## <a name="subdividable-d"></a>Onderverdelen (d)

De mogelijkheid om een token te verdelen in kleinere delen.

Een dollar kan bijvoorbeeld worden onderverdeeld in cent.

## <a name="transferable-t"></a>Overdraagbaar (t)

De mogelijkheid om het eigendom van het token over te dragen.

Een eigenschaps titel is bijvoorbeeld een overdraagbaar token dat kan worden overgedragen van een persoon naar een andere wanneer de eigenschap wordt verkocht.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [account beheer van Azure Block Chain-tokens](account-management.md).
