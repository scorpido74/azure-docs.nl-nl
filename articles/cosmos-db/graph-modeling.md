---
title: Grafiekgegevensmodellering voor Azure Cosmos DB Gremlin API
description: Meer informatie over het modelleren van een grafiekdatabase met Azure Cosmos DB Gremlin API. In dit artikel wordt beschreven wanneer u een grafiekdatabase en aanbevolen procedures moet gebruiken om entiteiten en relaties te modelleren.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: lbosq
ms.openlocfilehash: dc9a5616aa2bb1f7e09045b9cfe4f4d7e9c69be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898315"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Grafiekgegevensmodellering voor Azure Cosmos DB Gremlin API

Het volgende document is ontworpen om aanbevelingen voor het modelleren van grafiekgegevens te geven. Deze stap is van vitaal belang om de schaalbaarheid en prestaties van een grafiekdatabasesysteem te garanderen naarmate de gegevens evolueren. Een efficiënt datamodel is vooral belangrijk bij grootschalige grafieken.

## <a name="requirements"></a>Vereisten

Het proces dat in deze handleiding wordt beschreven, is gebaseerd op de volgende veronderstellingen:
 * De **entiteiten** in de probleemruimte worden geïdentificeerd. Deze entiteiten zijn bedoeld om voor elke aanvraag _atomair_ te worden geconsumeerd. Met andere woorden, het databasesysteem is niet ontworpen om de gegevens van één entiteit op te halen in meerdere queryaanvragen.
 * Er is inzicht in **lees- en schrijfvereisten** voor het databasesysteem. Deze vereisten zullen de optimalisaties sturen die nodig zijn voor het grafiekgegevensmodel.
 * De principes van de [Apache Tinkerpop eigenschappengrafieknorm](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) zijn goed begrepen.

## <a name="when-do-i-need-a-graph-database"></a>Wanneer heb ik een grafiekdatabase nodig?

Een grafiekdatabaseoplossing kan optimaal worden toegepast als de entiteiten en relaties in een gegevensdomein een van de volgende kenmerken hebben: 

* De entiteiten zijn **sterk verbonden** door beschrijvende relaties. Het voordeel in dit scenario is het feit dat de relaties blijven bestaan in opslag.
* Er zijn **cyclische relaties** of **zelf-verwezen entiteiten**. Dit patroon is vaak een uitdaging bij het gebruik van relationele of documentdatabases.
* Er zijn **dynamisch evoluerende relaties** tussen entiteiten. Dit patroon is vooral van toepassing op hiërarchische of structuurgestructureerde gegevens met veel niveaus.
* Er zijn **veel-op-veel relaties** tussen entiteiten.
* Er zijn **schrijf- en leesvereisten voor zowel entiteiten als relaties.** 

Als aan de bovenstaande criteria is voldaan, is het waarschijnlijk dat een grafiekdatabasebenadering voordelen biedt voor **de complexiteit van query's**, schaalbaarheid **van gegevensmodellen**en **queryprestaties**.

