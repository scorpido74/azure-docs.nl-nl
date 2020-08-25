---
title: Wat is Azure Cosmos DB Analytical Store (preview)?
description: Meer informatie over Azure Cosmos DB transactionele (op rijen gebaseerde) en analytische (op kolommen gebaseerde) opslag. Voor delen van de analytische opslag, invloed op de prestaties voor grootschalige workloads en automatische synchronisatie van gegevens van transactionele opslag naar een analytische opslag
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: rosouz
ms.openlocfilehash: b3d1371f486a73b40d352007e3681fd451a8a8b7
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815824"
---
# <a name="what-is-azure-cosmos-db-analytical-store-preview"></a>Wat is Azure Cosmos DB Analytical Store (preview)?

> [!IMPORTANT]
> Azure Cosmos DB Analytical Store is momenteel beschikbaar als preview-versie. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB Analytical Store is een volledig geïsoleerd kolom Archief voor het inschakelen van grootschalige analyses op basis van operationele gegevens in uw Azure Cosmos DB, zonder dat dit invloed heeft op uw transactionele werk belastingen.  

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Uitdagingen met grootschalige analyses op operationele gegevens

De Bedrijfs gegevens met meerdere modellen in een Azure Cosmos DB container worden intern opgeslagen in een geïndexeerde rij transactionele Store. De indeling van een rij-archief is ontworpen om snelle transactionele Lees-en schrijf bewerkingen toe te staan in de reactie tijden van de milliseconden en operationele query's. Als uw gegevensset erg groot wordt, kunnen complexe analytische query's kostbaar zijn in termen van ingerichte door Voer voor de gegevens die zijn opgeslagen in deze indeling. Hoog verbruik van ingerichte door Voer is op zijn beurt van invloed op de prestaties van transactionele workloads die worden gebruikt door uw realtime-toepassingen en-services.

Traditioneel worden operationele gegevens geëxtraheerd uit de transactionele Store van Azure Cosmos DB en opgeslagen in een afzonderlijke gegevenslaag, om grote hoeveel heden gegevens te analyseren. De gegevens worden bijvoorbeeld in een Data Warehouse of Data Lake opgeslagen in een geschikte indeling. Deze gegevens worden later gebruikt voor grootschalige analyses en geanalyseerd met behulp van Compute engine, zoals de Apache Spark clusters. Deze schei ding van analytische opslag en reken lagen van operationele gegevens resulteert in extra latentie, omdat de ETL-pijp lijnen (extract, trans formatie, Load) minder vaak worden uitgevoerd om de potentiële impact op uw transactionele workloads te beperken.

De ETL-pijp lijnen worden ook complex wanneer er updates worden verwerkt in de operationele gegevens in vergelijking met het verwerken van alleen nieuwe opgenomen operationele gegevens. 

## <a name="column-oriented-analytical-store"></a>Kolom gerichte analytische opslag

Azure Cosmos DB Analytical Store verhelpt de complexiteits-en latentie uitdagingen die zich voordoen met de traditionele ETL-pijp lijnen. Met Azure Cosmos DB Analytical Store kunnen uw operationele gegevens automatisch worden gesynchroniseerd in een afzonderlijk kolom archief. De indeling van Column Store is geschikt voor grootschalige analytische query's die op een geoptimaliseerde manier worden uitgevoerd, wat resulteert in het verbeteren van de latentie van dergelijke query's.

Met de koppeling Azure Synapse kunt u nu geen ETL-HTAP-oplossingen bouwen door rechtstreeks een koppeling te maken met Azure Cosmos DB Analytical Store van Synapse Analytics. Zo kunt u bijna realtime grootschalige analyses uitvoeren op uw operationele gegevens.

## <a name="analytical-store-details"></a>Details van analytische opslag

Wanneer u het analytische archief in een Azure Cosmos DB container inschakelt, wordt een nieuwe kolom opgeslagen die intern wordt gemaakt op basis van de operationele gegevens in de container. Dit kolom archief wordt afzonderlijk van het rij-georiënteerde transactionele Archief voor die container bewaard. De toevoegingen, updates en verwijderingen voor uw operationele gegevens worden automatisch gesynchroniseerd naar de analytische opslag. U hebt de wijzigings feed of ETL niet nodig om de gegevens te synchroniseren.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Kolom opslag voor analytische werk belastingen op operationele gegevens

Analytische werk belastingen omvatten doorgaans aggregaties en sequentiële scans van geselecteerde velden. Door de gegevens op te slaan in een kolom-primaire volg orde, kan de analytische opslag een groep waarden voor elk veld met elkaar worden geserialiseerd. Deze indeling vermindert de IOPS die nodig is om statistieken te scannen of te berekenen ten opzichte van specifieke velden. De reactie tijden van query's voor scans in grote gegevens sets worden aanzienlijk verbeterd. 

