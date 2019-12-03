---
title: Prestaties en afstemmings handleiding voor gegevens stromen toewijzen in Azure Data Factory
description: Meer informatie over de belangrijkste factoren die van invloed zijn op de prestaties van het toewijzen van gegevens stromen in Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 10/07/2019
ms.openlocfilehash: 9ae6ff5fb5a5bfc6ba9299e06bad9afafc1403f3
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671578"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Gegevens stromen toewijzen prestaties en afstemmings handleiding

Het toewijzen van gegevens stromen in Azure Data Factory bieden een code-Free interface voor het ontwerpen, implementeren en organiseren van gegevens transformaties op schaal. Als u niet bekend bent met het toewijzen van gegevens stromen, raadpleegt u het [overzicht gegevens stroom toewijzen](concepts-data-flow-overview.md).

Bij het ontwerpen en testen van gegevens stromen van de ADF UX, zorgt u ervoor dat u overschakelt naar de foutopsporingsmodus om uw gegevens stromen in realtime uit te voeren zonder te hoeven wachten tot een cluster is opgewarmd. Zie [debug mode (foutopsporingsmodus](concepts-data-flow-debug-mode.md)) voor meer informatie.

## <a name="monitoring-data-flow-performance"></a>Prestaties van de gegevens stroom bewaken

Bij het ontwerpen van gegevens stromen kunt u elke trans formatie per eenheid testen door te klikken op het tabblad voor beeld van gegevens in het configuratie venster. Wanneer u uw logica hebt gecontroleerd, test u uw gegevens stroom end-to-end als een activiteit in een pijp lijn. Voeg een activiteit gegevens stroom uitvoeren toe en gebruik de knop fout opsporing om de prestaties van uw gegevens stroom te testen. Als u het uitvoerings plan en prestatie profiel van uw gegevens stroom wilt openen, klikt u op het bril pictogram onder acties op het tabblad uitvoer van de pijp lijn.

![Monitor voor gegevens stroom](media/data-flow/mon002.png "Monitor voor gegevens stroom 2")

 U kunt deze informatie gebruiken om de prestaties van uw gegevens stroom te schatten voor gegevens bronnen met verschillende grootte. Zie [toewijzing van gegevens stromen controleren](concepts-data-flow-monitoring.md)voor meer informatie.

![Gegevensstroom controleren](media/data-flow/mon003.png "Monitor voor gegevens stroom 3")

 Voor het uitvoeren van de fout opsporing voor de pijp lijn is ongeveer één minuut van het instellen van de cluster tijd in uw algemene prestatie berekeningen vereist voor een warm cluster. Als u de standaard Azure Integration Runtime initialiseert, kan het aantal tijd van het draaien ongeveer 5 minuten duren.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>De reken grootte verg Roten in Azure Integration Runtime

Een Integration Runtime met meer kernen verhoogt het aantal knoop punten in de Spark-reken omgevingen en biedt meer verwerkings kracht voor het lezen, schrijven en transformeren van uw gegevens.
* Probeer een **geoptimaliseerde Compute** -cluster als u wilt dat uw verwerkings snelheid hoger is dan de invoer snelheid
* Probeer een cluster dat is **geoptimaliseerd voor geheugen** als u meer gegevens in het geheugen wilt opslaan.

![Nieuwe IR](media/data-flow/ir-new.png "Nieuwe IR")

Zie [Integration runtime in azure Data Factory](concepts-integration-runtime.md)voor meer informatie over het maken van een Integration runtime.

### <a name="increase-the-size-of-your-debug-cluster"></a>De grootte van het cluster voor fout opsporing verg Roten

Het inschakelen van debug maakt standaard gebruik van de standaard Azure Integration runtime die automatisch voor elke data factory wordt gemaakt. Deze standaard Azure IR is ingesteld op acht kernen, vier voor een driver knooppunt en vier voor een werk knooppunt, met behulp van algemene Compute-eigenschappen. Wanneer u met grotere gegevens test, kunt u de grootte van het cluster voor fout opsporing verg Roten door een Azure IR met grotere configuraties te maken en dit nieuwe Azure IR te kiezen wanneer u overschakelt op fout opsporing. Hiermee wordt ADF geadviseerd om deze Azure IR te gebruiken voor het voor beeld van gegevens en het opsporen van pijp lijnen met gegevens stromen.

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimaliseren voor Azure SQL Database en Azure SQL Data Warehouse

### <a name="partitioning-on-source"></a>Partitioneren op Bron

