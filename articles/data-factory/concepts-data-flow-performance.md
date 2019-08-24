---
title: Prestaties en afstemmings handleiding voor gegevens stromen toewijzen in Azure Data Factory | Microsoft Docs
description: Meer informatie over de belangrijkste factoren die van invloed zijn op de prestaties van gegevens stromen in Azure Data Factory wanneer u gegevens stromen toewijzing gebruikt.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 8eb244a0eff1569ac27feae68104db613373463a
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992352"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Gegevens stromen toewijzen prestaties en afstemmings handleiding

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory gegevens stromen voor toewijzing bieden een code-vrije browser interface voor het ontwerpen, implementeren en organiseren van gegevens transformaties op schaal.

> [!NOTE]
> Zie [overzicht van gegevens stromen](concepts-data-flow-overview.md) voordat u dit artikel leest als u niet bekend bent met gegevens stromen van ADF-toewijzing in het algemeen.
>

> [!NOTE]
> Bij het ontwerpen en testen van gegevens stromen vanuit de ADF-gebruikers interface, moet u ervoor zorgen dat u de schakel optie voor fout opsporing inschakelt, zodat u uw gegevens stromen in realtime kunt uitvoeren zonder te hoeven wachten tot een cluster is opgewarmd.
>

![Knop fout opsporing](media/data-flow/debugb1.png "Fouten opsporen")

## <a name="monitor-data-flow-performance"></a>Prestaties van de gegevens stroom bewaken

Tijdens het ontwerpen van uw toewijzings gegevens stromen in de browser, kunt u elke afzonderlijke trans formatie testen door te klikken op het tabblad voor beeld van gegevens in het deel venster onderste instellingen voor elke trans formatie. De volgende stap moet u uitvoeren om de gegevens stroom end-to-end te testen in de ontwerp functie voor pijp lijnen. Voeg een activiteit gegevens stroom uitvoeren toe en gebruik de knop fout opsporing om de prestaties van uw gegevens stroom te testen. In het onderste deel venster van het venster met de pijp lijn ziet u een Eyeglass pictogram onder ' acties ':

![Monitor voor gegevens stroom](media/data-flow/mon002.png "Monitor voor gegevens stroom 2")

Als u op dit pictogram klikt, wordt het uitvoerings plan en het volgende prestatie profiel van uw gegevens stroom weer gegeven. U kunt deze informatie gebruiken om de prestaties van uw gegevens stroom te schatten op basis van verschillende gegevens bronnen. Houd er rekening mee dat u 1 minuut van het instellen van de cluster taak uitvoering in uw algemene prestatie berekeningen kunt aannemen en als u de standaard Azure Integration Runtime gebruikt, moet u mogelijk ook 5 minuten van een cluster tijd voor het maken van clusters toevoegen.