Als uw operationele tabellen bijvoorbeeld de volgende indeling hebben:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Voor beeld van operationele tabel" border="false":::

In het rij-archief worden de bovenstaande gegevens opgeslagen in een geserialiseerde indeling, per rij, op de schijf. Met deze indeling kunnen transactionele Lees bewerkingen, schrijf bewerkingen en operationele query's sneller worden uitgevoerd, zoals ' retour informatie over Product1 '. Omdat de gegevensset echter groot groeit en als u complexe analytische query's wilt uitvoeren op de gegevens, kan het kostbaar zijn. Als u bijvoorbeeld ' de verkoop trends voor een product onder de categorie ' apparatuur ' over verschillende bedrijfs eenheden en maanden wilt krijgen, moet u een complexe query uitvoeren. Grote scans op deze gegevensset kunnen duur verkrijgen in termen van ingerichte door Voer en kunnen ook van invloed zijn op de prestaties van de transactionele workloads die uw realtime-toepassingen en-services inschakelen.

Analytische opslag, een column Store, is beter geschikt voor dergelijke query's, omdat deze soort gelijke velden met gegevens aan elkaar heeft geserial en de schijf-IOPS vermindert.

De volgende afbeelding toont transactionele rijen Store versus analytisch kolom archief in Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Transactionele rij-archief versus analytisch kolom archief in Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Ontkoppelde prestaties voor analytische werk belastingen

Er is geen invloed op de prestaties van uw transactionele workloads vanwege analytische query's, omdat het analytische archief gescheiden is van het transactionele archief.  Voor de analytische opslag zijn geen afzonderlijke aanvraag eenheden (RUs) nodig om te worden toegewezen.

### <a name="auto-sync"></a>Automatische synchronisatie

Automatische synchronisatie verwijst naar de volledig beheerde mogelijkheid van Azure Cosmos DB waarbij de toevoegingen, updates, verwijderingen van de operationele gegevens, binnen vijf minuten automatisch worden gesynchroniseerd vanuit transactioneel archief naar een analytische opslag.

De functie voor automatische synchronisatie in combi natie met analytische opslag biedt de volgende belang rijke voor delen:

#### <a name="scalability--elasticity"></a>Schaal baarheid & elasticiteit

Door gebruik te maken van horizontale partitionering kan Azure Cosmos DB transactionele Store de opslag en door Voer op elastische wijze schalen zonder uitval tijd. Horizon taal partitioneren in het transactionele archief biedt schaal baarheid & elasticiteit in automatische synchronisatie om ervoor te zorgen dat gegevens in bijna realtime worden gesynchroniseerd met de analytische opslag. De gegevens synchronisatie vindt plaats ongeacht de door Voer van transactionele verkeer, of het nu 1000 bewerkingen per seconde of 1.000.000 bewerkingen per seconde is en heeft geen invloed op de ingerichte door Voer in het transactionele archief. 

#### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Automatisch schema-updates verwerken

Azure Cosmos DB transactionele Store is schema-neutraal en biedt u de mogelijkheid om uw transactionele toepassingen te herhalen zonder schema-of index beheer te hoeven afhandelen. Azure Cosmos DB Analytical Store daarentegen is geschematiseerde om te optimaliseren voor analytische query prestaties. Met de functie Automatische synchronisatie beheert Azure Cosmos DB het schema dezicht over de meest recente updates van het transactionele archief.  Het beheert ook de schema weergave in de analytische out-of-the-box, inclusief het verwerken van geneste gegevens typen.

In het geval van een schema-evolutie, waarbij nieuwe eigenschappen na verloop van tijd worden toegevoegd, presenteert de analytische opslag automatisch een samenvoegings schema voor alle historische schema's in het transactionele archief.

Als alle operationele gegevens in Azure Cosmos DB een goed gedefinieerd schema voor analyse volgen, wordt het schema automatisch afgeleid en correct weer gegeven in de analytische opslag. Als het goed gedefinieerde schema voor analyse, zoals hieronder gedefinieerd, wordt geschonden door bepaalde items, worden deze niet opgenomen in de analytische opslag. Als u scenario's hebt geblokkeerd vanwege een goed gedefinieerd schema voor analyse definitie, moet u een e-mail sturen naar het [Azure Cosmos DB team](mailto:cosmosdbsynapselink@microsoft.com).

Een goed gedefinieerd schema voor analyse is gedefinieerd met de volgende overwegingen:

* Een eigenschap heeft altijd hetzelfde type over meerdere items

  * `{"a":123} {"a": "str"}`Heeft bijvoorbeeld geen goed gedefinieerd schema, omdat dit `"a"` soms een teken reeks is en soms een getal. 
  
    In dit geval registreert de analytische opslag het gegevens type van `“a”` als het gegevens type van `“a”` in het eerste item in de levens duur van de container. Items waarvan het gegevens type `“a”` verschilt, worden niet opgenomen in de analytische opslag.
  
    Deze voor waarde is niet van toepassing op null-eigenschappen. `{"a":123} {"a":null}`Is bijvoorbeeld nog steeds goed gedefinieerd.

* Matrix typen moeten één herhaald type bevatten

  * `{"a": ["str",12]}`Is bijvoorbeeld geen goed gedefinieerd schema, omdat de matrix een combi natie van integer-en teken reeks typen bevat

* Er zijn Maxi maal 200 eigenschappen voor een nest niveau van een schema en een maximale geneste diepte van 5

  * Een item met 201-eigenschappen op het hoogste niveau heeft geen goed gedefinieerd schema.

  * Een item met meer dan vijf geneste niveaus in het schema heeft ook geen goed gedefinieerd schema. Bijvoorbeeld: `{"level1": {"level2":{"level3":{"level4":{"level5":{"too many":12}}}}}}`

* Eigenschapnamen zijn uniek wanneer ze in een niet-hoofdletter gevoelige manier worden vergeleken

  * De volgende items hebben bijvoorbeeld geen goed gedefinieerd schema `{"Name": "fred"} {"name": "john"}` : `"Name"` en `"name"` zijn hetzelfde als in vergelijking met een niet-hoofdletter gevoelige manier

### <a name="cost-effective-archival-of-historical-data"></a>Rendabele archivering van historische gegevens

Gegevenslaaging verwijst naar de schei ding van gegevens tussen opslag infrastructuren die zijn geoptimaliseerd voor verschillende scenario's. Hierdoor worden de algehele prestaties en kosten effectiviteit van de end-to-end-gegevens stack verbeterd. Met een analytische winkel biedt Azure Cosmos DB nu ondersteuning voor het automatisch trapsgewijs delen van gegevens vanuit het transactionele archief naar een analytisch archief met verschillende gegevens indelingen. Met een analytische opslag die is geoptimaliseerd voor de opslag kosten in vergelijking met de transactionele Store, kunt u veel meer operationele gegevens bewaren voor historische analyse.

Nadat de analytische opslag is ingeschakeld, kunt u, op basis van de behoeften voor het bewaren van gegevens van de transactionele werk belastingen, de eigenschap transactionele Store Time to Live (transactionele TTL) zodanig configureren dat records automatisch uit het transactionele archief worden verwijderd na een bepaalde tijds periode. Op dezelfde manier kunt u met de ' analytische time-out voor analyse (analytische TTL) ' de levens cyclus beheren van de gegevens die in het analytische archief worden bewaard, onafhankelijk van het transactionele archief. Door analytische opslag in te scha kelen en TTL-eigenschappen te configureren, kunt u de Bewaar periode voor gegevens voor de twee winkels naadloos laag maken en definiëren.

### <a name="global-distribution"></a>Wereldwijde distributie

Als u een wereld wijd gedistribueerd Azure Cosmos DB account hebt, is het beschikbaar in alle regio's van dat account nadat u het analytische Archief voor een container hebt ingeschakeld.  Wijzigingen in de operationele gegevens worden globaal gerepliceerd in alle regio's. U kunt analytische query's effectief uitvoeren met de dichtstbijzijnde regionale kopie van uw gegevens in Azure Cosmos DB.

### <a name="security"></a>Beveiliging

Verificatie met het analytische archief is hetzelfde als het transactionele Archief voor een bepaalde data base. U kunt hoofd-of alleen-lezen sleutels gebruiken voor verificatie. U kunt gebruikmaken van gekoppelde services in Synapse Studio om te voor komen dat de Azure Cosmos DB sleutels in de Spark-notebooks worden geplakt. Toegang tot deze gekoppelde service is beschikbaar voor iedereen die toegang heeft tot de werk ruimte.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Ondersteuning voor meerdere Azure Synapse Analytics-Runtimes

De analytische opslag is geoptimaliseerd voor schaal baarheid, elasticiteit en prestaties voor analytische werk belastingen zonder enige afhankelijkheid op de reken tijden. De opslag technologie wordt zelf beheerd om uw analyse werkbelastingen te optimaliseren zonder hand matige inspanningen.

Als u het analytische opslag systeem loskoppelt van het analytische berekenings systeem, kunnen gegevens in Azure Cosmos DB-analytische opslag tegelijk worden opgevraagd bij de verschillende analyse-runtimes die worden ondersteund door Azure Synapse Analytics. Vanaf nu, ondersteunt Synapse Analytics Apache Spark en SQL Server zonder Azure Cosmos DB-analytische opslag.