1. Ga naar het tabblad **optimaliseren** en selecteer **partitioneren instellen**
1. Selecteer **bron**.
1. Stel bij **aantal partities**het maximum aantal verbindingen in voor uw Azure SQL-data base. U kunt een hogere instelling proberen om parallelle verbindingen met uw data base te krijgen. Sommige gevallen kunnen echter leiden tot snellere prestaties met een beperkt aantal verbindingen.
1. Selecteer of u wilt partitioneren op een specifieke tabel kolom of een query.
1. Als u **kolom**hebt geselecteerd, kiest u de kolom partitie.
1. Als u **query**hebt geselecteerd, voert u een query in die overeenkomt met het partitie schema van de database tabel. Met deze query kan de bron database-engine gebruikmaken van partitie-eliminatie. De tabellen van de bron database hoeven niet te worden gepartitioneerd. Als uw bron nog niet is gepartitioneerd, maakt ADF nog steeds gebruik van gegevenspartitionering in de Spark-transformatie omgeving op basis van de sleutel die u in de bron transformatie selecteert.

![Bron onderdeel](media/data-flow/sourcepart3.png "Bron onderdeel")

### <a name="source-batch-size-input-and-isolation-level"></a>Grootte, invoer en isolatie niveau van de bron batch

Onder **bron opties** in de bron transformatie kunnen de volgende instellingen invloed hebben op de prestaties:

* Batch grootte geeft ADF om gegevens op te slaan in sets in het geheugen in plaats van rijen per rij. Batch grootte is een optionele instelling en er zijn mogelijk onvoldoende resources op de reken knooppunten als ze niet op de juiste wijze zijn gewijzigd.
* Door een query in te stellen, kunt u rijen op de bron filteren voordat ze in de gegevens stroom voor verwerking arriveren. Zo kunt u de initiële gegevens sneller aanschaffen. Als u een query gebruikt, kunt u optionele query hints toevoegen voor uw Azure SQL-data base, zoals lezen niet-vastgelegd.
* Lezen niet-doorgevoerd levert snellere query resultaten voor de bron transformatie

![Bron](media/data-flow/source4.png "Bron")

### <a name="sink-batch-size"></a>Grootte van Sink-batch

Stel **Batch grootte** in op het tabblad Instellingen voor Azure SQL DB-en Azure SQL DW-sinks om te voor komen dat de gegevens stromen worden verwerkt door rijen. Als Batch grootte is ingesteld, verwerkt ADF database schrijf bewerkingen in batches op basis van de opgegeven grootte.

![Tenen](media/data-flow/sink4.png "Sink")

### <a name="partitioning-on-sink"></a>Partitioneren op Sink

Zelfs als uw gegevens niet in uw doel tabellen zijn gepartitioneerd, wordt het aanbevolen om uw gegevens in de Sink-trans formatie te partitioneren. Gepartitioneerde gegevens leiden vaak tot veel sneller laden via het afdwingen van alle verbindingen om één knoop punt/partitie te gebruiken. Ga naar het tabblad Optimize van uw Sink en selecteer *Round Robin* partitioneren om het ideale aantal partities te selecteren dat u naar uw Sink wilt schrijven.

### <a name="disable-indexes-on-write"></a>Indexen bij schrijven uitschakelen

Voeg in uw pijp lijn een [opgeslagen procedure activiteit](transform-data-using-stored-procedure.md) toe vóór de activiteit gegevens stroom, waardoor de indexen van uw doel tabellen die vanuit uw Sink zijn geschreven, worden uitgeschakeld. Voeg na de activiteit van de gegevens stroom een andere opgeslagen procedure activiteit toe waarmee deze indexen worden ingeschakeld.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Verg root de grootte van uw Azure SQL-data base en DW

Plan een grootte van uw bron en Sink Azure SQL DB en DW vóór de uitvoering van de pijp lijn om de door voer te verhogen en Azure-beperking te minimaliseren zodra u DTU-limieten bereikt. Nadat de uitvoering van de pijp lijn is voltooid, kunt u het formaat van uw data bases herstellen naar hun normale uitvoerings frequentie.

### <a name="azure-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Alleen Azure SQL DW] Gebruik fase ring om gegevens bulksgewijs te laden via Poly base

Als u wilt voor komen dat rijen worden ingevoegd in uw DW, schakelt u **fase ring inschakelen** in uw Sink-instellingen in, zodat ADF [poly base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)kan gebruiken. Met poly Base kan de gegevens bulksgewijs worden geladen met ADF.
* Wanneer u de gegevens stroom activiteit vanuit een pijp lijn uitvoert, moet u een BLOB of ADLS Gen2 opslag locatie selecteren om uw gegevens tijdens het bulksgewijs laden te zetten.

## <a name="optimizing-for-files"></a>Optimaliseren voor bestanden

U kunt bij elke trans formatie instellen welk partitie schema u data factory wilt gebruiken op het tabblad Optimize.
* Voor kleinere bestanden kan het selecteren van *één partitie* soms beter en sneller werken dan Spark om uw kleine bestanden te partitioneren.
* Als u niet voldoende informatie over de bron gegevens hebt, kiest u *Round Robin* partitioneren en stelt u het aantal partities in.
* Als uw gegevens kolommen bevatten die goede hash-sleutels kunnen zijn, kiest u *hash-partitionering*.

