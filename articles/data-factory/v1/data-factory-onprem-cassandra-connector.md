---
title: Gegevens van Cassandra verplaatsen met datafabriek
description: Meer informatie over het verplaatsen van gegevens uit een on-premises Cassandra-database met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 05cee60fb1f4d43d1b4ce371aa9f22650b4782da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281299"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Gegevens verplaatsen uit een on-premises Cassandra-database met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-onprem-cassandra-connector.md)
> * [Versie 2 (huidige versie)](../connector-cassandra.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de service Gegevensfabriek gebruikt, raadpleegt u [de Cassandra-connector in V2](../connector-cassandra.md).

In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit een on-premises Cassandra-database te verplaatsen. Het bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit.

U gegevens uit een on-premises Cassandra-gegevensarchief kopiëren naar elk ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die als sinks worden ondersteund door de kopieeractiviteit. Datafactory ondersteunt momenteel alleen het verplaatsen van gegevens van een Cassandra-gegevensarchief naar andere gegevensopslag, maar niet voor het verplaatsen van gegevens van andere gegevensopslag naar een Cassandra-gegevensarchief.

## <a name="supported-versions"></a>Ondersteunde versies
De Cassandra connector ondersteunt de volgende versies van Cassandra: 2.x en 3.x. Voor activiteiten die worden uitgevoerd op Self-hosted Integration Runtime, wordt Cassandra 3.x ondersteund sinds IR-versie 3.7 en hoger.

## <a name="prerequisites"></a>Vereisten
Als u de Azure Data Factory-service wilt verbinden met uw on-premises Cassandra-database, moet u een Data Management Gateway installeren op dezelfde machine die de database host of op een aparte machine om te voorkomen dat u concurreert om resources met de database. Data Management Gateway is een onderdeel dat on-premises gegevensbronnen op een veilige en beheerde manier verbindt met cloudservices. Zie [artikel Data Management Gateway](data-factory-data-management-gateway.md) voor meer informatie over Data Management Gateway. Zie [Gegevens verplaatsen van on-premises naar een cloudartikel](data-factory-move-data-between-onprem-and-cloud.md) voor stapsgewijze instructies voor het instellen van de gateway een gegevenspijplijn om gegevens te verplaatsen.

U moet de gateway gebruiken om verbinding te maken met een Cassandra-database, zelfs als de database wordt gehost in de cloud, bijvoorbeeld op een Azure IaaS VM. Y U de gateway op dezelfde VM hebben die de database host of op een afzonderlijke VM, zolang de gateway verbinding kan maken met de database.

Wanneer u de gateway installeert, installeert deze automatisch een Microsoft Cassandra ODBC-stuurprogramma dat wordt gebruikt om verbinding te maken met de Cassandra-database. Daarom hoeft u geen stuurprogramma's handmatig op de gatewaymachine te installeren bij het kopiëren van gegevens uit de Cassandra-database.

> [!NOTE]
> Zie [Problemen met de gateway oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor tips over het oplossen van verbindings-/gatewaygerelateerde problemen.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens verplaatst van een on-premises Cassandra-gegevensarchief met behulp van verschillende hulpprogramma's/API's.

- De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.
- U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven.
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling. Zie [JSON-voorbeeld: Gegevens kopiëren van Cassandra naar Azure Blob](#json-example-copy-data-from-cassandra-to-azure-blob) voor een voorbeeld van JSON voor entiteiten in gegevensfabriek die worden gebruikt om gegevens uit een on-premises Cassandra-gegevensarchief te kopiëren.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor een Cassandra-gegevensarchief:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor de gekoppelde service van Cassandra.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld **op: OnPremisesCassandra** |Ja |
| host |Een of meer IP-adressen of hostnamen van Cassandra-servers.<br/><br/>Geef een door komma's gescheiden lijst met IP-adressen of hostnamen op om tegelijkertijd verbinding te maken met alle servers. |Ja |
| poort |De TCP-poort die de Cassandra-server gebruikt om naar clientverbindingen te luisteren. |Nee, standaardwaarde: 9042 |
| authenticationType |Basic of Anoniem |Ja |
| gebruikersnaam |Geef de gebruikersnaam op voor het gebruikersaccount. |Ja, als authenticationType is ingesteld op Basic. |
| wachtwoord |Geef het wachtwoord voor het gebruikersaccount op. |Ja, als authenticationType is ingesteld op Basic. |
| gatewayNaam |De naam van de gateway die wordt gebruikt om verbinding te maken met de on-premises Cassandra-database. |Ja |
| versleuteldCredential |Referentie versleuteld door de gateway. |Nee |

>[!NOTE]
>Momenteel wordt de verbinding met Cassandra via SSL niet ondersteund.

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL, Azure blob, Azure table, etc.).

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie typeEigenschappen voor de gegevensset van het type **CassandraTable** heeft de volgende eigenschappen

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| sleutelruimte |Naam van de keyspace of het schema in de Cassandra-database. |Ja (Als **query** voor **CassandraSource** niet is gedefinieerd). |
| tableName |Naam van de tabel in de Cassandra database. |Ja (Als **query** voor **CassandraSource** niet is gedefinieerd). |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleid zijn beschikbaar voor alle soorten activiteiten.

Overwegende dat de eigenschappen die beschikbaar zijn in de sectie typeEigenschappen van de activiteit per activiteitstype verschillen. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

Wanneer de bron van het type **CassandraSource**is, zijn de volgende eigenschappen beschikbaar in de sectie typeEigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-92-query of CQL-query. Zie [CQL-verwijzing](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Wanneer u SQL-query gebruikt, geeft u **de naam keyspace name.table** op om de tabel weer te geven die u wilt opvragen. |Nee (als tabelNaam en sleutelruimte op de gegevensset zijn gedefinieerd). |
| consistentieNiveau |Het consistentieniveau geeft aan hoeveel replica's op een leesaanvraag moeten reageren voordat gegevens naar de clienttoepassing worden teruggekeerd. Cassandra controleert het opgegeven aantal replica's op gegevens om aan het leesverzoek te voldoen. |EEN, TWEE, DRIE, QUORUM, ALLES, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Zie [Gegevensconsistentie configureren](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) voor meer informatie. |Nee. Standaardwaarde is ÉÉN. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON-voorbeeld: Gegevens kopiëren van Cassandra naar Azure Blob
In dit voorbeeld worden voorbeeld-JSON-definities gegeven die u gebruiken om een pijplijn te maken met Behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Hierin wordt weergegeven hoe u gegevens uit een on-premises Cassandra-database kopiëren naar een Azure Blob Storage. Gegevens kunnen echter worden gekopieerd naar een van de putten die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) zijn vermeld met behulp van de kopieeractiviteit in Azure Data Factory.

> [!IMPORTANT]
> Dit voorbeeld bevat JSON-fragmenten. Het bevat geen stapsgewijze instructies voor het maken van de gegevensfabriek. Zie [gegevens verplaatsen tussen on-premises locaties en cloudartikelen](data-factory-move-data-between-onprem-and-cloud.md) voor stapsgewijze instructies.

Het voorbeeld heeft de volgende gegevensfabriekentiteiten:

* Een gekoppelde service van het type [OnPremisesCassandra](#linked-service-properties).
* Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Een [invoergegevensset](data-factory-create-datasets.md) van het type [CassandraTable](#dataset-properties).
* Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [CassandraSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

**Cassandra gekoppelde service:**

In dit **Cassandra** voorbeeld wordt gebruik genomen van de cassandra-service. Zie [de door Cassandra gekoppelde servicesectie](#linked-service-properties) voor de eigenschappen die door deze gekoppelde service worden ondersteund.

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Gekoppelde Azure Storage-service:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**Cassandra-invoergegevensset:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Als **u extern** naar true **instelt,** wordt de service Data Factory geïnformeerd dat de gegevensset buiten de gegevensfabriek staat en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

**Azure Blob-uitvoergegevensset:**

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Activiteit in een pijplijn kopiëren met Cassandra-bron en Blob-sink:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **CassandraSource** en is **het gootsteentype** ingesteld op **BlobSink**.

Zie [RelationalSource-teksteigenschappen](#copy-activity-properties) voor de lijst met eigenschappen die worden ondersteund door relationele bron.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```

### <a name="type-mapping-for-cassandra"></a>Typemapping voor Cassandra
| Cassandra Type | .NET-type |
| --- | --- |
| Ascii |Tekenreeks |
| Bigint |Int64 |
| Blob |Byte |
| Booleaanse |Booleaans |
| Decimaal |Decimal |
| Dubbele |Double |
| Float |Enkel |
| Inet |Tekenreeks |
| INT |Int32 |
| TEXT |Tekenreeks |
| Tijdstempel |DateTime |
| TIMEUUID |GUID |
| Uuid |GUID |
| Varchar |Tekenreeks |
| VARINT (VARINT) |Decimal |

> [!NOTE]
> Voor verzamelingstypen (kaart, set, lijst, enz.) raadpleegt u [Verzamelingstypen werken met Cassandra met behulp van virtuele tabelsectie.](#work-with-collections-using-virtual-table)
>
> Door de gebruiker gedefinieerde typen worden niet ondersteund.
>
> De lengte van binaire kolom- en tekenreekskolomlengtes mag niet groter zijn dan 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Werken met verzamelingen met behulp van virtuele tabel
Azure Data Factory gebruikt een ingebouwd ODBC-stuurprogramma om verbinding te maken met gegevens uit uw Cassandra-database en deze te kopiëren. Voor verzamelingstypen, waaronder kaart, set en lijst, normaliseert het stuurprogramma de gegevens opnieuw in overeenkomstige virtuele tabellen. Als een tabel verzamelingskolommen bevat, genereert het stuurprogramma de volgende virtuele tabellen:

* Een **basistabel**die dezelfde gegevens bevat als de echte tabel, behalve de verzamelingskolommen. De basistabel gebruikt dezelfde naam als de echte tabel die deze vertegenwoordigt.
* Een **virtuele tabel** voor elke verzamelingskolom, die de geneste gegevens uitbreidt. De virtuele tabellen die verzamelingen vertegenwoordigen, worden benoemd met de naam van de echte tabel, een separator "*vt*" en de naam van de kolom.

Virtuele tabellen verwijzen naar de gegevens in de echte tabel, zodat de bestuurder toegang heeft tot de gedenormaliseerde gegevens. Zie voorbeeldsectie voor meer informatie. U hebt toegang tot de inhoud van Cassandra-collecties door de virtuele tabellen op te vragen en lid te worden.

U de [wizard Kopiëren](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) gebruiken om intuïtief de lijst met tabellen in de Cassandra-database weer te geven, inclusief de virtuele tabellen, en een voorbeeld van de gegevens binnenin te bekijken. U ook een query maken in de wizard Kopiëren en valideren om het resultaat te bekijken.

### <a name="example"></a>Voorbeeld
De volgende 'ExampleTable' is bijvoorbeeld een Cassandra-databasetabel met een gehele primaire sleutelkolom met de naam 'pk_int', een tekstkolom met de naam waarde, een lijstkolom, een kolom en een setkolom (met de naam 'StringSet').

| pk_int | Waarde | Lijst | Kaart | StringSet (Tekenreeksset) |
| --- | --- | --- | --- | --- |
| 1 |"voorbeeldwaarde 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"voorbeeldwaarde 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Het stuurprogramma genereert meerdere virtuele tabellen om deze afzonderlijke tabel weer te geven. De kolommen met de buitenlandse sleutel in de virtuele tabellen verwijzen naar de primaire sleutelkolommen in de echte tabel en geven aan met welke echte tabelrij de virtuele tabelrij overeenkomt.

De eerste virtuele tabel is de basistabel met de naam 'Voorbeeldtabel' wordt weergegeven in de volgende tabel. De basistabel bevat dezelfde gegevens als de oorspronkelijke databasetabel, behalve voor de verzamelingen, die uit deze tabel worden weggelaten en in andere virtuele tabellen worden uitgevouwen.

| pk_int | Waarde |
| --- | --- |
| 1 |"voorbeeldwaarde 1" |
| 3 |"voorbeeldwaarde 3" |

In de volgende tabellen worden de virtuele tabellen weergegeven die de gegevens uit de kolommen Lijst, Kaart en StringSet opnieuw normaliseren. De kolommen met namen die eindigen met "_index" of "_key" geven de positie van de gegevens binnen de oorspronkelijke lijst of kaart aan. De kolommen met namen die eindigen met '_value' bevatten de uitgebreide gegevens uit de verzameling.

#### <a name="table-exampletable_vt_list"></a>Tabel "ExampleTable_vt_List":
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletable_vt_map"></a>Tabel "ExampleTable_vt_Map":
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletable_vt_stringset"></a>Tabel "ExampleTable_vt_StringSet":
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Kaartbron om kolommen te laten zinken
Zie Kolommen van [gegevenssetsin Azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in brongegevensset naar kolommen in sink dataset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaalbaar lezen uit relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevensopslag rekening met herhaalbaarheid om onbedoelde resultaten te voorkomen. In Azure Data Factory u een segment handmatig opnieuw uitvoeren. U ook het beleid voor een wijziging opnieuw configureren, zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment in beide richtingen wordt opnieuw uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht hoe vaak een segment wordt uitgevoerd. Zie [Herhaalbaar lezen uit relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Handleiding activiteitsprestaties kopiëren & tuningom](data-factory-copy-activity-performance.md) meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.
