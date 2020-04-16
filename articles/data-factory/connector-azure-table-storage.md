---
title: Gegevens kopiëren van en naar Azure Table-opslag
description: Meer informatie over het kopiëren van gegevens uit ondersteunde bronopslag naar Azure Table-opslag of van tabelopslag naar ondersteunde sinkstores met Behulp van Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: 6edd32f8f3579238d1f08f55ce9fb1528fa5d211
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417483"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar Azure Table-opslag met Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-azure-table-connector.md)
> * [Huidige versie](connector-azure-table-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruikt om gegevens van en naar Azure Table-opslag te kopiëren. Het bouwt voort op het [overzichtsartikel Activiteit kopiëren](copy-activity-overview.md) dat een algemeen overzicht van Kopieeractiviteit weergeeft.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Table-opslagconnector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit elk opgeslagen brongegevensarchief kopiëren naar Tabelopslag. U ook gegevens uit tabelopslag kopiëren naar een ondersteund sink-gegevensarchief. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen of sinks door de kopieeractiviteit.

Deze Azure Table-connector ondersteunt met name het kopiëren van gegevens met behulp van verificaties voor gedeelde accountsleutels en servicegedeelde toegang.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om gegevensfabrieksentiteiten te definiëren die specifiek zijn voor tabelopslag.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

### <a name="use-an-account-key"></a>Een accountsleutel gebruiken

U een gekoppelde Azure Storage-service maken met behulp van de accountsleutel. Het biedt de gegevensfabriek wereldwijde toegang tot opslag. De volgende eigenschappen worden ondersteund.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op **AzureTableStorage**. |Ja |
| Connectionstring | Geef de informatie op die nodig is om verbinding te maken met Opslag voor de eigenschap connectionString. <br/>U accountsleutel ook in Azure `accountKey` Key Vault plaatsen en de configuratie uit de verbindingstekenreeks halen. Raadpleeg de volgende voorbeelden en [store-referenties in het Azure Key Vault-artikel](store-credentials-in-key-vault.md) met meer details. |Ja |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U Azure Integration Runtime of Self-hosted Integration Runtime gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

>[!NOTE]
>Als u de gekoppelde service van het type AzureStorage gebruikt, wordt deze nog steeds ondersteund als u wordt voorgesteld om dit nieuwe gekoppelde servicetype 'AzureTableStorage' in de toekomst te gebruiken.

**Voorbeeld:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld: accountsleutel opslaan in Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-shared-access-signature-authentication"></a>Verificatie van gedeelde toegangshandtekeningen gebruiken

U ook een gekoppelde opslagservice maken met behulp van een gedeelde toegangshandtekening. Het biedt de gegevensfabriek beperkte/tijdgebonden toegang tot alle/specifieke bronnen in de opslag.

Een handtekening voor gedeelde toegang biedt gedelegeerde toegang tot bronnen in uw opslagaccount. U het gebruiken om een client beperkte machtigingen te verlenen aan objecten in uw opslagaccount voor een bepaalde tijd en met een opgegeven set machtigingen. U hoeft geen toegangssleutels voor uw account te delen. De handtekening voor gedeelde toegang is een URI die in de queryparameters alle informatie omvat die nodig is voor geverifieerde toegang tot een opslagbron. Om toegang te krijgen tot opslagbronnen met de handtekening van gedeelde toegang, hoeft de client alleen de handtekening voor gedeelde toegang door te geven aan de juiste constructor of methode. Zie Handtekeningen voor gedeelde toegang voor meer informatie over handtekeningen voor gedeelde [toegang: Het handtekeningmodel voor gedeelde toegang begrijpen](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Data Factory ondersteunt nu zowel **handtekeningen voor gedeelde toegang voor services** als handtekeningen voor gedeelde **accounttoegang.** Zie Beperkte toegang tot Azure [Storage-bronnen verlenen met behulp van gedeelde toegangshandtekeningen (SAS)](../storage/common/storage-sas-overview.md)voor meer informatie over handtekeningen voor gedeelde toegang. 

> [!TIP]
> Als u een handtekening voor gedeelde servicewilt genereren voor uw opslagaccount, u de volgende PowerShell-opdrachten uitvoeren. Vervang de tijdelijke aanduidingen en geef de benodigde toestemming.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Als u verificatie van gedeelde toegangshandtekeningen wilt gebruiken, worden de volgende eigenschappen ondersteund.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op **AzureTableStorage**. |Ja |
| sasUri sasUri | Geef SAS URI van de uri voor gedeelde toegangshandtekeningen op de tabel. <br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. U sas-token ook in Azure Key Vault plaatsen om automatische rotatie te gebruiken en het tokengedeelte te verwijderen. Raadpleeg de volgende voorbeelden en [store-referenties in het Azure Key Vault-artikel](store-credentials-in-key-vault.md) met meer details. | Ja |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. U de runtime azure-integratie of de runtime voor zelfgehoste integratie gebruiken (als uw gegevensarchief zich in een privénetwerk bevindt). Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

>[!NOTE]
>Als u de gekoppelde service van het type AzureStorage gebruikt, wordt deze nog steeds ondersteund als u wordt voorgesteld om dit nieuwe gekoppelde servicetype 'AzureTableStorage' in de toekomst te gebruiken.

**Voorbeeld:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<account>.table.core.windows.net/<table>?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld: accountsleutel opslaan in Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<account>.table.core.windows.net/<table>>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Wanneer u uri voor gedeelde toegangshandtekeningen maakt, moet u rekening houden met de volgende punten:

- Stel de juiste lees-/schrijfmachtigingen in op objecten op basis van hoe de gekoppelde service (lezen, schrijven, lezen/schrijven) wordt gebruikt in uw gegevensfabriek.
- Stel **de vervaldatum** op de juiste manier in. Zorg ervoor dat de toegang tot opslagobjecten niet verloopt binnen de actieve periode van de pijplijn.
- De URI moet worden gemaakt op het juiste tafelniveau op basis van de noodzaak.

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset Azure Table.

Als u gegevens wilt kopiëren van en naar Azure Table, stelt u de eigenschap typeeigenschap van de gegevensset in op **AzureTable**. De volgende eigenschappen worden ondersteund.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op **AzureTable**. |Ja |
| tableName |De naam van de tabel in de instantie tabelopslagdatabase waarnaar de gekoppelde service verwijst. |Ja |

**Voorbeeld:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schema op gegevensfabriek

Voor gegevensarchieven zonder schema, zoals Azure Table, leidt Data Factory het schema op een van de volgende manieren af:

* Als u de kolomtoewijzing opgeeft in kopieeractiviteit, gebruikt Data Factory de lijst met de bronzijdekolom om gegevens op te halen. Als een rij in dit geval geen waarde voor een kolom bevat, wordt er een null-waarde voor opgegeven.
* Als u de kolomtoewijzing in kopieeractiviteit niet opgeeft, leidt Data Factory het schema af met behulp van de eerste rij in de gegevens. Als de eerste rij in dit geval niet het volledige schema bevat (bijvoorbeeld sommige kolommen hebben null-waarde), worden sommige kolommen gemist in het resultaat van de kopieerbewerking.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron en gootsteen van de Azure Table.

### <a name="azure-table-as-a-source-type"></a>Azure-tabel als brontype

Als u gegevens uit Azure Table wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **AzureTableSource**. De volgende eigenschappen worden ondersteund in de **sectie** bron van kopieeractiviteit.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron voor kopieeractiviteit moet zijn ingesteld op **AzureTableSource**. |Ja |
| azureTableSourceQuery |Gebruik de aangepaste query tabelopslag om gegevens te lezen. Zie voorbeelden in de volgende sectie. |Nee |
| azureTableSourceIgnoreTableNotFound |Hiermee geeft u aan of de uitzondering van de tabel niet mag bestaan.<br/>Toegestane waarden zijn **Waar** en **Onwaar** (standaard). |Nee |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery-voorbeelden

>[!NOTE]
>De bewerking van Azure Table-query's is in 30 seconden uitgeschakeld, zoals [afgedwongen door de Azure Table-service](https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations). Meer informatie over het optimaliseren van de query vanuit [Ontwerp voor het opvragen van](../storage/tables/table-storage-design-for-query.md) artikelen.

Raadpleeg in Azure Data Factory de gegevens op een kolom met datumtijdtypen:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Als u de gegevens wilt filteren op een kolom van het tekenreekstype, raadpleegt u dit voorbeeld:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Als u de parameter pipeline gebruikt, cast u de datumtijdwaarde naar de juiste opmaak volgens de vorige monsters.

### <a name="azure-table-as-a-sink-type"></a>Azure Table als gootsteentype

Als u gegevens wilt kopiëren naar Azure Table, stelt u het gootsteentype in de kopieeractiviteit in op **AzureTableSink**. De volgende eigenschappen worden ondersteund in de sectie copy activity **sink.**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de inhoudslijst voor kopiëren moet worden ingesteld op **AzureTableSink**. |Ja |
| azureTableDefaultPartitionKeyValue |De standaardpartitiesleutelwaarde die door de gootsteen kan worden gebruikt. |Nee |
| azureTablePartitionKeyName |Geef de naam op van de kolom waarvan de waarden worden gebruikt als partitiesleutels. Als dit niet is opgegeven, wordt 'AzureTableDefaultPartitionKeyValue' gebruikt als partitiesleutel. |Nee |
| azuretablerowkeynaam |Geef de naam op van de kolom waarvan de kolomwaarden als rijsleutel worden gebruikt. Als dit niet is opgegeven, gebruikt u een GUID voor elke rij. |Nee |
| azureTableInsertType |De modus om gegevens in te voegen in Azure Table. Met deze eigenschap bepaalt u of bestaande rijen in de uitvoertabel met overeenkomende partitie- en rijsleutels hun waarden hebben vervangen of samengevoegd. <br/><br/>Toegestane waarden worden **samengevoegd** (standaard) en **vervangen**. <br/><br> Deze instelling is van toepassing op rijniveau en niet op het tabelniveau. Geen van beide opties verwijdert rijen in de uitvoertabel die niet in de invoer bestaan. Zie [Entiteit invoegen of samenvoegen](https://msdn.microsoft.com/library/azure/hh452241.aspx) en entiteit [invoegen of vervangen](https://msdn.microsoft.com/library/azure/hh452242.aspx)voor meer informatie over hoe de instellingen voor samenvoegen en vervangen werken. |Nee |
| writeBatchSize |Hiermee voegt u gegevens in Azure Table in wanneer batchgrootte of writeBatchTimeout wordt getroffen.<br/>Toegestane waarden zijn integer (aantal rijen). |Nee (standaard is 10.000) |
| writeBatchTimeout |Hiermee voegt u gegevens in Azure Table in wanneer batchgrootte of writeBatchTimeout wordt getroffen.<br/>Toegestane waarden zijn tijdspanne. Een voorbeeld is '00:20:00' (20 minuten). |Nee (standaard is 90 seconden, standaard time-out van de opslagclient) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

Wijs een bronkolom toe aan een doelkolom met de eigenschap **'vertaler'** voordat u de doelkolom als azureTablePartitionKeyName gebruiken.

In het volgende voorbeeld wordt bronkolom DivisionID toegewezen aan de doelkolom DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" is opgegeven als de partitiesleutel.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Toewijzing van gegevenstype voor Azure Table

Wanneer u gegevens van en naar Azure Table kopieert, worden de volgende toewijzingen gebruikt van Azure Table-gegevenstypen naar tijdelijke gegevenstypen van Data Factory. Zie [Schema en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de kopieeractiviteit het bronschema en het gegevenstype aan de gootsteen toebrengt.

Wanneer u gegevens van en naar Azure Table verplaatst, worden de volgende [toewijzingen die zijn gedefinieerd door Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) gebruikt van Azure Table OData-typen naar .NET-type en vice versa.

| Gegevenstype Azure Table | Tussentijds gegevenstype Data Factory | Details |
|:--- |:--- |:--- |
| Edm.Binary (Edm.Binary) |byte[] |Een array van bytes tot 64 KB. |
| Edm.Boolean |Booleaanse waarde |Een booleaanse waarde. |
| Edm.DateTime |DateTime |Een 64-bits waarde uitgedrukt als Coordinated Universal Time (UTC). Het ondersteunde DateTime-bereik begint middernacht, 1 januari 1601 na 16.01 uur. (C.E.), UTC. Het assortiment eindigt op 31 december 9999. |
| Edm.Double |double |Een 64-bits drijvende puntwaarde. |
| Edm.Guid Edm.Guid |GUID |Een 128-bits wereldwijd unieke id. |
| Edm.Int32 |Int32 |Een 32-bits geheel getal. |
| Edm.Int64 |Int64 |Een 64-bits geheel getal. |
| Edm.String |Tekenreeks |Een UTF-16-gecodeerde waarde. Tekenreekswaarden kunnen maximaal 64 KB zijn. |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Data Factory.
