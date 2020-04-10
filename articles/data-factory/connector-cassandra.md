---
title: Gegevens van Cassandra kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens van Cassandra naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 6aac3f1bb29ccfab0de4cb0a807ca0831a62af7e
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991549"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Gegevens van Cassandra kopiëren met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Huidige versie](connector-cassandra.md)

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit een Cassandra-database te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Cassandra-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit de Cassandra-database kopiëren naar elk ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Specifiek ondersteunt deze Cassandra connector:

- Cassandra **versies 2.x en 3.x**.
- Gegevens kopiëren met **basis-** of **anonieme** verificatie.

>[!NOTE]
>Voor activiteiten die worden uitgevoerd op Self-hosted Integration Runtime, wordt Cassandra 3.x ondersteund sinds IR-versie 3.7 en hoger.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

De Integration Runtime biedt een ingebouwde Cassandra driver, daarom hoeft u geen stuurprogramma handmatig te installeren bij het kopiëren van gegevens van/naar Cassandra.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor de Cassandra-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor Cassandra linked service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |Het type eigenschap moet worden ingesteld op: **Cassandra** |Ja |
| host |Een of meer IP-adressen of hostnamen van Cassandra-servers.<br/>Geef een door komma's gescheiden lijst met IP-adressen of hostnamen op om tegelijkertijd verbinding te maken met alle servers. |Ja |
| poort |De TCP-poort die de Cassandra-server gebruikt om naar clientverbindingen te luisteren. |Nee (standaard is 9042) |
| authenticationType | Type verificatie wordt gebruikt om verbinding te maken met de Cassandra-database.<br/>Toegestane waarden zijn: **Basic**en **Anoniem**. |Ja |
| gebruikersnaam |Geef de gebruikersnaam op voor het gebruikersaccount. |Ja, als authenticationType is ingesteld op Basic. |
| wachtwoord |Geef het wachtwoord voor het gebruikersaccount op. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) |Ja, als authenticationType is ingesteld op Basic. |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

>[!NOTE]
>Momenteel wordt de verbinding met Cassandra via TLS niet ondersteund.

**Voorbeeld:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de cassandra-gegevensset.

Als u gegevens van Cassandra wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **CassandraTable**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **CassandraTable** | Ja |
| sleutelruimte |Naam van de keyspace of het schema in de Cassandra-database. |Nee (als 'query' voor 'CassandraSource' is opgegeven) |
| tableName |Naam van de tabel in de Cassandra database. |Nee (als 'query' voor 'CassandraSource' is opgegeven) |

**Voorbeeld:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit


Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Cassandra-bron.

### <a name="cassandra-as-source"></a>Cassandra als bron

Als u gegevens van Cassandra wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **CassandraSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de kopieeractiviteit moet worden ingesteld op: **CassandraSource** | Ja |
| query |Gebruik de aangepaste query om gegevens te lezen. SQL-92-query of CQL-query. Zie [CQL-verwijzing](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Wanneer u SQL-query gebruikt, geeft u **de naam keyspace name.table** op om de tabel weer te geven die u wilt opvragen. |Nee (als 'tableName' en 'keyspace' in de gegevensset zijn opgegeven). |
| consistentieNiveau |Het consistentieniveau geeft aan hoeveel replica's op een leesaanvraag moeten reageren voordat gegevens naar de clienttoepassing worden teruggekeerd. Cassandra controleert het opgegeven aantal replica's op gegevens om aan het leesverzoek te voldoen. Zie [Gegevensconsistentie configureren](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) voor meer informatie.<br/><br/>De toegestane waarden zijn: **ÉÉN**, **TWEE**, **DRIE**, **QUORUM**, **ALLE**, **LOCAL_QUORUM**, **EACH_QUORUM**en **LOCAL_ONE**. |Nee (standaard `ONE`is) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Gegevenstypetoewijzing voor Cassandra

Bij het kopiëren van gegevens van Cassandra worden de volgende toewijzingen gebruikt van Cassandra-gegevenstypen naar tijdelijke gegevenstypen van Azure Data Factory. Zie [Schema- en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieeractiviteit het bronschema en het gegevenstype naar de gootsteen brengt.

| Cassandra-gegevenstype | Tussentijds gegevenstype gegevensfabriek |
|:--- |:--- |
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

## <a name="work-with-collections-using-virtual-table"></a>Werken met verzamelingen met behulp van virtuele tabel

Azure Data Factory gebruikt een ingebouwd ODBC-stuurprogramma om verbinding te maken met gegevens uit uw Cassandra-database en deze te kopiëren. Voor verzamelingstypen, waaronder kaart, set en lijst, normaliseert het stuurprogramma de gegevens opnieuw in overeenkomstige virtuele tabellen. Als een tabel verzamelingskolommen bevat, genereert het stuurprogramma de volgende virtuele tabellen:

* Een **basistabel**die dezelfde gegevens bevat als de echte tabel, behalve de verzamelingskolommen. De basistabel gebruikt dezelfde naam als de echte tabel die deze vertegenwoordigt.
* Een **virtuele tabel** voor elke verzamelingskolom, die de geneste gegevens uitbreidt. De virtuele tabellen die verzamelingen vertegenwoordigen, worden benoemd met de naam van de echte tabel, een separator "*vt*" en de naam van de kolom.

Virtuele tabellen verwijzen naar de gegevens in de echte tabel, zodat de bestuurder toegang heeft tot de gedenormaliseerde gegevens. Zie voorbeeldsectie voor meer informatie. U hebt toegang tot de inhoud van Cassandra-collecties door de virtuele tabellen op te vragen en lid te worden.

### <a name="example"></a>Voorbeeld

De volgende 'ExampleTable' is bijvoorbeeld een Cassandra-databasetabel met een gehele primaire sleutelkolom met de naam 'pk_int', een tekstkolom met de naam waarde, een lijstkolom, een kolom en een setkolom (met de naam 'StringSet').

| pk_int | Waarde | Lijst | Kaart | StringSet (Tekenreeksset) |
| --- | --- | --- | --- | --- |
| 1 |"voorbeeldwaarde 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"voorbeeldwaarde 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Het stuurprogramma genereert meerdere virtuele tabellen om deze afzonderlijke tabel weer te geven. De kolommen met de buitenlandse sleutel in de virtuele tabellen verwijzen naar de primaire sleutelkolommen in de echte tabel en geven aan met welke echte tabelrij de virtuele tabelrij overeenkomt.

De eerste virtuele tabel is de basistabel met de naam 'Voorbeeldtabel' wordt weergegeven in de volgende tabel: 

| pk_int | Waarde |
| --- | --- |
| 1 |"voorbeeldwaarde 1" |
| 3 |"voorbeeldwaarde 3" |

De basistabel bevat dezelfde gegevens als de oorspronkelijke databasetabel, behalve voor de verzamelingen, die uit deze tabel worden weggelaten en in andere virtuele tabellen worden uitgevouwen.

In de volgende tabellen worden de virtuele tabellen weergegeven die de gegevens uit de kolommen Lijst, Kaart en StringSet opnieuw normaliseren. De kolommen met namen die eindigen met "_index" of "_key" geven de positie van de gegevens binnen de oorspronkelijke lijst of kaart aan. De kolommen met namen die eindigen met '_value' bevatten de uitgebreide gegevens uit de verzameling.

**Tabel "ExampleTable_vt_List":**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Tabel "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**Tabel "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