Als u fouten opspoort in de voorbeeld weergave van gegevens en de fout opsporing voor de pijp lijn, zijn de limiet-en sampling groottes voor bron gegevens sets op basis van bestanden alleen van toepassing op het aantal geretourneerde rijen, niet het aantal rijen dat wordt gelezen. Dit kan invloed hebben op de prestaties van de uitvoeringen van de fout opsporing en kan ertoe leiden dat de stroom mislukt.
* Fout opsporing voor clusters is standaard kleine clusters met één knoop punt. het wordt aangeraden om voor beelden van kleine bestanden te gebruiken voor fout opsporing. Ga naar instellingen voor fout opsporing en wijs een kleine subset van uw gegevens aan met behulp van een tijdelijk bestand.

    ![Instellingen voor fout opsporing](media/data-flow/debugsettings3.png "Instellingen voor fout opsporing")

### <a name="file-naming-options"></a>Opties voor bestands naamgeving

De meest voorkomende manier om getransformeerde gegevens te schrijven in de toewijzing van gegevens stromen het schrijven van BLOB-of ADLS-bestands opslag. In uw Sink moet u een gegevensset selecteren die verwijst naar een container of map, niet een benoemd bestand. Als toewijzings gegevens stroom gebruikt Spark voor uitvoering, wordt uw uitvoer gesplitst over meerdere bestanden op basis van het partitie schema.

Een gemeen schappelijk schema voor partitionering is het kiezen van _uitvoer naar één bestand_, waarbij alle uitvoer onderdeel bestanden worden samengevoegd tot één bestand in uw Sink. Voor deze bewerking moet de uitvoer worden beperkt tot één partitie op één cluster knooppunt. U kunt geen cluster knooppunt bronnen meer gebruiken als u veel grote bron bestanden in één uitvoer bestand combineert.

Als u wilt voor komen dat bronnen met reken knooppunten uitgeput blijven, behoud dan het standaard geoptimaliseerde schema in de gegevens stroom en voeg een Kopieer activiteit toe aan de pijp lijn waarmee alle deel bestanden uit de uitvoermap worden samengevoegd naar een nieuw bestand. Deze techniek scheidt de actie van trans formatie van het samen voegen van bestanden en verkrijgt hetzelfde resultaat als het instellen van _uitvoer naar één bestand_.

### <a name="looping-through-file-lists"></a>Lijst met bestanden door lopen

Een toewijzings gegevens stroom wordt verbeterd wanneer de bron transformatie meer dan één bestand doorloopt in plaats van met behulp van de voor elke activiteit. Het is raadzaam om joker tekens of lijst met bestanden in de bron transformatie te gebruiken. Het proces voor gegevens stroom wordt sneller uitgevoerd, omdat de herhaling in het Spark-cluster kan worden uitgevoerd. Zie [joker tekens gebruiken in bron transformatie](data-flow-source.md#file-based-source-options)voor meer informatie.

Als u bijvoorbeeld een lijst met gegevens bestanden van juli 2019 hebt die u wilt verwerken in een map in Blob Storage, volgt een Joker teken dat u in de bron transformatie kunt gebruiken.

```DateFiles/*_201907*.txt```

Als u Joker tekens gebruikt, bevat uw pijp lijn slechts één gegevens stroom activiteit. Dit zal beter zijn dan een zoek actie voor de BLOB-Store die vervolgens doorloopt over alle overeenkomende bestanden met behulp van een ForEach met de activiteit gegevens stroom uitvoeren in.

### <a name="optimizing-for-cosmosdb"></a>Optimaliseren voor CosmosDB

Het instellen van de door Voer en batch-eigenschappen op CosmosDB-sinks worden alleen van kracht tijdens de uitvoering van de gegevens stroom van een pijplijn activiteit. De oorspronkelijke verzamelings instellingen worden tijdens de uitvoering van de gegevens stroom gehonoreerd door CosmosDB.

* Batch grootte: Bereken de omvang van de ruwe rijen van uw gegevens en zorg ervoor dat rowSize * Batch grootte kleiner is dan 2.000.000. Als dat het geval is, verg root u de Batch grootte om een betere door voer te krijgen
* Througput: Stel hier een hogere doorvoer instelling in zodat documenten sneller naar CosmosDB kunnen schrijven. Houd de hogere RU-kosten in acht op basis van een instelling voor hoge door voer.
*   Budget voor schrijf doorvoer: gebruik een waarde die kleiner is dan het totaal van RUs per minuut. Als u een gegevens stroom hebt met een groot aantal Spark-partitiongs, is het instellen van een budget doorvoer meer evenwicht over die partities.

## <a name="next-steps"></a>Volgende stappen

Zie andere artikelen over gegevens stromen met betrekking tot prestaties:

- [Tabblad gegevens stroom optimaliseren](concepts-data-flow-overview.md#optimize)
- [Activiteit gegevens stroom](control-flow-execute-data-flow-activity.md)
- [Prestaties van de gegevens stroom bewaken](concepts-data-flow-monitoring.md)
