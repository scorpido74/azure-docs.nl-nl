---
title: Gids voor de prestaties en schaal baarheid van de Kopieer activiteit
description: Meer informatie over de belangrijkste factoren die van invloed zijn op de prestaties van het verplaatsen van gegevens in Azure Data Factory wanneer u de Kopieer activiteit gebruikt.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/24/2019
ms.openlocfilehash: 28d0da369083d75bc175111d808828e186a366fc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444137"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Gids voor de prestaties en schaal baarheid van de Kopieer activiteit

> [!div class="op_single_selector" title1="Selecteer de versie van Azure Data Factory die u gebruikt:"]
> * [Versie 1:](v1/data-factory-copy-activity-performance.md)
> * [Huidige versie](copy-activity-performance.md)

Of u nu een grootschalige gegevens migratie wilt uitvoeren van data Lake of ENTER prise Data Warehouse (EDW) naar Azure, of dat u gegevens wilt opnemen op schaal uit verschillende bronnen in azure voor big data Analytics, het is essentieel om optimale prestaties te verwezenlijken en schaal baarheid.  Azure Data Factory biedt een krachtige, robuuste en kosteneffectieve methode voor het opnemen van gegevens op schaal, waardoor het handig is voor gegevens technici die zeer krachtige en schaal bare gegevens opname pijplijnen willen bouwen.

Na het lezen van dit artikel, kunt u zich de volgende vragen beantwoorden:

- Welk prestatie-en schaal niveau kan ik gebruiken voor het gebruik van ADF Copy-activiteiten voor gegevens migratie en gegevens opname scenario's?

- Welke stappen moet ik ondernemen om de prestaties van de ADF-Kopieer activiteit te optimaliseren?
- Wat kan ik doen met de methoden voor het optimaliseren van ADF? de prestaties voor een enkele Kopieer activiteit worden uitgevoerd?
- Wat andere factoren buiten ADF zijn om rekening mee te houden wanneer u de Kopieer prestaties optimaliseert?

> [!NOTE]
> Als u niet bekend bent met de Kopieer activiteit in het algemeen, raadpleegt u het overzicht van de [Kopieer activiteit](copy-activity-overview.md) voordat u dit artikel leest.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Kopieer prestaties en schaal baarheid die kan worden behaald met behulp van ADF

ADF biedt een serverloze architectuur die parallellisme op verschillende niveaus mogelijk maakt, zodat ontwikkel aars pijp lijnen kunnen bouwen om uw netwerk bandbreedte en opslag-IOPS en band breedte optimaal te benutten voor de door Voer van gegevens verplaatsing voor uw omgeving.  Dit betekent dat de door u gewenste door Voer kan worden geschat door de minimale door voer te meten die wordt aangeboden door de brongegevens opslag, het doel gegevens archief en de netwerk bandbreedte tussen de bron en de bestemming.  In de volgende tabel wordt de duur van de kopie berekend op basis van de gegevens grootte en de bandbreedte limiet voor uw omgeving. 

| Gegevens grootte/ <br/> bandbreedte | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 minuten    | 1,4 minuten   | 0,3 minuten   | 0,1 minuten  | 0,03 minuten | 0,01 minuten | 0,0 minuten   |
| **10 GB**                   | 27,3 minuten   | 13,7 minuten  | 2,7 minuten   | 1,3 minuten  | 0,3 minuten  | 0,1 minuten  | 0,03 minuten  |
| **100 GB**                  | 4,6 uur    | 2,3 uur   | 0,5 uur   | 0,2 uur  | 0,05 uur | 0,02 uur | 0,0 uur   |
| **1 TB**                    | 46,6 uur   | 23,3 uur  | 4,7 uur   | 2,3 uur  | 0,5 uur  | 0,2 uur  | 0,05 uur  |
| **10 TB**                   | 19,4 dagen  | 9,7 dagen  | 1,9 dagen  | 0,9 dagen | 0,2 dagen | 0,1 dagen | 0,02 dagen |
| **100 TB**                  | 194,2 dagen | 97,1 dagen | 19,4 dagen | 9,7 dagen | 1,9 dagen | 1 dagen   | 0,2 dagen  |
| **1 PB**                    | 64,7 mo    | 32,4 mo   | 6,5 mo    | 3,2 mo   | 0,6 mo   | 0,3 mo   | 0,06 mo   |
| **10 PB**                   | 647,3 mo   | 323,6 mo  | 64,7 mo   | 31,6 mo  | 6,5 mo   | 3,2 mo   | 0,6 mo    |

