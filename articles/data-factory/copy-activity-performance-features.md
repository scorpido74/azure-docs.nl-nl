---
title: Functies voor het optimaliseren van de activiteit prestaties
description: Meer informatie over de belangrijkste functies waarmee u de prestaties van de Kopieer activiteit kunt optimaliseren in Azure Data Factory Marketplace.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414195"
---
# <a name="copy-activity-performance-optimization-features"></a>Functies voor het optimaliseren van de activiteit prestaties

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel vindt u een overzicht van de functies voor het optimaliseren van de Kopieer activiteit, die u kunt gebruiken in Azure Data Factory.

## <a name="data-integration-units"></a>Eenheden voor gegevensintegratie

Een gegevens integratie-eenheid is een meting die de kracht vertegenwoordigt (een combi natie van CPU, geheugen en netwerk bron toewijzing) van één eenheid in Azure Data Factory. De gegevens integratie-eenheid is alleen van toepassing op [Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime), maar niet [zelf-hostende Integration runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

De toegestane DIUs om de uitvoering van een Kopieer activiteit te stimuleren, ligt **tussen 2 en 256**. Als u dit niet opgeeft of als u ' automatisch ' kiest in de gebruikers interface, Data Factory de optimale DIU-instelling dynamisch Toep assen op basis van uw combi natie van bron-Sink en gegevens patroon. De volgende tabel geeft een overzicht van de ondersteunde DIU-bereiken en het standaard gedrag in verschillende Kopieer scenario's:

| Scenario kopiëren | Ondersteund DIU-bereik | Standaard DIUs bepaald door service |
|:--- |:--- |---- |
| Tussen bestands archieven |- **Kopiëren van of naar één bestand**: 2-4 <br>- **Kopiëren van en naar meerdere bestanden**: 2-256, afhankelijk van het aantal en de grootte van de bestanden <br><br>Als u bijvoorbeeld gegevens kopieert vanuit een map met vier grote bestanden en ervoor kiest om de hiërarchie te behouden, is de maximale effectief DIU 16; Wanneer u ervoor kiest om het bestand samen te voegen, is de Maxi maal bedoel bare DIU 4. |Tussen 4 en 32, afhankelijk van het aantal en de grootte van de bestanden |
| Uit bestands archief naar niet-bestands archief |- **Kopiëren uit één bestand**: 2-4 <br/>- **Kopiëren uit meerdere bestanden**: 2-256, afhankelijk van het aantal en de grootte van de bestanden <br/><br/>Als u bijvoorbeeld gegevens kopieert vanuit een map met vier grote bestanden, is de maximale ingangs DIU 16. |- **Kopieer naar Azure SQL database of Azure Cosmos DB**: tussen 4 en 16, afhankelijk van de Sink-laag (Dtu's/RUs) en het bron bestands patroon<br>- **Kopieer naar Azure Synapse Analytics** met de poly base-of kopieer instructie: 2<br>-Ander scenario: 4 |
| Van niet-bestands opslag naar File Store |- **Kopiëren van gegevens archieven** die zijn ingeschakeld voor partities (inclusief [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256 bij het schrijven naar een map en 2-4 bij het schrijven naar één bestand. Opmerking per bron gegevens partitie kan Maxi maal 4 DIUs gebruiken.<br>- **Andere scenario's**: 2-4 |- **Kopiëren van rest of http**: 1<br/>- **Kopiëren van Amazon Redshift** met behulp van verwijderen: 2<br>- **Ander scenario**: 4 |
| Tussen niet-bestands archieven |- **Kopiëren van gegevens archieven** die zijn ingeschakeld voor partities (inclusief [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256 bij het schrijven naar een map en 2-4 bij het schrijven naar één bestand. Opmerking per bron gegevens partitie kan Maxi maal 4 DIUs gebruiken.<br/>- **Andere scenario's**: 2-4 |- **Kopiëren van rest of http**: 1<br>- **Ander scenario**: 4 |

U kunt de DIUs die voor elke Kopieer bewerking wordt gebruikt, bekijken in de weer gave controle activiteit controleren of activiteiten uitvoer. Zie voor meer informatie [controle activiteit kopiëren](copy-activity-monitoring.md). Als u deze standaard instelling wilt overschrijven, geeft u `dataIntegrationUnits` als volgt een waarde op voor de eigenschap. Het *werkelijke aantal DIUs* dat de Kopieer bewerking tijdens runtime gebruikt, is gelijk aan of kleiner dan de geconfigureerde waarde, afhankelijk van het gegevens patroon.

Er wordt een bedrag in rekening gebracht **met \* de gebruikte \* DIUs kopie duur eenheids prijs/DIU-uur**. Bekijk [hier](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)de huidige prijzen. Lokale valuta en afzonderlijke korting kunnen per abonnements type worden toegepast.

**Hierbij**

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

## <a name="self-hosted-integration-runtime-scalability"></a>Schaal baarheid van zelf-hostende Integration runtime

Als u een hogere door voer wilt doen, kunt u de zelf-hostende IR schalen of uitschalen:

- Als de CPU en het beschik bare geheugen op het zelf-hostende IR-knoop punt niet volledig worden gebruikt, maar de uitvoering van gelijktijdige taken de limiet bereikt, moet u omhoog schalen door het aantal gelijktijdige taken dat kan worden uitgevoerd op een knoop punt te verhogen.  Zie [hier](create-self-hosted-integration-runtime.md#scale-up) voor instructies.
- Als de CPU daarentegen hoog is op het zelf-hostende IR-knoop punt of het beschik bare geheugen is laag, kunt u een nieuw knoop punt toevoegen om de belasting over meerdere knoop punten te verg Roten.  Zie [hier](create-self-hosted-integration-runtime.md#high-availability-and-scalability) voor instructies.

Opmerking in de volgende scenario's kan de uitvoering van één Kopieer activiteit gebruikmaken van meerdere zelf-hostende IR-knoop punten:

- Gegevens kopiëren van archieven op basis van bestanden, afhankelijk van het aantal en de grootte van de bestanden.
- Gegevens kopiëren uit een gegevens archief met partitie opties (inclusief [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), [SAP-tabel](connector-sap-table.md#sap-table-as-source)en [SAP open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), afhankelijk van het aantal gegevens partities.

## <a name="parallel-copy"></a>Parallelle kopie

U kunt een parallelle kopie (`parallelCopies` eigenschap) instellen op Kopieer activiteit om aan te geven in welke parallellisme u de Kopieer activiteit wilt gebruiken. U kunt deze eigenschap beschouwen als het maximum aantal threads in de Kopieer activiteit dat vanuit uw bron wordt gelezen of op parallelle gegevens opslag wordt geschreven.

De parallelle kopie is een rechthoek met [gegevens integratie-eenheden](#data-integration-units) of [zelf-hostende IR-knoop punten](#self-hosted-integration-runtime-scalability). De waarde wordt geteld op alle DIUs of zelf-hostende IR-knoop punten.

Voor elke uitvoering van de Kopieer activiteit wordt standaard Azure Data Factory de instelling voor de optimale parallelle kopie dynamisch toe te passen op basis van uw bron-Sink en gegevens patroon. 

> [!TIP]
> Het standaard gedrag van parallelle kopieën biedt doorgaans de beste door Voer, die automatisch wordt bepaald door ADF op basis van uw source-Sink-paar, het gegevens patroon en het aantal DIUs of de zelf-hostende IR/het CPU/geheugen/het aantal knoop punten. Raadpleeg de [prestaties van de Kopieer activiteit oplossen](copy-activity-performance-troubleshooting.md) wanneer u parallelle kopieën afstemt.

De volgende tabel geeft een overzicht van het gedrag van parallelle kopieën:

| Scenario kopiëren | Gedrag van parallelle Kopieer bewerking |
| --- | --- |
| Tussen bestands archieven | `parallelCopies`bepaalt de parallellisme **op bestands niveau**. De Chunking binnen elk bestand vindt onder automatisch en transparant plaats. Het is ontworpen om de beste juiste segment grootte te gebruiken voor een gegeven type gegevens opslag om gegevens parallel te laden. <br/><br/>Het werkelijke aantal Kopieer activiteiten voor parallelle kopieën dat tijdens de uitvoerings tijd wordt gebruikt, is niet groter dan het aantal bestanden dat u hebt. Als het Kopieer gedrag wordt **mergeFile** in de bestands sink, kan de Kopieer activiteit niet profiteren van parallellisme op bestands niveau. |
| Uit bestands archief naar niet-bestands archief | -Bij het kopiëren van gegevens naar Azure SQL Database of Azure Cosmos DB is de standaard parallelle kopie ook afhankelijk van de Sink-laag (aantal Dtu's/RUs).<br>-Bij het kopiëren van gegevens naar een Azure-tabel is de standaard parallelle kopie 4. |
| Van niet-bestands opslag naar File Store | -Bij het kopiëren van gegevens uit een gegevens archief met partitie opties (inclusief [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), [SAP-tabel](connector-sap-table.md#sap-table-as-source)en [SAP open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), is de standaard parallelle kopie 4. Het werkelijke aantal kopieën van de parallelle Kopieer activiteit wordt gebruikt tijdens de uitvoering van het aantal gegevens partities dat u hebt. Wanneer u zelf-hostende Integration Runtime gebruikt en naar Azure Blob/ADLS Gen2 kopieert, noteert u de maximale efficiënte parallelle kopie 4 of 5 per IR-knoop punt.<br>-Voor andere scenario's worden parallelle kopieën niet van kracht. Zelfs als parallelisme is opgegeven, wordt het niet toegepast. |
| Tussen niet-bestands archieven | -Bij het kopiëren van gegevens naar Azure SQL Database of Azure Cosmos DB is de standaard parallelle kopie ook afhankelijk van de Sink-laag (aantal Dtu's/RUs).<br/>-Bij het kopiëren van gegevens uit een gegevens archief met partitie opties (inclusief [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), [SAP-tabel](connector-sap-table.md#sap-table-as-source)en [SAP open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), is de standaard parallelle kopie 4.<br>-Bij het kopiëren van gegevens naar een Azure-tabel is de standaard parallelle kopie 4. |

Als u de belasting wilt beheren op machines die uw gegevens archieven hosten, of als u de Kopieer prestaties wilt afstemmen, kunt u de standaard waarde `parallelCopies` overschrijven en een waarde voor de eigenschap opgeven. De waarde moet een geheel getal zijn dat groter is dan of gelijk is aan 1. Tijdens runtime gebruikt de Kopieer activiteit een waarde die kleiner is dan of gelijk is aan de waarde die u hebt ingesteld.

Wanneer u een waarde voor de `parallelCopies` eigenschap opgeeft, neemt u de belasting verhoging op uw bron-en Sink-gegevens op in het account. Denk ook na over de belasting verhoging voor de zelf-hostende Integration runtime als de Kopieer activiteit hiervoor is gemachtigd. Deze belasting toename treedt vooral op wanneer u meerdere activiteiten of gelijktijdige uitvoeringen hebt van dezelfde activiteiten die worden uitgevoerd op hetzelfde gegevens archief. Als u merkt dat het gegevens archief of de zelf-hostende Integration runtime wordt overspoeld met de belasting, vermindert `parallelCopies` u de waarde om de belasting te ontlasten.

**Hierbij**

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

Wanneer u gegevens uit een brongegevens archief naar een Sink-gegevens archief kopieert, kunt u ervoor kiezen om Blob-opslag te gebruiken als een tijdelijke faserings opslag. Fase ring is met name handig in de volgende gevallen:

- **U wilt gegevens uit verschillende gegevens archieven opnemen in SQL Data Warehouse via Poly base.** SQL Data Warehouse gebruikt poly Base als mechanisme voor hoge door Voer om een grote hoeveelheid gegevens in SQL Data Warehouse te laden. De bron gegevens moeten zich in Blob Storage of Azure Data Lake Store bekomen en moeten aan aanvullende criteria voldoen. Wanneer u gegevens laadt vanuit een ander gegevens archief dan Blob Storage of Azure Data Lake Store, kunt u het kopiëren van gegevens met behulp van tussenliggende staging-Blobopslag activeren. In dat geval voert Azure Data Factory de vereiste gegevens transformaties uit om ervoor te zorgen dat het voldoet aan de vereisten van poly base. Vervolgens wordt poly base gebruikt voor het efficiënt laden van gegevens naar SQL Data Warehouse. Zie [poly Base gebruiken om gegevens te laden in Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)voor meer informatie.
- **Soms duurt het even om een hybride gegevens verplaatsing (dat wil zeggen, kopiëren van een on-premises gegevens archief naar een gegevens archief in de Cloud) uit te voeren via een trage netwerk verbinding.** Om de prestaties te verbeteren, kunt u gefaseerde kopie gebruiken om de gegevens on-premises te comprimeren, zodat het minder tijd kost om gegevens te verplaatsen naar de faserings gegevens opslag in de Cloud. Vervolgens kunt u de gegevens in het faserings archief decomprimeren voordat u ze laadt in de doel gegevens opslag.
- **U wilt geen andere poorten dan poort 80 en poort 443 openen in uw firewall vanwege het IT-beleid van het bedrijf.** Wanneer u bijvoorbeeld gegevens kopieert van een on-premises gegevens archief naar een Azure SQL Database sink of een Azure SQL Data Warehouse-sink, moet u uitgaande TCP-communicatie activeren op poort 1433 voor zowel Windows Firewall als uw bedrijfs firewall. In dit scenario kan een gefaseerde kopie gebruikmaken van de zelf-hostende Integration runtime om eerst gegevens te kopiëren naar een staging-opslag instantie van een BLOB via HTTP of HTTPS op poort 443. Vervolgens kunnen de gegevens worden geladen in SQL Database of SQL Data Warehouse vanuit de fase van het maken van Blob-opslag. In deze stroom hoeft u poort 1433 niet in te scha kelen.

### <a name="how-staged-copy-works"></a>Hoe gefaseerd kopiëren werkt

Wanneer u de faserings functie activeert, worden de gegevens eerst uit de brongegevens opslag gekopieerd naar de staging Blob-opslag (neem uw eigen op). Vervolgens worden de gegevens uit de staging-gegevens opslag naar de Sink-gegevens opslag gekopieerd. Azure Data Factory beheert automatisch de twee fase stroom voor u. Azure Data Factory verwijdert ook tijdelijke gegevens uit de staging-opslag nadat de gegevens verplaatsing is voltooid.

![Gefaseerde kopie](media/copy-activity-performance/staged-copy.png)

Wanneer u gegevens verplaatsing activeert met behulp van een staging Store, kunt u opgeven of u wilt dat de gegevens worden gecomprimeerd voordat u gegevens verplaatst van het brongegevens archief naar een tussentijds-of faserings gegevensopslag en vervolgens wordt gedecomprimeerd voordat u gegevens verplaatst van een tussentijds-of faserings gegevens archief naar de Sink-gegevens opslag.

Op dit moment kunt u geen gegevens kopiëren tussen twee gegevens archieven die zijn verbonden via een andere zelf-hostende IRs, noch zonder een gefaseerde kopie. Voor dit scenario kunt u twee expliciet gekoppelde Kopieer activiteiten configureren om te kopiëren van bron naar fase ring en vervolgens van fase ring naar sink.

### <a name="configuration"></a>Configuratie

Configureer de instelling **enableStaging** in de Kopieer activiteit om op te geven of u wilt dat de gegevens in Blob Storage worden klaargezet voordat u deze in een doel gegevens archief laadt. Wanneer u **enableStaging** instelt op `TRUE`, geeft u de aanvullende eigenschappen op die in de volgende tabel worden weer gegeven. U moet ook een gekoppelde service voor het maken van een hand tekening voor gedeelde toegang met een opslag locatie voor Azure Storage of Storage gebruiken als u er geen hebt.

| Eigenschap | Beschrijving | Standaardwaarde | Vereist |
| --- | --- | --- | --- |
| enableStaging |Geef op of u gegevens wilt kopiëren via een tijdelijke faserings opslag. |False |Nee |
| linkedServiceName |Geef de naam op van een gekoppelde [opslag](connector-azure-blob-storage.md#linked-service-properties) -service die verwijst naar het exemplaar van de opslag die u gebruikt als een tijdelijke faserings opslag. <br/><br/> U kunt opslag niet gebruiken met een Shared Access Signature om gegevens te laden in SQL Data Warehouse via Poly base. U kunt deze gebruiken in alle andere scenario's. |N.v.t. |Ja, wanneer **enableStaging** is ingesteld op True |
| path |Geef het pad op van de Blob-opslag waarvoor u de gefaseerde gegevens wilt opnemen. Als u geen pad opgeeft, maakt de service een container om tijdelijke gegevens op te slaan. <br/><br/> Geef alleen een pad op als u opslag gebruikt met een hand tekening voor gedeelde toegang of als u wilt dat tijdelijke gegevens zich op een specifieke locatie bevinden. |N.v.t. |Nee |
| enableCompression |Hiermee geeft u op of gegevens moeten worden gecomprimeerd voordat ze naar het doel worden gekopieerd. Deze instelling vermindert het volume van de gegevens die worden overgedragen. |False |Nee |

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

### <a name="staged-copy-billing-impact"></a>Facturerings impact voor gefaseerde kopie

Er worden kosten in rekening gebracht op basis van twee stappen: de duur en het Kopieer type kopiëren.

* Wanneer u fase ring gebruikt tijdens een Cloud kopie, die gegevens uit een gegevens archief in de cloud kopieert naar een ander gegevens archief in de Cloud, wordt de [som van de Kopieer duur voor stap 1 en stap 2] x [eenheids prijs voor de Cloud kopie] in rekening gebracht.
* Wanneer u fase ring gebruikt tijdens een hybride kopie, waarmee gegevens worden gekopieerd van een on-premises gegevens opslag naar een gegevens archief in de Cloud, wordt één fase van een zelf-hostende Integration runtime in rekening gebracht voor [Hybrid Copy duration] x [hybride kopie van de eenheids prijs] + [duur van de Cloud kopie] x [eenheids prijs van de Cloud].

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen over Kopieer activiteiten:

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Gids voor de prestaties en schaal baarheid van de Kopieer activiteit](copy-activity-performance.md)
- [Prestaties van de Kopieer activiteit oplossen](copy-activity-performance-troubleshooting.md)
- [Azure Data Factory gebruiken om gegevens van uw data Lake of Data Warehouse te migreren naar Azure](data-migration-guidance-overview.md)
- [Gegevens migreren van Amazon S3 naar Azure Storage](data-migration-guidance-s3-azure-storage.md)