![Bewaking van gegevens stromen](media/data-flow/mon003.png "Monitor voor gegevens stroom 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimaliseren voor Azure SQL Database en Azure SQL Data Warehouse

![Bron onderdeel](media/data-flow/sourcepart3.png "Bron onderdeel")

### <a name="partition-your-source-data"></a>De bron gegevens partitioneren

* Ga naar Optimize en selecteer source. Stel een specifieke tabel kolom of een type in een query in.
* Als u ' kolom ' hebt gekozen, selecteert u de kolom partitie.
* Stel ook het maximum aantal verbindingen in voor uw Azure SQL-data base. U kunt een hogere instelling proberen om parallelle verbindingen met uw data base te krijgen. Sommige gevallen kunnen echter leiden tot snellere prestaties met een beperkt aantal verbindingen.
* De tabellen van de bron database hoeven niet te worden gepartitioneerd.
* Als u een query instelt in de bron transformatie die overeenkomt met het partitie schema van uw database tabel, kan de bron database-engine gebruikmaken van partitie-eliminatie.
* Als uw bron nog niet is gepartitioneerd, gebruikt ADF nog steeds gegevens partities in de Spark-transformatie omgeving op basis van de sleutel die u in de bron transformatie selecteert.

### <a name="set-batch-size-and-query-on-source"></a>De Batch grootte en de query voor de bron instellen

![Bron](media/data-flow/source4.png "Bron")

* Bij het instellen van de Batch grootte wordt ADF geïnstrueerd om gegevens op te slaan in sets in het geheugen in plaats van rijen per rij. Het is een optionele instelling en er zijn mogelijk onvoldoende resources op de reken knooppunten als ze niet op de juiste wijze zijn gewijzigd.
* Door een query in te stellen, kunt u rijen direct op de bron filteren voordat ze zelfs voor de gegevens stroom voor de verwerking arriveren, waardoor de initiële gegevens sneller kunnen worden geacquisition.
* Als u een query gebruikt, kunt u optionele query hints toevoegen voor uw Azure SQL-data base, d.w.z. lezen niet-vastgelegd

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>Het isolatie niveau instellen voor de bron transformatie instellingen voor SQL-gegevens sets

* Lezen niet-doorgevoerd levert snellere query resultaten voor de bron transformatie

![Isolatie niveau](media/data-flow/isolationlevel.png "Isolatie niveau")

### <a name="set-sink-batch-size"></a>Grootte van Sink-batch instellen

![Sink](media/data-flow/sink4.png "Sink")

* Als u wilt voor komen dat de gegevens stromen door rijen worden verwerkt, stelt u de Batch grootte in voor de Sink-instellingen voor Azure SQL DB. Dit geeft aan dat de schrijf bewerkingen in de data base in batches moeten worden verwerkt op basis van de beschik bare grootte.

### <a name="set-partitioning-options-on-your-sink"></a>Opties voor partitioneren instellen voor uw Sink

* Zelfs als u uw gegevens hebt gepartitioneerd in uw doel-Azure SQL DB-tabellen, gaat u naar het tabblad optimaliseren en partitioneren instellen.
* Vaak vertelt ADF het gebruik van Round Robin-partitionering in de Spark-uitvoerings clusters veel sneller laden van gegevens, in plaats van alle verbindingen van één knoop punt/partitie af te dwingen.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Verg root de grootte van uw reken machine in Azure Integration Runtime

![Nieuwe IR](media/data-flow/ir-new.png "Nieuwe IR")

* Verhoog het aantal kernen, waardoor het aantal knoop punten wordt verhoogd en u beschikt over meer verwerkings kracht voor het uitvoeren van query's en schrijven naar uw Azure SQL-data base.
* Gebruik de opties berekenings optimalisatie en geoptimaliseerd voor geheugen om meer bronnen toe te passen op uw reken knooppunten.

### <a name="unit-test-and-performance-test-with-debug"></a>Test-en prestatie test voor eenheid met fout opsporing

* Wanneer de eenheid gegevens stromen test, stelt u de knop ' fout opsporing van gegevens stroom ' in op aan.
* Gebruik in de ontwerp functie voor gegevens stromen het tabblad voor beeld van gegevens in trans formaties om de resultaten van uw transformatie logica weer te geven.
* Eenheid test uw gegevens stromen van de ontwerp functie voor de pijp lijn door een activiteit voor gegevens stromen op het ontwerp model voor de pijp lijn te plaatsen en de knop debug te gebruiken om te testen.
* Testen in foutopsporingsmodus werkt op een live gewarmde cluster omgeving zonder dat u hoeft te wachten op een just-in-time-cluster.

### <a name="disable-indexes-on-write"></a>Indexen bij schrijven uitschakelen
* Gebruik de opgeslagen procedure-activiteit van een ADF-pijp lijn vóór uw activiteit voor gegevens stromen, waardoor de indexen van uw doel tabellen die worden geschreven naar van uw Sink, worden uitgeschakeld.
* Voeg na de activiteit van de gegevens stroom een andere opgeslagen proc-activiteit toe die deze indexen heeft ingeschakeld.

### <a name="increase-the-size-of-your-azure-sql-db"></a>De grootte van uw Azure SQL-data base verg Roten
* Plan een grootte van uw bron en Sink Azure SQL DB voordat u uw pijp lijn uitvoert om de door voer te verhogen en Azure-beperking te minimaliseren zodra u DTU-limieten bereikt.
* Nadat de uitvoering van de pijp lijn is voltooid, kunt u de grootte van de data bases wijzigen in de normale uitvoerings frequentie.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Optimaliseren voor Azure SQL Data Warehouse

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>Gebruik fase ring om gegevens bulksgewijs te laden via Poly base

* Als u wilt voor komen dat de gegevens stromen worden verwerkt, stelt u de optie ' fase ring ' in de Sink-instellingen zodanig in dat ADF kan gebruikmaken van poly Base om te voor komen dat rijen worden ingevoegd in DW. Hiermee wordt ADF geadviseerd om poly Base te gebruiken, zodat gegevens bulksgewijs kunnen worden geladen.
* Wanneer u de gegevens stroom activiteit vanuit een pijp lijn uitvoert, terwijl de fase ring is ingeschakeld, moet u de locatie van de Blob-opslag van de faserings gegevens selecteren voor bulksgewijs laden.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Verg root de grootte van uw Azure SQL DW

* Plan het formaat van uw bron en Sink Azure SQL DW voordat u de pijp lijn uitvoert om de door voer te verhogen en Azure-beperking te minimaliseren wanneer u DWU-limieten bereikt.

* Nadat de uitvoering van de pijp lijn is voltooid, kunt u de grootte van de data bases wijzigen in de normale uitvoerings frequentie.

## <a name="optimize-for-files"></a>Optimaliseren voor bestanden

* U kunt bepalen hoeveel partities met ADF moeten worden gebruikt. Op elke bron-& Sink-trans formatie, evenals elke afzonderlijke trans formatie, kunt u een partitie schema instellen. Voor kleinere bestanden kan het selecteren van ' enkele partitie ' soms beter en sneller werken dan wanneer Spark wordt gevraagd om uw kleine bestanden te partitioneren.
* Als u niet voldoende informatie over de bron gegevens hebt, kunt u Round Robin kiezen en het aantal partities instellen.
* Als u uw gegevens verkent en weet dat u kolommen hebt die goede hash-sleutels kunnen zijn, gebruikt u de optie voor hash-partitionering.
* Als u fouten opspoort in de voorbeeld weergave van gegevens en de fout opsporing voor de pijp lijn, moet u er rekening mee houden dat de limiet en sampling grootte voor bron gegevens sets op basis van bestanden alleen van toepassing zijn op het aantal geretourneerde rijen, niet het aantal rijen dat Dit is belang rijk om aan te geven dat het de prestaties van uw debug-uitvoeringen kan beïnvloeden, waardoor de stroom mogelijk mislukt.
* Houd er rekening mee dat debug-clusters standaard kleine clusters met één knoop punt zijn, dus gebruik tijdelijke kleine bestanden voor fout opsporing. Ga naar instellingen voor fout opsporing en wijs een kleine subset van uw gegevens aan met behulp van een tijdelijk bestand.

![Instellingen voor fout opsporing](media/data-flow/debugsettings3.png "Instellingen voor fout opsporing")

### <a name="file-naming-options"></a>Opties voor bestands naamgeving

* De standaard aard van het schrijven van getransformeerde gegevens in ADF-toewijzings gegevens stromen is het schrijven naar een gegevensset met een BLOB of ADLS gekoppelde service. Stel in dat de gegevensset verwijst naar een map of container, niet naar een benoemd bestand.
* Gegevens stromen gebruiken Azure Databricks Spark voor uitvoering, wat betekent dat uw uitvoer wordt gesplitst over meerdere bestanden op basis van standaard Spark-partitionering of het partitie schema dat u expliciet hebt gekozen.
* Een veelvoorkomende bewerking in ADF-gegevens stromen is het kiezen van uitvoer naar één bestand, zodat alle bestanden met uitvoer onderdelen samen worden samengevoegd tot één uitvoer bestand.
* Voor deze bewerking moet de uitvoer echter worden beperkt tot één partitie op één cluster knooppunt.
* Houd dit in acht wanneer u deze populaire optie kiest. U kunt geen cluster knooppunt bronnen meer gebruiken als u veel grote bron bestanden combineert tot één uitvoer bestands partitie.
* Om te voor komen dat bronnen met reken knooppunten uitgeput blijven, kunt u het standaard-of expliciete partitie schema in de ADF plaatsen, wat de prestaties optimaliseert en vervolgens een volgende Kopieer activiteit toevoegen in de pijp lijn die alle deel bestanden van de uitvoermap samenvoegt naar een nieuwe single Profiler. In wezen scheidt deze techniek de actie van trans formatie van het samen voegen van bestanden en verkrijgt hetzelfde resultaat als het instellen van uitvoer naar één bestand.

### <a name="looping-through-file-lists"></a>Lijst met bestanden door lopen

In de meeste gevallen worden gegevens stromen in ADF beter uitgevoerd op basis van een pijp lijn waarmee de bron transformatie van de gegevens stroom over meerdere bestanden kan worden herhaald. Met andere woorden, het verdient de voor keur om joker tekens of bestands lijsten te gebruiken binnen de bron in de gegevens stroom dan om een grote lijst met bestanden met behulp van ForEach in de pijp lijn te herhalen, waarbij u een stroom voor het uitvoeren van gegevens aanroept voor elke iteratie. Het proces voor gegevens stroom wordt sneller uitgevoerd, omdat de herhaling kan worden uitgevoerd in de gegevens stroom.

Als ik bijvoorbeeld een lijst met gegevens bestanden van 1 juli 2019 die ik wil verwerken in een map in Blob Storage, is het beter om de activiteit gegevens stroom uitvoeren één keer van de pijp lijn aan te roepen en een Joker teken te gebruiken in uw bron, zoals deze :

```DateFiles/*_201907*.txt```

Dit zal beter zijn dan een zoek actie voor de Blob-opslag in een pijp lijn die vervolgens doorloopt over alle overeenkomende bestanden met behulp van een ForEach met de activiteit gegevens stroom uitvoeren in.

## <a name="next-steps"></a>Volgende stappen

Zie de andere artikelen over gegevens stromen met betrekking tot de prestaties:

- [Tabblad gegevens stroom optimaliseren](concepts-data-flow-optimize-tab.md)
- [Activiteit gegevens stroom](control-flow-execute-data-flow-activity.md)
- [Prestaties van de gegevens stroom bewaken](concepts-data-flow-monitoring.md)
