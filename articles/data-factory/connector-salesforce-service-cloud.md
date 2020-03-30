---
title: Gegevens van en naar Salesforce Service Cloud kopiëren
description: Meer informatie over het kopiëren van gegevens uit Salesforce Service Cloud naar ondersteunde sink data stores of van ondersteunde brongegevensopslag naar Salesforce Service Cloud met behulp van een kopieeractiviteit in een pijplijn voor gegevensfabrieken.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 4540b27a9241a14b3d1a153d11bf43900e8ae0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153846"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Gegevens van en naar Salesforce Service Cloud kopiëren met Azure Data Factory

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruiken om gegevens van en naar Salesforce Service Cloud te kopiëren. Het bouwt voort op het [overzichtsartikel Van activiteit kopiëren](copy-activity-overview.md) dat een algemeen overzicht van de kopieeractiviteit weergeeft.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Salesforce Service Cloud-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit Salesforce Service Cloud kopiëren naar elk ondersteund sink datastore. U ook gegevens uit elke ondersteunde brongegevenswinkel kopiëren naar Salesforce Service Cloud. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen of sinks door de activiteit Kopiëren.

Met name deze Salesforce Service Cloud-connector ondersteunt:

- Salesforce Developer, Professional, Enterprise of Unlimited editions.
- Gegevens kopiëren van en naar salesforce-productie, sandbox en aangepast domein.

De Salesforce-connector is gebouwd bovenop de Salesforce REST/Bulk API. Standaard gebruikt de connector [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) om gegevens van Salesforce te kopiëren en gebruikt [de V40 om](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) gegevens naar Salesforce te kopiëren. U ook expliciet instellen dat de API-versie wordt gebruikt om gegevens te lezen/schrijven via [ `apiVersion` eigenschap](#linked-service-properties) in gekoppelde service.

## <a name="prerequisites"></a>Vereisten

API-machtigingen moeten zijn ingeschakeld in Salesforce. Zie [API-toegang inschakelen in Salesforce voor](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) meer informatie

## <a name="salesforce-request-limits"></a>Limieten voor Salesforce-aanvragen

Salesforce heeft limieten voor zowel totale API-aanvragen als gelijktijdige API-aanvragen. Houd rekening met de volgende punten:

- Als het aantal gelijktijdige aanvragen de limiet overschrijdt, treedt beperking op en ziet u willekeurige fouten.
- Als het totale aantal aanvragen de limiet overschrijdt, wordt het Salesforce-account gedurende 24 uur geblokkeerd.

Mogelijk ontvangt u in beide scenario's ook het foutbericht 'REQUEST_LIMIT_EXCEEDED'. Zie de sectie 'API-aanvraaglimieten' in [salesforce-ontwikkelaarslimieten](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)voor meer informatie.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de Salesforce Service Cloud-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de gekoppelde Salesforce-service.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |De eigenschap type moet zijn ingesteld op **SalesforceServiceCloud**. |Ja |
| omgevingUrl | Geef de URL op van het exemplaar van de Salesforce Service Cloud. <br> - Standaard `"https://login.salesforce.com"`is . <br> - Geef gegevens uit `"https://test.salesforce.com"`sandbox op om gegevens uit sandbox te kopiëren. <br> - Als u gegevens uit het aangepaste `"https://[domain].my.salesforce.com"`domein wilt kopiëren, geeft u bijvoorbeeld op . |Nee |
| gebruikersnaam |Geef een gebruikersnaam op voor het gebruikersaccount. |Ja |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount.<br/><br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) |Ja |
| securityToken |Geef een beveiligingstoken op voor het gebruikersaccount. <br/><br/>Zie [Beveiliging en de API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)voor meer informatie over beveiligingstokens in het algemeen. Het beveiligingstoken kan alleen worden overgeslagen als u het IP-adres van Integration Runtime toevoegt aan de [vertrouwde IP-adreslijst](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) op Salesforce. Raadpleeg azure [integration runtime IP-adressen](azure-integration-runtime-ip-addresses.md)wanneer u Azure IR gebruikt.<br/><br/>Zie [Een beveiligingstoken opvragen voor](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)instructies over het instellen en opnieuw instellen van een beveiligingstoken. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) |Nee |
| apiVersion | Geef de Salesforce REST/Bulk API-versie op `48.0`die u wilt gebruiken, bijvoorbeeld . Standaard gebruikt de connector [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) om gegevens van Salesforce te kopiëren en gebruikt [de V40 om](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) gegevens naar Salesforce te kopiëren. | Nee |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. | Nee voor bron, Ja voor gootsteen als de brongekoppelde service geen inlooptijd voor integratie heeft |

