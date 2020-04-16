---
title: Functies voor optimalisatie van activiteitsprestaties kopiëren
description: Meer informatie over de belangrijkste functies waarmee u de prestaties van kopieeractiviteiten in Azure Data Factory optimaliseren.
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
ms.date: 03/09/2020
ms.openlocfilehash: fd7844340553809e1429097a9dda70f6bdb3e075
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414195"
---
# <a name="copy-activity-performance-optimization-features"></a>Functies voor optimalisatie van activiteitsprestaties kopiëren

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden de optimalisatiefuncties voor kopieeractiviteiten beschreven die u gebruiken in Azure Data Factory.

## <a name="data-integration-units"></a>Eenheden voor gegevensintegratie

Een data-integratie-eenheid is een maat die de kracht (een combinatie van CPU, geheugen en toewijzing van netwerkbronnen) van één eenheid in Azure Data Factory vertegenwoordigt. Data Integration Unit is alleen van toepassing op [Azure-integratieruntime,](concepts-integration-runtime.md#azure-integration-runtime)maar niet [op zelf gehoste runtime voor integratie.](concepts-integration-runtime.md#self-hosted-integration-runtime)

De toegestane DIU's om een kopieeractiviteit te machtigen, zijn **tussen 2 en 256**. Als u niet is opgegeven of als u 'Auto' kiest op de gebruikersinterface, past Data Factory dynamisch de optimale DIU-instelling toe op basis van uw bronsinkpaar en gegevenspatroon. In de volgende tabel worden de ondersteunde DIU-bereiken en het standaardgedrag in verschillende kopieerscenario's weergegeven:

| Scenario kopiëren | Ondersteund DIU-assortiment | Standaard DIU's bepaald door service |
|:--- |:--- |---- |
| Tussen bestandswinkels |- **Kopie van of naar enkel bestand:** 2-4 <br>- **Kopie van en naar meerdere bestanden:** 2-256, afhankelijk van het aantal en de grootte van de bestanden <br><br>Als u bijvoorbeeld gegevens kopieert uit een map met 4 grote bestanden en ervoor kiest de hiërarchie te behouden, is de maximale effectieve DIU 16; wanneer u ervoor kiest om bestand samen te voegen, is de maximale effectieve DIU 4. |Tussen 4 en 32, afhankelijk van het aantal en de grootte van de bestanden |
| Van bestandsarchief naar niet-bestandsarchief |- **Kopie uit één bestand**: 2-4 <br/>- **Kopie uit meerdere bestanden:** 2-256, afhankelijk van het aantal en de grootte van de bestanden <br/><br/>Als u bijvoorbeeld gegevens kopieert uit een map met 4 grote bestanden, is de maximale effectieve DIU 16. |- **Kopiëren naar Azure SQL Database of Azure Cosmos DB:** tussen 4 en 16, afhankelijk van de sink tier (DTU/RU's) en het patroon van bronbestanden<br>- **Kopiëren naar Azure Synapse Analytics** met PolyBase of COPY-instructie: 2<br>- Ander scenario: 4 |
| Van niet-bestandsarchief naar bestandsarchief |- **Kopieer uit gegevensarchieven** met partitieoptie (inclusief [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata):](connector-teradata.md#teradata-as-source)2-256 bij het schrijven naar een map en 2-4 bij het schrijven naar één enkel bestand. Opmerking per brongegevenspartitie kan maximaal 4 DIU's gebruiken.<br>- **Andere scenario's**: 2-4 |- **Kopie van REST of HTTP**: 1<br/>- **Kopiëren van Amazon Redshift** met UNLOAD: 2<br>- **Ander scenario**: 4 |
| Tussen niet-bestandswinkels |- **Kopieer uit gegevensarchieven** met partitieoptie (inclusief [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata):](connector-teradata.md#teradata-as-source)2-256 bij het schrijven naar een map en 2-4 bij het schrijven naar één enkel bestand. Opmerking per brongegevenspartitie kan maximaal 4 DIU's gebruiken.<br/>- **Andere scenario's**: 2-4 |- **Kopie van REST of HTTP**: 1<br>- **Ander scenario**: 4 |

U de DIU's zien die voor elke kopie worden uitgevoerd in de weergave voor controle van kopieeractiviteiten of activiteitsuitvoer. Zie [Activiteitscontrole kopiëren](copy-activity-monitoring.md)voor meer informatie . Als u deze standaardwaarde wilt `dataIntegrationUnits` overschrijven, geeft u als volgt een waarde voor de eigenschap op. Het *werkelijke aantal DIU's* dat de kopieerbewerking gebruikt tijdens de looptijd is gelijk aan of kleiner dan de geconfigureerde waarde, afhankelijk van uw gegevenspatroon.

Er worden kosten in rekening gebracht **voor de gebruikte DIU's voor de eenheidsprijs/DIU-uur. \* \* ** Bekijk [hier](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)de actuele prijzen. Lokale valuta en afzonderlijke kortingen kunnen per abonnementstype van toepassing zijn.

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
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Schaalbaarheid van zelfgehoste integratieruntime

Als u een hogere doorvoer wilt bereiken, u de zelfgehoste IR opschalen of opschalen:

- Als de CPU en het beschikbare geheugen op het zelfgehoste IR-knooppunt niet volledig worden gebruikt, maar de uitvoering van gelijktijdige taken de limiet bereikt, moet u opschalen door het aantal gelijktijdige taken te verhogen dat op een knooppunt kan worden uitgevoerd.  Zie [hier](create-self-hosted-integration-runtime.md#scale-up) voor instructies.
- Als de CPU daarentegen hoog staat op het zelfgehoste IR-knooppunt of het beschikbare geheugen laag is, u een nieuw knooppunt toevoegen om de belasting over de meerdere knooppunten te schalen.  Zie [hier](create-self-hosted-integration-runtime.md#high-availability-and-scalability) voor instructies.

Opmerking in de volgende scenario's, de uitvoering van één exemplaaractiviteit kan gebruikmaken van meerdere zelfgehoste IR-knooppunten:

- Kopieer gegevens uit bestanden, afhankelijk van het aantal en de grootte van de bestanden.
- Kopieer gegevens uit het gegevensarchief met partitieoptie (inclusief [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [SAP Table](connector-sap-table.md#sap-table-as-source)en SAP [Open Hub),](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)afhankelijk van het aantal gegevenspartities.

## <a name="parallel-copy"></a>Parallelle kopie

U parallelle`parallelCopies` kopie (eigenschap) op kopieeractiviteit instellen om de parallellisme aan te geven die u wilt dat de kopieeractiviteit gebruikt. U deze eigenschap zien als het maximum aantal threads binnen de kopieeractiviteit die uit uw bron leest of parallel naar uw sink data stores schrijft.

De parallelle kopie is orthogonaal naar [data-integratie-eenheden](#data-integration-units) of [zelf gehoste IR-knooppunten.](#self-hosted-integration-runtime-scalability) Het wordt geteld over alle DIU's of Self-hosted IR-knooppunten.

Voor elke kopieeractiviteit die wordt uitgevoerd, past Azure Data Factory standaard dynamisch de optimale parallelle kopieerinstelling toe op basis van uw bronsinkpaar en gegevenspatroon. 

> [!TIP]
> Het standaardgedrag van parallelle kopie geeft u meestal de beste doorvoer, die automatisch wordt bepaald door ADF op basis van uw bron-sink-paar, gegevenspatroon en aantal DIU's of het cpu/geheugen/knooppunt aantal van de zelfgehoste IR. Raadpleeg [Problemen met de prestaties van kopieeractiviteiten](copy-activity-performance-troubleshooting.md) op het moment waarop u parallelle tekst wilt afstemmen.

In de volgende tabel wordt het parallelle kopieergedrag weergegeven:

| Scenario kopiëren | Parallel kopieergedrag |
| --- | --- |
| Tussen bestandswinkels | `parallelCopies`bepaalt het parallellisme **op bestandsniveau**. De chunking binnen elk bestand gebeurt er automatisch en transparant onder. Het is ontworpen om de best geschikte chunk grootte te gebruiken voor een bepaald type gegevensarchief om gegevens parallel te laden. <br/><br/>Het werkelijke aantal parallelle kopieën kopieeractiviteit gebruikt tijdens de looptijd is niet meer dan het aantal bestanden dat u hebt. Als het kopieergedrag wordt **samengevoegdBestand** in bestandssink, kan de kopieeractiviteit geen gebruik maken van parallellisme op bestandsniveau. |
| Van bestandsarchief naar niet-bestandsarchief | - Bij het kopiëren van gegevens naar Azure SQL Database of Azure Cosmos DB is standaard parallelle kopie ook afhankelijk van de sink-laag (aantal DTU's/RU's).<br>- Bij het kopiëren van gegevens naar Azure Table is standaard parallelle kopie 4. |
| Van niet-bestandsarchief naar bestandsarchief | - Bij het kopiëren van gegevens uit het gegevensarchief met partitieoptie (inclusief [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)en SAP [Open Hub),](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)is standaard parallelle kopie 4. Het werkelijke aantal parallelle kopieën kopieeractiviteit gebruikt tijdens de looptijd is niet meer dan het aantal gegevenspartities die u hebt. Wanneer u Runtime voor zelfgehoste integratie gebruikt en naar Azure Blob/ADLS Gen2 kopieert, moet u er rekening mee houden dat de maximale effectieve parallelle kopie 4 of 5 per IR-knooppunt is.<br>- Voor andere scenario's wordt parallelle kopie niet van kracht. Zelfs als parallellisme is opgegeven, wordt het niet toegepast. |
| Tussen niet-bestandswinkels | - Bij het kopiëren van gegevens naar Azure SQL Database of Azure Cosmos DB is standaard parallelle kopie ook afhankelijk van de sink-laag (aantal DTU's/RU's).<br/>- Bij het kopiëren van gegevens uit het gegevensarchief met partitieoptie (inclusief [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)en SAP [Open Hub),](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)is standaard parallelle kopie 4.<br>- Bij het kopiëren van gegevens naar Azure Table is standaard parallelle kopie 4. |

Als u de belasting wilt beheren op machines die uw gegevensopslag hosten of om kopieerprestaties af te stemmen, u de standaardwaarde overschrijven en een waarde voor de `parallelCopies` eigenschap opgeven. De waarde moet een geheel getal zijn dat groter is dan of gelijk is aan 1. Bij uitvoering gebruikt de kopieeractiviteit voor de beste prestaties een waarde die kleiner is dan of gelijk is aan de waarde die u instelt.

Wanneer u een waarde `parallelCopies` voor de eigenschap opgeeft, moet u rekening houden met de belastingsverhoging van uw bron en gegevensopslag laten zinken. Houd ook rekening met de belastingsverhoging naar de zelfgehoste runtime voor integratie als de kopieeractiviteit erdoor wordt gemachtigd. Deze belastingsverhoging gebeurt vooral wanneer u meerdere activiteiten of gelijktijdige uitvoeringen van dezelfde activiteiten hebt die worden uitgevoerd tegen hetzelfde gegevensarchief. Als u merkt dat de runtime van het gegevensarchief of de `parallelCopies` zelfgehoste integratie-runtime wordt overweldigd door de belasting, verlaagt u de waarde om de belasting te verlichten.

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

## <a name="staged-copy"></a>Gefaseerde kopie

Wanneer u gegevens uit een brongegevensarchief kopieert naar een sink-gegevensarchief, u ervoor kiezen blobopslag te gebruiken als een tijdelijke faseringsopslag. Staging is vooral handig in de volgende gevallen:

- **U wilt via PolyBase gegevens uit verschillende datastores in nemen in SQL Data Warehouse.** SQL Data Warehouse gebruikt PolyBase als een mechanisme met hoge doorvoer om een grote hoeveelheid gegevens in SQL Data Warehouse te laden. De brongegevens moeten zich in blobopslag of Azure Data Lake Store bevinden en moeten aan aanvullende criteria voldoen. Wanneer u gegevens laadt uit een ander gegevensarchief dan Blob-opslag of Azure Data Lake Store, u het kopiëren van gegevens activeren via blob-opslag met tijdelijke fasering. In dat geval voert Azure Data Factory de vereiste gegevenstransformaties uit om ervoor te zorgen dat deze voldoet aan de vereisten van PolyBase. Vervolgens gebruikt het PolyBase om gegevens efficiënt in SQL Data Warehouse te laden. Zie [PolyBase gebruiken om gegevens te laden in Azure SQL Data Warehouse voor](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)meer informatie.
- **Soms duurt het een tijdje om een hybride gegevensbeweging uit te voeren (dat wil zeggen om te kopiëren van een on-premises gegevensarchief naar een clouddataarchief) via een langzame netwerkverbinding.** Om de prestaties te verbeteren, u gefaseerde kopieën gebruiken om de gegevens on-premises te comprimeren, zodat het minder tijd kost om gegevens naar het gegevensarchief voor fasering in de cloud te verplaatsen. Vervolgens u de gegevens in het faseringsarchief decomprimeren voordat u in het doelgegevensarchief wordt geladen.
- **U wilt geen andere poorten dan poort 80 en poort 443 openen in uw firewall vanwege het IT-beleid van bedrijven.** Wanneer u bijvoorbeeld gegevens uit een on-premises gegevensarchief kopieert naar een Azure SQL Database-sink of een Azure SQL Data Warehouse-sink, moet u uitgaande TCP-communicatie activeren op poort 1433 voor zowel de Windows-firewall als uw bedrijfsfirewall. In dit scenario kan gefaseerde kopie profiteren van de zelf gehoste implementatieruntime om gegevens eerst naar een Blob-opslagfaseringsinstantie via HTTP of HTTPS op poort 443 te kopiëren. Vervolgens kan de gegevens worden geladen in SQL Database of SQL Data Warehouse vanuit Blob-opslagfasering. In deze stroom hoeft u poort 1433 niet in te schakelen.

### <a name="how-staged-copy-works"></a>Hoe geënsceneerde kopie werkt

Wanneer u de faseringsfunctie activeert, worden de gegevens eerst gekopieerd van het brongegevensarchief naar de opslag van de staging Blob (breng uw eigen opslag mee). Vervolgens worden de gegevens gekopieerd van het gegevensarchief staging naar het sink data store. Azure Data Factory beheert automatisch de tweetrapsstroom voor u. Azure Data Factory ruimt ook tijdelijke gegevens op uit de faseringsopslag nadat de gegevensverplaatsing is voltooid.

![Gefaseerde kopie](media/copy-activity-performance/staged-copy.png)

Wanneer u gegevensverplaatsing activeert met behulp van een faseringsarchief, u opgeven of u wilt dat de gegevens worden gecomprimeerd voordat u gegevens van het brongegevensarchief naar een gegevensarchief verplaatst naar een gegevensarchief en vervolgens decomprimeert voordat u gegevens verplaatst van een tussentijds of faseringsgegevensarchief naar het gegevensarchief van de gootsteen.

Momenteel u geen gegevens kopiëren tussen twee gegevensopslag die zijn verbonden via verschillende zelfgehoste IRs, noch met noch zonder gefaseerde kopie. Voor een dergelijk scenario u twee expliciet geketende kopieeractiviteit configureren om van bron naar fasering te kopiëren en vervolgens van staging naar sink.

### <a name="configuration"></a>Configuratie

Configureer de instelling **enableStaging** in de kopieeractiviteit om op te geven of u wilt dat de gegevens worden gefaseerd in blob-opslag voordat u deze in een doelgegevensarchief laadt. Wanneer u Staging `TRUE` **inschakelt,** geeft u de extra eigenschappen op die in de volgende tabel worden vermeld. U moet ook een azure storage- of opslagservice voor gedeelde toegang maken met handtekening voor fasering als u die niet hebt.

| Eigenschap | Beschrijving | Standaardwaarde | Vereist |
| --- | --- | --- | --- |
| inschakelen |Geef op of u gegevens wilt kopiëren via een tijdelijke faseringsopslag. |False |Nee |
| linkedServiceName |Geef de naam op van een [gekoppelde AzureStorage-service,](connector-azure-blob-storage.md#linked-service-properties) die verwijst naar de instantie van opslag die u als tijdelijke faseringsopslag gebruikt. <br/><br/> U Opslag met een handtekening met gedeelde toegang niet gebruiken om gegevens via PolyBase in SQL Data Warehouse te laden. U het gebruiken in alle andere scenario's. |N.v.t. |Ja, wanneer **enableStaging** is ingesteld op WAAR |
| path |Geef het blob-opslagpad op dat u de gefaseerde gegevens wilt bevatten. Als u geen pad opgeeft, maakt de service een container om tijdelijke gegevens op te slaan. <br/><br/> Geef een pad alleen op als u Opslag met een handtekening voor gedeelde toegang gebruikt of als u tijdelijke gegevens op een specifieke locatie nodig hebt. |N.v.t. |Nee |
| inschakelenCompressie |Hiermee geeft u op of gegevens moeten worden gecomprimeerd voordat deze naar de bestemming worden gekopieerd. Deze instelling vermindert het volume van de gegevens die worden overgedragen. |False |Nee |

>[!NOTE]
> Als u gefaseerde kopie gebruikt met compressie ingeschakeld, wordt de serviceprincipal- of MSI-verificatie voor gekoppelde koppelingsservice met blobs niet ondersteund.

Hier vindt u een voorbeelddefinitie van een kopieeractiviteit met de eigenschappen die in de vorige tabel worden beschreven:

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

### <a name="staged-copy-billing-impact"></a>Impact op gefaseerde facturering van kopieën

Er worden kosten in rekening gebracht op basis van twee stappen: kopieerduur en kopieertype.

* Wanneer u staging gebruikt tijdens een cloudkopie, die gegevens kopieert van een cloudgegevensarchief naar een ander cloudgegevensarchief, beide fasen die worden ondersteund door de runtime van Azure-integratie, wordt de [som van de kopieerduur voor stap 1 en stap 2] x [prijs voor cloudkopieëen/ eenheidsopslagruimte) in rekening gebracht.
* Wanneer u staging gebruikt tijdens een hybride kopie, die gegevens kopieert van een on-premises gegevensarchief naar een cloudgegevensarchief, een fase die wordt ondersteund door een zelfgehoste runtime voor integratie, worden kosten in rekening gebracht voor [hybride kopieerduur] x [prijs voor hybride kopieën eenheid] + [duur van cloudkopieëen] x [prijs voor cloudkopieëe eenheden].

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen van de exemplaaractiviteit:

- [Overzicht van activiteit kopiëren](copy-activity-overview.md)
- [Handleiding voor activiteitsprestaties en schaalbaarheid kopiëren](copy-activity-performance.md)
- [Problemen met de prestaties van kopieeractiviteiten oplossen](copy-activity-performance-troubleshooting.md)
- [Azure Data Factory gebruiken om gegevens uit uw gegevensmeer of gegevensmagazijn naar Azure te migreren](data-migration-guidance-overview.md)
- [Gegevens migreren van Amazon S3 naar Azure Storage](data-migration-guidance-s3-azure-storage.md)