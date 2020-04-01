---
title: Prestaties en afstemmingshandleiding voor gegevensstromen in kaart brengen
description: Meer informatie over belangrijke factoren die van invloed zijn op de prestaties van kaartgegevensstromen in Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: 4baf7974bdb0a5efe4cb556e820e9d13aeac5d8a
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409846"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Prestaties en tuninggids voor gegevensstromen in kaart brengen

Gegevensstromen toewijzen in Azure Data Factory bieden een codevrije interface voor het ontwerpen, implementeren en orkestreren van gegevenstransformaties op schaal. Zie het [overzicht van gegevensstroom toewijzen](concepts-data-flow-overview.md)als u niet bekend bent met het toewijzen van gegevensstromen.

Wanneer u gegevensstromen uit de ADF-UX ontwerpt en test, moet u de foutopsporingsmodus inschakelen om uw gegevensstromen in realtime uit te voeren zonder te wachten tot een cluster is opgewarmd. Zie [Foutopsporingsmodus](concepts-data-flow-debug-mode.md)voor meer informatie .

In deze video ziet u een aantal voorbeeldtijden waarin gegevens worden getransformeerd met gegevensstromen:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Prestaties van de gegevensstroom bewaken

Tijdens het ontwerpen van toewijzingsgegevensstromen u elke transformatie testen door op het tabblad gegevensvoorbeeld in het configuratiedeelvenster te klikken. Zodra u uw logica hebt geverifieerd, test u uw gegevensstroom end-to-end als een activiteit in een pijplijn. Voeg een activiteit Gegevensstroom uitvoeren toe en gebruik de foutopsporingsknop om de prestaties van uw gegevensstroom te testen. Als u het uitvoeringsplan en het prestatieprofiel van uw gegevensstroom wilt openen, klikt u op het pictogram met de bril onder 'acties' in het uitvoertabblad van uw pijplijn.

![Gegevensstroommonitor](media/data-flow/mon002.png "Gegevensstroommonitor 2")

 U deze informatie gebruiken om de prestaties van uw gegevensstroom te schatten aan de hand van gegevensbronnen van verschillende grootte. Zie [Gegevensstromen voor](concepts-data-flow-monitoring.md)meer informatie controleren.

![Gegevensstroombewaking](media/data-flow/mon003.png "Gegevensstroommonitor 3")

 Voor pijplijnfoutfouten is ongeveer één minuut clusterconfiguratietijd vereist in uw algemene prestatieberekeningen voor een warm cluster. Als u de standaard-Azure Integration Runtime initialiseert, kan de spin-uptijd ongeveer 5 minuten duren.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Compute-grootte vergroten in Azure Integration Runtime

Een Integratieruntijd met meer kernen verhoogt het aantal knooppunten in de Spark-compute-omgevingen en biedt meer verwerkingskracht om uw gegevens te lezen, schrijven en transformeren.
* Probeer een **compute optimized** cluster als u wilt dat de verwerkingssnelheid hoger is dan de invoersnelheid.
* Probeer een **cluster met geheugengeoptimaliseerde** cluster als u meer gegevens in het geheugen wilt opslaan. Geheugen geoptimaliseerd heeft een hogere prijs-punt per core dan Compute Optimized, maar zal waarschijnlijk resulteren in hogere transformatie snelheden.

![Nieuwe IR](media/data-flow/ir-new.png "Nieuwe IR")

Zie [Runtime integratie in Azure Data Factory](concepts-integration-runtime.md)voor meer informatie over het maken van een runtime voor integratie.

### <a name="increase-the-size-of-your-debug-cluster"></a>De grootte van uw foutopsporingscluster vergroten

Als u foutopsporing inschakelt, wordt standaard de runtime van Azure Integration gebruikt die automatisch wordt gemaakt voor elke gegevensfabriek. Deze standaard Azure IR is ingesteld voor acht cores, vier voor een stuurprogrammaknooppunt en vier voor een werknemersknooppunt, met behulp van algemene compute-eigenschappen. Terwijl u test met grotere gegevens, u de grootte van uw foutopsporingscluster vergroten door een Azure IR met grotere configuraties te maken en deze nieuwe Azure IR te kiezen wanneer u foutopsporingsinschakelt. Hierdoor wordt ADF geïnstrueerd om deze Azure IR te gebruiken voor gegevensvoorbeeld en pijplijnfoutbug met gegevensstromen.

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimaliseren voor Azure SQL Database en Azure SQL Data Warehouse

### <a name="partitioning-on-source"></a>Partitioneren op bron

