---
title: Gegevens van Salesforce verplaatsen met Data Factory
description: Meer informatie over het verplaatsen van gegevens van Salesforce met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b94f6388d77cca2ef74c802aec7648091172775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281130"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Gegevens van Salesforce verplaatsen met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-salesforce-connector.md)
> * [Versie 2 (huidige versie)](../connector-salesforce.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Salesforce-connector in V2](../connector-salesforce.md)als u de huidige versie van de Data Factory-service gebruikt.

In dit artikel wordt beschreven hoe u Activiteit kopiëren in een Azure-gegevensfabriek gebruiken om gegevens van Salesforce te kopiëren naar gegevensarchief dat wordt vermeld onder de kolom Sink in de [tabel ondersteunde bronnen en sinks.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Dit artikel bouwt voort op het artikel [over gegevensverplaatsingsactiviteiten,](data-factory-data-movement-activities.md) dat een algemeen overzicht van gegevensverplaatsing met Kopieeractiviteit en ondersteunde combinaties van gegevensopslagpresenteert.

Azure Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van Salesforce naar [ondersteunde sink data stores,](data-factory-data-movement-activities.md#supported-data-stores-and-formats)maar ondersteunt geen verplaatsing van gegevens van andere gegevensopslag naar Salesforce.

## <a name="supported-versions"></a>Ondersteunde versies
Deze connector ondersteunt de volgende edities van Salesforce: Developer Edition, Professional Edition, Enterprise Edition of Unlimited Edition. En het ondersteunt kopiëren vanuit Salesforce-productie, sandbox en aangepast domein.

## <a name="prerequisites"></a>Vereisten
* API-toestemming moet zijn ingeschakeld. Zie [Hoe schakel ik API-toegang in Salesforce in met toestemmingsset?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Als u gegevens van Salesforce wilt kopiëren naar on-premises gegevensarchieven, moet u ten minste Data Management Gateway 2.0 in uw on-premises omgeving hebben geïnstalleerd.

## <a name="salesforce-request-limits"></a>Limieten voor Salesforce-aanvragen
Salesforce heeft limieten voor zowel totale API-aanvragen als gelijktijdige API-aanvragen. Houd rekening met de volgende punten:

- Als het aantal gelijktijdige aanvragen de limiet overschrijdt, treedt beperking op en ziet u willekeurige fouten.
- Als het totale aantal aanvragen de limiet overschrijdt, wordt het Salesforce-account gedurende 24 uur geblokkeerd.

Mogelijk ontvangt u in beide scenario's ook de fout 'REQUEST_LIMIT_EXCEEDED'. Zie de sectie 'API-aanvraaglimieten' in het artikel [Salesforce Developer Limits](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) voor meer informatie.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens van Salesforce verplaatst met behulp van verschillende hulpprogramma's/API's.

De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven.
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling. Zie [JSON-voorbeeld: Gegevens van Salesforce kopiëren naar Azure Blob](#json-example-copy-data-from-salesforce-to-azure-blob) voor een voorbeeld van JSON met JSON-definities voor entiteiten in gegevensfabriek die worden gebruikt om gegevens van Salesforce te kopiëren naar Azure Blob.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in Data Factory te definiëren die specifiek zijn voor Salesforce:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
In de volgende tabel vindt u beschrijvingen voor JSON-elementen die specifiek zijn voor de gekoppelde Salesforce-service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet zijn ingesteld op: **Salesforce**. |Ja |
| omgevingUrl | Geef de URL van Salesforce-instantie op. <br><br> - Standaard is\/"https: /login.salesforce.com". <br> - Om gegevens uit sandboxhttps://test.salesforce.comte kopiëren, geeft u " "op" op. <br> - Als u gegevens uit een aangepast domein wilt kopiëren, geeft u bijvoorbeeld "https://[domain].my.salesforce.com" op. |Nee |
| gebruikersnaam |Geef een gebruikersnaam op voor het gebruikersaccount. |Ja |
| wachtwoord |Geef een wachtwoord op voor het gebruikersaccount. |Ja |
| securityToken |Geef een beveiligingstoken op voor het gebruikersaccount. Zie [Beveiligingstoken opvragen](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) voor instructies over het resetten/krijgen van een beveiligingstoken. Zie [Beveiliging en de API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)voor meer informatie over beveiligingstokens in het algemeen. |Ja |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL, Azure blob, Azure-tabel, enzovoort).

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie typeEigenschappen voor een gegevensset van het type **RelationalTable** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel in Salesforce. |Nee (als een **query** van **RelationalSource** is opgegeven) |

> [!IMPORTANT]
> Het "__c" deel van de API-naam is nodig voor elk aangepast object.

![Data Factory - Salesforce-verbinding - API-naam](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en verschillende beleidsregels zijn beschikbaar voor alle soorten activiteiten.

De eigenschappen die beschikbaar zijn in de sectie tekstEigenschappen van de activiteit, daarentegen, variëren per activiteitstype. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

Wanneer de bron in de kopieeractiviteit het type **RelationalSource** (dat Salesforce bevat) bevat, is de volgende eigenschappen beschikbaar in de sectie typeEigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |Een SQL-92-query of [SOQL-query (Salesforce Object Query Language).](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) Bijvoorbeeld: `select * from MyTable__c`. |Nee (als de **tabelNaam** van de **gegevensset** is opgegeven) |

> [!IMPORTANT]
> Het "__c" deel van de API-naam is nodig voor elk aangepast object.

![Data Factory - Salesforce-verbinding - API-naam](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Querytips
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Gegevens ophalen met de plaats waar de component op de kolom DateTime
Wanneer u de SOQL- of SQL-query opgeeft, moet u aandacht besteden aan het verschil in de DatumTime-indeling. Bijvoorbeeld:

* **SOQL-monster**:`$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **SQL-voorbeeld**:
    * **De wizard Kopiëren gebruiken om de query op te geven:**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Json-bewerking gebruiken om de query op te geven (escape char goed):**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Gegevens ophalen uit Salesforce-rapport
U gegevens ophalen uit Salesforce-rapporten door query's op te geven als `{call "<report name>"}`'bijvoorbeeld'. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Verwijderde records ophalen uit Salesforce-prullenbak
Als u de zachte verwijderde records wilt opvragen in Salesforce Recycle Bin, u **'IsDeleted = 1'** opgeven in uw query. Bijvoorbeeld:

* Als u alleen de verwijderde records wilt opvragen, geeft u op "selecteren * uit MyTable__c **waar IsDeleted= 1**"
* Als u alle records wilt opvragen, inclusief de bestaande en de verwijderde, geeft u op "selecteer * uit MyTable__c **waar Wordt verwijderd = 0 of IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>JSON-voorbeeld: gegevens van Salesforce kopiëren naar Azure Blob
In het volgende voorbeeld vindt u voorbeeld-JSON-definities die u gebruiken om een pijplijn te maken met behulp van Visual [Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Ze laten zien hoe u gegevens van Salesforce naar Azure Blob Storage kopieert. Gegevens kunnen echter worden gekopieerd naar een van de putten die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) zijn vermeld met behulp van de kopieeractiviteit in Azure Data Factory.

Hier volgen de Artefacten van De Gegevensfabriek die u moet maken om het scenario te implementeren. De secties die de lijst volgen, geven details over deze stappen.

* Een gekoppelde service van het type [Salesforce](#linked-service-properties)
* Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Een [invoergegevensset](data-factory-create-datasets.md) van het type [RelationalTable](#dataset-properties)
* Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [RelationalSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) gebruikt

**Salesforce gekoppelde service**

In dit voorbeeld wordt gebruik gemaakt van de **gekoppelde** Salesforce-service. Bekijk de sectie [Gekoppelde Salesforce-service](#linked-service-properties) voor de eigenschappen die worden ondersteund door deze gekoppelde service. Zie [Beveiligingstoken opvragen](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) voor instructies over het opnieuw instellen/krijgen van het beveiligingstoken.

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```
**Gekoppelde Azure Storage-service**

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
**Salesforce-invoergegevensset**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
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

> [!IMPORTANT]
> Het "__c" deel van de API-naam is nodig voor elk aangepast object.

![Data Factory - Salesforce-verbinding - API-naam](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Gegevensset Azure blob-uitvoer**

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
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pijplijn met kopieeractiviteit**

De pijplijn bevat kopieeractiviteit, die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken en wordt elk uur uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **RelationalSource**en wordt het **gootsteentype** ingesteld op **BlobSink**.

Zie [RelationalSource-teksteigenschappen](#copy-activity-properties) voor de lijst met eigenschappen die worden ondersteund door relationelebron.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
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
> [!IMPORTANT]
> Het "__c" deel van de API-naam is nodig voor elk aangepast object.

![Data Factory - Salesforce-verbinding - API-naam](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Typetoewijzing voor Salesforce

| Salesforce-type | . NET-gebaseerd type |
| --- | --- |
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

> [!NOTE]
> Zie Kolommen van [gegevensset toewijzing in Azure Data Factory](data-factory-map-columns.md)als u kolommen wilt toewijzen van brongegevensset naar kolommen uit de sink-gegevensset.

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
Zie de [handleiding voor prestaties en tuning kopiëren](data-factory-copy-activity-performance.md) om meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.
