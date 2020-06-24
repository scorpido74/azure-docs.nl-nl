---
title: Consistentie niveaus in Azure Cosmos DB
description: Azure Cosmos DB heeft vijf consistentie niveaus voor het verdelen van uiteindelijk consistentie, Beschik baarheid en latentie.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 5b80a0b03959ec3fba90ed21e291c1fd021916c2
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/20/2020
ms.locfileid: "85119287"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Consistentie niveaus in Azure Cosmos DB

Gedistribueerde data bases die afhankelijk zijn van replicatie voor hoge Beschik baarheid, lage latentie of beide, maken de fundamentele verhouding tussen de Lees consistentie versus Beschik baarheid, latentie en door voer. De meeste commerciële beschik bare gedistribueerde data bases vragen ontwikkel aars om te kiezen tussen de twee uiterst consistente consistentie modellen: *sterke* consistentie en *uiteindelijke* consistentie. De linearizability van het sterke consistentie model is de Gold Standard van data Programmable. Er wordt echter een prijs van een hogere schrijf latentie (in stationaire toestand) en een lagere Beschik baarheid (tijdens fouten) toegevoegd. Aan de andere kant bieden uiteindelijke consistentie een hogere Beschik baarheid en betere prestaties, maar maakt het Program meren van toepassingen lastig.

Azure Cosmos DB consistentie van gegevens als een breed scala aan opties in plaats van twee extreme. Ontwikkel aars kunnen deze opties gebruiken om nauw keurige keuzes en gedetailleerde afwegingen te maken met betrekking tot hoge Beschik baarheid en prestaties.

Met Azure Cosmos DB kunnen ontwikkel aars kiezen uit vijf goed gedefinieerde consistentie niveaus op het consistentie spectrum. Dit zijn de niveaus *sterk*, *gebonden veroudering*, *sessie*, *consistent voor voegsel*en *uiteindelijke* consistentie. De niveaus zijn goed gedefinieerd en intuïtief en kunnen worden gebruikt voor specifieke praktijk scenario's. Elk niveau biedt [Beschik baarheid en prestatie afwegingen](consistency-levels-tradeoffs.md) en wordt ondersteund door service overeenkomsten. In de volgende afbeelding ziet u de verschillende consistentie niveaus als een spectrum.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Consistentie als een spectrum" border="false" :::

De consistentie niveaus zijn neutraal en worden gegarandeerd voor alle bewerkingen, ongeacht de regio van waaruit de lees-en schrijf bewerkingen worden uitgevoerd, het aantal regio's dat aan uw Azure Cosmos-account is gekoppeld, of of uw account is geconfigureerd met één of meerdere schrijf regio's.

## <a name="scope-of-the-read-consistency"></a>Bereik van de Lees consistentie

Lees consistentie is van toepassing op één Lees bewerking binnen een logische partitie. De Lees bewerking kan worden uitgegeven door een externe client of een opgeslagen procedure.

## <a name="configure-the-default-consistency-level"></a>Het standaardconsistentieniveau configureren

U kunt op elk gewenst moment het standaard consistentie niveau configureren voor uw Azure Cosmos-account. Het standaard consistentie niveau dat voor uw account is geconfigureerd, is van toepassing op alle Azure Cosmos-data bases en containers onder dat account. Alle Lees bewerkingen en query's die zijn uitgegeven voor een container of een Data Base, gebruiken standaard het opgegeven consistentie niveau. Zie [het standaard consistentie niveau configureren](how-to-manage-consistency.md#configure-the-default-consistency-level)voor meer informatie.

## <a name="guarantees-associated-with-consistency-levels"></a>Garanties die zijn gekoppeld aan de consistentie niveaus

De uitgebreide Sla's van Azure Cosmos DB garandeert dat 100 procent van de Lees aanvragen voldoet aan de consistentie garantie voor welk consistentie niveau u kiest. Een lees aanvraag voldoet aan de SLA voor consistentie als aan alle consistentie garanties van het consistentie niveau is voldaan. De exacte definities van de vijf consistentie niveaus in Azure Cosmos DB met behulp van de TLA + specificatie taal zijn opgenomen in de [Azure-Cosmos-TLA](https://github.com/Azure/azure-cosmos-tla) github opslag plaats.