1. Ga naar het tabblad **Optimaliseren** en selecteer **Partitionering instellen**
1. Selecteer **Bron**.
1. Stel **onder Aantal partities**het maximumaantal verbindingen met uw Azure SQL DB in. U een hogere instelling proberen om parallelle verbindingen met uw database te krijgen. In sommige gevallen kunnen echter snellere prestaties met een beperkt aantal verbindingen worden uitgevoerd.
1. Selecteer of u wilt partitioneren op basis van een specifieke tabelkolom of een query.
1. Als u **Kolom**hebt geselecteerd, kiest u de partitiekolom.
1. Als u **Query**hebt geselecteerd, voert u een query in die overeenkomt met het partitieschema van de databasetabel. Met deze query kan de brondatabase-engine gebruikmaken van partitieverwijdering. Uw brondatabasetabellen hoeven niet te worden verdeeld. Als uw bron nog niet is verdeeld, gebruikt ADF nog steeds gegevenspartitionering in de Spark-transformatieomgeving op basis van de sleutel die u selecteert in de brontransformatie.

![Brononderdeel](media/data-flow/sourcepart3.png "Brononderdeel")

> [!NOTE]
> Een goede handleiding om u te helpen kiezen aantal partities voor uw bron is gebaseerd op het aantal cores dat u hebt ingesteld voor uw Azure Integration Runtime en vermenigvuldig dat aantal met vijf. Dus, bijvoorbeeld, als u een reeks bestanden in uw ADLS-mappen transformeert en u een 32-core Azure IR gaat gebruiken, is het aantal partities dat u zou targeten 32 x 5 = 160 partities.

### <a name="source-batch-size-input-and-isolation-level"></a>Grootte van de bronbatch, invoer en isolatieniveau

Onder **Bronopties** in de brontransformatie kunnen de volgende instellingen de prestaties beïnvloeden:

* Batchgrootte instrueert ADF om gegevens op te slaan in sets in Spark-geheugen in plaats van rij voor rij. Batchgrootte is een optionele instelling en u geen resources meer hebben op de compute nodes als ze niet goed zijn groot. Als u deze eigenschap niet instelt, worden de standaardwaarden voor spark-cachingbatch gebruikt.
* Als u een query instelt, u rijen bij de bron filteren voordat ze in Gegevensstroom aankomen voor verwerking. Dit kan de eerste gegevensverzameling sneller maken. Als u een query gebruikt, u optionele queryhints toevoegen voor uw Azure SQL DB, zoals LEZEN NIET-VASTGELEGD.
* Lees niet-vastgelegd biedt snellere queryresultaten over brontransformatie

![Bron](media/data-flow/source4.png "Bron")

### <a name="sink-batch-size"></a>Batchgrootte gootsteen

Als u verwerking van uw gegevensstromen rij voor rij wilt voorkomen, stelt u **batchgrootte** in op het tabblad Instellingen voor Azure SQL DB en Azure SQL DW-sinks. Als batchgrootte is ingesteld, verwerkt ADF databaseschrijft in batches op basis van de opgegeven grootte. Als u deze eigenschap niet instelt, worden de standaardwaarden voor spark-cachingbatch gebruikt.

![Sink](media/data-flow/sink4.png "Sink")

### <a name="partitioning-on-sink"></a>Het verdelen op gootsteen

Zelfs als u uw gegevens niet hebt verdeeld in uw doeltabellen, wordt aanbevolen om uw gegevens te laten verdelen in de sinktransformatie. Partitiegegevens resulteren vaak in een veel sneller laden over het dwingen van alle verbindingen om een enkele node / partitie te gebruiken. Ga naar het tabblad Optimaliseren van uw gootsteen en selecteer *Round Robin* partitioning om het ideale aantal partities te selecteren om naar uw gootsteen te schrijven.

### <a name="disable-indexes-on-write"></a>Indexen uitschakelen op schrijven

Voeg in uw pijplijn een [activiteit Opgeslagen procedure](transform-data-using-stored-procedure.md) toe voordat uw activiteit Gegevensstroom wordt uitgeschakeld die indexen uitschakelt op uw doeltabellen die vanuit uw gootsteen zijn geschreven. Voeg na uw activiteit Gegevensstroom een andere activiteit Opgeslagen procedure toe waarmee deze indexen worden opgeslagen. Of gebruik maken van de pre-processing en post-processing scripts in een database gootsteen.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>De grootte van uw Azure SQL DB en DW vergroten

