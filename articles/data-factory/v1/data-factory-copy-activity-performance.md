---
title: Kopiëren en afstemmingshandleiding van activiteit
description: Meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van de verplaatsing van gegevens in Azure Data Factory wanneer u een Kopieeractiviteit gebruiken.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9ca44b1917cfaed5d01c31f8f06d98e5e4b611a8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438922"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Kopiëren en afstemmingshandleiding van activiteit

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](data-factory-copy-activity-performance.md)
> * [Versie 2 (huidige versie)](../copy-activity-performance.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [hand leiding Copy activity Performance and Tuning for Data Factory](../copy-activity-performance.md).

Copy Activity in Azure Data Factory biedt een eersteklas veilige, betrouwbare en hoogwaardige gegevens laden. Dit kunt u gegevens kunt kopiëren tientallen van terabytes aan gegevens elke dag in uitgebreide tal van cloud en het on-premises gegevensarchieven. Zeer snel gegevens laden van prestaties is erg belangrijk om te controleren of u zich kunt richten op het probleem van de 'big data' core: het bouwen van geavanceerde analyseoplossingen en diepe inzichten ophalen uit alle gegevens.

Azure biedt een set geavanceerde datawarehouseoplossingen voor opslag en gegevensbeheer en Copy Activity biedt een uiterst geoptimaliseerde gegevens laden die gemakkelijk te configureren en instellen. Met slechts één kopieeractiviteit, kunt u bereiken:

* Het laden van gegevens in **Azure SQL Data Warehouse** op **1,2 GBps**. Zie voor een overzicht met een use case [1 TB laden in Azure SQL Data Warehouse minder dan 15 minuten met Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Het laden van gegevens in **Azure Blob-opslag** op **1,0 GBps**
* Het laden van gegevens in **Azure Data Lake Store** op **1,0 GBps**

Dit artikel wordt beschreven:

* [Naslaginformatie over prestatiecijfers](#performance-reference) voor ondersteunde bron- en sink-gegevensopslag voor het plannen van uw project.
* Functies waarmee de Kopieer doorvoer kan worden verhoogd in verschillende scenario's, waaronder [gegevens verplaatsings eenheden](#cloud-data-movement-units)voor de Cloud, [parallelle kopie](#parallel-copy)en [gefaseerde kopie](#staged-copy);
* [Richtlijnen voor afstemmen van prestaties](#performance-tuning-steps) over het afstemmen van de prestaties en de belangrijkste factoren die de kopieerprestaties kunnen beïnvloeden.

> [!NOTE]
> Als u niet bekend bent met de Kopieer activiteit in het algemeen, raadpleegt u [gegevens verplaatsen met behulp van Kopieer activiteit voordat u](data-factory-data-movement-activities.md) dit artikel leest.
>

## <a name="performance-reference"></a>Naslaginformatie over prestaties

Als referentie wordt onder tabel het Kopieer doorvoer nummer in MBps weer gegeven voor de opgegeven bron-en Sink-paren op basis van interne tests. Ter vergelijking ziet u ook hoe verschillende instellingen van de [verplaatsings eenheden voor Cloud gegevens](#cloud-data-movement-units) of [Data Management Gateway schaal baarheid](data-factory-data-management-gateway-high-availability-scalability.md) (meerdere gateway knooppunten) kunnen helpen bij het kopiëren van prestaties.

![Matrix van prestaties](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>In Azure Data Factory versie 1 zijn de minimale eenheden voor het verplaatsen van de Cloud gegevens voor Cloud-naar-Cloud kopie twee. Als u dit niet opgeeft, ziet u standaard eenheden voor gegevens verplaatsing die worden gebruikt in [eenheden voor gegevens verplaatsing](#cloud-data-movement-units)in de Cloud.

**Punten om te noteren:**
* Doorvoer wordt berekend met behulp van de volgende formule: [grootte van de gegevens lezen uit bron] / [Copy Activity uitvoeringsduur].
* De prestatie referentie nummers in de tabel zijn gemeten met behulp van de [TPC-H-](http://www.tpc.org/tpch/) gegevensset in één exemplaar van de Kopieer activiteit.
* In de Azure-gegevensopslag zijn de bron en sink in dezelfde Azure-regio.
* Voor hybride kopieën tussen on-premises en gegevens opslag in de Cloud, werd elk gateway knooppunt uitgevoerd op een computer die is gescheiden van de on-premises gegevens opslag met onderstaande specificatie. Als er één activiteit op de gateway werd uitgevoerd, gebruikt de Kopieer bewerking slechts een klein deel van de CPU, het geheugen of de netwerk bandbreedte van de test machine. Meer informatie vindt u in [overweging voor Data Management Gateway](#considerations-for-data-management-gateway).
    <table>
    <tr>
        <td>CPU</td>
        <td>32 kernen 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Geheugen</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Netwerk</td>
        <td>Internet-interface: 10 Gbps; intranet-interface: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> U kunt een hogere door Voer gebruiken door gebruik te maken van meer DMUs (Data units) dan de standaard maximale DMUs. Dit is 32 voor het uitvoeren van een Cloud-naar-Cloud-Kopieer activiteit. Met 100 DMUs kunt u bijvoorbeeld gegevens van Azure-Blob kopiëren naar Azure Data Lake Store op **1.0 Gbps**. Zie de sectie [eenheden voor gegevens verplaatsing](#cloud-data-movement-units) in de Cloud voor meer informatie over deze functie en het Ondersteunde scenario. Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/) om meer DMUs aan te vragen.

## <a name="parallel-copy"></a>Parallelle kopie
U kunt gegevens uit de bron lezen of gegevens naar het doel schrijven, **parallel binnen het uitvoeren van een Kopieer activiteit**. Deze functie verbetert de door Voer van een Kopieer bewerking en vermindert de tijd die nodig is om gegevens te verplaatsen.

Deze instelling wijkt af van de **gelijktijdigheids** eigenschap in de definitie van de activiteit. De **gelijktijdigheids** eigenschap bepaalt het aantal **uitvoeringen van de gelijktijdige Kopieer activiteit** voor het verwerken van gegevens uit verschillende activiteiten Vensters (1 tot 2 uur, 2 uur tot 3 uur, 3 tot 4 uur, enzovoort). Deze functie is handig wanneer u een historische belasting uitvoert. De functionaliteit voor parallel kopiëren is van toepassing op **één uitvoering**van de activiteit.

Laten we eens kijken naar een voorbeeld scenario. In het volgende voor beeld moeten meerdere segmenten van het verleden worden verwerkt. Data Factory voert een exemplaar van de Kopieer activiteit (een uitvoering van de activiteit) uit voor elk segment:

* Het gegevens segment van het eerste activiteiten venster (1 uur tot 2 uur) = = > activiteit uitvoeren 1
* Het gegevens segment van het tweede activiteiten venster (2 tot 3 uur) = = > activiteit 2
* Het gegevens segment van het tweede activiteiten venster (3 tot 4 uur) = = > activiteit uitvoeren 3

Enzovoort.

In dit voor beeld, wanneer de **gelijktijdigheids** waarde is ingesteld op 2, **activiteit uitvoeren 1** en **activiteit 2** gegevens gelijktijdig kopiëren van twee activiteits **Vensters,** om de prestaties van de gegevens verplaatsing te verbeteren. Als er echter meerdere bestanden zijn gekoppeld aan de uitvoering van de activiteit 1, kopieert de gegevens verplaatsings service bestanden van de bron naar de bestemming één bestand per keer.

### <a name="cloud-data-movement-units"></a>Eenheden voor gegevens verplaatsing in de Cloud
Een **DMU (Cloud gegevens verplaatsings eenheid)** is een meting die de kracht vertegenwoordigt (een combi natie van CPU, geheugen en netwerk bron toewijzing) van één eenheid in Data Factory. DMU is van toepassing op Kopieer bewerkingen in de Cloud, maar niet in een hybride kopie.

**De minimale eenheden voor gegevens verplaatsing in de cloud om de uitvoering van de Kopieer activiteit te stimuleren, is twee.** Als dat niet is opgegeven, wordt in de volgende tabel de standaard DMUs weer gegeven die worden gebruikt in verschillende Kopieer scenario's:

| Scenario kopiëren | Standaard DMUs bepaald door service |
|:--- |:--- |
| Gegevens kopiëren tussen winkels op basis van bestanden | Tussen 4 en 16, afhankelijk van het aantal en de grootte van de bestanden. |
| Alle andere kopieerscenario 's | 4 |

Als u deze standaard instelling wilt overschrijven, geeft u als volgt een waarde op voor de eigenschap **cloudDataMovementUnits** . De **toegestane waarden** voor de eigenschap **cloudDataMovementUnits** zijn 2, 4, 8, 16, 32. Het **werkelijke aantal Cloud DMUs** dat de Kopieer bewerking wordt gebruikt tijdens runtime, is gelijk aan of kleiner dan de geconfigureerde waarde, afhankelijk van het gegevens patroon. Zie voor informatie over het niveau van prestatieverbetering krijgt u mogelijk bij het configureren van meer eenheden voor een specifiek exemplaar gegevensbronnen en sinks, de [prestaties verwijzing](#performance-reference).

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

> [!NOTE]
> Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/)als u meer Cloud DMUs nodig hebt voor een hogere door voer. De instelling van 8 en hoger werkt momenteel alleen wanneer u **meerdere bestanden kopieert van Blob Storage/Data Lake Store/Amazon S3/Cloud FTP/Cloud SFTP naar Blob Storage/Data Lake Store/Azure SQL database**.
>

### <a name="parallelcopies"></a>parallelCopies
U kunt de **parallelCopies** eigenschap om aan te geven van de parallelle uitvoering die u wilt dat de Kopieeractiviteit om te gebruiken. U kunt deze eigenschap beschouwen als het maximum aantal threads in een Kopieeractiviteit waarmee u kunt vanuit de bron lezen of schrijven naar de sinkgegevensopslag parallel.

Data Factory bepaalt voor elke kopie-activiteit die wordt uitgevoerd, het aantal parallelle exemplaren te gebruiken om gegevens te kopiëren van de bron-gegevens opslaan en op te slaan op de doelgegevens. Het standaard aantal parallelle kopieën dat wordt gebruikt, is afhankelijk van het type bron en Sink dat u gebruikt.

| Bron en Sink | Standaard parallelle kopie aantal bepaald door de service |
| --- | --- |
| Gegevens kopiëren tussen archieven op basis van bestanden (Blob Storage; Data Lake Store; Amazon S3; een on-premises bestands systeem; een on-premises HDFS) |Tussen 1 en 32. Is afhankelijk van de grootte van de bestanden en het aantal DMUs (Cloud data units) dat wordt gebruikt voor het kopiëren van gegevens tussen twee gegevens archieven in de Cloud of de fysieke configuratie van de gateway computer die wordt gebruikt voor een hybride kopie (om gegevens naar of van een on-premises gegevens opslag te kopiëren). |
| Gegevens uit **een brongegevens archief kopiëren naar Azure-tabel opslag** |4 |
| Alle andere bron-en Sink-paren |1 |

Normaal gesp roken moet het standaard gedrag u de beste door voer geven. Als u echter de belasting wilt beheren op machines die uw gegevens archieven hosten, of als u de Kopieer prestaties wilt afstemmen, kunt u ervoor kiezen de standaard waarde te overschrijven en een waarde voor de eigenschap **parallelCopies** op te geven. De waarde moet tussen 1 en 32 (inclusief) zijn. Tijdens de uitvoering voor de beste prestaties Copy-activiteit maakt gebruik van een waarde die kleiner is dan of gelijk zijn aan de waarde die u hebt ingesteld.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
Die u moet weten:

* Als u gegevens tussen bestanden zijn gebaseerd, kopieert de **parallelCopies** bepalen de parallelle uitvoering op bestandsniveau. De logische groepen te verdelen binnen een enkel bestand onder er zou gebeuren automatisch en transparant en is ontworpen om de beste geschikt chunkgrootte voor een bepaalde bron-gegevenstype store gebruiken om gegevens in parallelle en rechthoekige naar parallelCopies te laden. Het werkelijke aantal parallelle exemplaren data movement service wordt gebruikt voor de kopieerbewerking tijdens runtime is niet meer dan het aantal bestanden dat u hebt. Als flattenhierarchy is **mergeFile**, Kopieeractiviteit niet profiteren van parallelle uitvoering op bestandsniveau.
* Wanneer u een waarde voor de eigenschap **parallelCopies** opgeeft, moet u rekening houden met de toename van de belasting van uw bron-en Sink-gegevens opslag, en naar de gateway als dit een hybride kopie is. Dit gebeurt met name wanneer u hebt meerdere activiteiten of gelijktijdige uitvoeringen van de dezelfde activiteiten worden uitgevoerd voor hetzelfde gegevensarchief. Als u merkt dat het gegevens archief of de gateway is overbelast met de belasting, verlaagt u de **parallelCopies** -waarde om de belasting te ontlasten.
* Wanneer u gegevens van winkels die niet op bestanden zijn gebaseerd op winkels die op basis van bestanden kopieert, data movement service negeert de **parallelCopies** eigenschap. Zelfs als parallelle uitvoering is opgegeven, wordt deze niet toegepast in dit geval.

> [!NOTE]
> U moet Data Management Gateway versie 1,11 of hoger gebruiken om de functie **parallelCopies** te gebruiken wanneer u een hybride kopie gaat maken.
>
>

Als u deze twee eigenschappen beter wilt gebruiken en de door Voer van gegevens verplaatsing wilt uitbreiden, raadpleegt u de voor beelden van use cases. U hoeft **parallelCopies** niet te configureren om te profiteren van het standaard gedrag. Als u configureert en **parallelCopies** te klein is, is het mogelijk dat meerdere Cloud-DMUs niet volledig worden gebruikt.

### <a name="billing-impact"></a>Facturerings impact
Er **belangrijk** te onthouden dat de kosten worden berekend op basis van de totale tijd van de kopieerbewerking. Als een Kopieer taak wordt gebruikt om één uur te maken met één Cloud eenheid en nu 15 minuten duurt met vier Cloud eenheden, blijft de volledige factuur bijna hetzelfde. U kunt bijvoorbeeld vier Cloud eenheden gebruiken. De eerste cloud eenheid besteedt tien minuten, de tweede, 10 minuten, de derde, 5 minuten en de vierde, 5 minuten, alle in één Kopieer activiteit uit. Er worden kosten in rekening gebracht voor de totale Kopieer tijd (gegevens verplaatsing), die 10 + 10 + 5 + 5 = 30 minuten is. Het gebruik van **parallelCopies** heeft geen invloed op facturering.

## <a name="staged-copy"></a>Gefaseerd kopiëren
Wanneer u gegevens van een brongegevensarchief naar een sink-gegevensarchief kopieert, kunt u Blob-opslag gebruiken als een tussentijdse faseringsopslag. Fasering is vooral nuttig in de volgende gevallen:

1. **U wilt opnemen van gegevens uit verschillende gegevensarchieven in SQL Data Warehouse met PolyBase**. SQL Data Warehouse gebruikmaakt van een grote hoeveelheid gegevens laden in SQL Data Warehouse PolyBase als een mechanisme voor hoge doorvoer. De bron gegevens moeten echter in Blob Storage zijn en moeten aan aanvullende criteria voldoen. Wanneer u gegevens laadt vanuit een ander gegevens archief dan Blob-opslag, kunt u het kopiëren van gegevens met behulp van tussenliggende staging-Blobopslag activeren. In dat geval voert de Data Factory de benodigde gegevenstransformaties om ervoor te zorgen dat het voldoet aan de vereisten van PolyBase. Vervolgens deze gebruikmaakt van PolyBase om gegevens te laden in SQL Data Warehouse. Zie [poly Base gebruiken om gegevens te laden in Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)voor meer informatie. Zie voor een overzicht met een use case [1 TB laden in Azure SQL Data Warehouse minder dan 15 minuten met Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Soms duurt het even om een hybride gegevens verplaatsing uit te voeren (dat wil zeggen, kopiëren tussen een on-premises gegevens opslag en een gegevens archief in de Cloud) via een trage netwerk verbinding**. U kunt de prestaties verbeteren door de gegevens on-premises te comprimeren, zodat het minder tijd kost om gegevens te verplaatsen naar de faserings gegevens opslag in de Cloud. Vervolgens kunt u de gegevens in het faserings archief decomprimeren voordat u deze laadt in de doel gegevens opslag.
3. **U niet wilt openen van poorten dan poort 80 en poort 443 in uw firewall vanwege bedrijfsbeleid IT**. Wanneer u gegevens van een on-premises gegevensarchief naar een Azure SQL Database-sink of een Azure SQL Data Warehouse-sink kopiëren, moet u bijvoorbeeld uitgaande TCP-communicatie op poort 1433 voor zowel de Windows firewall en firewall van uw bedrijf te activeren. In dit scenario maakt u gebruik van de gateway om eerst gegevens te kopiëren naar een staging-opslag kopie van een Blob Storage via HTTP of HTTPS op poort 443. Laad vervolgens de gegevens in SQL Database of SQL Data Warehouse vanuit het faseren van de Blob-opslag. In deze stroom moet u geen poort 1433 in te schakelen.

### <a name="how-staged-copy-works"></a>Hoe gefaseerd kopiëren werkt
Wanneer u de faserings functie activeert, worden de gegevens eerst van de brongegevens opslag gekopieerd naar de staging-gegevens opslag (neem uw eigen op). Vervolgens is de gegevens van de faseringsgegevensopslag gekopieerd naar de sinkgegevensopslag. Data Factory wordt automatisch de gegevensstroom twee fasen voor u beheerd. Tijdelijke gegevens uit de opslag staging ruimt Data Factory ook nadat de verplaatsing van gegevens voltooid is.

In het scenario voor het kopiëren van de Cloud (zowel de bron-als de Sink-gegevens opslag bevinden zich in de Cloud), wordt de gateway niet gebruikt. De Data Factory-service voert de Kopieer bewerkingen uit.

![Gefaseerde kopie: Cloud scenario](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

In het scenario voor hybride kopieën (bron is on-premises en Sink bevindt zich in de Cloud), verplaatst de gateway gegevens van het brongegevens archief naar een staging-gegevens opslag. Data Factory-service verplaatst gegevens van de staging-gegevens opslag naar de Sink-gegevens opslag. Het kopiëren van gegevens uit een gegevens archief in de Cloud naar een on-premises gegevens opslag via staging wordt ook ondersteund met de omgekeerde stroom.

![Gefaseerde kopie: hybride scenario](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Wanneer u verplaatsing van gegevens met behulp van een faseringsopslag activeert, kunt u opgeven of u wilt dat de gegevens worden gecomprimeerd voordat het verplaatsen van gegevens uit de brongegevensopslag naar een tijdelijke of tijdelijke gegevensopslag en vervolgens voor het verplaatsen van gegevens vanuit een tussentijdse of faseert gegevens gedecomprimeerd opslaan naar de sinkgegevensopslag.

U kunt gegevens tussen twee on-premises gegevensarchieven met behulp van een faseringsopslag op dit moment niet kopiëren. Deze optie is binnenkort beschikbaar.

### <a name="configuration"></a>Configuratie
Configureer de **enableStaging** instellen in de Kopieeractiviteit om op te geven of u wilt dat de gegevens naar de tijdelijk opgeslagen in Blob-opslag voordat u deze te in een doelgegevensarchief laden. Wanneer u **enableStaging** INSTELT op True, geeft u de aanvullende eigenschappen op die in de volgende tabel worden weer gegeven. Als u niet hebt, moet u ook om te maken van een Azure Storage of opslag shared access signature-gekoppelde service voor fasering.

| Eigenschap | Beschrijving | Standaardwaarde | Verplicht |
| --- | --- | --- | --- |
| **enableStaging** |Geef op of u wilt het kopiëren van gegevens via een tussentijdse opslag staging. |Onwaar |Nee |
| **linkedServiceName** |Geef de naam op van een gekoppelde [opslag](data-factory-azure-blob-connector.md#azure-storage-linked-service) -of [azurestoragesas zijn](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) -service, die verwijst naar het exemplaar van de opslag die u gebruikt als een tijdelijke faserings opslag. <br/><br/> U kunt opslag met een shared access signature niet gebruiken om gegevens te laden in SQL Data Warehouse met PolyBase. U kunt deze gebruiken in alle andere scenario's. |N/A |Ja, wanneer **enableStaging** is ingesteld op TRUE |
| **path** |Geef het pad op Blob-opslag die u wilt de voorbereide gegevens bevatten. Als u een pad opgeeft, wordt een container voor het opslaan van tijdelijke gegevens gemaakt in de service. <br/><br/> Geef een pad alleen als u Storage met een shared access signature gebruiken of u tijdelijke gegevens op een specifieke locatie te vereisen. |N/A |Nee |
| **enableCompression** |Hiermee geeft u op of gegevens moeten worden gecomprimeerd voordat deze wordt gekopieerd naar de bestemming. Deze instelling beperkt de hoeveelheid gegevens die worden overgebracht. |Onwaar |Nee |

Hier volgt een voorbeelddefinitie van Kopieeractiviteit met de eigenschappen die worden beschreven in de voorgaande tabel:

```json
"activities":[
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>Facturerings impact
De kosten worden berekend op basis van twee stappen: duur van de kopieerbewerking en type kopiëren.

* Wanneer u fase ring gebruikt tijdens een Cloud kopie (gegevens uit een gegevens archief in de Cloud kopiëren naar een ander gegevens archief in de Cloud), wordt de [som van de Kopieer duur voor stap 1 en stap 2] x [eenheids prijs van de Cloud] in rekening gebracht.
* Wanneer u fase ring gebruikt tijdens een hybride kopie (het kopiëren van gegevens van een on-premises gegevens archief naar een gegevens archief in de Cloud), worden er kosten in rekening gebracht voor [Hybrid Copy duration] x [hybride kopie van de eenheids prijs] + [duur van de Cloud kopie] x [eenheids prijs voor de Cloud].

## <a name="performance-tuning-steps"></a>Prestaties afstemmen stappen
Het is raadzaam dat u deze stappen voor het afstemmen van de prestaties van uw Data Factory-service met de Kopieeractiviteit uitvoeren:

1. **Basislijn van**. Test uw pijplijn met behulp van de Kopieeractiviteit op basis van een representatieve steekproef tijdens de ontwikkelingsfase. U kunt het Data Factory [segment model](data-factory-scheduling-and-execution.md) gebruiken om de hoeveelheid gegevens te beperken waarmee u samenwerkt.

   De uitvoerings tijd en prestatie kenmerken verzamelen met behulp van de **app voor bewaking en beheer**. Kies **controleren & beheren** op de start pagina van Data Factory. Kies de **uitvoer gegevensset**in de structuur weergave. Kies in de lijst **activiteit Windows** de uitvoering van de Kopieer activiteit. **Activiteit Windows** vermeldt de duur van de Kopieer activiteit en de grootte van de gekopieerde gegevens. De door Voer wordt weer gegeven in het **activiteiten venster Verkenner**. Zie [Azure Data Factory pijp lijnen bewaken en beheren met behulp van de app voor bewaking en beheer](data-factory-monitor-manage-app.md)voor meer informatie over de app.

   ![Details uitvoering van activiteit](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Verderop in dit artikel kunt u de prestaties en configuratie van uw scenario vergelijken om de [prestatie referentie](#performance-reference) van de activiteit van onze tests te kopiëren.
2. **Diagnose en prestaties te optimaliseren**. Als de prestaties die u ziet dat niet voldoet aan uw verwachtingen, moet u prestatieknelpunten. Vervolgens prestaties als u wilt verwijderen of het effect van knelpunten te optimaliseren. Een volledige beschrijving van de prestatiediagnose van valt buiten het bereik van dit artikel, maar hier volgen enkele algemene overwegingen:

   * Prestatiefuncties:
     * [Parallelle kopiëren](#parallel-copy)
     * [Eenheden voor gegevens verplaatsing in de Cloud](#cloud-data-movement-units)
     * [Gefaseerd kopiëren](#staged-copy)
     * [Schaal baarheid Data Management Gateway](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Gegevensbeheergateway](#considerations-for-data-management-gateway)
   * [Bron](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Serialisatie en deserialisatie](#considerations-for-serialization-and-deserialization)
   * [Compressie](#considerations-for-compression)
   * [Toewijzen van kolommen](#considerations-for-column-mapping)
   * [Andere overwegingen](#other-considerations)
3. **De configuratie van de uitbreiden naar uw volledige gegevensset**. Wanneer u tevreden bent met de resultaten en prestaties van de uitvoering, kunt u de actieve periode van de definitie en pijp lijn uitvouwen om uw hele gegevensset te behandelen.

## <a name="considerations-for-data-management-gateway"></a>Overwegingen voor Data Management Gateway
**Gateway-installatie**: we raden u aan om een toegewezen computer te gebruiken om data management gateway te hosten. Zie [overwegingen voor het gebruik van Data Management Gateway](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Gateway bewaking en-omhoog/uitschalen**: Eén logische gateway met een of meer gateway knooppunten kan tegelijkertijd meerdere exemplaren van de Kopieer activiteit uitvoeren. U kunt bijna realtime moment opnamen van resource gebruik (CPU, geheugen, netwerk (in/uit), enzovoort) weer geven op een gateway computer, evenals het aantal gelijktijdige taken dat tegenover de limiet in het Azure Portal wordt uitgevoerd, Zie [gateway in de portal controleren](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Als u veel behoefte hebt aan hybride gegevens verplaatsing, met een groot aantal gelijktijdig uitgevoerde Kopieer activiteiten of met grote hoeveel heden gegevens die moeten worden gekopieerd, kunt u overwegen om de [gateway te verg Roten of te schalen](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) , zodat u uw resource beter kunt gebruiken of meer resources kunt inrichten.

## <a name="considerations-for-the-source"></a>Overwegingen voor de bron
### <a name="general"></a>Algemeen
Zorg ervoor dat het onderliggende gegevensarchief niet wordt overbelast door andere werkbelastingen die worden uitgevoerd op of tegen.

Zie voor Microsoft-gegevensarchieven, [bewaking en afstemming van onderwerpen](#performance-reference) die specifiek zijn voor gegevensarchieven en geven u inzicht in gegevens van de prestatiekenmerken opslaan, responstijden te minimaliseren en doorvoer te maximaliseren.

Als u gegevens kopieert van Blob-opslag naar SQL Data Warehouse, kunt u overwegen **poly base** te gebruiken om de prestaties te verbeteren. Zie [gebruik PolyBase om gegevens te laden in Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) voor meer informatie. Zie voor een overzicht met een use case [1 TB laden in Azure SQL Data Warehouse minder dan 15 minuten met Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Opgeslagen gegevens op basis van bestanden
*(Inclusief Blob Storage, Data Lake Store, Amazon S3, on-premises bestands systemen en on-premises HDFS)*

* **Gemiddelde van de bestandsgrootte en het aantal bestanden**: Kopieeractiviteit brengt gegevens één bestand tegelijk. Met de dezelfde hoeveelheid gegevens worden verplaatst, wordt de algehele doorvoer lager als de gegevens van veel kleine bestanden in plaats van enkele grote bestanden vanwege de bootstrap-fase voor elk bestand bestaat. Indien mogelijk, dus kleine bestanden combineren in grotere bestanden om te krijgen hogere doorvoer.
* **Bestand-indeling en compressie**: Zie voor meer manieren om prestaties te verbeteren, de [aandachtspunten voor serialisatie en deserialisatie](#considerations-for-serialization-and-deserialization) en [overwegingen voor compressie](#considerations-for-compression) secties.
* Zie de sectie [overwegingen voor Data Management Gateway](#considerations-for-data-management-gateway) voor het scenario voor een **on-premises bestands systeem** , waarin **Data Management Gateway** is vereist.

### <a name="relational-data-stores"></a>Op opgeslagen relationele gegevens
*(Bevat SQL Database; SQL Data Warehouse; Amazon RedShift; SQL Server data bases; en Oracle-, MySQL-, DB2-, Teradata-, Sybase-en PostgreSQL-data bases, enzovoort.)*

* **Een gegevenspatroon**: uw tabelschema is van invloed op de doorvoer van de kopie. Een grote rijgrootte biedt u een betere prestaties dan kleine rijgrootte, om te kopiëren van de dezelfde hoeveelheid gegevens. De reden is dat de database minder batches van gegevens die minder rijen bevatten efficiënter kunt ophalen.
* **Query of een opgeslagen procedure**: de logica van de query of een opgeslagen procedure die u in de bron van Kopieeractiviteit opgeeft voor het ophalen van gegevens efficiënter te optimaliseren.
* Voor **on-premises relationele data bases**, zoals SQL Server en Oracle, waarvoor het gebruik van **Data Management Gateway**is vereist, raadpleegt u de sectie overwegingen voor Data Management Gateway.

## <a name="considerations-for-the-sink"></a>Overwegingen voor de sink
### <a name="general"></a>Algemeen
Zorg ervoor dat het onderliggende gegevensarchief niet wordt overbelast door andere werkbelastingen die worden uitgevoerd op of tegen.

Raadpleeg voor Microsoft-gegevensarchieven, [bewaking en afstemming van onderwerpen](#performance-reference) die specifiek zijn voor gegevensopslag. Deze onderwerpen vindt u informatie over data store prestatiekenmerken en hoe u responstijden te minimaliseren en doorvoer te maximaliseren.

Als u gegevens kopieert van **Blob-opslag** naar **SQL Data Warehouse**, kunt u overwegen **poly base** te gebruiken om de prestaties te verbeteren. Zie [gebruik PolyBase om gegevens te laden in Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) voor meer informatie. Zie voor een overzicht met een use case [1 TB laden in Azure SQL Data Warehouse minder dan 15 minuten met Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Opgeslagen gegevens op basis van bestanden
*(Inclusief Blob Storage, Data Lake Store, Amazon S3, on-premises bestands systemen en on-premises HDFS)*

* **Kopieer gedrag**: als u gegevens van een ander bestand op basis van gegevensarchief kopieert, Copy-activiteit heeft drie opties via de **copyBehavior** eigenschap. Deze hiërarchie behoudt, hiërarchie worden samengevoegd of bestanden worden samengevoegd. Hiërarchie relatiegegevens of behouden heeft weinig of geen performance overhead, maar samenvoegen van bestanden zorgt ervoor dat de prestatieoverhead te verhogen.
* **Bestand-indeling en compressie**: Zie de [aandachtspunten voor serialisatie en deserialisatie](#considerations-for-serialization-and-deserialization) en [overwegingen voor compressie](#considerations-for-compression) secties voor meer manieren om prestaties te verbeteren.
* **Blob-opslag**: momenteel ondersteunt Blob Storage alleen blok-blobs voor geoptimaliseerde gegevens overdracht en door voer.
* Zie de sectie [overwegingen voor Data Management Gateway](#considerations-for-data-management-gateway) voor **on-premises scenario's voor bestands systemen** waarvoor het gebruik van **Data Management Gateway**is vereist.

### <a name="relational-data-stores"></a>Op opgeslagen relationele gegevens
*(Inclusief SQL Database, SQL Data Warehouse, SQL Server data bases en Oracle-data bases)*

* **Kopieer gedrag**: afhankelijk van de eigenschappen die u hebt ingesteld voor **sqlSink**, Kopieeractiviteit schrijft gegevens naar de doeldatabase op verschillende manieren.
  * Standaard voegt de data movement service wordt gebruikt de bulksgewijs kopiëren-API om gegevens in te voegen modus, waarbij de beste prestaties biedt.
  * Als u een opgeslagen procedure in de sink is geconfigureerd, is de database de één rij met gegevens van toepassing op een tijdstip in plaats van als bulksgewijs laden. Prestaties aanzienlijk wordt geweigerd. Als uw gegevensset groot is, kunt u, indien van toepassing, overwegen om over te scha kelen naar het gebruik van de eigenschap **sqlWriterCleanupScript** .
  * Als u de eigenschap **sqlWriterCleanupScript** configureert voor elke uitvoering van de Kopieer activiteit, wordt het script door de service geactiveerd en vervolgens gebruikt u de API voor bulk kopieën om de gegevens in te voegen. Bijvoorbeeld, als u wilt overschrijven de hele tabel met de meest recente gegevens, kunt u een script voor het eerst alle records verwijderen voordat u de nieuwe gegevens uit de bron voor bulksgewijs laden.
* **Patroon en batch gegevensgrootte**:
  * Uw tabelschema is van invloed op de doorvoer van de kopie. Als u wilt kopiëren van de dezelfde hoeveelheid gegevens, biedt een grote rijgrootte betere prestaties dan een kleine rijgrootte omdat de database kan efficiënter minder batches van de gegevens worden doorgevoerd.
  * Kopieeractiviteit voegt de gegevens in een reeks van batches. U kunt het aantal rijen in een batch instellen met behulp van de **writeBatchSize** eigenschap. Als uw gegevens klein rijen is, kunt u instellen de **writeBatchSize** eigenschap met een hogere waarde om te profiteren van lagere batch overhead en hogere doorvoer. Als de rijgrootte van uw gegevens te groot is, Wees voorzichtig wanneer u verhogen **writeBatchSize**. Een hoge waarde kan leiden tot een mislukte kopieerbewerking veroorzaakt door overbelasting van de database.
* Zie de sectie [overwegingen voor Data Management Gateway](#considerations-for-data-management-gateway) voor **on-premises relationele data bases** , zoals SQL Server en Oracle, waarvoor het gebruik van **Data Management Gateway**is vereist.

### <a name="nosql-stores"></a>NoSQL-archieven
*(Bevat tabel opslag en Azure Cosmos DB)*

* Voor **Table storage**:
  * **Partitie**: gegevens schrijven naar een interleaved partities aanzienlijk vermindert de prestaties. De brongegevens op partitiesleutel sorteren, zodat de gegevens efficiënt in één partitie is ingevoegd achter elkaar, of aanpassen van de logica voor het schrijven van de gegevens naar één partitie.
* Voor **Azure Cosmos DB**:
  * **Batch grootte**: de eigenschap **writeBatchSize** stelt het aantal parallelle aanvragen voor de Azure Cosmos DB-service in om documenten te maken. U kunt betere prestaties verwachten wanneer u **writeBatchSize** verhoogt, omdat er meer parallelle aanvragen naar Azure Cosmos DB worden verzonden. U kunt echter wel controleren op beperking wanneer u naar Azure Cosmos DB schrijft (het fout bericht is ' de aanvraag snelheid is groot '). Verschillende factoren kunnen leiden tot een beperking, zoals de document grootte, het aantal voor waarden in de documenten en het indexerings beleid van de doel verzameling. Voor een hogere Kopieer doorvoer kunt u overwegen om een betere verzameling te gebruiken, bijvoorbeeld S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Overwegingen voor serialisatie en deserialisatie
Serialisatie en deserialisatie kunnen optreden wanneer uw gegevensset voor invoer of uitvoer gegevensset een bestand is. Zie [ondersteunde indelingen voor bestanden en compressie](data-factory-supported-file-and-compression-formats.md) met informatie over ondersteunde bestandsindelingen door de Kopieeractiviteit.

**Kopieer gedrag**:

* Kopiëren van bestanden tussen gegevensarchieven in op basis van bestanden:
  * Als invoer-en uitvoer gegevens sets dezelfde instellingen voor de bestands indeling hebben, voert de service voor gegevens verplaatsing een binaire kopie uit zonder serialisatie of deserialisatie. Ziet u een hogere doorvoer in vergelijking met het scenario, waarin de bron en sink-bestandsindelingsinstellingen zich van elkaar verschillen.
  * Als invoer en uitvoer gegevenssets beide zijn in de tekstindeling en alleen de codering type is anders, data movement service biedt alleen codering conversie. Doet het nog serialisatie en deserialisatie, waardoor overhead in vergelijking met een binaire kopie prestaties.
  * Brongegevens om te streamen, transformeren en deze vervolgens te serialiseren in de indeling van de uitvoer die u hebt aangegeven als invoer- en data-sets beide hebben verschillende bestandsindelingen of verschillende configuraties, zoals scheidingstekens, data movement service worden gedeserialiseerd. Deze bewerking resulteert in een veel belangrijker performance overhead in vergelijking met andere scenario's.
* Wanneer u kopieert bestanden naar/van een gegevensarchief dat niet op basis van bestanden (bijvoorbeeld in een store, op basis van bestanden naar een relationele archief), wordt de stap serialisatie of deserialisatie is vereist. Deze stap resulteert in van aanzienlijke prestatieoverhead.

**Bestandsindeling**: de bestandsindeling die u kiest voor het kan kopiëren prestaties beïnvloeden. Avro is bijvoorbeeld een compacte binaire indeling die metagegevens op met de gegevens slaat. Het biedt een brede ondersteuning in het Hadoop-ecosysteem voor het verwerken en uitvoeren van query's. Avro is echter duurder voor serialisatie en deserialisatie, wat in het onderste exemplaar doorvoer in vergelijking met de tekstindeling resulteert. Uw keuze van de bestandsindeling in de stroom voor de verwerking van zuinigste maken. Beginnen met welke vorm de gegevens worden opgeslagen in brongegevens of moet worden geëxtraheerd uit externe systemen; de beste indeling voor opslag, analytische verwerking en uitvoeren van query's; en in welke indeling moet de gegevens worden geëxporteerd naar de datamarts voor hulpprogramma's voor rapportage en visualisatie. Soms een bestandsindeling die is suboptimale voor lezen en schrijven prestaties mogelijk zijn een goede keuze wanneer u rekening houden met het algemene analytische proces.

## <a name="considerations-for-compression"></a>Overwegingen voor compressie
Als uw gegevensset voor invoer- of een bestand is, kunt u Copy Activity om uit te voeren van compressie of decompressie als gegevens naar de bestemming schrijft instellen. Wanneer u compressie kiest, maakt u een balans tussen input/output (I/O) en de CPU. Comprimeren van de kosten van de gegevens een extra in compute-resources. Maar hierna het i/o-netwerk en opslag verlaagt. Afhankelijk van uw gegevens ziet u een boost in de algemene doorvoer van de kopie.

**Codec**: Kopieer activiteit biedt ondersteuning voor gzip-, bzip2-en deflater compressie typen. Azure HDInsight kan alle drie de typen voor verwerking in beslag nemen. Elke compressie-codec heeft voor delen. Bijvoorbeeld, bzip2 heeft de laagste kopie doorvoer, maar u krijgt de beste prestaties van Hive-query met bzip2 omdat u deze voor verwerking opsplitsen kunt. Gzip is de meest met gelijke taakverdeling optie en deze het vaakst wordt gebruikt. Kies de codec die het beste bij uw end-to-end-scenario.

**Niveau**: U kunt kiezen uit twee opties voor elke compressiecodec: snelste gecomprimeerde en optimaal gecomprimeerd. De snelste gecomprimeerde optie worden de gegevens zo snel mogelijk, gecomprimeerd, zelfs als het resulterende bestand is niet optimaal gecomprimeerd. De optimaal gecomprimeerde optie meer tijd in de taakwachtrij doorbrengt op compressie en resulteert in een minimale hoeveelheid gegevens. U kunt beide opties om te zien waarmee u betere algehele prestaties in uw geval testen.

**Overweging**: als u een grote hoeveelheid gegevens wilt kopiëren tussen een on-premises opslag en de Cloud, kunt u overwegen om tussenliggende Blob-opslag te gebruiken met compressie. Tijdelijke opslag is nuttig wanneer de bandbreedte van uw bedrijfsnetwerk en uw Azure-services de beperkende factor is en u wilt dat de gegevensset voor invoer en uitvoer gegevensset beide in niet-gecomprimeerde vorm. Meer specifiek kunt u een enkele Kopieer activiteit in twee Kopieer activiteiten afsplitsen. De eerste Kopieer activiteit kopieert van de bron naar een tijdelijke of tijdelijke Blob in gecomprimeerde vorm. De tweede Kopieer activiteit kopieert de gecomprimeerde gegevens uit de fase ring en vervolgens gedecomprimeerd tijdens het schrijven naar de sink.

## <a name="considerations-for-column-mapping"></a>Overwegingen voor kolomtoewijzing
U kunt instellen dat de **columnMappings** eigenschap in de Kopieeractiviteit alle kaart of een subset van de invoerkolommen voor de uitvoerkolommen. Nadat de data movement service leest de gegevens van de bron, moet deze kolomtoewijzing uitvoeren voor de gegevens voordat deze de gegevens naar de sink schrijft. Deze extra verwerking vermindert kopie doorvoer.

Als uw bron-gegevensopslag waarin u kunt zoeken, bijvoorbeeld als het is een relationele opslagplaats, zoals SQL-Database of SQL Server, of als het is een NoSQL-archief, zoals Table storage of Azure Cosmos DB, kunt u de kolom filteren pushen en logica voor opnieuw ordenen de **query** eigenschap in plaats van in de kolomtoewijzing. Op deze manier de projectie treedt op wanneer data movement service gegevens uit de brongegevensopslag leest, waar het is veel efficiënter.

## <a name="other-considerations"></a>Andere overwegingen
Als de grootte van de gegevens die u wilt kopiëren groot is, kunt u uw bedrijfs logica aanpassen om de gegevens verder te partitioneren met behulp van het segmenteer mechanisme in Data Factory. Plan vervolgens de Kopieer activiteit om de gegevens grootte voor elke uitvoering van de Kopieer activiteit vaker te laten uitvoeren.

Wees voorzichtig met het aantal gegevens sets en kopieer activiteiten die tegelijk Data Factory naar de connector naar hetzelfde gegevens archief moeten worden gestuurd. Veel gelijktijdige kopie taken mogelijk beperken van een gegevensarchief en leiden tot verminderde prestaties, kopie taak interne nieuwe pogingen, en in sommige gevallen, fouten bij de uitvoering.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Voorbeeldscenario: kopiëren vanuit een on-premises SQL Server naar Blob-opslag
**Scenario**: een pijplijn is gebouwd om te kopiëren van gegevens uit een on-premises SQL Server naar Blob storage in CSV-indeling. Als u sneller de taak voor het kopiëren, moeten de CSV-bestanden worden gecomprimeerd naar bzip2-indeling.

**Test- en analyse**: de doorvoer van de Kopieeractiviteit is minder dan 2 MBps, dit is veel lager is dan de benchmark prestaties.

**Analyse van prestaties en afstemmen**: voor het oplossen van het prestatieprobleem, laten we kijken hoe de gegevens worden verwerkt en verplaatst.

1. **Gegevens lezen**: de gateway opent een verbinding met SQL Server en verzendt de query. SQL Server reageert door de gegevens stroom via het intranet naar de gateway te verzenden.
2. **Gegevens serialiseren en comprimeren**: de gateway serialeert de gegevens stroom naar de CSV-indeling en comprimeert de gegevens naar een bzip2-stroom.
3. **Gegevens schrijven**: gateway uploadt de bzip2-stroom naar Blob-opslag via internet.

Zoals u kunt zien, worden de gegevens verwerkt en verplaatst op een opeenvolgende manier: SQL Server > LAN > Gateway > WAN > Blob Storage. **De algehele prestaties worden geregeld door de minimale doorvoer via de pijplijn**.

![Gegevensstroom](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Een of meer van de volgende factoren kan leiden tot de bottleneck in de prestaties:

* **Bron**: SQL Server zelf heeft lage doorvoer vanwege de zware belastingen.
* **Data Management Gateway**:
  * **LAN**: de gateway bevindt zich ver van de SQL Server machine en heeft een verbinding met een lage band breedte.
  * **Gateway**: de beperkingen voor het laden van de gateway zijn bereikt om de volgende bewerkingen uit te voeren:
    * **Serialisatie**: traag doorvoer tijdens het serialiseren van de gegevensstroom naar de CSV-indeling heeft.
    * **Compressie**: U hebt ervoor gekozen een trage compressiecodec (bijvoorbeeld, bzip2, namelijk 2,8 MBps met Core i7).
  * **WAN**: de bandbreedte tussen het bedrijfsnetwerk en uw Azure-services is laag (bijvoorbeeld T1 = 1,544 kbps. Tijdstip T2 = 6,312 kbps).
* **Sink-** : Blob-opslag heeft lage doorvoer. (In dit scenario is niet waarschijnlijk omdat de SLA wordt gegarandeerd minimaal 60 MBps.)

In dit geval kan bzip2 gegevenscompressie worden vertraagd de hele pijplijn. Overschakelen naar een compressiecodec gzip, kan dit knelpunt vereenvoudigen.

## <a name="sample-scenarios-use-parallel-copy"></a>Voorbeeld scenario's: parallelle kopie gebruiken
**Scenario I:** Kopieer 1.000 1-MB bestanden van het on-premises bestands systeem naar Blob Storage.

**Analyse en prestaties afstemmen**: als u een gateway op een quad core-computer hebt geïnstalleerd, maakt Data Factory gebruik van 16 parallelle kopieën om bestanden van het bestands systeem naar de Blob-opslag gelijktijdig te verplaatsen. Deze parallelle uitvoering moet resulteren in een hoge door voer. U kunt ook expliciet het aantal parallelle kopieën opgeven. Wanneer u veel kleine bestanden kopieert, wordt de door Voer van parallelle kopieën aanzienlijk geholpen door resources effectiever te gebruiken.

![Scenario 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenario II**: Kopieer 20 blobs van 500 MB van de Blob-opslag naar Data Lake Store Analytics en stel vervolgens de prestaties af.

**Analyse en prestaties afstemmen**: in dit scenario Data Factory kopieert de gegevens uit de Blob-opslag naar Data Lake Store met behulp van één kopie (**parallelCopies** ingesteld op 1) en gegevens verplaatsings eenheden met één Cloud. De door u geobserveerde door Voer is bijna hetzelfde als de gegevens die worden beschreven in de [sectie prestatie verwijzing](#performance-reference).

![Scenario 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenario III**: de afzonderlijke bestands grootte is groter dan tien tallen MBS en het totale volume is groot.

**Analyse en prestaties inschakelen**: het verhogen van **parallelCopies** leidt niet tot betere Kopieer prestaties vanwege de resource beperkingen van een DMU met één Cloud. In plaats daarvan moet u meer Cloud DMUs opgeven om meer bronnen te krijgen om de gegevens verplaatsing uit te voeren. Geef geen waarde op voor de eigenschap **parallelCopies** . Data Factory de parallelle uitvoering voor u afhandelt. Als u in dit geval **cloudDataMovementUnits** instelt op 4, treedt er een door Voer van ongeveer vier keer op.

![Scenario 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referentie
Hier volgen de prestaties en het afstemmen van verwijzingen voor een aantal ondersteunde gegevens archieven:

* Azure Blob-opslag: [schaal baarheid en prestatie doelen voor Blob Storage](../../storage/blobs/scalability-targets.md) en de [controle lijst voor prestaties en schaal baarheid voor Blob Storage](../../storage/blobs/storage-performance-checklist.md).
* Azure-tabel opslag: [schaal baarheid en prestatie doelen voor](../../storage/tables/scalability-targets.md) de [controle lijst voor tabel opslag en prestaties en schaal baarheid voor tabel opslag](../../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: U kunt [bewaken van de prestaties](../../sql-database/sql-database-single-database-monitor.md) en controleert u het percentage van database transaction unit (DTU)
* Azure SQL datawarehouse: De mogelijkheid wordt gemeten in datawarehouse units (dwu's); Zie [beheren rekenkracht in Azure SQL Data Warehouse (overzicht)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [prestatieniveaus in Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* On-premises SQL Server: [controleren en afstemmen van prestaties](https://msdn.microsoft.com/library/ms189081.aspx)
* On-premises bestandsserver: [afstemmen van prestaties voor bestandsservers](https://msdn.microsoft.com/library/dn567661.aspx)