ADF-kopie is schaalbaar op verschillende niveaus:

![de schaal van ADF kopiëren](media/copy-activity-performance/adf-copy-scalability.png)

- De controle stroom van ADF kan meerdere Kopieer activiteiten parallel starten, bijvoorbeeld [voor elke lus](control-flow-for-each-activity.md).
- Eén Kopieer activiteit kan profiteren van schaal bare reken bronnen: wanneer u Azure Integration Runtime gebruikt, kunt u [Maxi maal 256 DIUs](#data-integration-units) voor elke Kopieer activiteit op serverloze wijze opgeven. Wanneer u zelf-hostende Integration Runtime gebruikt, kunt u de machine hand matig opschalen of uitschalen naar meerdere machines ([Maxi maal 4 knoop punten](create-self-hosted-integration-runtime.md#high-availability-and-scalability)), en met één Kopieer activiteit wordt de bestands set gepartitioneerd op alle knoop punten.
- Eén Kopieer activiteit leest van en schrijft naar het gegevens archief met behulp van meerdere threads [parallel](#parallel-copy).

## <a name="performance-tuning-steps"></a>Prestaties afstemmen stappen

Voer de volgende stappen uit om de prestaties van uw Azure Data Factory-service af te stemmen met de Kopieer activiteit.

1. **Haal een test gegevensset op en stel een basis lijn in.** Test uw pijp lijn tijdens de ontwikkelings fase met behulp van de Kopieer activiteit op basis van een representatief gegevens voorbeeld. De gegevensset die u kiest, moet uw typische gegevens patronen vertegenwoordigen (mapstructuur, bestands patroon, gegevens schema enzovoort) en is groot genoeg om de Kopieer prestaties te evalueren, bijvoorbeeld omdat het 10 minuten of langer duurt voordat de Kopieer activiteit is voltooid. Verzamel Details van de uitvoerings gegevens en prestatie kenmerken na het controleren van de [activiteit](copy-activity-overview.md#monitoring).

2. **De prestaties van één Kopieer activiteit maximaliseren**:

   Om te beginnen, raden we u aan eerst de prestaties te maximaliseren met één Kopieer activiteit.

   **Als de Kopieer activiteit wordt uitgevoerd op een Azure Integration Runtime:**

   Begin met standaard waarden voor de instellingen voor [gegevens integratie-eenheden (DIU)](#data-integration-units) en [parallelle kopieën](#parallel-copy) .  Voer een prestatie test uit en Bekijk de prestaties die zijn behaald, evenals de werkelijke waarden die worden gebruikt voor DIUs en parallelle kopieën.  Raadpleeg de [controle activiteit kopiëren](copy-activity-overview.md#monitoring) voor het verzamelen van resultaten en prestatie-instellingen die worden gebruikt.

   Voer nu extra prestatie test uitvoeringen uit, telkens wanneer u de waarde voor de DIU-instelling verdubbelt.  Als u denkt dat de prestaties die zijn behaald met de standaard instelling ver onder uw verwachting liggen, kunt u de DIU-instelling drastisch verhogen in de volgende test uitvoering.

   De Kopieer activiteit moet bijna perfect worden geschaald wanneer u de instelling DIU verhoogt.  Als u de DIU-instelling die u niet ziet de door Voer dubbelklikt, kunnen er twee dingen gebeuren:

   - Het specifieke Kopieer patroon dat u uitvoert, heeft geen voor deel om meer DIUs toe te voegen.  Hoewel u een grotere DIU-waarde hebt opgegeven, heeft het gebruikte werkelijke DIU hetzelfde gebleven. Daarom krijgt u dezelfde door Voer als voorheen.  Als dit het geval is, maximaliseert u de cumulatieve door voer door meerdere exemplaren gelijktijdig te verwijzen naar stap 3.
   - Door meer DIUs toe te voegen (meer paarden kracht) en zo een hogere snelheid van gegevens extractie, overdracht en laden te hebben, kan het brongegevens archief, het netwerk tussen of het doel gegevens archief het knel punt hebben bereikt en mogelijk wordt beperkt.  Als dit het geval is, neemt u contact op met de beheerder van uw gegevens opslag of uw netwerk beheerder om de bovengrens in te stellen, of door de DIU-instelling te verlagen totdat de beperking stopt.

   **Als de Kopieer activiteit wordt uitgevoerd op een zelf-hostende Integration Runtime:**

   U kunt het beste een toegewezen computer gebruiken die onafhankelijk is van de server die als host fungeert voor de gegevens opslag voor de integratie-runtime.

   Begin met de standaard waarden voor de instelling voor [parallelle kopieën](#parallel-copy) en gebruik één knoop punt voor de zelf-hostende IR.  Voer een prestatie test uit en Bekijk de bereikte prestaties.

   Als u een hogere door voer wilt doen, kunt u de zelf-hostende IR schalen of uitschalen:

   - Als de CPU en het beschik bare geheugen op het zelf-hostende IR-knoop punt niet volledig worden gebruikt, maar de uitvoering van gelijktijdige taken de limiet bereikt, moet u omhoog schalen door het aantal gelijktijdige taken dat kan worden uitgevoerd op een knoop punt te verhogen.  Zie [hier](create-self-hosted-integration-runtime.md#scale-up) voor instructies.
   - Als de CPU daarentegen hoog is op het zelf-hostende IR-knoop punt of het beschik bare geheugen is laag, kunt u een nieuw knoop punt toevoegen om de belasting over meerdere knoop punten te verg Roten.  Zie [hier](create-self-hosted-integration-runtime.md#high-availability-and-scalability) voor instructies.

   Wanneer u de capaciteit van de zelf-hostende IR opschaalt of uitbreidt, herhaalt u de uitvoering van de prestatie test om te zien of u een betere door Voer krijgt.  Als de door Voer niet meer wordt verbeterd, is het mogelijk dat het brongegevens archief, het netwerk tussen of het doel gegevens archief het knel punt heeft bereikt en het proces kan worden vertraagd. Als dit het geval is, neemt u contact op met de beheerder van uw gegevens opslag of uw netwerk beheerder om de bovengrens te verhogen, of gaat u terug naar de vorige schaal instelling voor de zelf-hostende IR. 

3. **De geaggregeerde door Voer maximaliseren door meerdere exemplaren gelijktijdig uit te voeren:**

   Nu u de prestaties van één Kopieer activiteit hebt gemaximaliseerd, kunt u, als u nog niet de bovengrens voor door Voer van uw omgeving hebt bereikt: netwerk, brongegevens archief en doel gegevens opslag, meerdere Kopieer activiteiten tegelijk uitvoeren met de constructies van de ADF-controle stroom, zoals [voor elke lus](control-flow-for-each-activity.md).

4. **Tips voor het afstemmen van prestaties en optimaliserings functies.** In sommige gevallen ziet u, wanneer u een Kopieer activiteit uitvoert in Azure Data Factory, het bericht ' prestatie afstemmings tips ' boven op de bewaking van de [Kopieer activiteit](copy-activity-overview.md#monitor-visually), zoals wordt weer gegeven in het volgende voor beeld. Het bericht vertelt u het knel punt dat is geïdentificeerd voor de opgegeven kopie-uitvoering. Ook wordt u begeleid bij het verbeteren van de door Voer van de Kopieer capaciteit. De tips voor het afstemmen van de prestaties bieden momenteel suggesties als:

   - Gebruik poly Base als u gegevens naar Azure SQL Data Warehouse kopieert.
   - Verg root Azure Cosmos DB aanvraag eenheden of Azure SQL Database Dtu's (data base-doorvoer eenheden) wanneer de resource aan de kant van het gegevens archief het knel punt is.
   - Verwijder de onnodige kopie die u wilt gefaseerd.

   De regels voor het afstemmen van de prestaties worden ook geleidelijk verrijkt.

   **Voor beeld: kopiëren naar Azure SQL Database met tips voor het afstemmen van prestaties**

   In dit voor beeld, tijdens het uitvoeren van een kopie, Azure Data Factory wordt de Sink Azure SQL Database het hoge DTU-gebruik te bereiken, waardoor de schrijf bewerkingen worden vertraagd. Het voor stel is om de Azure SQL Database-laag te verg Roten met meer Dtu's. 

   ![Bewaking met tips voor het afstemmen van prestaties kopiëren](media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

   Daarnaast moet u rekening houden met de volgende functies van prestatie optimalisatie:

   - [Parallelle kopiëren](#parallel-copy)
   - [Eenheden voor gegevensintegratie](#data-integration-units)
   - [Gefaseerd kopiëren](#staged-copy)
   - [Schaal baarheid van zelf-hostende Integration runtime](concepts-integration-runtime.md#self-hosted-integration-runtime)

5. **Breid de configuratie uit naar uw volledige gegevensset.** Wanneer u tevreden bent met de resultaten en prestaties van de uitvoering, kunt u de definitie en pijp lijn uitvouwen om uw hele gegevensset te bedekken.

## <a name="copy-performance-optimization-features"></a>Functies voor het optimaliseren van prestaties kopiëren

Azure Data Factory biedt de volgende functies voor prestatie optimalisatie:

- [Parallelle kopiëren](#parallel-copy)
- [Eenheden voor gegevensintegratie](#data-integration-units)
- [Gefaseerd kopiëren](#staged-copy)

### <a name="data-integration-units"></a>Gegevens integratie-eenheden

Een gegevens integratie-eenheid is een meting die de kracht vertegenwoordigt (een combi natie van CPU, geheugen en netwerk bron toewijzing) van één eenheid in Azure Data Factory. De gegevens integratie-eenheid is alleen van toepassing op [Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime), maar niet [zelf-hostende Integration runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

U wordt gefactureerd voor het **aantal gebruikte DIUs \* Kopieer duur \* eenheids prijs/DIU-uur**. Bekijk [hier](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)de huidige prijzen. Lokale valuta en afzonderlijke korting kunnen per abonnements type worden toegepast.

De toegestane DIUs om de uitvoering van een Kopieer activiteit te stimuleren, ligt **tussen 2 en 256**. Als u dit niet opgeeft of als u ' automatisch ' kiest in de gebruikers interface, Data Factory de optimale DIU-instelling dynamisch Toep assen op basis van uw combi natie van bron-Sink en gegevens patroon. De volgende tabel bevat de standaard DIUs die worden gebruikt in verschillende Kopieer scenario's:

| Scenario kopiëren | Standaard DIUs bepaald door de service |
|:--- |:--- |
| Gegevens kopiëren tussen winkels op basis van bestanden | Tussen 4 en 32, afhankelijk van het aantal en de grootte van de bestanden |
| Gegevens kopiëren naar Azure SQL Database of Azure Cosmos DB |Tussen 4 en 16, afhankelijk van de Azure SQL Database van de sink of de Cosmos DB laag (aantal Dtu's/RUs) |
| Alle andere scenario's voor kopiëren | 4 |

Als u wilt deze standaardwaarde onderdrukken, Geef een waarde op voor de **dataIntegrationUnits** eigenschap als volgt te werk. De *werkelijke aantal DIUs* dat gebruikmaakt van de kopieerbewerking tijdens de uitvoering is gelijk aan of kleiner is dan de geconfigureerde waarde, afhankelijk van het patroon van uw gegevens.

U kunt de DIUs die wordt gebruikt voor elke Kopieer bewerking zien in de uitvoer van de Kopieer activiteit wanneer u een uitvoering van een activiteit bewaakt. Zie voor meer informatie [controle activiteit kopiëren](copy-activity-overview.md#monitoring).

> [!NOTE]
> De instelling van DIUs die groter is dan vier, is alleen van toepassing wanneer u meerdere bestanden kopieert van Azure Blob/ADLS Gen1/ADLS Gen2/Amazon S3/Google Cloud Storage/Cloud FTP/Cloud SFTP of van een partitie-optie-relationeel gegevens archief (inclusief [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)) naar andere gegevens archieven in de Cloud.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="parallel-copy"></a>Parallelle kopie

U kunt de eigenschap **parallelCopies** gebruiken om aan te geven in welke parallellisme u de Kopieer activiteit wilt gebruiken. U kunt deze eigenschap beschouwen als het maximum aantal threads in de Kopieer activiteit dat kan worden gelezen vanuit uw bron of naar uw Sink-gegevens opslag kunt schrijven, parallel.

Voor elke uitvoering van de Kopieer activiteit bepaalt Azure Data Factory het aantal parallelle kopieën dat moet worden gebruikt voor het kopiëren van gegevens uit de brongegevens opslag en naar het doel gegevens archief. Het standaard aantal parallelle kopieën dat wordt gebruikt, is afhankelijk van het type bron en Sink dat u gebruikt.

| Scenario kopiëren | Standaard parallelle kopie aantal bepaald door de service |
| --- | --- |
| Gegevens kopiëren tussen winkels op basis van bestanden |Is afhankelijk van de grootte van de bestanden en het aantal DIUs dat wordt gebruikt voor het kopiëren van gegevens tussen twee gegevens archieven in de Cloud of de fysieke configuratie van de zelf-hostende Integration runtime-computer. |
| Kopiëren van relationeel gegevens archief met ingeschakelde partitie optie (inclusief [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP-tabel](connector-sap-table.md#sap-table-as-source)en [SAP open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source))|4 |
| Gegevens uit een bron archief kopiëren naar Azure-tabel opslag |4 |
| Alle andere kopieerscenario 's |1 |

> [!TIP]
> Wanneer u gegevens kopieert tussen archieven op basis van bestanden, biedt het standaard gedrag doorgaans de beste door voer. Het standaard gedrag wordt automatisch bepaald op basis van het patroon van het bron bestand.

Als u de belasting wilt beheren op machines die uw gegevens archieven hosten, of als u de Kopieer prestaties wilt afstemmen, kunt u de standaard waarde overschrijven en een waarde opgeven voor de eigenschap **parallelCopies** . De waarde moet een geheel getal groter dan of gelijk aan 1 zijn. Tijdens runtime gebruikt de Kopieer activiteit een waarde die kleiner is dan of gelijk is aan de waarde die u hebt ingesteld.

**Punten om te noteren:**

- Wanneer u gegevens kopieert tussen archieven op basis van bestanden, bepaalt **parallelCopies** de parallelle factor op bestands niveau. De Chunking binnen één bestand vindt onder automatisch en transparant plaats. Het is ontworpen om de beste geschikte segment grootte te gebruiken voor een bepaald type brongegevens opslag om gegevens parallel en rechthoekig naar **parallelCopies**te laden. Het werkelijke aantal parallelle exemplaren data movement service wordt gebruikt voor de kopieerbewerking tijdens runtime is niet meer dan het aantal bestanden dat u hebt. Als het Kopieer gedrag **mergeFile**is, kan de Kopieer activiteit niet profiteren van parallellisme op bestands niveau.
- Wanneer u gegevens kopieert uit winkels die niet op bestanden zijn gebaseerd (met uitzonde ring van [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP-tabel](connector-sap-table.md#sap-table-as-source)en [SAP open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) -connector als bron voor het inschakelen van gegevens partities), wordt de eigenschap **parallelCopies** genegeerd door de service voor gegevens verplaatsing. Zelfs als parallelle uitvoering is opgegeven, wordt deze niet toegepast in dit geval.
- De eigenschap **parallelCopies** is rechthoekig naar **dataIntegrationUnits**. De eerste wordt in de eenheden voor de integratie van gegevens geteld.
- Wanneer u een waarde voor de eigenschap **parallelCopies** opgeeft, moet u rekening houden met de toename van de belasting van uw bron-en Sink-gegevens opslag. Houd ook rekening met de toename van de belasting voor de zelf-hostende Integration runtime als de Kopieer activiteit hiervoor is gemachtigd, bijvoorbeeld voor hybride kopieën. Deze belasting toename treedt vooral op wanneer u meerdere activiteiten of gelijktijdige uitvoeringen hebt van dezelfde activiteiten die worden uitgevoerd op hetzelfde gegevens archief. Als u merkt dat het gegevens archief of de zelf-hostende Integration runtime wordt overspoeld met de belasting, verlaagt u de **parallelCopies** -waarde om de belasting te ontlasten.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

### <a name="staged-copy"></a>Gefaseerd kopiëren

Wanneer u gegevens van een brongegevensarchief naar een sink-gegevensarchief kopieert, kunt u Blob-opslag gebruiken als een tussentijdse faseringsopslag. Fasering is vooral nuttig in de volgende gevallen:

- **U wilt gegevens uit verschillende gegevens archieven opnemen in SQL Data Warehouse via Poly base.** SQL Data Warehouse gebruikmaakt van een grote hoeveelheid gegevens laden in SQL Data Warehouse PolyBase als een mechanisme voor hoge doorvoer. De bron gegevens moeten zich in Blob Storage of Azure Data Lake Store bekomen en moeten aan aanvullende criteria voldoen. Bij het laden van gegevens vanuit een ander gegevensarchief dan Blob storage of Azure Data Lake Store, kunt u gegevens kopiëren via tussentijdse staging Blob-opslag kunt activeren. In dat geval voert Azure Data Factory de vereiste gegevens transformaties uit om ervoor te zorgen dat het voldoet aan de vereisten van poly base. Vervolgens maakt PolyBase om gegevens te laden in SQL Data Warehouse efficiënt gebruik. Zie voor meer informatie, [gebruik PolyBase om gegevens te laden in Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Soms duurt het even om een hybride gegevens verplaatsing (dat wil zeggen, kopiëren van een on-premises gegevens archief naar een gegevens archief in de Cloud) uit te voeren via een trage netwerk verbinding.** Om de prestaties te verbeteren, kunt u gefaseerde kopie gebruiken om de gegevens on-premises te comprimeren, zodat het minder tijd kost om gegevens te verplaatsen naar de faserings gegevens opslag in de Cloud. Vervolgens kunt u de gegevens in het faserings archief decomprimeren voordat u ze laadt in de doel gegevens opslag.
- **U wilt geen andere poorten dan poort 80 en poort 443 openen in uw firewall vanwege het IT-beleid van het bedrijf.** Wanneer u gegevens van een on-premises gegevensarchief naar een Azure SQL Database-sink of een Azure SQL Data Warehouse-sink kopiëren, moet u bijvoorbeeld uitgaande TCP-communicatie op poort 1433 voor zowel de Windows firewall en firewall van uw bedrijf te activeren. In dit scenario kan een gefaseerde kopie gebruikmaken van de zelf-hostende Integration runtime om eerst gegevens te kopiëren naar een staging-opslag instantie van een BLOB via HTTP of HTTPS op poort 443. Vervolgens kunnen de gegevens worden geladen in SQL Database of SQL Data Warehouse vanuit de fase van het maken van Blob-opslag. In deze stroom moet u geen poort 1433 in te schakelen.

#### <a name="how-staged-copy-works"></a>Hoe gefaseerd kopiëren werkt

Wanneer u de functie voor gefaseerde installatie activeert, eerst de gegevens worden gekopieerd uit de brongegevensopslag naar de Blob-opslag staging (Voeg uw eigen). Vervolgens is de gegevens van de faseringsgegevensopslag gekopieerd naar de sinkgegevensopslag. Azure Data Factory beheert automatisch de twee fase stroom voor u. Azure Data Factory verwijdert ook tijdelijke gegevens uit de staging-opslag nadat de gegevens verplaatsing is voltooid.

![Gefaseerd kopiëren](media/copy-activity-performance/staged-copy.png)

Wanneer u gegevens verplaatsing activeert met behulp van een faserings opslag, kunt u opgeven of u wilt dat de gegevens worden gecomprimeerd voordat u gegevens verplaatst van de brongegevens opslag naar een tussentijds-of faserings gegevens archief en vervolgens wordt gedecomprimeerd voordat u gegevens verplaatst van een tussentijds of fasering dat een archief naar de Sink-gegevens opslag.

Op dit moment kunt u geen gegevens kopiëren tussen twee gegevens archieven die zijn verbonden via een andere zelf-hostende IRs, noch zonder een gefaseerde kopie. Voor dit scenario kunt u twee expliciet gekoppelde Kopieer activiteiten configureren om te kopiëren van bron naar fase ring en vervolgens van fase ring naar sink.

#### <a name="configuration"></a>Configuratie

Configureer de instelling **enableStaging** in de Kopieer activiteit om op te geven of u wilt dat de gegevens in Blob Storage worden klaargezet voordat u deze in een doel gegevens archief laadt. Wanneer u **enableStaging** instelt op `TRUE`, geeft u de aanvullende eigenschappen op die in de volgende tabel worden weer gegeven. U moet ook een gekoppelde service voor het maken van een hand tekening voor gedeelde toegang met een opslag locatie voor Azure Storage of Storage gebruiken als u er geen hebt.

| Eigenschap | Beschrijving | Standaardwaarde | Verplicht |
| --- | --- | --- | --- |
| enableStaging |Geef op of u wilt het kopiëren van gegevens via een tussentijdse opslag staging. |Onwaar |Nee |
| linkedServiceName |Geef de naam van een [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) gekoppelde service, die verwijst naar het exemplaar van de opslag die u als een tussentijdse faseringsopslag. <br/><br/> U kunt opslag niet gebruiken met een Shared Access Signature om gegevens te laden in SQL Data Warehouse via Poly base. U kunt deze gebruiken in alle andere scenario's. |N/A |Ja, wanneer **enableStaging** is ingesteld op TRUE |
| Pad |Geef het pad op Blob-opslag die u wilt de voorbereide gegevens bevatten. Als u geen pad opgeeft, maakt de service een container om tijdelijke gegevens op te slaan. <br/><br/> Geef een pad alleen als u Storage met een shared access signature gebruiken of u tijdelijke gegevens op een specifieke locatie te vereisen. |N/A |Nee |
| enableCompression |Hiermee geeft u op of gegevens moeten worden gecomprimeerd voordat ze naar het doel worden gekopieerd. Deze instelling beperkt de hoeveelheid gegevens die worden overgebracht. |Onwaar |Nee |

>[!NOTE]
> Als u een gefaseerde kopie gebruikt terwijl compressie is ingeschakeld, wordt de service-principal of MSI-verificatie voor de gekoppelde BLOB-hostservice niet ondersteund.

Hier volgt een voor beeld van een Kopieer activiteit met de eigenschappen die in de voor gaande tabel worden beschreven:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

#### <a name="staged-copy-billing-impact"></a>Gefaseerd kopiëren facturering impact

Er worden kosten in rekening gebracht op basis van twee stappen: de duur en het Kopieer type kopiëren.

* Wanneer u fase ring gebruikt tijdens een Cloud kopie, die gegevens uit een gegevens archief in de cloud kopieert naar een ander gegevens archief in de Cloud, wordt de [som van de Kopieer duur voor stap 1 en stap 2] x [eenheids prijs voor de Cloud kopie] in rekening gebracht.
* Wanneer u fase ring gebruikt tijdens een hybride kopie, waarmee gegevens worden gekopieerd van een on-premises gegevens opslag naar een gegevens archief in de Cloud, wordt één fase van een zelf-hostende Integration runtime in rekening gebracht voor [Hybrid Copy duration] x [hybride kopie van eenheids prijs] + [duur van de Cloud kopiëren] x [prijs per eenheid voor Cloud Copy].

## <a name="references"></a>Naslaginformatie

Hier volgen de prestaties en het afstemmen van verwijzingen voor een aantal ondersteunde gegevens archieven:

* Azure Blob-opslag: [schaal baarheid en prestatie doelen voor Blob Storage](../storage/blobs/scalability-targets.md) en de [controle lijst voor prestaties en schaal baarheid voor Blob Storage](../storage/blobs/storage-performance-checklist.md).
* Azure-tabel opslag: [schaal baarheid en prestatie doelen voor](../storage/tables/scalability-targets.md) de [controle lijst voor tabel opslag en prestaties en schaal baarheid voor tabel opslag](../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: u kunt [de prestaties bewaken](../sql-database/sql-database-single-database-monitor.md) en het DTU-percentage (data base Trans Action Unit) controleren.
* Azure SQL Data Warehouse: de mogelijkheid ervan wordt gemeten in data warehouse units (Dwu's). Zie [reken kracht beheren in Azure SQL Data Warehouse (overzicht)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [prestatie niveaus in azure Cosmos DB](../cosmos-db/performance-levels.md).
* On-premises SQL Server: [prestaties bewaken en afstemmen](https://msdn.microsoft.com/library/ms189081.aspx).
* On-premises Bestands server: [prestaties afstemmen voor bestands servers](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen over Kopieer activiteiten:

- [Overzicht kopieeractiviteit](copy-activity-overview.md)
- [Azure Data Factory gebruiken om gegevens van uw data Lake of Data Warehouse te migreren naar Azure](data-migration-guidance-overview.md)
- [Gegevens migreren van Amazon S3 naar Azure Storage](data-migration-guidance-s3-azure-storage.md)