Plan een herformaat van uw bron en sink Azure SQL DB en DW voordat uw pijplijn wordt uitgevoerd om de doorvoer te verhogen en Azure throttling te minimaliseren zodra u DTU-limieten hebt bereikt. Nadat de pijplijnuitvoering is voltooid, wijzigt u het formaat van uw databases weer op de normale uitvoeringssnelheid.

* SQL DB-brontabel met 887k-rijen en 74 kolommen naar een SQL DB-tabel met een enkele afgeleide kolomtransformatie duurt ongeveer 3 minuten end-to-end met geheugengeoptimaliseerde Azure-iR's met geheugen.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Alleen Azure Synapse SQL DW] Staging gebruiken om gegevens in bulk te laden via Polybase

Als u wilt voorkomen dat rij voor rij wordt ingevoegd in uw DW, schakelt u **Fasering in** uw sink-instellingen inschakelen in, zodat ADF [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)kan gebruiken. Met PolyBase kan ADF de gegevens in bulk laden.
* Wanneer u uw gegevensstroomactiviteit uitvoert vanuit een pijplijn, moet u een Blob- of ADLS Gen2-opslaglocatie selecteren om uw gegevens tijdens het laden van bulkteplaatsen te fasen.

* Bestandsbron van 421 Mb-bestand met 74 kolommen naar een Synapse-tabel en een enkele afgeleide kolomtransformatie duurt ongeveer 4 minuten end-to-end met behulp van geheugengeoptimaliseerde Azure-iR's met een kern van 80 cores.

## <a name="optimizing-for-files"></a>Optimaliseren voor bestanden

Bij elke transformatie u het partitieschema instellen dat u wilt dat de gegevensfabriek gebruikt op het tabblad Optimaliseren. Het is een goede gewoonte om eerst te testen file-based sinks houden van de standaard partitionering en optimalisaties.

* Voor kleinere bestanden u merken dat het kiezen van minder partities soms beter en sneller kan werken dan spark vragen om uw kleine bestanden te partitioneren.
* Als u niet genoeg informatie hebt over uw brongegevens, kiest u *Afronding van Robin-partities* en stelt u het aantal partities in.
* Als uw gegevens kolommen bevatten die goede hashsleutels kunnen zijn, kiest u *Hash partitioneren*.

* Bestandsbron met bestandssink van een 421Mb-bestand met 74 kolommen en een enkele afgeleide kolomtransformatie duurt ongeveer 2 minuten end-to-end met behulp van geheugengeoptimaliseerde Azure IRs met geheugen.

Bij foutopsporing in gegevensvoorbeeld en pijplijnfoutbug zijn de limiet- en bemonsteringsgroottes voor bestandsgebaseerde brongegevenssets alleen van toepassing op het aantal geretourneerde rijen, niet op het aantal gelezen rijen. Dit kan van invloed zijn op de prestaties van uw foutopsporingsuitvoeringen en kan er mogelijk voor zorgen dat de stroom mislukt.
* Foutopsporingsclusters zijn standaard kleine clusters met één knooppunt en we raden u aan om voorbeeldkleine bestanden te gebruiken voor foutopsporing. Ga naar Foutopsporingsinstellingen en wijs een kleine subset van uw gegevens aan met een tijdelijk bestand.

    ![Foutopsporingsinstellingen](media/data-flow/debugsettings3.png "Foutopsporingsinstellingen")

### <a name="file-naming-options"></a>Opties voor bestandsnaamgeving

De meest voorkomende manier om getransformeerde gegevens te schrijven in het toewijzen van gegevensstromen die Blob- of ADLS-bestandsarchief schrijven. In uw gootsteen moet u een gegevensset selecteren die naar een container of map verwijst, niet naar een benoemd bestand. Aangezien de toewijzingsgegevensstroom Spark gebruikt voor uitvoering, wordt uw uitvoer verdeeld over meerdere bestanden op basis van uw partitieschema.

Een gemeenschappelijk partitieschema is om _Uitvoer naar één bestand te_kiezen, waarbij alle uitvoer-PART-bestanden worden samengevoegd tot één bestand in uw gootsteen. Deze bewerking vereist dat de uitvoer wordt verminderd tot één partitie op één clusterknooppunt. U geen clusterknooppuntbronnen meer hebben als u veel grote bronbestanden combineert in één uitvoerbestand.

