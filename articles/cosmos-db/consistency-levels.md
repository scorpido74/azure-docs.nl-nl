---
title: Consistentieniveaus in Azure Cosmos DB
description: Azure Cosmos DB heeft vijf consistentieniveaus om eventuele consistentie, beschikbaarheid en latentietrade-offs in evenwicht te brengen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e5966f142ece32f148c56edb5b0ef5dfd88603aa
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380085"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Consistentieniveaus in Azure Cosmos DB

Gedistribueerde databases die afhankelijk zijn van replicatie voor hoge beschikbaarheid, lage latentie of beide, maken de fundamentele afweging tussen de leesconsistentie versus beschikbaarheid, latentie en doorvoer. De meeste commercieel beschikbare gedistribueerde databases vragen ontwikkelaars om te kiezen tussen de twee extreme consistentiemodellen: *sterke* consistentie en *uiteindelijke* consistentie. De linearizability van het sterke consistentiemodel is de gouden standaard van data-programmeerbaarheid. Maar het voegt een prijs van hogere schrijflatentie (in steady state) en verminderde beschikbaarheid (tijdens storingen). Aan de andere kant biedt uiteindelijke consistentie een hogere beschikbaarheid en betere prestaties, maar maakt het moeilijk om toepassingen te programmeren.

Azure Cosmos DB benadert gegevensconsistentie als een spectrum van keuzes in plaats van twee uitersten. Ontwikkelaars kunnen deze opties gebruiken om nauwkeurige keuzes en gedetailleerde afwegingen te maken met betrekking tot hoge beschikbaarheid en prestaties.

Met Azure Cosmos DB kunnen ontwikkelaars kiezen uit vijf goed gedefinieerde consistentieniveaus op het consistentiespectrum. Deze niveaus omvatten *sterke*, *begrensde staleness*, *sessie,* *consistent voorvoegsel*en *uiteindelijke* consistentie. De levels zijn goed gedefinieerd en intuïtief en kunnen worden gebruikt voor specifieke scenario's in de echte wereld. Elk niveau biedt [beschikbaarheid en prestatieafwegingen](consistency-levels-tradeoffs.md) en wordt ondersteund door SLA's. De volgende afbeelding toont de verschillende consistentieniveaus als een spectrum.

![Consistentie als spectrum](./media/consistency-levels/five-consistency-levels.png)

De consistentieniveaus zijn regio-agnostisch en zijn gegarandeerd voor alle bewerkingen, ongeacht het gebied waarvan de reads en schrijfbewerkingen worden weergegeven, het aantal regio's dat is gekoppeld aan uw Azure Cosmos-account of dat uw account is geconfigureerd met één of meerdere schrijfregio's.

## <a name="scope-of-the-read-consistency"></a>Bereik van de leesconsistentie

Leesconsistentie is van toepassing op één leesbewerking die binnen een logische partitie is ondergebracht. De leesbewerking kan worden uitgevoerd door een externe client of een opgeslagen procedure.

## <a name="configure-the-default-consistency-level"></a>Het standaardconsistentieniveau configureren

U het standaardconsistentieniveau op uw Azure Cosmos-account op elk gewenst moment configureren. Het standaardconsistentieniveau dat is geconfigureerd voor uw account, is van toepassing op alle Azure Cosmos-databases en -containers onder dat account. Alle lees- en query's die zijn uitgegeven tegen een container of een database gebruiken standaard het opgegeven consistentieniveau. Zie voor meer informatie hoe u [het standaardconsistentieniveau configureert.](how-to-manage-consistency.md#configure-the-default-consistency-level)

## <a name="guarantees-associated-with-consistency-levels"></a>Garanties in verband met consistentieniveaus

De uitgebreide SLA's van Azure Cosmos DB garanderen dat 100 procent van de leesaanvragen voldoet aan de consistentiegarantie voor elk consistentieniveau dat u kiest. Een leesaanvraag voldoet aan de consistentieSLA als aan alle consistentiegaranties die aan het consistentieniveau zijn gekoppeld, is voldaan. De precieze definities van de vijf consistentieniveaus in Azure Cosmos DB met behulp van de TLA+ specificatietaal worden geleverd in de [azuurblauwe-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub repo.