>[!IMPORTANT]
>Wanneer u gegevens kopieert naar Salesforce Service Cloud, kan de standaard Azure Integration Runtime niet worden gebruikt om kopieën uit te voeren. Met andere woorden, als uw brongekoppelde service geen opgegeven ingebruikmaaktijd voor integratie heeft, maakt u expliciet [een Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) met een locatie in de buurt van uw Salesforce Service Cloud-exemplaar. Koppel de gekoppelde Salesforce Service Cloud-service aan als in het volgende voorbeeld.

**Voorbeeld: Referenties opslaan in gegevensfabriek**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voorbeeld: Referenties opslaan in Key Vault**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Salesforce Service Cloud-gegevensset.

Als u gegevens van en naar Salesforce Service Cloud wilt kopiëren, worden de volgende eigenschappen ondersteund.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op **SalesforceServiceCloudObject**.  | Ja |
| objectApiName | De naam van het Salesforce-object om gegevens uit op te halen. | Nee voor bron, Ja voor gootsteen |

> [!IMPORTANT]
> Het "__c" deel van **API Name** is nodig voor elk aangepast object.

![Naam datafabriek Salesforce-verbindingsAPI](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Voorbeeld:**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op **RelationalTable**. | Ja |
| tableName | Naam van de tabel in Salesforce Service Cloud. | Nee (als 'query' in de activiteitsbron is opgegeven) |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron en de sink van Salesforce Service Cloud.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Salesforce Service Cloud als brontype

Als u gegevens uit Salesforce Service Cloud wilt kopiëren, worden de volgende eigenschappen ondersteund in de sectie **kopieeractiviteitsbron.**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron voor kopieeractiviteit moet zijn ingesteld op **SalesforceServiceCloudSource**. | Ja |
| query |Gebruik de aangepaste query om gegevens te lezen. U [SOQL-query of](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) SQL-92-query (Salesforce Object Query Language) of SQL-92-query gebruiken. Bekijk meer tips in de sectie [querytips.](#query-tips) Als query niet is opgegeven, worden alle gegevens van het Salesforce Service Cloud-object dat is opgegeven in 'objectApiName' in de gegevensset opgehaald. | Nee (als 'objectApiName' in de gegevensset is opgegeven) |
| leesGedrag | Hiermee geeft u aan of u de bestaande records moet opvragen of alle records moet opvragen, inclusief de verwijderde records. Als dit niet is opgegeven, is het standaardgedrag het eerste. <br>Toegestane waarden: **query** (standaard), **queryAlles**.  | Nee |

> [!IMPORTANT]
> Het "__c" deel van **API Name** is nodig voor elk aangepast object.

![Lijst met API-naam van Data Factory Salesforce-verbinding](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
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
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Salesforce Service Cloud als sinktype

Als u gegevens wilt kopiëren naar Salesforce Service Cloud, worden de volgende eigenschappen ondersteund in de sectie **logboeken voor kopieeractiviteiten.**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de kopieeractiviteit moet worden ingesteld op **SalesforceServiceCloudSink**. | Ja |
| writeBehavior | Het schrijfgedrag voor de operatie.<br/>Toegestane waarden zijn **Invoegen** en **Upsert**. | Nee (standaard is Invoegen) |
| externalidfieldname | De naam van het externe ID-veld voor de upsert-bewerking. Het opgegeven veld moet worden gedefinieerd als 'Extern id-veld' in het object Salesforce Service Cloud. Het kan geen NULL-waarden in de bijbehorende invoergegevens bevatten. | Ja voor "Upsert" |
| writeBatchSize | Het aantal gegevens dat in elke batch naar Salesforce Service Cloud is geschreven. | Nee (standaard is 5.000) |
| negerenNullValues | Hiermee geeft u aan of NULL-waarden moeten worden genegeerd uit invoergegevens tijdens een schrijfbewerking.<br/>Toegestane waarden zijn **waar** en **onwaar.**<br>- **True:** Laat de gegevens in het doelobject ongewijzigd wanneer u een upsert- of updatebewerking uitvoert. Voeg een gedefinieerde standaardwaarde in wanneer u een invoegbewerking doet.<br/>- **False:** Werk de gegevens in het doelobject bij aan NULL wanneer u een upsert- of updatebewerking uitvoert. Voeg een NULL-waarde in wanneer u een invoegbewerking doet. | Nee (standaard is onwaar) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Querytips

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Gegevens ophalen uit een Salesforce Service Cloud-rapport

U gegevens ophalen uit Salesforce Service Cloud-rapporten door een query op te geven als `{call "<report name>"}`. Een voorbeeld is `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Verwijderde records ophalen uit de Salesforce Service Cloud Prullenbak

Als u de zachte verwijderde records wilt opvragen in `readBehavior` `queryAll`de Salesforce Service Cloud Recycle Bin, u opgeven als. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Verschil tussen SOQL- en SQL-querysyntaxis

Wanneer u gegevens kopieert vanuit Salesforce Service Cloud, u SOQL-query of SQL-query gebruiken. Houd er rekening mee dat deze twee verschillende syntaxis- en functionaliteitsondersteuning hebben, niet mengen. U wordt aangeraden de SOQL-query te gebruiken die native wordt ondersteund door Salesforce Service Cloud. In de volgende tabel worden de belangrijkste verschillen vermeld:

| Syntaxis | SOQL-modus | SQL-modus |
|:--- |:--- |:--- |
| Kolomselectie | De velden die in de query moeten worden gekopieerd, moeten worden opgemaakt, bijvoorbeeld`SELECT field1, filed2 FROM objectname` | `SELECT *`wordt ondersteund naast kolomselectie. |
| Aanhalingstekens | Gearchiveerde/objectnamen kunnen niet worden geciteerd. | Veld-/objectnamen kunnen worden geciteerd, bijv.`SELECT "id" FROM "Account"` |
| Datumtijdnotatie |  Raadpleeg [hier](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) de details en voorbeelden in de volgende sectie. | Raadpleeg [hier](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) de details en voorbeelden in de volgende sectie. |
| Booleaanse waarden | Vertegenwoordigd als `False` `True`en , bijv. `SELECT … WHERE IsDeleted=True` | Vertegenwoordigd als 0 of 1, `SELECT … WHERE IsDeleted=1`bijv. |
| Kolom naamwijzigen | Wordt niet ondersteund. | Ondersteund, bijvoorbeeld: `SELECT a AS b FROM …`. |
| Relatie | Ondersteund, bijv. `Account_vod__r.nvs_Country__c` | Wordt niet ondersteund. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Gegevens ophalen met behulp van een where-component in de kolom DateTime

Wanneer u de SOQL- of SQL-query opgeeft, moet u aandacht besteden aan het verschil in de DatumTime-indeling. Bijvoorbeeld:

* **SOQL-monster**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-voorbeeld**:`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Fout van MALFORMED_QUERY:Afgekapt

Als u een fout van "MALFORMED_QUERY: Afgekapt" raakt, is dit normaal gesproken te wijten aan de kolom JunctionIdList-type in gegevens en heeft Salesforce een beperking op het ondersteunen van dergelijke gegevens met een groot aantal rijen. Als u de kolom JunctionIdList wilt beperken of het aantal rijen wilt beperken dat moet worden gekopieerd (u partitioneren tot meerdere kopieeractiviteitsuitvoeringen).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Toewijzing van gegevenstype voor Salesforce Service Cloud

Wanneer u gegevens uit Salesforce Service Cloud kopieert, worden de volgende toewijzingen gebruikt van Salesforce Service Cloud-gegevenstypen naar tussentijdse gegevenstypen van Data Factory. Zie [Schema en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de kopieeractiviteit het bronschema en het gegevenstype aan de gootsteen toebrengt.

| Gegevenstype Salesforce Service Cloud | Tussentijds gegevenstype Data Factory |
|:--- |:--- |
| Automatisch nummer |Tekenreeks |
| Checkbox |Booleaans |
| Valuta |Decimal |
| Date |DateTime |
| Datum/tijd |DateTime |
| Email |Tekenreeks |
| Id |Tekenreeks |
| Opzoekrelatie |Tekenreeks |
| Lijst met selectie selecteren met meerdere selecties |Tekenreeks |
| Aantal |Decimal |
| Percentage |Decimal |
| Telefoon |Tekenreeks |
| Picklist |Tekenreeks |
| Tekst |Tekenreeks |
| Tekstgebied |Tekenreeks |
| Tekstgebied (lang) |Tekenreeks |
| Tekstgebied (Rijk) |Tekenreeks |
| Tekst (versleuteld) |Tekenreeks |
| URL |Tekenreeks |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Volgende stappen
Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Data Factory.