De volgende stap is om te bepalen of de grafiek zal worden gebruikt voor analytische of transactionele doeleinden. Als de grafiek bedoeld is om te worden gebruikt voor zware berekeningen en gegevensverwerking workloads, zou het de moeite waard om de [Cosmos DB Spark connector](https://docs.microsoft.com/azure/cosmos-db/spark-connector) en het gebruik van de [GraphX bibliotheek](https://spark.apache.org/graphx/)te verkennen. 

## <a name="how-to-use-graph-objects"></a>Grafiekobjecten gebruiken

De [eigenschapsgrafiek van Apache Tinkerpop](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) definieert twee typen objecten **Vertices** en **Edges**. 

De volgende procedures voor de eigenschappen in de grafiekobjecten zijn de volgende opties:

| Object | Eigenschap | Type | Opmerkingen |
| --- | --- | --- |  --- |
| Hoekpunt | Id | Tekenreeks | Uniek afgedwongen per partitie. Als een waarde niet wordt geleverd bij het invoegen, wordt een automatisch gegenereerde GUID opgeslagen. |
| Hoekpunt | label | Tekenreeks | Deze eigenschap wordt gebruikt om het type entiteit te definiëren dat het hoekpunt vertegenwoordigt. Als een waarde niet wordt geleverd, wordt een standaardwaarde "hoekpunt" gebruikt. |
| Hoekpunt | properties | Tekenreeks, Boolean, Numeriek | Een lijst met afzonderlijke eigenschappen die zijn opgeslagen als sleutelwaardeparen in elk hoekpunt. |
| Hoekpunt | partitiesleutel | Tekenreeks, Boolean, Numeriek | Deze eigenschap bepaalt waar het hoekpunt en de uitgaande randen worden opgeslagen. Lees meer over [grafiekpartitionering](graph-partitioning.md). |
| Edge | Id | Tekenreeks | Uniek afgedwongen per partitie. Automatisch gegenereerd standaard. Randen hebben meestal niet de noodzaak om uniek te worden opgehaald door een ID. |
| Edge | label | Tekenreeks | Deze eigenschap wordt gebruikt om het type relatie te definiëren dat twee vertices hebben. |
| Edge | properties | Tekenreeks, Boolean, Numeriek | Een lijst met afzonderlijke eigenschappen die zijn opgeslagen als sleutelwaardeparen in elke rand. |

> [!NOTE]
> Randen vereisen geen partitiesleutelwaarde, omdat de waarde ervan automatisch wordt toegewezen op basis van hun bronhoekpunt. Meer informatie in [het artikel voor het verdelen van grafieken.](graph-partitioning.md)

## <a name="entity-and-relationship-modeling-guidelines"></a>Richtlijnen voor entiteits- en relatiemodellering

Hieronder volgt een reeks richtlijnen voor de aanpak van gegevensmodellering voor een Azure Cosmos DB Gremlin API-grafiekdatabase. Deze richtlijnen gaan ervan uit dat er een bestaande definitie van een gegevensdomein en query's voor.

> [!NOTE]
> De onderstaande stappen worden gepresenteerd als aanbevelingen. Het uiteindelijke model moet worden geëvalueerd en getest voordat het wordt overwogen als productieklaar. Bovendien zijn de onderstaande aanbevelingen specifiek voor de Gremlin API-implementatie van Azure Cosmos DB. 

### <a name="modeling-vertices-and-properties"></a>Modellering van vertices en eigenschappen 

De eerste stap voor een grafiekgegevensmodel is om elke geïdentificeerde entiteit in kaart te brengen in een **hoekpuntobject.** Een één-op-één toewijzing van alle entiteiten om vertices moet een eerste stap en onderhevig aan verandering.

Een veel voorkomende valkuil is om eigenschappen van een enkele entiteit in kaart te brengen als afzonderlijke vertices. Bekijk het onderstaande voorbeeld, waarin dezelfde entiteit op twee verschillende manieren wordt vertegenwoordigd:

* **Eigenschappen op basis van Vertex**: In deze benadering gebruikt de entiteit drie afzonderlijke hoekpunten en twee randen om de eigenschappen ervan te beschrijven. Hoewel deze aanpak redundantie kan verminderen, verhoogt het de complexiteit van het model. Een toename van de complexiteit van het model kan resulteren in extra latentie, querycomplexiteit en berekeningskosten. Dit model kan ook uitdagingen opleveren bij het partitioneren.

![Entiteitsmodel met vertices voor eigenschappen.](./media/graph-modeling/graph-modeling-1.png)

* **Ingesloten hoekjes met eigendom:** deze benadering maakt gebruik van de lijst met sleutelwaardenom alle eigenschappen van de entiteit in een hoekpunt weer te geven. Deze aanpak zorgt voor minder modelcomplexiteit, wat zal leiden tot eenvoudigere query's en meer kostenefficiënte traversals.

![Entiteitsmodel met vertices voor eigenschappen.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> De bovenstaande voorbeelden tonen een vereenvoudigd grafiekmodel om alleen de vergelijking tussen de twee manieren weer te geven om entiteitseigenschappen te verdelen.

Het **in gebouwde verticespatroon** voor eigendomzorgt over het algemeen voor een performante en schaalbare aanpak. De standaardbenadering van een nieuw grafiekgegevensmodel moet naar dit patroon worden aangetrokken.

Er zijn echter scenario's waarin verwijzingen naar een eigenschap voordelen kunnen bieden. Bijvoorbeeld: als de eigenschap waarnaar wordt verwezen regelmatig wordt bijgewerkt. Als u een afzonderlijk hoekpunt gebruikt om een eigenschap weer te geven die voortdurend wordt gewijzigd, wordt de hoeveelheid schrijfbewerkingen geminimaliseerd die de update nodig zou hebben.

### <a name="relationship-modeling-with-edge-directions"></a>Relatiemodellering met randrichtingen

Nadat de vertices zijn gemodelleerd, kunnen de randen worden toegevoegd om de relaties tussen hen aan te duiden. Het eerste aspect dat moet worden geëvalueerd is de **richting van de relatie**. 

Randobjecten hebben een standaardrichting die wordt gevolgd `out()` door `outE()` een traversal wanneer u de functie of gebruikt. Het gebruik van deze natuurlijke richting resulteert in een efficiënte werking, omdat alle vertices worden opgeslagen met hun uitgaande randen. 

Het oversteken in de tegenovergestelde richting van `in()` een rand, met behulp van de functie, zal echter altijd resulteren in een cross-partitie query. Meer informatie over [grafiekpartitionering](graph-partitioning.md). Als er een noodzaak is om `in()` voortdurend te doorkruisen met behulp van de functie, is het raadzaam om randen toe te voegen in beide richtingen.

U de randrichting `.to()` bepalen `.from()` door de `.addE()` of predicaten aan de Gremlin-stap te gebruiken. Of met behulp van de [bulk executor bibliotheek voor Gremlin API](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Randobjecten hebben standaard een richting.

### <a name="relationship-labeling"></a>Relatielabeling

Het gebruik van beschrijvende relatielabels kan de efficiëntie van randomzettingsbewerkingen verbeteren. Dit patroon kan op de volgende manieren worden toegepast:
* Gebruik niet-generieke termen om een relatie te labelen.
* Koppel het label van het bronhoekpunt aan het label van het doelpunt met de relatienaam.

![Voorbeelden van relatielabeling.](./media/graph-modeling/graph-modeling-3.png)

Hoe specifieker het etiket dat de traverser zal gebruiken om de randen te filteren, hoe beter. Deze beslissing kan ook een aanzienlijke invloed hebben op de querykosten. U de querykosten op elk gewenst moment evalueren [met de stap executionProfile](graph-execution-profile.md).


## <a name="next-steps"></a>Volgende stappen: 
* Bekijk de lijst met ondersteunde [Gremlin-stappen.](gremlin-support.md)
* Meer informatie over [het partitioneren van grafiekgegevens](graph-partitioning.md) om met grootschalige grafieken om te gaan.
* Evalueer uw Gremlin-query's met de [stap Uitvoeringsprofiel.](graph-execution-profile.md)