De semantiek van de vijf consistentieniveaus wordt hier beschreven:

- **Sterk**: Sterke consistentie biedt een linearizability garantie. Linearizability verwijst naar het gelijktijdig dienen van verzoeken. De reads zijn gegarandeerd de meest recente vastgelegde versie van een item terug te keren. Een client ziet nooit een niet-vastgelegde of gedeeltelijke schrijf. Gebruikers zijn altijd gegarandeerd om de laatste geëngageerde schrijven te lezen.

  De volgende afbeelding illustreert de sterke consistentie met muzieknoten. Nadat de gegevens naar de regio 'West US 2' zijn geschreven, krijgt u de meest recente waarde wanneer u de gegevens uit andere regio's leest:

  ![video](media/consistency-levels/strong-consistency.gif)

- **Begrensde staleness**: De reads zijn gegarandeerd om de consistente-voorvoegsel garantie te eren. De leest kan achterblijven schrijft door de meeste *"K"* versies (dat wil zeggen, "updates") van een item of door *"T"* tijdsinterval. Met andere woorden, wanneer u grenzende staleness kiest, kan de "staleness" op twee manieren worden geconfigureerd:

- Het aantal versies *(K)* van het item
- Het tijdsinterval (*T*) waardoor de leest kan achterblijven bij de schrijft

Bounded staleness biedt totale wereldwijde orde buiten het 'staleness-venster'. Wanneer een klant leesbewerkingen uitvoert binnen een regio die schrijft accepteert, zijn de garanties die worden geboden door de consistentie van de begrensde staleness identiek aan die garanties door de sterke consistentie.

In het staleness venster biedt Bounded Staleness de volgende consistentiegaranties:

- Consistentie voor clients in dezelfde regio voor een account met één master = Sterk
- Consistentie voor clients in verschillende regio's voor één master-account = consistent voorvoegsel
- Consistentie voor clients die naar één regio schrijven voor een multimaster-account = consistent voorvoegsel
- Consistentie voor clients die naar verschillende regio's schrijven voor een multi-master account = Eventual

  Bounded staleness wordt vaak gekozen door wereldwijd gedistribueerde applicaties die lage schrijflatencies verwachten, maar totale wereldwijde ordergarantie vereisen. Bounded staleness is zeer geschikt voor toepassingen met groepssamenwerking en delen, voorraadticker, publiceren-abonneren /in de wachtrij staan enz. De volgende afbeelding illustreert de begrensde staleness consistentie met muzikale noten. Nadat de gegevens naar de regio "West US 2" zijn geschreven, lezen de regio's 'Oost-VS 2' en 'Australië-Oost' de geschreven waarde op basis van de geconfigureerde maximale vertragingstijd of de maximale bewerkingen:

  ![video](media/consistency-levels/bounded-staleness-consistency.gif)

- **Sessie**: Binnen een enkele client sessie leest zijn gegarandeerd ter ere van de consistente-prefix, monotoon leest, monotoon schrijft, read-your-writes, en write-follows-reads garanties. Dit veronderstelt een enkele "writer" sessie of het delen van de sessie token voor meerdere schrijvers.

Klanten buiten de sessie die schrijft, krijgen de volgende garanties te zien:

- Consistentie voor clients in dezelfde regio voor een account met één master = Consistent voorvoegsel
- Consistentie voor clients in verschillende regio's voor één master-account = consistent voorvoegsel
- Consistentie voor clients die naar één regio schrijven voor een multimaster-account = consistent voorvoegsel
- Consistentie voor clients die naar meerdere regio's schrijven voor een multi-master account = Eventual

  Sessieconsistentie is het veelgebruikte consistentieniveau voor zowel afzonderlijke regio's als wereldwijd gedistribueerde toepassingen. Het biedt schrijflatencies, beschikbaarheid en leesdoorvoer die vergelijkbaar is met die van uiteindelijke consistentie, maar biedt ook de consistentiegaranties die passen bij de behoeften van toepassingen die zijn geschreven om te werken in de context van een gebruiker. De volgende afbeelding illustreert de sessie consistentie met muzikale noten. De "West US 2 schrijver" en de "West US 2 reader" zijn met behulp van dezelfde sessie (Sessie A), zodat ze allebei dezelfde gegevens te lezen op hetzelfde moment. Terwijl de "Australië Oost" regio is met behulp van "Sessie B" dus, het ontvangt gegevens later, maar in dezelfde volgorde als de schrijft.

  ![video](media/consistency-levels/session-consistency.gif)