De semantiek van de vijf consistentie niveaus worden hier beschreven:

- **Sterk**: sterke consistentie biedt een linearizability-garantie. Linearizability verwijst naar de gelijktijdigheid van aanvragen. De Lees bewerkingen worden gegarandeerd de meest recente doorgevoerde versie van een item te retour neren. Een client ziet nooit een niet-doorgevoerde of gedeeltelijke schrijf bewerking. Gebruikers worden altijd gegarandeerd de laatste vastgelegde schrijf bewerkingen te lezen.

  In de volgende afbeelding ziet u de sterke consistentie met muzikale notities. Wanneer u de gegevens van andere regio's hebt geschreven naar de regio vs-West 2, krijgt u de meest recente waarde:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="video":::

- **Gebonden veroudering**: de Lees bewerkingen worden gegarandeerd de consistentie van het voor voegsel garanderen. De Lees bewerkingen kunnen vertraging oplopen bij schrijf bewerkingen door de meeste *"K"* -versies (dat wil zeggen "updates") van een item of door een *T* -outinterval, afhankelijk van wat het eerst wordt bereikt. Met andere woorden, wanneer u de gebonden veroudering kiest, kan de ' verouderd ' op twee manieren worden geconfigureerd:

- Het aantal versies (*K*) van het item
- Het tijds interval (*T*) waarmee de Lees bewerkingen kunnen worden vertraagd achter de schrijf bewerkingen

Gebonden veroudering bieden de totale wereld wijde volg orde buiten het verouderde venster. Wanneer een client lees bewerkingen uitvoert binnen een regio die schrijf acties accepteert, zijn de garanties die worden geboden door de gebonden verouderde consistentie, identiek aan die garanties van de sterke consistentie.

Binnen het verouderde venster biedt gebonden veroudering de volgende consistentie garanties:

- Consistentie voor clients in dezelfde regio voor een account met één Master = Strong
- Consistentie voor clients in verschillende regio's voor een account met één master = consistent voor voegsel
- Consistentie voor clients die schrijven naar één regio voor een multi-master account = consistent voor voegsel
- Consistentie voor clients die schrijven naar verschillende regio's voor een multi-master account = mogelijk

  Gebonden verouderd wordt vaak gekozen door wereld wijd gedistribueerde toepassingen die weinig schrijf latentie verwachten, maar die de totale garantie voor de globale bestelling vereisen. Gebonden verouderd is handig voor toepassingen met groeps samenwerking en delen, aandelen tikker, publiceren/abonneren/wachtrij, enzovoort. In de volgende afbeelding ziet u de gebonden consistentie van veroudering met muzikale notities. Nadat de gegevens zijn geschreven naar de regio vs-West 2, lezen de regio's ' vs Oost 2 ' en ' Australië-oost ' de geschreven waarde op basis van de geconfigureerde maximale vertragings tijd of het maximum aantal bewerkingen:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="video":::

- **Sessie**: in een enkele client sessie Lees bewerkingen worden gegarandeerd het consistente voor voegsel, monotone Lees bewerkingen, monotone schrijf bewerkingen, lees-uw-schrijf bewerkingen en lees-en schrijf bewerkingen. Hierbij wordt ervan uitgegaan dat u één schrijver-sessie hebt of het sessie token deelt voor meerdere schrijvers.

Clients buiten de sessie die schrijf bewerkingen uitvoeren, zien de volgende garanties:

- Consistentie voor clients in dezelfde regio voor een account met één master = consistent voor voegsel
- Consistentie voor clients in verschillende regio's voor een account met één master = consistent voor voegsel
- Consistentie voor clients die schrijven naar één regio voor een multi-master account = consistent voor voegsel
- Consistentie voor clients die schrijven naar meerdere regio's voor een multi-master account = mogelijk

  Sessie consistentie is het meest gebruikte consistentie niveau voor zowel de ene regio als wereld wijd gedistribueerde toepassingen. Het biedt schrijf latentie, Beschik baarheid en lees doorvoer die vergelijkbaar zijn met die van uiteindelijke consistentie, maar biedt ook de consistentie garanties die van toepassing zijn op de behoeften van toepassingen die zijn geschreven om te kunnen worden gebruikt in de context van een gebruiker. In de volgende afbeelding ziet u de consistentie van de sessie met muzikale notities. De "West US 2 Writer" en de "West US 2 Reader" gebruiken dezelfde sessie (sessie A), zodat ze beide dezelfde gegevens op hetzelfde moment lezen. Terwijl de regio ' Australië-oost ' gebruikmaakt van ' sessie B ', worden gegevens later ontvangen, maar in dezelfde volg orde als de schrijf bewerkingen.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="video":::

