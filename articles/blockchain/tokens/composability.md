---
title: Composability azure blockchain-tokens
description: De composability van Azure Blockchain Tokens biedt flexibiliteit om tokens te maken voor geavanceerde scenario's.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: a3fe1b290917de20b7c3af31fe386ed93580d850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325125"
---
# <a name="azure-blockchain-tokens-composability"></a>Composability azure blockchain-tokens

[!INCLUDE [Preview note](./includes/preview.md)]

Token-composability biedt flexibiliteit om tokens te maken voor geavanceerde scenario's. Mogelijk hebt u een complex scenario dat niet kan worden geïmplementeerd met behulp van de [vier vooraf gebouwde tokensjablonen.](templates.md#base-token-types) Met tokencomposability u uw eigen tokensjablonen ontwerpen door gedefinieerd gedrag toe te voegen of te verwijderen om uw eigen tokensjabloon te bouwen. Bij het maken van een nieuwe tokensjabloon verifieert Azure Blockchain Tokens alle regels voor tokengrammatica. Samengestelde sjablonen worden opgeslagen in de Azure Blockchain Tokens-service voor uitgifte op verbonden blockchain-netwerken.

U het [tokengedrag](templates.md#token-behaviors) in de volgende secties gebruiken om uw tokensjabloon te ontwerpen.

## <a name="burnable-b"></a>Brandbaar (b)

Mogelijkheid om de tokens uit de voorraad te verwijderen.

Wanneer u bijvoorbeeld online creditcardpunten inwisselt voor een cadeaubon, worden de creditcardpunten verbrand.

## <a name="delegable-g"></a>Delegable (g)

Mogelijkheid om de acties te delegeren die zijn uitgevoerd op het token dat u bezit.

De gemachtigde kan acties uitvoeren als eigenaar van het token. U bijvoorbeeld een delegable token gebruiken om een stemming uit te voeren. Met een delegable token kan de eigenaar van het stemtoken iemand anders namens hen laten stemmen.

## <a name="logable-l"></a>Logable (l)

Mogelijkheid om in te loggen.

U bijvoorbeeld een aanmeldbaar token uitgeven voor een filmdistributie naar elk theater met een specifieke film. Om de film te kunnen afspelen, moet de vertoning een transactie registreren voor elke vertoning, omdat royalty-uitbetalingen per vertoning zijn tijdens de releaserun van de film. De acteurs bouwen kan de film tokens gebruiken om uitbetalingen per film weergegeven per theater in de distributie te valideren.

## <a name="mint-able-m"></a>Mint-staat (m)

Mogelijkheid om extra tokens te slaan voor de tokenklasse. De minter rol omvat het mintable gedrag.

Bijvoorbeeld, een retail bedrijf, die wil een loyaliteitsprogramma te implementeren kan mintable tokens gebruiken voor hun loyaliteitsprogramma. Ze kunnen extra loyaliteitspunten voor hun klanten slaan naarmate hun klantenbestand groeit.  

## <a name="non-subdividable-or-whole-d"></a>Niet-ondergedeeld of geheel (~d)

Beperking om te voorkomen dat een token wordt verdeeld in kleinere delen.

Een enkel schilderij kan bijvoorbeeld niet worden onderverdeeld in meerdere kleinere delen. 

## <a name="non-transferable-t"></a>Niet-overdraagbaar (~t)

Beperking om een verandering van eigenaar van de oorspronkelijke token-eigenaar te voorkomen.

Een universitair diploma is bijvoorbeeld een niet-overdraagbaar token. Zodra een diploma aan een afgestudeerde wordt gegeven, kan het niet worden overgedragen van de afgestudeerde naar een andere persoon.

## <a name="roles-r"></a>Rollen (r)

Mogelijkheid om rollen binnen de sjabloonklasse token te definiëren voor specifiek gedrag.

U een lijst met rolnamen opmaken die een token ondersteunt op de tijd voor het maken van token. Wanneer rollen zijn opgegeven, kan de gebruiker rollen toewijzen aan deze gedragingen. Momenteel wordt alleen de minter-rol ondersteund.

## <a name="singleton-s"></a>Singleton (s)

Beperking om een levering van één token toe te staan.

Een museumartefact is bijvoorbeeld een singleton-token. Museum artefacten zijn uniek. Een token dat een artefact vertegenwoordigt, heeft slechts één item in de voorraad.

## <a name="subdividable-d"></a>Ondergedeeld (d)

Mogelijkheid om een token in kleinere delen te verdelen.

Bijvoorbeeld, een dollar kan worden onderverdeeld in centen.

## <a name="transferable-t"></a>Overdraagbaar (t)

Mogelijkheid om het eigendom van het token over te dragen.

Een eigendomstitel is bijvoorbeeld een overdraagbaar token, dat van de ene persoon naar de andere kan worden overgedragen wanneer de eigenschap wordt verkocht.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Blockchain Tokens-accountbeheer](account-management.md).