- **Consistent voorvoegsel:** Updates die worden geretourneerd bevatten een voorvoegsel van alle updates, zonder hiaten. Consistent voorvoegselconsistentieniveau garandeert dat lezen nooit out-of-order-schrijfbewerkingen ziet.

Als schrijfbewerkingen in de volgorde `A, B, C` zijn uitgevoerd, ziet de client `A`, `A,B` of `A,B,C`, maar nooit schrijfbewerkingen die niet op volgorde plaatsvinden, zoals `A,C` of `B,A,C`. Consistent Prefix biedt schrijflatencies, beschikbaarheid en leesdoorvoer die vergelijkbaar is met die van eventuele consistentie, maar biedt ook de ordergaranties die passen bij de behoeften van scenario's waarin volgorde belangrijk is. 

Hieronder vindt u de consistentiegaranties voor Consistent Prefix:

- Consistentie voor clients in dezelfde regio voor een account met één master = Consistent voorvoegsel
- Consistentie voor clients in verschillende regio's voor één master-account = consistent voorvoegsel
- Consistentie voor clients die naar één regio schrijven voor een multimaster-account = consistent voorvoegsel
- Consistentie voor clients die naar meerdere regio's schrijven voor een multi-master account = Eventual

De volgende afbeelding illustreert de consistentie voorvoegsel consistentie met muzieknoten. In alle regio's, de leest nooit zien buiten de orde schrijft:

  ![video](media/consistency-levels/consistent-prefix.gif)

- **Eventual**: Er is geen bestelgarantie voor reads. Bij afwezigheid van verdere schrijft, de replica's uiteindelijk convergeren.  
Uiteindelijke consistentie is de zwakste vorm van consistentie omdat een client de waarden kan lezen die ouder zijn dan de waarden die hij eerder had gelezen. Uiteindelijke consistentie is ideaal wanneer de toepassing geen bestelgaranties vereist. Voorbeelden hiervan zijn het aantal retweets, vind-ik-leuks of niet-threaded opmerkingen. De volgende afbeelding illustreert de uiteindelijke samenhang met muzieknoten.

  ![video](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>Aanvullende lezing

Lees de volgende artikelen voor meer informatie over consistentieconcepten:

- [TLA+-specificaties op hoog niveau voor de vijf consistentieniveaus die Azure Cosmos DB biedt](https://github.com/Azure/azure-cosmos-tla)
- [Gerepliceerde data consistentie uitgelegd door honkbal (video) door Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Gerepliceerde data consistentie uitgelegd door honkbal (whitepaper) door Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Sessiegaranties voor zwak consistente gerepliceerde gegevens](https://dl.acm.org/citation.cfm?id=383631)
- [Consistentie afwegingen in moderne gedistribueerde database systemen ontwerp: CAP is slechts een deel van het verhaal](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) voor praktische gedeeltelijke quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Uiteindelijk Consistent - Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie over consistentieniveaus in Azure Cosmos DB:

- [Kies het juiste consistentieniveau voor uw toepassing](consistency-levels-choosing.md)
- [Consistentieniveaus in Azure Cosmos DB API's](consistency-levels-across-apis.md)
- [Beschikbaarheid en prestatieafwegingen voor verschillende consistentieniveaus](consistency-levels-tradeoffs.md)
- [Het standaardconsistentieniveau configureren](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Het standaardconsistentieniveau overschrijven](how-to-manage-consistency.md#override-the-default-consistency-level)
