---
title: Gegevens kopiëren van en naar Sales Force
description: Meer informatie over het kopiëren van gegevens uit Sales Force naar ondersteunde Sink-gegevens archieven of van ondersteunde bron gegevens archieven naar Sales Force met behulp van een Kopieer activiteit in een data factory pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: de14d62f0356a8edf37f23fc85fb974dbca4e9a6
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895594"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar Sales Force met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-salesforce-connector.md)
> * [Huidige versie](connector-salesforce.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Sales Force. Dit is gebaseerd op de [overzicht van Kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Sales Force-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)


U kunt gegevens uit Sales Force kopiëren naar elk ondersteund Sink-gegevens archief. U kunt ook gegevens van elk ondersteund brongegevens archief kopiëren naar Sales Force. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bronnen of sinks op basis van de Kopieer activiteit.

Met name deze Sales Force-connector ondersteunt:

- Edities van Sales Force Developer, Professional, Enter prise of Unlimited.
- Gegevens kopiëren van en naar Sales Force-productie, sandbox en aangepast domein.

De Sales Force-connector is gebouwd op het niveau van de sales/bulk-API voor de andere software, met [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) voor het kopiëren van gegevens van en [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) voor het kopiëren van gegevens naar.

## <a name="prerequisites"></a>Vereisten

API-machtiging moet zijn ingeschakeld in Sales Force. Zie [Enable API Access in Sales Force by permission set](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) (Engelstalig) voor meer informatie.

## <a name="salesforce-request-limits"></a>Limieten voor Sales Force-aanvragen

Sales Force heeft limieten voor zowel het totale aantal API-aanvragen als gelijktijdige API-aanvragen. Houd rekening met de volgende punten:

- Als het aantal gelijktijdige aanvragen de limiet overschrijdt, wordt er een beperking weer gegeven en ziet u wille keurige fouten.
- Als het totale aantal aanvragen de limiet overschrijdt, wordt het Sales Force-account 24 uur geblokkeerd.

Het fout bericht ' REQUEST_LIMIT_EXCEEDED ' kan in beide scenario's ook worden weer gegeven. Zie de sectie ' API-aanvraag limieten ' in [Sales Force-ontwikkelaars limieten](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)voor meer informatie.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de Sales Force-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service Sales Force.

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type |De eigenschap type moet worden ingesteld op **Sales Force**. |Ja |
| environmentUrl | Geef de URL van het Sales Force-exemplaar op. <br> -De standaard waarde is `"https://login.salesforce.com"`. <br> -Als u gegevens wilt kopiëren uit de sandbox, geeft u `"https://test.salesforce.com"`op. <br> -Als u gegevens wilt kopiëren uit een aangepast domein, geeft u bijvoorbeeld `"https://[domain].my.salesforce.com"`op. |Nee |
| gebruikersnaam |Geef een gebruikers naam op voor het gebruikers account. |Ja |
| wachtwoord |Geef een wacht woord op voor het gebruikers account.<br/><br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory, of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| securityToken |Geef een beveiligings token op voor het gebruikers account. Zie [een beveiligings Token ophalen](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)voor instructies over het opnieuw instellen en ophalen van een beveiligings token. Zie [beveiliging en de API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)voor meer informatie over beveiligings tokens in het algemeen.<br/><br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory, of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | De [integratieruntime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. Als niet is opgegeven, wordt de standaard Azure Integration Runtime. | Nee voor bron, ja voor Sink als de gekoppelde bron service geen Integration runtime heeft |

>[!IMPORTANT]
>Wanneer u gegevens naar Sales Force kopieert, kan de standaard Azure Integration Runtime niet worden gebruikt voor het uitvoeren van een kopie. Met andere woorden, als uw gekoppelde bron service geen opgegeven Integration runtime heeft, maakt u expliciet [een Azure Integration runtime](create-azure-integration-runtime.md#create-azure-ir) met een locatie in de buurt van uw Sales Force-exemplaar. Koppel de gekoppelde service Sales Force als in het volgende voor beeld.

**Voor beeld: referenties opslaan in Data Factory**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
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

**Voor beeld: referenties opslaan in Key Vault**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
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

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Sales Force-gegevensset.

Als u gegevens wilt kopiëren van en naar Sales Force, stelt u de eigenschap type van de gegevensset in op **SalesforceObject**. De volgende eigenschappen worden ondersteund.

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **SalesforceObject**.  | Ja |
| objectApiName | De naam van het Sales Force-object waaruit gegevens worden opgehaald. | Nee voor bron, Ja voor sink |

> [!IMPORTANT]
> Het deel ' __c ' van de **API-naam** is vereist voor een aangepast object.

![Naam van Data Factory Sales Force-API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Voorbeeld:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

>[!NOTE]
>Voor achterwaartse compatibiliteit: wanneer u gegevens uit Sales Force kopieert en u het vorige type gegevensset ' RelationalTable ' gebruikt, blijft deze werken terwijl er een suggestie wordt weer gegeven om over te scha kelen naar het nieuwe type ' SalesforceObject '.

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op **RelationalTable**. | Ja |
| tableName | De naam van de tabel in Sales Force. | Nee (als ' query ' in de activiteit bron is opgegeven) |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Sales Force-bron en Sink.

### <a name="salesforce-as-a-source-type"></a>Sales Force als bron type

Als u gegevens wilt kopiëren uit Sales Force, stelt u het bron type in de Kopieer activiteit in op **SalesforceSource**. De volgende eigenschappen worden ondersteund in de kopieeractiviteit **bron** sectie.

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **SalesforceSource**. | Ja |
| query |Gebruik de aangepaste query om gegevens te lezen. U kunt de [Sales Force object query language-query (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) of SQL-92-query gebruiken. Meer tips vindt u in de sectie [query tips](#query-tips) . Als er geen query is opgegeven, worden alle gegevens van het Sales Force-object opgegeven in ' objectApiName ' in dataset opgehaald. | Nee (als "objectApiName" in de gegevensset is opgegeven) |
| readBehavior | Hiermee wordt aangegeven of een query moet worden doorzocht op de bestaande records, of dat alle records moeten worden opgevraagd met een query. Als deze niet wordt opgegeven, is het standaard gedrag het voormalige. <br>Toegestane waarden: **query** (standaard), **queryAll**.  | Nee |

> [!IMPORTANT]
> Het deel ' __c ' van de **API-naam** is vereist voor een aangepast object.

![Lijst met namen van Data Factory Sales Force-API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Voor achterwaartse compatibiliteit: wanneer u gegevens uit Sales Force kopieert en u het vorige type kopie ' RelationalSource ' gebruikt, blijft de bron actief terwijl u een suggestie ziet om over te scha kelen naar het nieuwe type ' SalesforceSource '.

### <a name="salesforce-as-a-sink-type"></a>Sales Force als Sink-type

Als u gegevens wilt kopiëren naar Sales Force, stelt u het sink-type in de Kopieer activiteit in op **SalesforceSink**. De volgende eigenschappen worden ondersteund in het gedeelte **sink** van de Kopieer activiteit.

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op **SalesforceSink**. | Ja |
| writeBehavior | Het schrijf gedrag voor de bewerking.<br/>Toegestane waarden zijn **Insert** en **Upsert**. | Nee (standaard waarde is invoegen) |
| externalIdFieldName | De naam van het veld externe ID voor de bewerking upsert. Het opgegeven veld moet worden gedefinieerd als externe ID-veld in het Sales Force-object. De waarde mag geen NULL-waarden bevatten in de bijbehorende invoer gegevens. | Ja voor ' Upsert ' |
| writeBatchSize | Het aantal rijen van gegevens dat in elke batch naar Sales Force wordt geschreven. | Nee (de standaard waarde is 5.000) |
| ignoreNullValues | Hiermee wordt aangegeven of NULL-waarden moeten worden genegeerd uit de invoer gegevens tijdens een schrijf bewerking.<br/>Toegestane waarden zijn **waar** en **Onwaar**.<br>- **waar**: laat de gegevens in het doel object ongewijzigd wanneer u een upsert of update-bewerking doet. Voeg een gedefinieerde standaard waarde in wanneer u een INSERT-bewerking wilt uitvoeren.<br/>- **False**: werk de gegevens in het doel object bij naar NULL wanneer u een upsert of update-bewerking uitgevoerd. Voeg een NULL-waarde toe wanneer u een INSERT-bewerking uitgevoerd. | Nee (standaard waarde is False) |

**Voor beeld: Sales Force-sink in een Kopieer activiteit**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Query tips

### <a name="retrieve-data-from-a-salesforce-report"></a>Gegevens ophalen uit een Sales Force-rapport

U kunt gegevens ophalen uit Sales Force-rapporten door een query op te geven als `{call "<report name>"}`. Een voorbeeld is `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Verwijderde records uit de Prullenbak voor Sales Force ophalen

Als u de voorlopig verwijderde records uit de Prullenbak voor Sales Force wilt opvragen, kunt u `readBehavior` als `queryAll`opgeven. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Verschil tussen de SOQL-en SQL-query syntaxis

Bij het kopiëren van gegevens uit Sales Force kunt u de SOQL-query of SQL-query gebruiken. Houd er rekening mee dat deze twee verschillende syntaxis-en functionaliteits ondersteuning hebben, maar niet kunnen combi neren. U wordt aangeraden de SOQL-query te gebruiken die standaard wordt ondersteund door Sales Force. De volgende tabel bevat de belangrijkste verschillen:

| Syntaxis | SOQL-modus | SQL-modus |
|:--- |:--- |:--- |
| Kolom selectie | U moet de velden opsommen die moeten worden gekopieerd in de query, bijvoorbeeld `SELECT field1, filed2 FROM objectname` | `SELECT *` wordt naast kolom selectie ondersteund. |
| Aanhalings tekens | Gearchiveerde/object namen kunnen niet worden opgenomen in een aanhalings teken. | Namen van velden en objecten kunnen worden genoteerd, bijvoorbeeld `SELECT "id" FROM "Account"` |
| Datum notatie |  Raadpleeg [hier](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) de details en voor beelden in de volgende sectie. | Raadpleeg [hier](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) de details en voor beelden in de volgende sectie. |
| Booleaanse waarden | Wordt weer gegeven als `False` en `True`, bijvoorbeeld `SELECT … WHERE IsDeleted=True`. | Wordt weer gegeven als 0 of 1, bijvoorbeeld `SELECT … WHERE IsDeleted=1`. |
| Kolom naam wijzigen | Wordt niet ondersteund. | Ondersteund, bijvoorbeeld: `SELECT a AS b FROM …`. |
| Relatie | Ondersteund, bijvoorbeeld `Account_vod__r.nvs_Country__c`. | Wordt niet ondersteund. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Gegevens ophalen met behulp van een WHERE-component in de datum/tijd-kolom

Wanneer u de SOQL of SQL-query opgeeft, moet u rekening best Eden aan het verschil in datum-en tijd notatie. Bijvoorbeeld:

* Voor **beeld van SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL**-voor beeld: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Fout van MALFORMED_QUERY: afgekapt

Als u de fout melding ' MALFORMED_QUERY: afgekapt ' krijgt, is het normaal dat de kolom Type JunctionIdList in data en Sales Force is beperkt tot het ondersteunen van dergelijke gegevens met een groot aantal rijen. Als u het probleem wilt oplossen, probeert u de kolom JunctionIdList uit te sluiten of het aantal te kopiëren rijen te beperken (u kunt partitioneren op meerdere uitvoeringen van de Kopieer activiteit).

## <a name="data-type-mapping-for-salesforce"></a>Toewijzing van gegevens type voor Sales Force

Wanneer u gegevens uit Sales Force kopieert, worden de volgende toewijzingen gebruikt uit Sales Force-gegevens typen voor het Data Factory van tussenliggende gegevens typen. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit het bron schema en het gegevens type aan de Sink toewijst.

| Sales Force-gegevens type | Data Factory tussentijdse gegevenstype |
|:--- |:--- |
| Auto Number |Tekenreeks |
| Checkbox |Booleaans |
| Valuta |Decimal |
| Datum |Datum/tijd |
| Datum/tijd |Datum/tijd |
| E-mail |Tekenreeks |
| Id |Tekenreeks |
| Lookup Relationship |Tekenreeks |
| Multi-Select Picklist |Tekenreeks |
| Aantal |Decimal |
| Procent |Decimal |
| Telefoon |Tekenreeks |
| Picklist |Tekenreeks |
| Tekst |Tekenreeks |
| Text Area |Tekenreeks |
| Text Area (Long) |Tekenreeks |
| Text Area (Rich) |Tekenreeks |
| Text (Encrypted) |Tekenreeks |
| URL |Tekenreeks |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