Als u wilt voorkomen dat resources voor het berekenen van knooppuntresources worden uitgeput, houdt u het standaard, geoptimaliseerde schema in de gegevensstroom en voegt u een kopieeractiviteit toe in uw pijplijn waarin alle ONDERDEEL-bestanden uit de uitvoermap worden samengevoegd tot een nieuw afzonderlijk bestand. Deze techniek scheidt de actie van transformatie van het samenvoegen van bestanden en bereikt hetzelfde resultaat als het instellen _van Uitvoer op één bestand_.

### <a name="looping-through-file-lists"></a>Bestandenlijsten doorlussen

Een mapping data flow zal beter worden uitgevoerd wanneer de brontransformatie iterates over meerdere bestanden in plaats van looping via de Voor elke activiteit. We raden u aan wildcards of bestandslijsten te gebruiken in uw brontransformatie. Het datastroomproces wordt sneller uitgevoerd doordat de lussen binnen het Spark-cluster kunnen plaatsvinden. Zie [Wildcarding in Brontransformatie](connector-azure-data-lake-storage.md#mapping-data-flow-properties)voor meer informatie.

Als u bijvoorbeeld een lijst met gegevensbestanden van juli 2019 hebt die u wilt verwerken in een map in Blob Storage, hieronder is een wildcard die u gebruiken in uw brontransformatie.

```DateFiles/*_201907*.txt```

Door wildcarding te gebruiken, bevat uw pijplijn slechts één activiteit gegevensstroom. Dit zal beter presteren dan een lookup ten opzichte van de Blob Store die vervolgens iterates over alle overeenkomende bestanden met behulp van een ForEach met een Activiteit Gegevensstroom uitvoeren binnen.

### <a name="optimizing-for-cosmosdb"></a>Optimaliseren voor CosmosDB

Het instellen van doorvoer- en batch-eigenschappen op CosmosDB-putten wordt alleen van kracht tijdens de uitvoering van die gegevensstroom van een activiteit in de gegevensstroom van pijplijnen. De oorspronkelijke verzamelingsinstellingen worden door CosmosDB gehonoreerd na de uitvoering van uw gegevensstroom.

* Batchgrootte: bereken de ruwe rijgrootte van uw gegevens en zorg ervoor dat de grootte van de batch van rowSize * kleiner is dan twee miljoen. Als dit het is, verhoog dan de batchgrootte om een betere doorvoer te krijgen
* Doorvoer: Stel hier een hogere doorvoerinstelling in, zodat documenten sneller naar CosmosDB kunnen schrijven. Houd rekening met de hogere RU-kosten op basis van een hoge doorvoerinstelling.
*   Budget voor schrijfdoorvoer: gebruik een waarde die kleiner is dan de totale RU's per minuut. Als u een gegevensstroom hebt met een hoog aantal Spark-partities, u een budgetdoorvoer instellen voor meer balans tussen deze partities.

## <a name="join-performance"></a>Deelnemen aan prestaties

Het beheren van de prestaties van joins in uw gegevensstroom is een veel voorkomende bewerking die u gedurende de levenscyclus van uw gegevenstransformaties zult uitvoeren. In ADF vereisen gegevensstromen niet dat gegevens worden gesorteerd voordat ze worden verbonden, omdat deze bewerkingen worden uitgevoerd als hash-joins in Spark. U echter profiteren van verbeterde prestaties met de optimalisatie Broadcast Join. Dit voorkomt shuffles door het indrukken van de inhoud van beide zijden van uw join relatie in de Spark knooppunt. Dit werkt goed voor kleinere tabellen die worden gebruikt voor referentie-lookups. Grotere tabellen die mogelijk niet in het geheugen van het knooppunt passen, zijn geen goede kandidaten voor broadcast-optimalisatie.

Een andere Join optimalisatie is om je joins te bouwen op een zodanige wijze dat het vermijdt Spark's neiging om cross joins te implementeren. Wanneer u bijvoorbeeld letterlijke waarden opneemt in uw join-voorwaarden, kan Spark dat zien als een vereiste om eerst een volledig cartesiaans product uit te voeren en vervolgens de samengevoegde waarden uitfilteren. Maar als u ervoor zorgt dat u kolomwaarden aan beide zijden van uw joinconditie hebt, u dit door spark-geïnduceerde cartesiaans product vermijden en de prestaties van uw joins en gegevensstromen verbeteren.

## <a name="next-steps"></a>Volgende stappen

Zie andere gegevensstroomartikelen met betrekking tot prestaties:

- [Tabblad Gegevensstroom optimaliseren](concepts-data-flow-overview.md#optimize)
- [Activiteit gegevensstroom](control-flow-execute-data-flow-activity.md)
- [Prestaties gegevensstroom bewaken](concepts-data-flow-monitoring.md)
