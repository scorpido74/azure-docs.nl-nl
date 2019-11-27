---
title: Sjablonen voor Azure Block Chain-tokens
description: Sjablonen voor Azure Block Chain-tokens zijn gestandaardiseerde en herbruikbare sjablonen waarmee het maken en implementeren van op het groot boek gebaseerde tokens wordt vereenvoudigd.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324975"
---
# <a name="azure-blockchain-tokens-templates"></a>Sjablonen voor Azure Block Chain-tokens

[!INCLUDE [Preview note](./includes/preview.md)]

Een Azure Block Chain-tokens-sjabloon is een gestandaardiseerde en herbruikbare sjabloon waarmee het maken en implementeren van op het groot boek gebaseerde tokens wordt vereenvoudigd. Een sjabloon bestaat uit een formule die is gebaseerd op de [ttf-grammatica (token taxonomie Framework)](overview.md#token-taxonomy-framework) . De grammatica omvat het basis token type en de set gedragingen voor het token.  

Bijvoorbeeld, **τϜ {d, m, b, r}-** token sjabloon beschrijft een vervangbaar-basis token dat sub-elementbaar, mintable, brandbaar is en functie ondersteuning heeft.
  
## <a name="base-token-types"></a>Basis token typen

Wanneer u het op het groot boek gebaseerde token voor uw specifieke Asset definieert en maakt, is het belang rijk om te bepalen welk basis token u wilt gebruiken.

### <a name="fungible"></a>Vervangbaar

Vervangbaar tokens (τF) hebben elkaar onderling verwisselbaar, zolang ze zich in dezelfde klasse of reeks bevinden. Een token heeft dezelfde waarde als een ander token of een bepaalde hoeveelheid tokens heeft dezelfde waarde als een andere gelijk aantal. Een dollar is bijvoorbeeld een vervangbaar-token. Als twee personen elk een geld van de dollar hebben, kunnen ze deze dollar kosten zonder enige voor delen. De geld facturen hebben een gelijke waarde. 

### <a name="non-fungible"></a>Niet-vervangbaar

Niet-vervangbaar tokens (τN) zijn niet uitwisselbaar met andere tokens van hetzelfde type, aangezien ze doorgaans verschillende waarden hebben. Een eigenschaps titel is bijvoorbeeld een niet-vervangbaar-token. Eigenschaps titels voor twee verschillende appartementen in een Apartment-complex zijn niet noodzakelijkerwijs gelijk aan een gelijke waarde, omdat de locatie van de eenheid of de basis van de eenheid is. De waargenomen waarde van de twee eigenschaps titel tokens is niet gelijk.

### <a name="hybrid"></a>Hybride

Hybride tokens zijn tokens met onderdelen van zowel vervangbaar-tokens als niet-vervangbaar-tokens. Een hybride token is een basis token type dat eigenaar is van een klasse van het andere token type.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Hybride niet-vervangbaar Base met vervangbaar-segmenten

Een hybride niet-vervangbaar Base met vervangbaar segmenten token heeft een niet-vervangbaar Base met vervangbaar-token segmenten.
Een concert ticket is bijvoorbeeld een hybride token waarbij de datum en tijd van het concert het basis token niet-vervangbaar zijn. De tickets in verschillende Seat-secties voor het gegeven concert zijn de segmenten met vervangbaar-tokens. De tickets kunnen worden uitgewisseld in hun afzonderlijke zitplaatsen, maar niet in secties.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Hybride vervangbaar-basis met niet-vervangbaar segmenten

Een hybride vervangbaar-basis met een niet-vervangbaar segmenten-token heeft een vervangbaar Base met niet-vervangbaar-token segmenten. Een voor beeld van een hypothecaire beveiliging is een Hybrid-token waarbij meerdere eigen aren de vervangbaar Base zijn die over veel eigen aren wordt gesplitst. De beveiliging is uitwisselbaar. De afzonderlijke hypotheken zijn de niet-vervangbaar segmenten die de specifieke, voor de beveiliging van hypothecaire veiligheid vertegenwoordigen.

## <a name="token-behaviors"></a>Token gedrag

Een token gedrag definieert mogelijkheden of beperkingen van het token. Het gedrag bevat ondersteunende eigenschappen die deel uitmaken van de token definitie. Gedragingen kunnen worden toegepast op alle token typen of alleen op één. Gedragingen kunnen intern of extern zijn, afhankelijk van de gedrags effecten. Een intern gedrag maakt of beperkt eigenschappen van het token zelf. Een extern gedrag maakt of beperkt de aanroep van het gedrag van een externe actor.

Zie [token-samenstel](composability.md)baarheid voor meer informatie over tokens die worden ondersteund door tokens van Azure Block chain.

## <a name="pre-built-token-templates"></a>Vooraf samengestelde token sjablonen

Azure Block Chain tokens biedt vier vooraf ontwikkelde token sjablonen die zonder wijzigingen kunnen worden gebruikt. U kunt deze vooraf gemaakte sjablonen voor de meeste use-cases aanroepen om uw tokens snel te maken, implementeren en beheren.

### <a name="commodity-tokens"></a>Basisproduct tokens

Basisproduct tokens hebben een consistente waarde en kunnen worden overschreven. Bijvoorbeeld een vat olie of een energie-eenheid.

**τF {~ d, t, m, b, r}** -vervangbaar, volledig, overdraagbaar, mintable, brandbaar en hebben functie ondersteuning

Veel Block Chain-scenario's vereisen transparantie en zicht baarheid in de toeleverings keten of meerdere organisaties. Basisproduct tokens zijn gebaseerd op deze algemene use cases. De tokens zijn onderling verwisselbaar en consistent. De sjabloon voor het basisproduct token is flexibel en aanpasbaar met meta gegevens.

### <a name="qualified-tokens"></a>Gekwalificeerde tokens

Gekwalificeerde tokens vertegenwoordigen een toegewezen object en zijn meestal gekoppeld aan één entiteit en kunnen niet worden overgedragen. Bijvoorbeeld een diploma of een parkeer schending.

**τN {s, ~ t}** -niet-vervangbaar, Singleton en niet-overdraag bare

De eigendom van het token kan niet worden gewijzigd vanwege verschillende audit-en attest scenario's. Er is een set use-cases die een gekwalificeerd token moeten bieden, ongeacht of de koppeling goed of slecht is.

### <a name="asset-tokens"></a>Asset-tokens

Asset-tokens hebben een unieke waarde die afhankelijk is van het item en niet commoditized. Bijvoorbeeld een museum artefact of een eigenschaps titel.

**τN {s, t}** -niet-vervangbaar, Singleton en overdraagbaar

Asset-tokens kunnen worden verward met basisproduct tokens. Het belangrijkste verschil tussen de twee tokens is dat Asset-tokens inherent uniek zijn en dat de waarde onafhankelijk is van het type token. Zo is een stuk illustratie zoals een olie tekening door een gevestigde kunstenaar een Asset-token. Een Art-afdruk van de Mona Lisa wordt echter beschouwd als een basisproduct token. Op dezelfde manier is een eigenschaps titel een Asset-token, omdat de waarde bestaat in de subjectieve kwaliteiten van de eigenschap.

### <a name="ticket-tokens"></a>Ticket tokens

Ticket tokens hebben een consistente waarde, maar verlopen normaal gesp roken. Bijvoorbeeld een vlieg ticket.

**τN {m, b, r}** -non-vervangbaar, mintable, brandable en hebben ondersteuning voor rollen.

Ticket tokens hebben doorgaans een verloop datum die ze afwijkt van een regulier basisproduct token. Bijvoorbeeld, een vliegtuig ticket, een concert ticket of een sport ticket hebben allemaal opties voor toegewezen zitplaatsen met specifieke datum van gebruik. U kunt tickets niet eenvoudig uitwisselen tussen datums of plaatsen.

## <a name="next-steps"></a>Volgende stappen

Als u meer flexibiliteit nodig hebt voor uw scenario, kunt u leren hoe u uw eigen token sjablonen maakt met behulp van [token-opstellen](composability.md).
