---
title: Copy Activity performance and tuning guide (Gids voor prestaties en configuratie van Activiteit kopiëren)
description: Meer informatie over belangrijke factoren die van invloed zijn op de prestaties van gegevensverplaatsing in Azure Data Factory wanneer u Kopieeractiviteit gebruikt.
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
ms.openlocfilehash: c4ca328aa0ddc61d86a435b93fe775f294287b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527381"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Copy Activity performance and tuning guide (Gids voor prestaties en configuratie van Activiteit kopiëren)

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-copy-activity-performance.md)
> * [Versie 2 (huidige versie)](../copy-activity-performance.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Activiteitsprestaties en stemhandleiding kopiëren voor Gegevensfabriek](../copy-activity-performance.md)als u de huidige versie van de gegevensfabriekservice gebruikt.

Azure Data Factory Copy Activity biedt een eersteklas veilige, betrouwbare en krachtige oplossing voor het laden van gegevens. Hiermee u elke dag tientallen terabytes aan gegevens kopiëren in een rijke verscheidenheid aan cloud- en on-premises gegevensarchieven. Razendsnelle prestaties voor het laden van gegevens zijn essentieel om ervoor te zorgen dat u zich concentreren op het kernprobleem big data: het bouwen van geavanceerde analyseoplossingen en het verkrijgen van diepgaande inzichten uit al die gegevens.

Azure biedt een set oplossingen voor gegevensopslag en gegevensmagazijnen op bedrijfsniveau en Kopieeractiviteit biedt een sterk geoptimaliseerde ervaring voor het laden van gegevens die eenvoudig te configureren en in te stellen is. Met slechts één exemplaaractiviteit u het belangrijkste bereiken:

* Gegevens laden in **Azure SQL Data Warehouse** op **1,2 GBps**. Zie 1 TB laden in [Azure SQL Data Warehouse binnen 15 minuten laden met Azure Data Factory](data-factory-load-sql-data-warehouse.md)voor een walkthrough met een use case.
* Gegevens laden in **Azure Blob-opslag** met **1,0 GBps**
* Gegevens laden in **Azure Data Lake Store** op **1,0 GBps**

In dit artikel wordt beschreven:

* [Prestatiereferentienummers](#performance-reference) voor ondersteunde bron- en sinkgegevensarchieven om u te helpen uw project te plannen;
* Functies die de kopieerdoorvoer in verschillende scenario's kunnen stimuleren, waaronder [gegevensverplaatsingseenheden](#cloud-data-movement-units)voor wolken, [parallelle kopie](#parallel-copy)en [gefaseerde kopie](#staged-copy);
* [Richtlijnen voor prestatieafstemming](#performance-tuning-steps) voor het afstemmen van de prestaties en de belangrijkste factoren die van invloed kunnen zijn op de kopieerprestaties.

> [!NOTE]
> Zie [Gegevens verplaatsen met Kopieeractiviteit voordat](data-factory-data-movement-activities.md) u dit artikel leest als u niet bekend bent met Kopieeractiviteit in het algemeen.
>

## <a name="performance-reference"></a>Prestatiereferentie

Als referentie, onder de tabel toont de kopie doorvoer nummer in MBps voor de gegeven bron en gootsteen paren op basis van in-house testen. Ter vergelijking, het toont ook aan hoe verschillende instellingen van [cloud data movement units](#cloud-data-movement-units) of Data Management Gateway [schaalbaarheid](data-factory-data-management-gateway-high-availability-scalability.md) (meerdere gateway nodes) kan helpen bij kopieerprestaties.

![Prestatiematrix](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>In Versie 1 van Azure Data Factory zijn de minimale gegevensbewegingseenheden voor cloud-to-cloud-kopieën twee. Zie standaardgegevensverplaatsingseenheden die worden gebruikt in [bewegingseenheden voor cloudgegevens](#cloud-data-movement-units)als dit niet is opgegeven.

**Aandachtspunten:**
* Doorvoer wordt berekend met behulp van de volgende formule: [grootte van gegevens gelezen uit bron]/[Runsduur kopiëren activiteit].
* De prestatiereferentienummers in de tabel werden gemeten met behulp van [TPC-H-gegevensset](http://www.tpc.org/tpch/) in één exemplaaractiviteit.
* In Azure-gegevensarchieven bevinden de bron en gootsteen zich in dezelfde Azure-regio.
* Voor hybride kopie tussen on-premises en clouddatastores werd elk gatewayknooppunt uitgevoerd op een machine die gescheiden was van het on-premises gegevensarchief met onderstaande specificatie. Wanneer een enkele activiteit op gateway werd uitgevoerd, verbruikt de kopieerbewerking slechts een klein deel van de CPU, geheugen of netwerkbandbreedte van de testmachine. Meer informatie over [de overweging voor Data Management Gateway](#considerations-for-data-management-gateway).
    <table>
    <tr>
        <td>CPU</td>
        <td>32 cores 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Geheugen</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Netwerk</td>
        <td>Internetinterface: 10 Gbps; intranetinterface: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> U een hogere doorvoer bereiken door gebruik te maken van meer data movement units (DM's) dan de standaard maximale DMU's, wat 32 is voor een cloud-to-cloud-kopieeractiviteit. Met 100 DM's u bijvoorbeeld gegevens uit Azure Blob kopiëren naar Azure Data Lake Store op **1,0GBps.** Zie de sectie [Gegevensverplaatsingseenheden cloud](#cloud-data-movement-units) voor meer informatie over deze functie en het ondersteunde scenario. Neem contact op met [Azure-ondersteuning](https://azure.microsoft.com/support/) om meer DM's aan te vragen.

## <a name="parallel-copy"></a>Parallelle kopie
U gegevens uit de bron lezen of gegevens parallel naar de bestemming schrijven **in een uitvoeren van kopieeractiviteit**. Deze functie verbetert de doorvoer van een kopieerbewerking en vermindert de tijd die nodig is om gegevens te verplaatsen.

Deze instelling verschilt van de eigenschap **gelijktijdigheid** in de activiteitsdefinitie. De eigenschap **gelijktijdigheid** bepaalt het aantal **gelijktijdige copy activiteitsuitvoeringen** om gegevens te verwerken van verschillende activiteitvensters (1:00 tot 2:00 uur, 2.00 tot 03.00 uur, 3:00 tot 04:00 uur). Deze mogelijkheid is handig wanneer u een historische belasting uitvoert. De parallelle kopieermogelijkheid is van toepassing op **één activiteitsrun**.

Laten we eens kijken naar een voorbeeld scenario. In het volgende voorbeeld moeten meerdere segmenten uit het verleden worden verwerkt. Data Factory voert een instantie van Kopieeractiviteit (een activiteitsrun) uit voor elk segment:

* Het gegevenssegment van het eerste activiteitsvenster (1:00 tot 2:00 uur) ==> Activiteitsrun 1
* Het gegevenssegment van het tweede activiteitsvenster (2.00 tot 3.00 uur) ==> Activiteitsrun 2
* Het gegevenssegment van het tweede activiteitsvenster (3.00 tot 4.00 uur) ==> Activiteitsrun 3

Enzovoort.

In dit voorbeeld, wanneer de **gelijktijdigheidswaarde** is ingesteld op 2, voeren **Activiteitsrun 1** en **Activiteit 2** kopieergegevens uit twee activiteitvensters gelijktijdig uit om **de** prestaties van de gegevensverplaatsing te verbeteren. Als er echter meerdere bestanden zijn gekoppeld aan Activiteitsrun 1, kopieert de service voor gegevensverplaatsing bestanden van de bron naar het doel één bestand tegelijk.

### <a name="cloud-data-movement-units"></a>Eenheden voor gegevensverkeer in de cloud
Een **cloud data movement unit (DMU)** is een maat die de kracht (een combinatie van CPU, geheugen en toewijzing van netwerkbronnen) van één eenheid in Data Factory vertegenwoordigt. DMU is van toepassing op cloud-to-cloud-kopieerbewerkingen, maar niet in een hybride kopie.

**De minimale gegevensbewegingseenheden voor cloudgegevens om de run van kopieeractiviteit te versterken, zijn twee.** Als dit niet is opgegeven, worden in de volgende tabel de standaard-DGPU's weergegeven die in verschillende kopieerscenario's worden gebruikt:

| Scenario kopiëren | Standaard DM's bepaald door service |
|:--- |:--- |
| Gegevens kopiëren tussen bestanden | Tussen 4 en 16, afhankelijk van het aantal en de grootte van de bestanden. |
| Alle andere kopieerscenario's | 4 |

Als u deze standaardwaarde wilt overschrijven, geeft u als volgt een waarde op voor de eigenschap **cloudDataMovementUnits.** De **toegestane waarden** voor de eigenschap **cloudDataMovementUnits** zijn 2, 4, 8, 16, 32. Het **werkelijke aantal cloud-DMU's** dat de kopieerbewerking gebruikt tijdens de looptijd is gelijk aan of kleiner dan de geconfigureerde waarde, afhankelijk van uw gegevenspatroon. Zie de [prestatiereferentie](#performance-reference)voor informatie over het prestatieniveau dat u krijgen wanneer u meer eenheden configureert voor een specifieke kopieerbron en -sink.

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
> Als u meer cloud-DM's nodig hebt voor een hogere doorvoer, neemt u contact op met [Azure-ondersteuning](https://azure.microsoft.com/support/). Instelling van 8 en hoger werkt momenteel alleen wanneer u **meerdere bestanden kopieert van Blob-opslag/Data Lake Store/Amazon S3/cloud FTP/cloud SFTP naar Blob-opslag/Data Lake Store/Azure SQL-database**.
>

### <a name="parallelcopies"></a>parallellekopieën
U de eigenschap **parallelCopies** gebruiken om de parallellisme aan te geven die u wilt kopiëren activiteit te gebruiken. U deze eigenschap zien als het maximum aantal threads binnen Kopieeractiviteit dat kan lezen van uw bron of parallel naar uw sink data stores kan schrijven.

Voor elke run Met kopieeractiviteit bepaalt Data Factory het aantal parallelle kopieën dat moet worden gebruikt om gegevens uit het brongegevensarchief en naar het doelgegevensarchief te kopiëren. Het standaardaantal parallelle kopieën dat wordt gebruikt, is afhankelijk van het type bron en de gootsteen die u gebruikt.

| Bron en gootsteen | Standaard aantal parallelle exemplaren, bepaald door de service |
| --- | --- |
| Gegevens kopiëren tussen bestanden op basis van bestanden (Blob-opslag; Data Lake Store; Amazon S3; een on-premises bestandssysteem; een on-premises HDFS) |Tussen 1 en 32. Afhankelijk van de grootte van de bestanden en het aantal clouddata movement units (DMU's) dat wordt gebruikt om gegevens te kopiëren tussen twee cloudgegevensopslag, of de fysieke configuratie van de Gateway-machine die wordt gebruikt voor een hybride kopie (om gegevens naar of van een on-premises gegevensarchief te kopiëren). |
| Gegevens uit **een brongegevensarchief kopiëren naar Azure Table-opslag** |4 |
| Alle andere bron- en gootsteenparen |1 |

Meestal moet het standaardgedrag u de beste doorvoer geven. Als u echter de belasting wilt beheren op machines die uw gegevensopslag hosten of om de kopieerprestaties af te stemmen, u ervoor kiezen om de standaardwaarde te overschrijven en een waarde op te geven voor de eigenschap **parallelCopies.** De waarde moet tussen 1 en 32 liggen (beide inclusief). Bij uitvoering gebruikt Kopieeractiviteit voor de beste prestaties een waarde die kleiner is dan of gelijk is aan de waarde die u instelt.

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
Aandachtspunten:

* Wanneer u gegevens kopieert tussen bestandsopslag, bepalen de **parallelKopieën** het parallellisme op bestandsniveau. De chunking in een enkel bestand zou gebeuren onder automatisch en transparant, en het is ontworpen om de best geschikte chunk grootte te gebruiken voor een bepaalde bron gegevens archief type om gegevens te laden in parallel en orthogonaal parallelKopieën. Het werkelijke aantal parallelle kopieën dat de gegevensverplaatsingsservice gebruikt voor de kopieerbewerking tijdens de looptijd, is niet meer dan het aantal bestanden dat u hebt. Als het kopieergedrag **mergeFile**is, kan Kopieeractiviteit geen gebruik maken van parallellisme op bestandsniveau.
* Wanneer u een waarde opgeeft voor de eigenschap **parallelCopies,** moet u rekening houden met de belastingsverhoging op uw bron- en sinkgegevensopslag en naar gateway als het een hybride kopie is. Dit gebeurt vooral wanneer u meerdere activiteiten of gelijktijdige uitvoeringen van dezelfde activiteiten hebt die worden uitgevoerd tegen hetzelfde gegevensarchief. Als u merkt dat het gegevensarchief of de gateway wordt overspoeld met de belasting, verlaagt u de **waarde parallelKopieën** om de belasting te verlichten.
* Wanneer u gegevens uit winkels kopieert die niet zijn gebaseerd op bestanden naar bestanden die op basis van bestanden zijn gebaseerd, negeert de service gegevensverplaatsing de eigenschap **parallelCopies.** Zelfs als parallellisme is opgegeven, wordt het in dit geval niet toegepast.

> [!NOTE]
> U moet Data Management Gateway-versie 1.11 of hoger gebruiken om de functie **parallelCopies** te gebruiken wanneer u een hybride kopie maakt.
>
>

Zie de voorbeeldgebruiksvoorbeelden om deze twee eigenschappen beter te gebruiken en om de doorvoer van gegevensverplaatsingen te verbeteren. U hoeft **geen parallelle kopieën** te configureren om te profiteren van het standaardgedrag. Als u configureert en **parallelKopieën** te klein is, worden meerdere cloud-DM's mogelijk niet volledig gebruikt.

### <a name="billing-impact"></a>Impact op facturering
Het is **belangrijk** om te onthouden dat er kosten in rekening worden gebracht op basis van de totale tijd van de kopieerbewerking. Als een kopieertaak gebruikt om een uur te nemen met een cloud-eenheid en nu duurt het 15 minuten met vier cloud-eenheden, de totale factuur blijft bijna hetzelfde. U gebruikt bijvoorbeeld vier cloudeenheden. De eerste cloud-eenheid besteedt 10 minuten, de tweede, 10 minuten, de derde, 5 minuten, en de vierde, 5 minuten, alles in een Copy Activity run. Er wordt rekening gebracht voor de totale tijd van het kopiëren (gegevensverkeer), dat is 10 + 10 + 5 + 5 = 30 minuten. Het gebruik **van parallelCopies** heeft geen invloed op facturering.

## <a name="staged-copy"></a>Gefaseerde kopie
Wanneer u gegevens uit een brongegevensarchief kopieert naar een sink-gegevensarchief, u ervoor kiezen blobopslag te gebruiken als een tijdelijke faseringsopslag. Staging is vooral handig in de volgende gevallen:

1. **U wilt via PolyBase gegevens uit verschillende datastores in nemen in SQL Data Warehouse.** SQL Data Warehouse gebruikt PolyBase als een mechanisme met hoge doorvoer om een grote hoeveelheid gegevens in SQL Data Warehouse te laden. De brongegevens moeten echter in blobopslag zijn en moeten voldoen aan aanvullende criteria. Wanneer u gegevens laadt uit een ander gegevensarchief dan Blob-opslag, u het kopiëren van gegevens activeren via tussentijdse blobopslag met tijdelijke fasering. In dat geval voert Data Factory de vereiste gegevenstransformaties uit om ervoor te zorgen dat deze voldoet aan de vereisten van PolyBase. Vervolgens gebruikt het PolyBase om gegevens in SQL Data Warehouse te laden. Zie [PolyBase gebruiken om gegevens te laden in Azure SQL Data Warehouse voor](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)meer informatie. Zie 1 TB laden in [Azure SQL Data Warehouse binnen 15 minuten laden met Azure Data Factory](data-factory-load-sql-data-warehouse.md)voor een walkthrough met een use case.
2. **Soms duurt het een tijdje om een hybride gegevensbeweging uit te voeren (dat wil zeggen om te kopiëren tussen een on-premises gegevensarchief en een cloudgegevensarchief) via een langzame netwerkverbinding.** Om de prestaties te verbeteren, u de gegevens on-premises comprimeren, zodat het minder tijd kost om gegevens naar het gegevensarchief voor fasering in de cloud te verplaatsen. Vervolgens u de gegevens in het faseringsarchief decomprimeren voordat u deze in het doelgegevensarchief laadt.
3. **U wilt geen andere poorten dan poort 80 en poort 443 openen in uw firewall, vanwege het IT-beleid van bedrijven.** Wanneer u bijvoorbeeld gegevens uit een on-premises gegevensarchief kopieert naar een Azure SQL Database-sink of een Azure SQL Data Warehouse-sink, moet u uitgaande TCP-communicatie activeren op poort 1433 voor zowel de Windows-firewall als uw bedrijfsfirewall. Maak in dit scenario gebruik van de gateway om eerst gegevens naar een blob-opslagfaseinstantie te kopiëren via HTTP of HTTPS op poort 443. Laad de gegevens vervolgens in SQL Database of SQL Data Warehouse vanuit Blob-opslagfasering. In deze stroom hoeft u poort 1433 niet in te schakelen.

### <a name="how-staged-copy-works"></a>Hoe geënsceneerde kopie werkt
Wanneer u de faseringsfunctie activeert, worden de gegevens eerst gekopieerd van het brongegevensarchief naar het staging-gegevensarchief (breng uw eigen gegevensarchief mee). Vervolgens worden de gegevens gekopieerd van het gegevensarchief staging naar het sink data store. Data Factory beheert automatisch de tweetrapsstroom voor u. Data Factory ruimt ook tijdelijke gegevens op uit de faseringsopslag nadat de gegevensverplaatsing is voltooid.

In het scenario voor cloudkopieëen (zowel bron- als sinkdataopslag bevinden zich in de cloud), wordt gateway niet gebruikt. De service Data Factory voert de kopieerbewerkingen uit.

![Gefaseerde kopie: Cloudscenario](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

In het hybride kopieerscenario (bron is on-premises en sink is in de cloud), verplaatst de gateway gegevens van het brongegevensarchief naar een gegevensarchief met fasering. Data Factory-service verplaatst gegevens van het gegevensarchief met fasering naar het sink-gegevensarchief. Het kopiëren van gegevens uit een cloudgegevensarchief naar een on-premises gegevensarchief via staging wordt ook ondersteund met de omgekeerde stroom.

![Gefaseerde kopie: hybride scenario](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Wanneer u gegevensverplaatsing activeert met behulp van een faseringsarchief, u opgeven of u wilt dat de gegevens worden gecomprimeerd voordat gegevens van het brongegevensarchief naar een gegevensarchief worden verplaatst en vervolgens worden gedecomprimeerd voordat gegevens van een tussentijdse of tijdelijke gegevens worden verplaatst opslaan in het gootsteengegevensarchief.

Momenteel u gegevens tussen twee on-premises gegevensarchieven niet kopiëren met behulp van een staging-opslag. We verwachten dat deze optie binnenkort beschikbaar zal zijn.

### <a name="configuration"></a>Configuratie
Configureer de instelling **EnableStaging** in Activiteit kopiëren om op te geven of u wilt dat de gegevens worden gefaseerd in blob-opslag voordat u deze in een doelgegevensarchief laadt. Wanneer u **Staging inschakelt** op TRUE, geeft u de extra eigenschappen op die in de volgende tabel worden vermeld. Als u er geen hebt, moet u ook een azure storage- of opslagservice voor gedeelde toegang maken die is gekoppeld aan handtekeningen voor fasering.

| Eigenschap | Beschrijving | Standaardwaarde | Vereist |
| --- | --- | --- | --- |
| **inschakelen** |Geef op of u gegevens wilt kopiëren via een tijdelijke faseringsopslag. |False |Nee |
| **linkedServiceName** |Geef de naam op van een [gekoppelde AzureStorage-](data-factory-azure-blob-connector.md#azure-storage-linked-service) of [AzureStorageSas-service,](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) die verwijst naar de instantie van opslag die u als tijdelijke faseringsopslag gebruikt. <br/><br/> U Opslag met een handtekening met gedeelde toegang niet gebruiken om gegevens via PolyBase in SQL Data Warehouse te laden. U het gebruiken in alle andere scenario's. |N.v.t. |Ja, wanneer **enableStaging** is ingesteld op WAAR |
| **Pad** |Geef het blob-opslagpad op dat u de gefaseerde gegevens wilt bevatten. Als u geen pad opgeeft, maakt de service een container om tijdelijke gegevens op te slaan. <br/><br/> Geef een pad alleen op als u Opslag met een handtekening voor gedeelde toegang gebruikt of als u tijdelijke gegevens op een specifieke locatie nodig hebt. |N.v.t. |Nee |
| **inschakelenCompressie** |Hiermee geeft u op of gegevens moeten worden gecomprimeerd voordat deze naar de bestemming worden gekopieerd. Deze instelling vermindert het volume van de gegevens die worden overgedragen. |False |Nee |

Hier vindt u een voorbeelddefinitie van Kopieeractiviteit met de eigenschappen die in de vorige tabel worden beschreven:

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

### <a name="billing-impact"></a>Impact op facturering
Er worden kosten in rekening gebracht op basis van twee stappen: kopieerduur en kopieertype.

* Wanneer u staging gebruikt tijdens een cloudkopie (het kopiëren van gegevens uit een cloudgegevensarchief naar een ander cloudgegevensarchief), wordt de [som van de kopieerduur voor stap 1 en stap 2] x [prijs voor cloudkopieën eenheid] in rekening gebracht.
* Wanneer u staging gebruikt tijdens een hybride kopie (het kopiëren van gegevens uit een on-premises gegevensarchief naar een cloudgegevensarchief), worden kosten in rekening gebracht voor [hybride kopieerduur] x [prijs voor hybride kopieën eenheid] + [duur van de cloudkopie] x [prijs voor kopiëren in de cloud).

## <a name="performance-tuning-steps"></a>Stappen voor prestatieafstemming
We raden u aan de volgende stappen te ondernemen om de prestaties van uw Data Factory-service af te stemmen met Kopieeractiviteit:

1. **Stel een basislijn vast**. Test tijdens de ontwikkelingsfase uw pijplijn met Kopieeractiviteit op basis van een representatieve gegevenssteekproef. U het [slicingmodel](data-factory-scheduling-and-execution.md) Gegevensfabriek gebruiken om de hoeveelheid gegevens waarmee u werkt te beperken.

   Verzamel uitvoeringstijd en prestatiekenmerken met behulp van de **monitoring- en beheerapp.** Kies **Monitor & beheren** op de startpagina van uw Gegevensfabriek. Kies in de structuurweergave de **uitvoergegevensset**. Kies **in** de lijst Activiteit Windows de activiteit kopiëren. **Activiteit Windows** bevat de duur van activiteit kopiëren en de grootte van de gegevens die zijn gekopieerd. De doorvoer wordt weergegeven in **Activiteitsvensterverkenner**. Zie [Azure Data Factory-pijplijnen controleren en beheren met behulp van de app Monitoring en Beheer](data-factory-monitor-manage-app.md)voor meer informatie over de app.

   ![Details uitvoering van activiteit](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Later in het artikel u de prestaties en configuratie van uw scenario vergelijken met de [prestatiereferentie](#performance-reference) van Copy Activity uit onze tests.
2. **Diagnose en optimaliseer de prestaties.** Als de prestaties die u waarneemt niet aan uw verwachtingen voldoen, moet u prestatieknelpunten identificeren. Optimaliseer vervolgens de prestaties om het effect van knelpunten te verwijderen of te verminderen. Een volledige beschrijving van de prestaties diagnose is buiten het bereik van dit artikel, maar hier zijn enkele veelvoorkomende overwegingen:

   * Prestatiefuncties:
     * [Parallelle kopie](#parallel-copy)
     * [Eenheden voor gegevensverkeer in de cloud](#cloud-data-movement-units)
     * [Gefaseerde kopie](#staged-copy)
     * [Schaalbaarheid van de Data Management Gateway](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Gegevensbeheergateway](#considerations-for-data-management-gateway)
   * [Bron](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Serialisatie en deserialisatie](#considerations-for-serialization-and-deserialization)
   * [Compressie](#considerations-for-compression)
   * [Kolomtoewijzing](#considerations-for-column-mapping)
   * [Andere overwegingen](#other-considerations)
3. **Vouw de configuratie uit naar uw volledige gegevensset.** Wanneer u tevreden bent met de uitvoeringsresultaten en -prestaties, u de definitie- en pijplijnactieve periode uitbreiden om uw volledige gegevensset te dekken.

## <a name="considerations-for-data-management-gateway"></a>Overwegingen voor datamanagementgateway
**Gateway-instelling:** We raden u aan een speciale machine te gebruiken om De Gateway van het Beheer van gegevens te hosten. Zie [Overwegingen voor het gebruik van Data Management Gateway](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Gatewaymonitoring en scale-up/out:** een logische gateway met een of meer gatewayknooppunten kan meerdere copy activity-runs tegelijkertijd gelijktijdig uitvoeren. U bijna realtime momentopname van resourcegebruik (CPU, geheugen, netwerk(in/uit), enz.) op een gatewaymachine bekijken, evenals het aantal gelijktijdige taken dat wordt uitgevoerd versus beperken in de Azure-portal, zie [Monitorgateway in de portal.](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal) Als u grote behoefte hebt aan hybride gegevensverkeer, hetzij met een groot aantal gelijktijdige kopieeractiviteit wordt uitgevoerd of met een groot volume aan gegevens om te kopiëren, overweeg dan om gateway op te [schalen of uit](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) te schalen om uw bron beter te gebruiken of om meer resource te leveren om kopieën te machtigen.

## <a name="considerations-for-the-source"></a>Overwegingen voor de bron
### <a name="general"></a>Algemeen
Zorg ervoor dat het onderliggende gegevensarchief niet wordt overspoeld door andere workloads die erop of tegen worden uitgevoerd.

Zie voor Microsoft-gegevensarchieven [onderwerpen die](#performance-reference) specifiek zijn voor gegevensarchieven en help u de prestatiekenmerken van gegevensop te slaan, responstijden te minimaliseren en de doorvoer te maximaliseren.

Als u gegevens uit Blob-opslag kopieert naar SQL Data Warehouse, u **overwegen PolyBase** te gebruiken om de prestaties te verbeteren. Zie [PolyBase gebruiken om gegevens te laden in Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) voor meer informatie. Zie 1 TB laden in [Azure SQL Data Warehouse binnen 15 minuten laden met Azure Data Factory](data-factory-load-sql-data-warehouse.md)voor een walkthrough met een use case.

### <a name="file-based-data-stores"></a>Op bestanden gebaseerde gegevensarchieven
*(Inclusief Blob-opslag, Data Lake Store, Amazon S3, on-premises bestandssystemen en on-premises HDFS)*

* **Gemiddelde bestandsgrootte en bestandstelling:** Kopieeractiviteit brengt gegevens één bestand tegelijk over. Met dezelfde hoeveelheid gegevens die moet worden verplaatst, is de totale doorvoer lager als de gegevens uit veel kleine bestanden bestaan in plaats van een paar grote bestanden vanwege de bootstrapfase voor elk bestand. Daarom, indien mogelijk, combineren kleine bestanden in grotere bestanden om een hogere doorvoer te krijgen.
* **Bestandsindeling en compressie:** zie de [overwegingen voor serialisatie en deserialisatie en](#considerations-for-serialization-and-deserialization) [overwegingen voor compressiesecties voor](#considerations-for-compression) meer manieren om de prestaties te verbeteren.
* Zie de sectie [Overwegingen voor gegevensbeheergateway](#considerations-for-data-management-gateway) voor het scenario van het **on-premises bestandssysteem,** waarin **Data Management Gateway** vereist is.

### <a name="relational-data-stores"></a>Relationele gegevensopslag
*(Inclusief SQL-database; SQL Data Warehouse; Amazon Redshift; SQL Server-databases; en Oracle,MySQL, DB2, Teradata, Sybase en PostgreSQL databases, enz.)*

* **Gegevenspatroon:** uw tabelschema is van invloed op de kopieerdoorvoer. Een grote rijgrootte geeft u betere prestaties dan kleine rijgrootte, om dezelfde hoeveelheid gegevens te kopiëren. De reden hiervoor is dat de database efficiënter minder batches van gegevens kan ophalen die minder rijen bevatten.
* **Query of opgeslagen procedure:** Optimaliseer de logica van de query of de opgeslagen procedure die u opgeeft in de bron Activiteit kopiëren om gegevens efficiënter op te halen.
* Zie de sectie Overwegingen voor gegevensbeheergateway voor **on-premises relationele databases**, zoals SQL Server en Oracle, waarvoor gebruik van **Data Management Gateway**vereist is.

## <a name="considerations-for-the-sink"></a>Overwegingen voor de gootsteen
### <a name="general"></a>Algemeen
Zorg ervoor dat het onderliggende gegevensarchief niet wordt overspoeld door andere workloads die erop of tegen worden uitgevoerd.

Raadpleeg voor Microsoft-gegevensarchieven [het bewaken en afstemmen van onderwerpen](#performance-reference) die specifiek zijn voor gegevensarchieven. Met deze onderwerpen u inzicht krijgen in de prestatiekenmerken van gegevensopte plaatsen en hoe u de responstijden minimaliseren en de doorvoer maximaliseren.

Als u gegevens kopieert van **Blob-opslag** naar **SQL Data Warehouse,** u **overwegen PolyBase** te gebruiken om de prestaties te verbeteren. Zie [PolyBase gebruiken om gegevens te laden in Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) voor meer informatie. Zie 1 TB laden in [Azure SQL Data Warehouse binnen 15 minuten laden met Azure Data Factory](data-factory-load-sql-data-warehouse.md)voor een walkthrough met een use case.

### <a name="file-based-data-stores"></a>Op bestanden gebaseerde gegevensarchieven
*(Inclusief Blob-opslag, Data Lake Store, Amazon S3, on-premises bestandssystemen en on-premises HDFS)*

* **Kopieergedrag:** Als u gegevens uit een ander gegevensarchief op basis van bestanden kopieert, heeft Kopieeractiviteit drie opties via de eigenschap **copyBehavior.** Het behoudt hiërarchie, hiermee wordt hiërarchie afgevlakt of worden bestanden samengevoegd. Het behouden of afvlakken van hiërarchie heeft weinig of geen prestatieoverhead, maar het samenvoegen van bestanden zorgt ervoor dat de overhead van de prestaties toeneemt.
* **Bestandsindeling en compressie:** zie [de overwegingen voor serialisatie en deserialisatie](#considerations-for-serialization-and-deserialization) [en overwegingen voor compressiesecties](#considerations-for-compression) voor meer manieren om de prestaties te verbeteren.
* **Blob-opslag**: Blob-opslag ondersteunt momenteel alleen blokblobs voor geoptimaliseerde gegevensoverdracht en -doorvoer.
* Zie de sectie [Overwegingen voor gegevensbeheergateway](#considerations-for-data-management-gateway) voor **on-premises bestandssystemen** waarvoor gebruik moet worden gemaakt van **de Data Management Gateway.**

### <a name="relational-data-stores"></a>Relationele gegevensopslag
*(Inclusief SQL Database, SQL Data Warehouse, SQL Server-databases en Oracle-databases)*

* **Kopieergedrag**: Afhankelijk van de eigenschappen die u hebt ingesteld voor **sqlSink,** schrijft Copy Activity gegevens op verschillende manieren naar de doeldatabase.
  * Standaard gebruikt de gegevensverplaatsingsservice de Bulk Copy API om gegevens in te voegen in de toevoegmodus, wat de beste prestaties biedt.
  * Als u een opgeslagen procedure in de gootsteen configureert, past de database de gegevens één rij tegelijk toe in plaats van als bulkbelasting. De prestaties dalen aanzienlijk. Als uw gegevensset groot is, u, indien van toepassing, overschakelen naar de eigenschap **sqlWriterCleanupScript.**
  * Als u de eigenschap **sqlWriterCleanupScript** configureert voor elke copy activity-run, activeert de service het script en gebruikt u de Bulk Copy API om de gegevens in te voegen. Als u bijvoorbeeld de hele tabel met de meest recente gegevens wilt overschrijven, u een script opgeven om eerst alle records te verwijderen voordat u de nieuwe gegevens van de bron in bulk laadt.
* **Gegevenspatroon en batchgrootte**:
  * Het tabelschema is van invloed op de kopieerdoorvoer. Om dezelfde hoeveelheid gegevens te kopiëren, geeft een grote rijgrootte u betere prestaties dan een kleine rijgrootte, omdat de database efficiënter minder batches van gegevens kan vastleggen.
  * Met Activiteit kopiëren voegt gegevens in een reeks batches in. U het aantal rijen in een batch instellen met de eigenschap **writeBatchSize.** Als uw gegevens kleine rijen hebben, u de eigenschap **writeBatchSize** met een hogere waarde instellen om te profiteren van lagere batchoverhead en hogere doorvoer. Als de rijgrootte van uw gegevens groot is, moet u voorzichtig zijn wanneer u **writeBatchSize**verhoogt. Een hoge waarde kan leiden tot een kopiefout die wordt veroorzaakt door overbelasting van de database.
* Voor **on-premises relationele databases** zoals SQL Server en Oracle, waarvoor gebruik van **Data Management Gateway**vereist is, raadpleegt u de sectie Considerations for Data Management [Gateway.](#considerations-for-data-management-gateway)

### <a name="nosql-stores"></a>NoSQL-winkels
*(Inclusief tabelopslag en Azure Cosmos DB)*

* Voor **tafelopslag**:
  * **Partitie**: Het schrijven van gegevens naar interleaved partities drastisch degradeert de prestaties. Sorteer uw brongegevens op partitiesleutel, zodat de gegevens efficiënt in de ene partitie na de andere worden ingevoegd, of pas de logica aan om de gegevens naar één partitie te schrijven.
* Voor **Azure Cosmos DB:**
  * **Batchgrootte:** met de eigenschap **writeBatchSize** wordt het aantal parallelle aanvragen ingesteld op de Azure Cosmos DB-service om documenten te maken. U betere prestaties verwachten wanneer u **writeBatchSize** verhoogt omdat er meer parallelle aanvragen naar Azure Cosmos DB worden verzonden. Let echter op beperking wanneer u naar Azure Cosmos DB schrijft (het foutbericht is 'Aanvraagsnelheid is groot'). Verschillende factoren kunnen leiden tot beperking, waaronder de grootte van het document, het aantal termen in de documenten en het indexeringsbeleid van de doelverzameling. Om een hogere kopieerdoorvoer te bereiken, u overwegen een betere verzameling te gebruiken, bijvoorbeeld S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Overwegingen voor serialisatie en deserialisatie
Serialisatie en deserialisatie kunnen optreden wanneer uw invoergegevensset of uitvoergegevensset een bestand is. Zie [Ondersteunde bestands- en compressie-indelingen](data-factory-supported-file-and-compression-formats.md) met details over ondersteunde bestandsindelingen door Activiteit kopiëren.

**Kopieergedrag:**

* Bestanden kopiëren tussen bestandsgebaseerde gegevensarchieven:
  * Wanneer invoer- en uitvoergegevenssets beide dezelfde of geen bestandsindelingsinstellingen hebben, voert de gegevensverplaatsingsservice een binaire kopie uit zonder serialisatie of deserialisatie. U ziet een hogere doorvoer in vergelijking met het scenario, waarin de instellingen voor bron- en sinkbestandsindeling van elkaar verschillen.
  * Wanneer invoer- en uitvoergegevenssets beide in tekstformaat zijn en alleen het coderingstype anders is, doet de gegevensverplaatsingsservice alleen coderingsconversie. Het doet geen serialisatie en deserialisatie, die sommige prestaties overhead in vergelijking met een binaire kopie veroorzaakt.
  * Wanneer invoer- en uitvoergegevenssets beide verschillende bestandsindelingen of verschillende configuraties hebben, zoals delimiters, deserialiseert de gegevensverplaatsingsservice brongegevens om brongegevens te streamen, te transformeren en vervolgens te serialiseren in de uitvoerindeling die u hebt aangegeven. Deze bewerking resulteert in een veel belangrijkere prestatieoverhead in vergelijking met andere scenario's.
* Wanneer u bestanden kopieert van/naar een gegevensarchief dat niet op bestanden is gebaseerd (bijvoorbeeld van een archief op basis van bestanden naar een relationeel archief), is de serialisatie- of deserialisatiestap vereist. Deze stap resulteert in aanzienlijke prestaties overhead.

**Bestandsindeling:** de bestandsindeling die u kiest, kan van invloed zijn op de kopieerprestaties. Avro is bijvoorbeeld een compact binair formaat dat metadata met gegevens opslaat. Het heeft brede ondersteuning in het Hadoop-ecosysteem voor verwerking en query's. Avro is echter duurder voor serialisatie en deserialisatie, wat resulteert in een lagere kopieerdoorvoer in vergelijking met tekstformaat. Maak uw keuze van bestandsindeling in de hele verwerkingsstroom holistisch. Begin met in welke vorm de gegevens worden opgeslagen, brongegevensopslag of te worden geëxtraheerd uit externe systemen; de beste notatie voor opslag, analytische verwerking en query's; en in welk formaat de gegevens moeten worden geëxporteerd naar datamarts voor rapportage- en visualisatietools. Soms is een bestandsindeling die suboptimaal is voor lees- en schrijfprestaties een goede keuze wanneer u het algehele analytische proces overweegt.

## <a name="considerations-for-compression"></a>Overwegingen voor compressie
Wanneer uw invoer- of uitvoergegevensset een bestand is, u Kopieeractiviteit instellen om compressie of decompressie uit te voeren terwijl gegevens naar de bestemming worden geschreven. Wanneer u kiest voor compressie, maakt u een afweging tussen input / output (I/O) en CPU. Het comprimeren van de gegevenskosten extra in compute resources. Maar in ruil daarvoor vermindert het netwerk I /O en opslag. Afhankelijk van uw gegevens ziet u mogelijk een boost in de algehele kopieerdoorvoer.

**Codec:** Kopieeractiviteit ondersteunt gzip-, bzip2- en leeglopencompressietypen. Azure HDInsight kan alle drie de typen voor verwerking gebruiken. Elke compressiecodec heeft voordelen. Bzip2 heeft bijvoorbeeld de laagste kopieerdoorvoer, maar u krijgt de beste Hive-queryprestaties met bzip2 omdat u deze splitsen voor verwerking. Gzip is de meest gebalanceerde optie, en het wordt het meest gebruikt. Kies de codec die het beste past bij uw end-to-end scenario.

**Niveau**: U kiezen uit twee opties voor elke compressiecodec: snelste gecomprimeerd en optimaal gecomprimeerd. De snelste gecomprimeerde optie comprimeert de gegevens zo snel mogelijk, zelfs als het resulterende bestand niet optimaal is gecomprimeerd. De optimaal gecomprimeerde optie besteedt meer tijd aan compressie en levert een minimale hoeveelheid gegevens op. U beide opties testen om te zien welke betere algemene prestaties biedt in uw geval.

**Een overweging:** Als u een grote hoeveelheid gegevens wilt kopiëren tussen een on-premises winkel en de cloud, u overwegen tussentijdse blobopslag met compressie te gebruiken. Het gebruik van tussentijdse opslag is handig wanneer de bandbreedte van uw bedrijfsnetwerk en uw Azure-services de beperkende factor is en u wilt dat de invoergegevensset en uitvoergegevensset in niet-gecomprimeerde vorm zijn. Meer specifiek u een enkele kopieeractiviteit opsplitsen in twee kopieeractiviteiten. De eerste kopie activiteit kopieën van de bron naar een tussentijdse of fasering blob in gecomprimeerde vorm. De tweede kopieeractiviteit kopieert de gecomprimeerde gegevens van fasering en decomprimeert vervolgens terwijl deze naar de gootsteen wordt geschreven.

## <a name="considerations-for-column-mapping"></a>Overwegingen voor kolomtoewijzing
U de eigenschap **kolomToewijzingen** in Kopieeractiviteit instellen om alle of een subset van de invoerkolommen aan de uitvoerkolommen toe te voegen. Nadat de gegevensverplaatsingsservice de gegevens van de bron heeft gelezen, moet deze kolomtoewijzing uitvoeren op de gegevens voordat de gegevens naar de gootsteen worden geschreven. Deze extra verwerking vermindert de kopieerdoorvoer.

Als uw brongegevensarchief queryable is, bijvoorbeeld als het een relationeel archief is zoals SQL Database of SQL Server, of als het een NoSQL-archief is zoals Tabelopslag of Azure Cosmos DB, u overwegen de kolomfiltering- en herorderlogica naar de **queryeigenschap** te duwen in plaats van kolomtoewijzing te gebruiken. Op deze manier vindt de projectie plaats terwijl de gegevensverplaatsingsservice gegevens uit het brongegevensarchief leest, waar deze veel efficiënter is.

## <a name="other-considerations"></a>Andere overwegingen
Als de grootte van de gegevens die u wilt kopiëren groot is, u uw bedrijfslogica aanpassen om de gegevens verder te verdelen met behulp van het snijmechanisme in Data Factory. Plan vervolgens Activiteit kopiëren om vaker uit te voeren om de gegevensgrootte voor elke uitvoeren van kopieeractiviteiten te verkleinen.

Wees voorzichtig met het aantal gegevenssets en kopieeractiviteiten waarvoor Data Factory tegelijkertijd moet worden aangesloten op hetzelfde gegevensarchief. Veel gelijktijdige kopieertaken kunnen een gegevensarchief beperken en leiden tot gedegradeerde prestaties, interne pogingen kopiëren en in sommige gevallen uitvoeringsfouten.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Voorbeeldscenario: Kopiëren van een on-premises SQL Server naar Blob-opslag
**Scenario:** Er is een pijplijn gemaakt om gegevens van een on-premises SQL Server naar Blob-opslag in CSV-indeling te kopiëren. Om de kopieertaak sneller te maken, moeten de CSV-bestanden worden gecomprimeerd in bzip2-indeling.

**Test en analyse**: De doorvoer van kopieeractiviteit is minder dan 2 MBps, wat veel langzamer is dan de prestatiebenchmark.

**Prestatieanalyse en tuning:** Laten we eens kijken hoe de gegevens worden verwerkt en verplaatst om het prestatieprobleem op te lossen.

1. **Leesgegevens**: Gateway opent een verbinding met SQL Server en verzendt de query. SQL Server reageert door de gegevensstroom via het intranet naar Gateway te sturen.
2. **Gegevens serialiseren en comprimeren:** Gateway serialiseert de gegevensstroom naar CSV-indeling en comprimeert de gegevens naar een bzip2-stream.
3. **Gegevens schrijven:** Gateway uploadt de bzip2-stream naar Blob-opslag via het internet.

Zoals u zien, worden de gegevens op een opeenvolgende streamingmanier verwerkt en verplaatst: SQL Server > LAN > Gateway > WAN > Blob-opslag. **De algehele prestaties worden afgesloten door de minimale doorvoer over de pijplijn**.

![Gegevensstroom](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Een of meer van de volgende factoren kunnen leiden tot het prestatieknelpunt:

* **Bron**: SQL Server zelf heeft een lage doorvoer vanwege zware belastingen.
* **Data Management Gateway:**
  * **LAN:** Gateway bevindt zich ver van de SQL Server-machine en heeft een verbinding met een lage bandbreedte.
  * **Gateway:** Gateway heeft zijn belastingsbeperkingen bereikt om de volgende bewerkingen uit te voeren:
    * **Serialisatie**: Serialisatie van de gegevensstroom naar CSV-indeling heeft een langzame doorvoer.
    * **Compressie**: U koos voor een langzame compressie codec (bijvoorbeeld bzip2, dat is 2,8 MBps met Core i7).
  * **WAN**: De bandbreedte tussen het bedrijfsnetwerk en uw Azure-services is laag (bijvoorbeeld T1 = 1.544 kbps; T2 = 6.312 kbps).
* **Sink:** Blob-opslag heeft een lage doorvoersnelheid. (Dit scenario is onwaarschijnlijk omdat de SLA een minimum van 60 MBps garandeert.)

In dit geval kan bzip2-gegevenscompressie de hele pijplijn vertragen. Overschakelen naar een gzip compressie codec kan dit knelpunt te verlichten.

## <a name="sample-scenarios-use-parallel-copy"></a>Voorbeeldscenario's: Parallelle kopie gebruiken
**Scenario I:** Kopieer bestanden van 1000 MB van het on-premises bestandssysteem naar Blob-opslag.

**Analyse- en prestatieafstemming:** Als u bijvoorbeeld gateway op een quad core-machine hebt geïnstalleerd, gebruikt Data Factory 16 parallelle kopieën om bestanden gelijktijdig van het bestandssysteem naar blob-opslag te verplaatsen. Deze parallelle uitvoering moet resulteren in een hoge doorvoer. U ook expliciet het aantal parallelle exemplaren opgeven. Wanneer u veel kleine bestanden kopieert, helpen parallelle kopieën de doorvoer drastisch door resources effectiever te gebruiken.

![Scenario 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenario II:** Kopieer 20 blobs van elk 500 MB van Blob-opslag naar Data Lake Store Analytics en stem de prestaties af.

**Analyse- en prestatieafstemming:** In dit scenario kopieert Data Factory de gegevens van Blob-opslag naar Data Lake Store met behulp van single-copy **(parallelCopies** ingesteld op 1) en single-cloud databewegingseenheden. De doorvoer die u waarneemt, komt dicht in de buurt van die beschreven in de [sectie prestatieverwijzing](#performance-reference).

![Scenario 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenario III**: De individuele bestandsgrootte is groter dan tientallen GB's en het totale volume is groot.

**Analyse en prestatiedraaien:** het verhogen **van parallelKopieën** resulteert niet in betere kopieerprestaties vanwege de resourcebeperkingen van een single-cloud DMU. In plaats daarvan moet u meer cloud-DM's opgeven om meer resources te krijgen om de gegevensverplaatsing uit te voeren. Geef geen waarde op voor de eigenschap **parallelCopies.** Data Factory behandelt het parallellisme voor u. Als u **cloudDataMovementUnits** instelt op 4, treedt in dit geval een doorvoer van ongeveer vier keer op.

![Scenario 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Naslaginformatie
Hier volgen prestatiebewaking en tuningreferenties voor enkele van de ondersteunde gegevensarchieven:

* Azure Blob-opslag: [schaalbaarheids- en prestatiedoelen voor de](../../storage/blobs/scalability-targets.md) checklist voor blobopslag en [-prestaties en schaalbaarheid voor Blob-opslag.](../../storage/blobs/storage-performance-checklist.md)
* Azure Table-opslag: [schaalbaarheids- en prestatiedoelstellingen voor de](../../storage/tables/scalability-targets.md) checklist voor tabelopslag en [-prestaties en schaalbaarheid voor tabelopslag](../../storage/tables/storage-performance-checklist.md).
* Azure SQL-database: u [de prestaties controleren](../../sql-database/sql-database-single-database-monitor.md) en het DTU-percentage (databasetransactie-eenheid) controleren
* Azure SQL Data Warehouse: de capaciteit wordt gemeten in data warehouse units (DWUs); zie [Rekenkracht beheren in Azure SQL Data Warehouse (Overzicht)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [prestatieniveaus in Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* On-premises SQL Server: [controleren en afstemmen op prestaties](https://msdn.microsoft.com/library/ms189081.aspx)
* On-premises bestandsserver: [prestatieafstemming voor bestandsservers](https://msdn.microsoft.com/library/dn567661.aspx)