> [!NOTE]
> U kunt alleen lezen uit de analytische opslag met behulp van Synapse Analytics run time. U kunt de gegevens terugschrijven naar uw transactionele archief als een te leveren laag.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Koers

In de analytische opslag wordt een prijs model op basis van verbruik gevolgd waarvoor u in rekening wordt gebracht:

* Opslag: het volume van de gegevens dat elke maand in het analytische archief wordt bewaard, inclusief historische gegevens zoals gedefinieerd door de analyse-TTL.

* Analytische schrijf bewerkingen: de volledig beheerde synchronisatie van operationele gegevens updates naar het analytische archief vanuit het transactionele archief (automatische synchronisatie)

* Analytische Lees bewerkingen: de Lees bewerkingen die worden uitgevoerd voor het analytische archief van Synapse Analytics Spark en SQL serverloze uitvoerings tijden.

> [!NOTE]
> Azure Cosmos DB Analytical Store is momenteel beschikbaar in open bare Preview gratis van kosten.

Prijzen voor een analytische opslag zijn gescheiden van het prijs model voor de transactie opslag. Er is geen concept van het ingerichte RUs in de analytische opslag. Zie [Azure Cosmos DB-pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/)voor meer informatie over het prijs model voor de analytische opslag.

Als u een schatting wilt maken van de kosten op hoog niveau om analytische opslag op een Azure Cosmos DB-container in te scha kelen, kunt u de [Azure Cosmos DB capacity planner](https://cosmos.azure.com/capacitycalculator/) gebruiken en een schatting maken van uw analytische opslag-en schrijf bewerkings kosten. Analytische Lees bewerkings kosten zijn afhankelijk van de kenmerken van de analytische werk belasting, maar als een schatting op hoog niveau is het scannen van 1 TB aan gegevens in de analytische opslag meestal het resultaat van 130.000 analytische Lees bewerkingen en de kosten van $0,065.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Analytische time-to-Live (TTL)

Analytical TTL geeft aan hoe lang gegevens voor een container moeten worden bewaard in uw analytische opslag. 

Als het analytische archief is ingeschakeld, worden invoeg acties, updates en verwijderingen van operationele gegevens automatisch gesynchroniseerd vanuit transactioneel archief naar een analytische archief, onafhankelijk van de transactionele TTL-configuratie. De retentie van deze operationele gegevens in de analytische opslag kan worden bepaald door de analytische TTL-waarde op container niveau, zoals hieronder is aangegeven:

De analyse-TTL voor een container wordt ingesteld met behulp van de `AnalyticalStoreTimeToLiveInSeconds` eigenschap:

* Als de waarde is ingesteld op 0, ontbreekt (of is ingesteld op null): het analytische archief is uitgeschakeld en er worden geen gegevens gerepliceerd uit transactionele Store naar een analytische archief

* Indien aanwezig en de waarde is ingesteld op '-1 ': de analytische opslag behoudt alle historische gegevens, ongeacht de Bewaar periode van de gegevens in het transactionele archief. Met deze instelling geeft u aan dat het analytische archief oneindig bewaren van uw operationele gegevens bevat

* Indien aanwezig en de waarde is ingesteld op een positief getal ' n ': items verlopen vanaf de ' n ' seconden van het analytische archief na de laatste wijziging in het transactionele archief. Deze instelling kan worden gebruikt als u uw operationele gegevens gedurende een beperkte periode wilt bewaren in de analytische opslag, ongeacht de retentie van de gegevens in het transactionele archief

Enkele punten om in overweging te nemen:
*   Nadat de analytische opslag is ingeschakeld met een analytische TTL-waarde, kan deze later worden bijgewerkt naar een andere geldige waarde. 
*   Hoewel transactionele TTL kan worden ingesteld op container-of item niveau, kan analytische TTL alleen op het container niveau worden ingesteld.
*   U kunt uw operationele gegevens in het analytische archief langer bewaren door de analytische TTL >= transactionele TTL op het niveau van de container in te stellen.
*   De analytische opslag kan worden gemaakt om het transactionele archief te spie gelen door analytische TTL = transactionele TTL in te stellen.

Zie [analytische TTL configureren voor een container](configure-synapse-link.md#create-analytical-ttl)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende documenten voor meer informatie:

* [Azure Synapse-koppeling voor Azure Cosmos DB](synapse-link.md)

* [Aan de slag met Azure Synapse Link voor Azure Cosmos DB](configure-synapse-link.md)

* [Veelgestelde vragen over Synapse Link voor Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Use cases voor Azure Synapse Link voor Azure Cosmos DB](synapse-link-use-cases.md)
