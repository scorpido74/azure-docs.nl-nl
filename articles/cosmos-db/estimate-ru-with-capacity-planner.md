---
title: Kosten schatten met behulp van de Azure Cosmos DB-capaciteitsplanner
description: Met de Azure Cosmos DB-capaciteitsplanner u de vereiste doorvoer (RU/s) en de kosten voor uw werkbelasting schatten. In dit artikel wordt beschreven hoe u de nieuwe versie van de capaciteitsplanner gebruiken om de vereiste doorvoer en kosten te schatten.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68707628"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>RU/s schatten met behulp van de Azure Cosmos DB-capaciteitsplanner

Het configureren van uw Azure Cosmos-databases en -containers met de juiste hoeveelheid ingerichte doorvoer of [aanvraageenheden (RU/s)](request-units.md)voor uw werkbelasting is essentieel voor het optimaliseren van kosten en prestaties. In dit artikel wordt beschreven hoe u de Azure Cosmos [DB-capaciteitsplanner](https://cosmos.azure.com/capacitycalculator/) gebruiken om een schatting te krijgen van de vereiste RU/s en de kosten van uw werkbelasting. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Overslag en kosten schatten met Azure Cosmos DB-capaciteitsplanner

De capaciteitsplanner kan in twee modi worden gebruikt.

|**Modus**  |**Beschrijving**  |
|---------|---------|
|Basic|Biedt een snelle, hoogwaardige RU/s en kostenraming. In deze modus wordt uitgegaan van de standaardAzure Cosmos DB-instellingen voor indexeringsbeleid, consistentie en andere parameters. <br/><br/>Gebruik de basismodus voor een snelle schatting op hoog niveau wanneer u een potentiële werkbelasting evalueert die moet worden uitgevoerd op Azure Cosmos DB.|
|Geavanceerd|Biedt een meer gedetailleerde RU/s en kostenraming, met de mogelijkheid om aanvullende instellingen af te stemmen : indexeringsbeleid, consistentieniveau en andere parameters die van invloed zijn op de kosten en doorvoer. <br/><br/>Gebruik de geavanceerde modus wanneer u RU/s schat voor een nieuw project of een meer gedetailleerde schatting wilt. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Ingerichte doorvoer en kosten schatten met basismodus
Als u een snelle schatting wilt krijgen van uw werkbelasting met de basismodus, navigeert u naar de [capaciteitsplanner.](https://cosmos.azure.com/capacitycalculator/) Voer de volgende parameters in op basis van uw werkbelasting: 

|**Invoer**  |**Beschrijving**  |
|---------|---------|
|Aantal regio's|Azure Cosmos DB is beschikbaar in alle Azure-regio's. Selecteer het aantal regio's dat nodig is voor uw werkbelasting. U een willekeurig aantal regio's koppelen aan uw Cosmos-account. Zie [wereldwijde distributie](distribute-data-globally.md) in Azure Cosmos DB voor meer informatie.|
|Schrijfbewerkingen in meerdere regio's|Als u [schrijven met meerdere regio's](distribute-data-globally.md#key-benefits-of-global-distribution)inschakelt, kan uw toepassing lezen en schrijven naar een Azure-regio. Als u schrijven met meerdere regio's uitschakelt, kan uw toepassing gegevens naar één regio schrijven. <br/><br/> Schakel schrijft meerdere regio's in als u verwacht een actief actieve werkbelasting te hebben waarvoor een lage latentie vereist is in verschillende regio's. Bijvoorbeeld een IOT-workload die gegevens naar de database schrijft bij hoge volumes in verschillende regio's. <br/><br/> Multi-region schrijft garanties 99,999% lees- en schrijfbeschikbaarheid. Multi-regio schrijft vereisen meer doorvoer in vergelijking met de enkele schrijfregio's. Zie voor meer informatie [hoe DE's verschillen voor eenartikel over afzonderlijke en meerdere schrijfregio's.](optimize-cost-regions.md)|
|Totaal opgeslagen gegevens (per regio)|Totaal geschatte gegevens die in GB zijn opgeslagen in één regio.|
|Itemgrootte|De geschatte grootte van het gegevensitem (bijvoorbeeld document), variërend van 1 KB tot 2 MB. |
|Leest/sec per regio|Aantal reads verwacht per seconde. |
|Schrijft/sec per regio|Aantal writes verwacht per seconde. |

Nadat u de vereiste gegevens hebt ingevuld, selecteert u **Berekenen**. Op het tabblad **Kostenraming** worden de totale kosten voor opslag en ingerichte doorvoer weergegeven. U de koppeling **Details weergeven** op dit tabblad uitbreiden om de verdeling van de doorvoer te krijgen die nodig is voor lees- en schrijfverzoeken. Elke keer dat u de waarde van een veld wijzigt, selecteert u **Berekenen** om de geschatte kosten opnieuw te berekenen. 

![Basismodus capaciteitsplanner](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Ingerichte doorvoer en kosten schatten met behulp van de geavanceerde modus

Met de geavanceerde modus u meer instellingen bieden die van invloed zijn op de schatting van de RU/s. Als u deze optie wilt gebruiken, navigeert u naar de [capaciteitsplanner](https://cosmos.azure.com/capacitycalculator/) en meldt u zich aan bij het hulpprogramma met een account dat u voor Azure gebruikt. De aanmeldingsoptie is beschikbaar in de rechterhoek. 

Nadat u zich hebt aangemeld, u extra velden zien in vergelijking met de velden in de basismodus. Voer de extra parameters in op basis van uw werkbelasting. 

|**Invoer**  |**Beschrijving**  |
|---------|---------|
|API|Azure Cosmos DB is een multi-model en multi-API service. Selecteer SQL (Core) API voor nieuwe workloads. |
|Aantal regio's|Azure Cosmos DB is beschikbaar in alle Azure-regio's. Selecteer het aantal regio's dat nodig is voor uw werkbelasting. U een willekeurig aantal regio's koppelen aan uw Cosmos-account. Zie [wereldwijde distributie](distribute-data-globally.md) in Azure Cosmos DB voor meer informatie.|
|Schrijfbewerkingen in meerdere regio's|Als u [schrijven met meerdere regio's](distribute-data-globally.md#key-benefits-of-global-distribution)inschakelt, kan uw toepassing lezen en schrijven naar een Azure-regio. Als u schrijven met meerdere regio's uitschakelt, kan uw toepassing gegevens naar één regio schrijven. <br/><br/> Schakel schrijft meerdere regio's in als u verwacht een actief actieve werkbelasting te hebben waarvoor een lage latentie vereist is in verschillende regio's. Bijvoorbeeld een IOT-workload die gegevens naar de database schrijft bij hoge volumes in verschillende regio's. <br/><br/> Multi-region schrijft garanties 99,999% lees- en schrijfbeschikbaarheid. Multi-regio schrijft vereisen meer doorvoer in vergelijking met de enkele schrijfregio's. Zie voor meer informatie [hoe DE's verschillen voor eenartikel over afzonderlijke en meerdere schrijfregio's.](optimize-cost-regions.md)|
|Standaardconsistentie|Azure Cosmos DB ondersteunt 5 consistentieniveaus, zodat ontwikkelaars de afweging tussen consistentie, beschikbaarheid en latentieafwegingen kunnen balanceren. Zie het artikel [consistentieniveaus](consistency-levels.md) voor meer informatie. <br/><br/> Azure Cosmos DB maakt standaard gebruik van sessieconsistentie, wat de mogelijkheid garandeert om uw eigen schrijfbewerkingen in een sessie te lezen. <br/><br/> Het kiezen van sterke of begrensde staleness vereist het dubbele van de vereiste RU / s voor leest, in vergelijking met sessie, consistente voorvoegsel, en eventuele consistentie. Sterke consistentie met multi-regio schrijft wordt niet ondersteund en zal automatisch standaard naar single-region schrijft met een sterke consistentie. |
|Indexeringsbeleid|Azure Cosmos DB [indexeert](index-policy.md) standaard alle eigenschappen in alle items voor flexibele en efficiënte query's (kaarten aan het **beleid voor automatisch** indexeren). <br/><br/> Als u **zich afkiest,** wordt geen van de eigenschappen geïndexeerd. Dit resulteert in de laagste RU-kosten voor schrijft. Selecteer **beleid uit** als u alleen [puntreads](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) verwacht te doen (key value lookups) en/of schrijft en geen query's. <br/><br/> Met het aangepaste indexeringsbeleid u specifieke eigenschappen opnemen of uitsluiten van de index voor lagere schrijfdoorvoer en -opslag. Zie [indexeringsbeleid](index-overview.md) en [voorbeeldindexerbeleid](how-to-manage-indexing-policy.md#indexing-policy-examples) voor meer informatie.|
|Totaal opgeslagen gegevens (per regio)|Totaal geschatte gegevens die in GB zijn opgeslagen in één regio.|
|Werkbelastingmodus|Selecteer **Stabiel** als het werkbelastingsvolume constant is. <br/><br/> Selecteer **Variabel** als het werkbelastingvolume in de loop van de tijd verandert.  Bijvoorbeeld tijdens een specifieke dag of een maand. <br/><br/> De volgende instellingen zijn beschikbaar als u de variabele werkbelastingoptie kiest:<ul><li>Percentage tijd op het hoogtepunt: percentage van de tijd in een maand waar uw werklast piek (hoogste) doorvoer vereist. <br/><br/> Bijvoorbeeld, als u een werklast die hoge activiteit heeft tijdens 9am - 18:00 doordeweekse kantooruren, dan is het percentage van de tijd op de piek is: 45 uur op piek / 730 uur / maand = ~ 6%.<br/><br/></li><li>Reads/sec per regio op piek - Aantal reads verwacht per seconde op het hoogtepunt.</li><li>Schrijft/sec per regio op piek – Aantal writes verwacht per seconde op piek.</li><li>Reads/sec per regio off peak – Aantal reads verwacht per seconde tijdens de daluren.</li><li>Schrijft / sec per regio off peak - Aantal schrijft verwacht per seconde tijdens de daluren.</li></ul>Met piek- en dalintervallen u uw kosten optimaliseren door [uw ingerichte doorvoer programmatisch](set-throughput.md#update-throughput-on-a-database-or-a-container) op en neer te schalen.|
|Itemgrootte|De grootte van het gegevensitem (bijvoorbeeld document), variërend van 1 KB tot 2 MB. <br/><br/>U ook **een voorbeelddocument (JSON) uploaden** voor een nauwkeurigere schatting.<br/><br/>Als uw werkbelasting meerdere soorten items (met verschillende JSON-inhoud) in dezelfde container heeft, u meerdere JSON-documenten uploaden en de schatting krijgen. Gebruik de knop **Nieuw item toevoegen** om meerdere voorbeeld-JSON-documenten toe te voegen.|

U ook de knop **Schat opslaan** gebruiken om een CSV-bestand met de huidige schatting te downloaden. 

![De geavanceerde modus Capaciteitsplanner](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

De prijzen in de Azure Cosmos DB-capaciteitsplanner zijn schattingen op basis van de openbare prijstarieven voor doorvoer en opslag. Alle prijzen worden weergegeven in Amerikaanse dollars. Raadpleeg de [prijspagina azure cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) om alle tarieven per regio te bekijken.  

## <a name="estimating-throughput-for-queries"></a>Doorvoer voor query's schatten

De azure cosmos-capaciteitscalculator gaat ervan uit dat punt leest (een lezing van een enkel item, bijvoorbeeld document, op id- en partitiesleutelwaarde) en schrijft voor de werkbelasting. Als u de doorvoer wilt schatten die nodig is voor query's, voert u uw query uit op een representatieve gegevensset in een cosmos-container en [verkrijgt u de RU-kosten.](find-request-unit-charge.md) Vermenigvuldig de RU-kosten met het aantal query's dat u per seconde verwacht uit te voeren om de totale vereiste RU/s te krijgen. 

Als uw werkbelasting bijvoorbeeld een ``SELECT * FROM c WHERE c.id = 'Alice'`` query vereist, die 100 keer per seconde wordt uitgevoerd en de RU-kosten van de query 10 RU's zijn, hebt u 100 query/sec * 10 RU / query = 1000 RU/s in totaal nodig om deze aanvragen te kunnen uitvoeren. Voeg deze RU/s toe aan de RU/s die nodig zijn voor het lezen of schrijven van lezen of schrijven in uw werkbelasting.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het prijsmodel van Azure Cosmos DB](how-pricing-works.md).
* Maak een nieuw [Cosmos-account, database en container](create-cosmosdb-resources-portal.md).
* Meer informatie over het [optimaliseren van de ingerichte doorvoerkosten](optimize-cost-throughput.md).
* Meer informatie over het [optimaliseren van de kosten met gereserveerde capaciteit.](cosmos-db-reserved-capacity.md)