- **Consistent voor voegsel**: updates die worden geretourneerd, bevatten een voor voegsel van alle updates, zonder onderbrekingen. Consistent consistentie niveau van het voor voegsel zorgt ervoor dat lees bewerkingen die nooit worden uitgevoerd, niet worden weer gegeven.

Als schrijf bewerkingen zijn uitgevoerd in de volg orde `A, B, C` , ziet een client een `A` van de, `A,B` , of `A,B,C` , maar niet-beschik bare permutaties zoals `A,C` of `B,A,C` . Consistent voor voegsel biedt schrijf latentie, Beschik baarheid en lees doorvoer die vergelijkbaar zijn met die van uiteindelijke consistentie, maar biedt ook de volg orde van de bestellingen die voldoen aan de behoeften van scenario's waarin de volg orde belang rijk is. 

Hieronder vindt u de consistentie garanties voor consistent voor voegsel:

- Consistentie voor clients in dezelfde regio voor een account met één master = consistent voor voegsel
- Consistentie voor clients in verschillende regio's voor een account met één master = consistent voor voegsel
- Consistentie voor clients die schrijven naar één regio voor een multi-master account = consistent voor voegsel
- Consistentie voor clients die schrijven naar meerdere regio's voor een multi-master account = mogelijk

In de volgende afbeelding ziet u de consistentie van het consistentie voorvoegsel met muzikale notities. In alle regio's zien de Lees bewerkingen nooit buiten de juiste volg orde:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="video":::

- **Uiteindelijk**: er is geen garantie voor lees bewerkingen. Als er verder geen schrijf bewerkingen worden uitgevoerd, convergeren de replica's uiteindelijk.  
Uiteindelijke consistentie is de zwakke vorm van consistentie, omdat een client de waarden kan lezen die ouder zijn dan de waarde die het eerder had gelezen. Uiteindelijke consistentie is ideaal wanneer de toepassing geen garantie voor het ordenen van de toepassingen vereist. Voor beelden zijn het aantal retweeten, leuk of niet-threaded opmerkingen. In de volgende afbeelding ziet u de uiteindelijke consistentie met muzikale notities.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="video":::

## <a name="additional-reading"></a>Aanvullende Lees bewerkingen

Lees de volgende artikelen voor meer informatie over consistentie concepten:

- [TLA en specificaties op hoog niveau voor de vijf consistentie niveaus die worden geboden door Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Consistentie van gerepliceerde gegevens die wordt uitgelegd via voetbal (video) door Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Consistentie van gerepliceerde gegevens die wordt uitgelegd via honkbal (White Paper) door Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Sessie garanties voor zwak consistente gerepliceerde gegevens](https://dl.acm.org/citation.cfm?id=383631)
- [Consistentie-afwegingen in het moderne ontwerp van gedistribueerde data base systemen: CAP maakt alleen deel uit van het verhaal](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic gebonden veroudering (PBS) voor praktische gedeeltelijke quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Uiteindelijk consistent-gestart](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie over de consistentie niveaus in Azure Cosmos DB:

- [Het juiste consistentie niveau voor uw toepassing kiezen](consistency-levels-choosing.md)
- [Consistentie niveaus voor Azure Cosmos DB-Api's](consistency-levels-across-apis.md)
- [Beschik baarheid en prestaties voor diverse consistentie niveaus](consistency-levels-tradeoffs.md)
- [Het standaardconsistentieniveau configureren](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Het standaardconsistentieniveau overschrijven](how-to-manage-consistency.md#override-the-default-consistency-level)
