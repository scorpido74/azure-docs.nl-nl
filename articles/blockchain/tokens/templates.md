---
title: Azure Blockchain-tokenssjablonen
description: Azure Blockchain Tokens-sjablonen zijn gestandaardiseerde en herbruikbare sjablonen die het maken en implementeren van tokens op basis van grootboek vereenvoudigen.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252205"
---
# <a name="azure-blockchain-tokens-templates"></a>Azure Blockchain-tokenssjablonen

[!INCLUDE [Preview note](./includes/preview.md)]

Een Azure Blockchain Tokens-sjabloon is een gestandaardiseerde en herbruikbare sjabloon die het maken en implementeren van tokens op basis van grootboek vereenvoudigt. Een sjabloon bestaat uit een formule, die is gebaseerd op de [grammatica van het Token Taxonomy Framework (TTF).](overview.md#token-taxonomy-framework) De grammatica omvat het basistokentype en de set gedragingen voor het token.  

Zo beschrijft de tokensjabloon **τ{d,m,b,r}** een fungibel basistoken dat ondergedeeld, vervangbaar, brandbaar en rolondersteuning heeft.
  
## <a name="base-token-types"></a>Basistokentypen

Bij het definiëren en maken van het token op basis van grootboek voor uw specifieke asset, is het belangrijk om te overwegen welk basistoken u moet gebruiken.

### <a name="fungible"></a>Fungibel

Fungibele tokens (τF) hebben verwisselbare waarde met elkaar, zolang ze in dezelfde klasse of serie. Een token heeft dezelfde waarde als een ander token of een bepaalde hoeveelheid tokens heeft dezelfde waarde als een andere evenveel. Een dollar is bijvoorbeeld een fungibel token. Als twee mensen elk een dollarbiljet vasthouden, kunnen ze deze dollarbiljetten zonder consequenties omwisselen. De dollarbiljetten hebben dezelfde waarde. 

### <a name="non-fungible"></a>Niet-fungibel

Niet-fungibele tokens (τN) zijn niet uitwisselbaar met andere tokens van hetzelfde type als ze meestal verschillende waarden hebben. Een eigenschapstitel is bijvoorbeeld een niet-fungibel token. Eigendomstitels van twee verschillende appartementen in een appartementencomplex zijn niet noodzakelijkerwijs van gelijke waarde, vanwege de locatie van de eenheid of op welke verdieping de eenheid zich bevindt. De waargenomen waarde van de twee eigenschaptiteltokens is niet gelijk.

### <a name="hybrid"></a>Hybride

Hybride tokens zijn tokens die componenten hebben van zowel fungibele tokens als niet-fungibele tokens. Een hybride token is een basistokentype dat eigenaar is van een klasse van het andere tokentype.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Hybride niet-fungibele basis met fungibele segmenten

Een hybride niet-fungibele basis met fungibele segmenten token heeft een niet-fungibele basis met fungibele token segmenten.
Een concertticket is bijvoorbeeld een hybride token waarbij de datum en het tijdstip van het concert het niet-fungibele basistoken is. De tickets in verschillende zitgedeeltes voor het gegeven concert zijn de segmenten met fungibele tokens. De tickets zijn inwisselbaar in hun individuele zitgedeeltes, maar niet over secties.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Hybride fungibele basis met niet-fungibele segmenten

Een hybride fungibele basis met een niet-fungibele segmenten token heeft een fungibele basis met niet-fungibele token segmenten. Bijvoorbeeld, een hypotheek ondersteund beveiliging is een hybride token waar meerdere eigenaren zijn de fungibele basis die is verdeeld over veel eigenaren. De beveiliging is uitwisselbaar. De individuele hypotheken zijn de niet-fungibele segmenten die de specifieke hypotheek ondersteund zekerheid vertegenwoordigen.

## <a name="token-behaviors"></a>Tokengedrag

Een tokengedrag definieert mogelijkheden of beperkingen van het token. Het gedrag omvat ondersteunende eigenschappen die deel uitmaken van de tokendefinitie. Gedragingen kunnen worden toegepast op alle tokentypen of slechts één. Gedrag kan intern of extern zijn, afhankelijk van wat de gedrageffecten zijn. Een intern gedrag maakt of beperkt eigenschappen op het token zelf. Een extern gedrag maakt of beperkt de aanroep van het gedrag van een externe actor.

Zie [token composability](composability.md)voor meer informatie over Azure Blockchain Tokens die token taxonomy framework (TTF) worden ondersteund.

## <a name="pre-built-token-templates"></a>Vooraf gebouwde tokensjablonen

Azure Blockchain Tokens biedt vier vooraf gebouwde tokensjablonen die zonder wijziging kunnen worden gebruikt. U een beroep doen op deze vooraf gebouwde sjablonen voor de meeste use cases om snel aan de slag te gaan met het maken, implementeren en beheren van uw tokens.

### <a name="commodity-tokens"></a>Commodity tokens

Commodity tokens hebben een consistente waarde en zijn overdraagbaar. Bijvoorbeeld een vat olie of een eenheid energie.

**τF{~d,t,m,b,r}** - fungibel, geheel, overdraagbaar, mintbaar, brandbaar en rolondersteuning

Veel blockchain-scenario's vereisen transparantie en zichtbaarheid in de supply chain of meerdere organisaties. Commodity tokens zijn gebaseerd op deze veelvoorkomende use cases. De tokens zijn uitwisselbaar en consistent. De sjabloon voor commodity-token is flexibel en aanpasbaar met metagegevens.

### <a name="qualified-tokens"></a>Gekwalificeerde tokens

Gekwalificeerde tokens vertegenwoordigen iets verdiend en zijn meestal gekoppeld aan één entiteit en kunnen niet worden overgedragen. Bijvoorbeeld een diploma of een parkeerovertreding.

**τN{s,~t}** - niet-fungibel, singleton en niet-overdraagbaar

Verschillende controle- en attestscenario's vereisen dat het eigendom van het token niet kan worden gewijzigd. Er is een set van use cases, die een behoefte hebben om een gekwalificeerde token te verstrekken of de vereniging goed of slecht is.

### <a name="asset-tokens"></a>Assettokens

Asset tokens hebben een unieke waarde afhankelijk van het item en zijn niet gecommoditiseerd. Bijvoorbeeld een museumartefact of een eigendomstitel.

**τN{s,t}** - niet-fungibel, singleton en overdraagbaar

Asset tokens kunnen worden verward met commodity tokens. Het grote verschil tussen de twee tokens is dat asset tokens inherent uniek zijn en waarde onafhankelijk is van het type token dat het is. Een kunstwerk als een olieverfschilderij van een gevestigde kunstenaar is bijvoorbeeld een assettoken. Echter, een kunstafdruk van de Mona Lisa wordt beschouwd als een commodity token. Op dezelfde manier is een eigendomstitel een activatoken omdat de waarde bestaat in de subjectieve kwaliteiten van het onroerend goed.

### <a name="ticket-tokens"></a>Tickettokens

Tickettokens hebben een consistente waarde, maar verlopen meestal. Bijvoorbeeld een vliegticket.

**τN{m,b,r}** - niet-fungibel, mintbaar, brandbaar en hebben rolondersteuning.

Tickettokens hebben meestal een vervaldatum waardoor ze verschillen van een normaal commodity-token. Een vliegticket, concertticket of sportticket hebben bijvoorbeeld allemaal opties voor toegewezen zitplaatsen met specifieke gebruiksdata. U niet gemakkelijk tickets uitwisselen tussen data of zitjes.

## <a name="next-steps"></a>Volgende stappen

Als u meer flexibiliteit voor uw scenario nodig hebt, leest u meer over het maken van uw eigen tokensjablonen met behulp van [tokencomposability.](composability.md)
