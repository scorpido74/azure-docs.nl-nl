---
title: Gegevens kopiëren naar of van Azure Data Lake Storage Gen1 met behulp van Data Factory | Microsoft Docs
description: Informatie over het kopiëren van gegevens uit ondersteunde brongegevens archieven naar Azure Data Lake Store, of van Data Lake Store naar ondersteunde Sink-archieven, met behulp van Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 4bb57190a310e1ea4b8e5c511f1acd90f53b8f09
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813464"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Gegevens kopiëren naar of van Azure Data Lake Storage Gen1 met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Azure Data Factory die u gebruikt:"]
> * [Versie 1:](v1/data-factory-azure-datalake-connector.md)
> * [Huidige versie](connector-azure-data-lake-store.md)

In dit artikel wordt beschreven hoe u gegevens kopieert van en naar Azure Data Lake Storage Gen1. Lees voor meer informatie over Azure Data Factory, de [inleidende artikel](introduction.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Data Lake Storage Gen1-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron-of sink-matrix](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)
- [GetMetadata-activiteit](control-flow-get-metadata-activity.md)

Met name met deze connector kunt u het volgende doen:

- Kopieer bestanden met behulp van een van de volgende verificatie methoden: Service-Principal of beheerde identiteiten voor Azure-resources.
- Kopieer bestanden als bestand of parser of Genereer bestanden met de [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Als u gegevens kopieert met behulp van de zelf-hostende Integration runtime, moet u de bedrijfs firewall zodanig `<ADLS account name>.azuredatalakestore.net` configureren `login.microsoftonline.com/<tenant>/oauth2/token` dat uitgaand verkeer naar en op poort 443 wordt toegestaan. De laatste is de Azure-beveiligings token service waarmee de Integration runtime moet communiceren om het toegangs token op te halen.

## <a name="get-started"></a>Aan de slag

> [!TIP]
> Zie [gegevens laden in azure data Lake Store](load-azure-data-lake-store.md)voor een overzicht van het gebruik van de Azure data Lake Store-connector.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Data Lake Store.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor de Azure Data Lake Store gekoppelde service:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De `type` eigenschap moet worden ingesteld op **AzureDataLakeStore**. | Ja |
| dataLakeStoreUri | Informatie over de Azure Data Lake Store-account. Deze informatie wordt een van de volgende indelingen: `https://[accountname].azuredatalakestore.net/webhdfs/v1` of `adl://[accountname].azuredatalakestore.net/`. | Ja |
| subscriptionId | De Azure-abonnements-ID waartoe het Data Lake Store-account behoort. | Vereist voor sink |
| resourceGroupName | De naam van de Azure-resource groep waartoe het Data Lake Store-account behoort. | Vereist voor sink |
| connectVia | De [integratieruntime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. U kunt de Azure Integration runtime of een zelf-hostende Integration runtime gebruiken als uw gegevens archief zich in een particulier netwerk bevindt. Als deze eigenschap niet is opgegeven, wordt de standaard Azure Integration runtime gebruikt. |Nee |

### <a name="use-service-principal-authentication"></a>Service-Principal-verificatie gebruiken

Als u Service-Principal-verificatie wilt gebruiken, registreert u een toepassings entiteit in Azure Active Directory en verleent u deze toegang tot Data Lake Store. Zie voor gedetailleerde stappen [Service-naar-serviceverificatie](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Noteer de volgende waarden, die u gebruikt voor het definiëren van de gekoppelde service:

- Toepassings-id
- Toepassingssleutel
- Tenant-id

>[!IMPORTANT]
> Verleen de service-principal de juiste machtiging in Data Lake Store:
>- **Als bron**: Ken in**toegang tot** **gegevens Verkenner** > ten minste **Lees** -en uitvoer machtiging toe voor het weer geven en kopiëren van de bestanden in mappen en submappen. U kunt ook **Lees** machtiging verlenen om één bestand te kopiëren. U kunt ervoor kiezen om toe te voegen aan **deze map en alle onderliggende items** voor recursief, en toe te voegen als **een toegangs machtiging en een standaard machtigings vermelding**. Er is geen vereiste op toegangs beheer op account niveau (IAM).
>- **Als Sink**: Geef in**toegang tot** **gegevens Verkenner** > ten minste de machtiging **schrijven en uitvoeren** op om onderliggende items in de map te maken. U kunt ervoor kiezen om toe te voegen aan **deze map en alle onderliggende items** voor recursief, en toe te voegen als **een toegangs machtiging en een standaard machtigings vermelding**. Als u een Azure Integration runtime gebruikt om te kopiëren (zowel de bron-als de Sink bevinden zich in de Cloud), moet u in IAM ten minste de rol van **lezer** toekennen om Data Factory de regio voor data Lake Store te laten detecteren. Als u deze IAM-rol wilt vermijden, maakt u expliciet [een Azure Integration runtime](create-azure-integration-runtime.md#create-azure-ir) met de locatie van data Lake Store. Als uw Data Lake Store bijvoorbeeld in Europa-west is, maakt u een Azure Integration runtime waarbij de locatie is ingesteld op ' Europa-west '. Koppel ze aan de Data Lake Store gekoppelde service, zoals wordt weer gegeven in het volgende voor beeld.

>[!NOTE]
>Als u mappen wilt weer geven vanaf de hoofdmap, moet u de machtiging voor de service-principal instellen op het **hoofd niveau met de machtiging ' uitvoeren '** . Dit geldt wanneer u het volgende gebruikt:
>- **Kopieer het hulp programma data** om Kopieer pijp lijn te schrijven.
>- **Data Factory gebruikers interface** voor het testen van de verbinding en het navigeren door mappen tijdens het ontwerpen.
>Als u problemen hebt met het verlenen van machtigingen op hoofd niveau, slaat u tijdens het ontwerpen de test verbinding over en voert u een paraent-pad in met toestemming toegekend en kiest u vervolgens om te bladeren vanuit het opgegeven pad. De Kopieer activiteit werkt als de Service-Principal is verleend met de juiste machtigingen voor de bestanden die moeten worden gekopieerd.

De volgende eigenschappen worden ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| servicePrincipalId | Opgeven van de toepassing client-ID. | Ja |
| servicePrincipalKey | Geef de sleutel van de toepassing. Markeer dit veld als een `SecureString` om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Geef de Tenant gegevens op, zoals de domein naam of Tenant-ID, waaronder uw toepassing zich bevindt. U kunt het ophalen van de muis in de rechterbovenhoek van de Azure-portal. | Ja |

**Voorbeeld:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Beheerde identiteiten gebruiken voor Azure-bronnen verificatie

Een data factory kan worden gekoppeld aan een [beheerde identiteit voor de Azure-resources](data-factory-service-identity.md), die staat voor deze specifieke data factory. U kunt deze beheerde identiteit rechtstreeks gebruiken voor Data Lake Store verificatie, vergelijkbaar met het gebruik van uw eigen service-principal. Hiermee kan deze toegewezen Factory toegang krijgen tot gegevens en deze kopiëren naar of van Data Lake Store.

Beheerde om identiteiten te gebruiken voor verificatie van de Azure-resources:

1. [Haal de Data Factory beheerde identiteits gegevens](data-factory-service-identity.md#retrieve-managed-identity) op door de waarde te kopiëren van de toepassings-id van de service-identiteit die samen met uw fabriek is gegenereerd.
2. Verleen de beheerde identiteit toegang tot Data Lake Store, op dezelfde manier als voor de Service-Principal, de volgende opmerkingen.

>[!IMPORTANT]
> Zorg ervoor dat u de juiste machtiging voor de data factory beheerde identiteit in Data Lake Store verleent:
>- **Als bron**: Ken in**toegang tot** **gegevens Verkenner** > ten minste **Lees** -en uitvoer machtiging toe voor het weer geven en kopiëren van de bestanden in mappen en submappen. U kunt ook **Lees** machtiging verlenen om één bestand te kopiëren. U kunt ervoor kiezen om toe te voegen aan **deze map en alle onderliggende items** voor recursief, en toe te voegen als **een toegangs machtiging en een standaard machtigings vermelding**. Er is geen vereiste op toegangs beheer op account niveau (IAM).
>- **Als Sink**: Geef in**toegang tot** **gegevens Verkenner** > ten minste de machtiging **schrijven en uitvoeren** op om onderliggende items in de map te maken. U kunt ervoor kiezen om toe te voegen aan **deze map en alle onderliggende items** voor recursief, en toe te voegen als **een toegangs machtiging en een standaard machtigings vermelding**. Als u een Azure Integration runtime gebruikt om te kopiëren (zowel de bron-als de Sink bevinden zich in de Cloud), moet u in IAM ten minste de rol van **lezer** toekennen om Data Factory de regio voor data Lake Store te laten detecteren. Als u deze IAM-rol wilt vermijden, maakt u expliciet [een Azure Integration runtime](create-azure-integration-runtime.md#create-azure-ir) met de locatie van data Lake Store. Koppel ze aan de Data Lake Store gekoppelde service, zoals wordt weer gegeven in het volgende voor beeld.

>[!NOTE]
>Als u mappen wilt weer geven vanaf de hoofdmap, moet u de machtiging van de beheerde identiteit die wordt verleend aan **op hoofd niveau met de machtiging ' uitvoeren '** instellen. Dit geldt wanneer u het volgende gebruikt:
>- **Kopieer het hulp programma data** om Kopieer pijp lijn te schrijven.
>- **Data Factory gebruikers interface** voor het testen van de verbinding en het navigeren door mappen tijdens het ontwerpen.
>Als u problemen hebt met het verlenen van machtigingen op hoofd niveau, slaat u tijdens het ontwerpen de test verbinding over en voert u een bovenliggend pad in met toestemming toegekend en kiest u vervolgens om te bladeren vanuit het opgegeven pad. De Kopieer activiteit werkt als de Service-Principal is verleend met de juiste machtigingen voor de bestanden die moeten worden gekopieerd.

In Azure Data Factory hoeft u geen eigenschappen op te geven naast de algemene Data Lake Store informatie in de gekoppelde service.

**Voorbeeld:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. 

- Raadpleeg voor **Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indeling**, de sectie [Parquet, gescheiden tekst, JSON, AVRO en binaire indeling gegevensset](#format-based-dataset) .
- Raadpleeg de sectie [andere indelings gegevensset](#other-format-dataset) voor andere indelingen, zoals **Orc-indeling**.

### <a name="format-based-dataset"></a>Parquet, gescheiden tekst, JSON, AVRO en binaire indeling gegevensset

Als u gegevens wilt kopiëren naar en van **Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indeling**, raadpleegt u [Parquet-indeling](format-parquet.md), [tekst indeling met scheidings tekens](format-delimited-text.md), [Avro-indeling](format-avro.md) en [binaire-indelings](format-binary.md) artikel op op indeling gebaseerde gegevensset en ondersteunde instellingen .
De volgende eigenschappen worden ondersteund voor Azure data Lake Store gen1 onder `location` instellingen in de op indeling gebaseerde gegevensset:

| Eigenschap   | Description                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| Type       | De eigenschap type onder `location` in de gegevensset moet worden ingesteld op **AzureDataLakeStoreLocation**. | Ja      |
| folderPath | Het pad naar een map. Als u een Joker teken wilt gebruiken om mappen te filteren, slaat u deze instelling over en geeft u deze op in de activiteiten bron instellingen. | Nee       |
| fileName   | De bestands naam onder de opgegeven folderPath. Als u een Joker teken wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u deze op in de activiteiten bron instellingen. | Nee       |

> [!NOTE]
>
> De **AzureDataLakeStoreFile** -gegevensset met Parquet of tekst indeling die in de volgende sectie wordt vermeld, wordt nog steeds ondersteund als voor Copy-, lookup-en GetMetadata-activiteiten voor achterwaartse compatibiliteit. Maar dit werkt niet met de functie gegevens stroom toewijzen. U wordt aangeraden dit nieuwe model verder te gebruiken. Deze nieuwe typen worden gegenereerd door de gebruikers interface van de Data Factory-ontwerp functie.

**Voorbeeld:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Gegevensset voor andere indeling

Als u gegevens wilt kopiëren naar en van Azure Data Lake Store gen1 in **Orc-indeling**, worden de volgende eigenschappen ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de DataSet moet worden ingesteld op **AzureDataLakeStoreFile**. |Ja |
| folderPath | Pad naar de map in Data Lake Store. Indien niet opgegeven, wordt deze verwijst naar de hoofdmap. <br/><br/>Het Joker teken filter wordt ondersteund. Toegestane joker tekens zijn `*` (komt overeen met nul of meer tekens `?` ) en (komt overeen met nul of één teken). Gebruik `^` om te escapen als uw werkelijke mapnaam een Joker teken of escape-teken bevat. <br/><br/>Bijvoorbeeld: root folder/submap/. Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). |Nee |
| fileName | De naam of het Joker teken filter voor de bestanden onder het opgegeven folderPath. Als u een waarde voor deze eigenschap niet opgeeft, wordt de gegevensset verwijst naar alle bestanden in de map. <br/><br/>Voor het filter zijn `*` de toegestane joker tekens (komt overeen met nul of meer tekens) en `?` (komt overeen met nul of één teken).<br/>-Voorbeeld 1: `"fileName": "*.csv"`<br/>-Voorbeeld 2: `"fileName": "???20180427.txt"`<br/>Gebruik `^` om te escapen als uw werkelijke bestands naam een Joker teken of escape-teken bevat.<br/><br/>Als er geen bestands naam is opgegeven voor een uitvoer-gegevensset en **preserveHierarchy** niet is opgegeven in de Sink van de activiteit, genereert de Kopieer activiteit automatisch de bestands naam met het volgende patroon: "*Gegevens. [GUID van run-ID van activiteit]. [GUID if FlattenHierarchy]. [indeling indien geconfigureerd]. [compressie indien geconfigureerd]* ', bijvoorbeeld ' data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt. gz '. Als u van een bron in tabel vorm kopieert met behulp van een tabel naam in plaats van een query, is het naam patroon ' *[tabel naam]. [ indeling]. [compressie indien geconfigureerd]* ', bijvoorbeeld ' mytable. csv '. |Nee |
| modifiedDatetimeStart | Bestanden filteren op basis van het kenmerk dat het laatst is gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik `modifiedDatetimeStart` ligt `modifiedDatetimeEnd`tussen en. De tijd wordt toegepast op de UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br/><br/> De algehele prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter wilt uitvoeren met enorme hoeveel heden bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast. Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar null is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter is dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd. Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd.| Nee |
| modifiedDatetimeEnd | Bestanden filteren op basis van het kenmerk dat het laatst is gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik `modifiedDatetimeStart` ligt `modifiedDatetimeEnd`tussen en. De tijd wordt toegepast op de UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br/><br/> De algehele prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter wilt uitvoeren met enorme hoeveel heden bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast. Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar null is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter is dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd. Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd.| Nee |
| format | Als u bestanden wilt kopiëren als zich bevindt tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie opmaak over in de gegevensset voor invoer en uitvoer.<br/><br/>Als u bestanden wilt parseren of genereren met een specifieke indeling, worden de volgende typen bestands indelingen ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**en **ParquetFormat**. Stel de **type** eigenschap onder **indeling** op een van deze waarden. Zie voor meer informatie de [tekstindeling](supported-file-formats-and-compression-codecs.md#text-format), [JSON-indeling](supported-file-formats-and-compression-codecs.md#json-format), [Avro-indeling](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-indeling](supported-file-formats-and-compression-codecs.md#orc-format), en [Parquet-indeling ](supported-file-formats-and-compression-codecs.md#parquet-format) secties. |Nee (alleen voor binaire kopie-scenario) |
| compression | Geef het type en het niveau van compressie voor de gegevens. Zie voor meer informatie, [ondersteunde indelingen en codecs voor compressie](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Ondersteunde typen zijn **GZip**, **Deflate**, **BZip2**, en **ZipDeflate**.<br/>Ondersteunde niveaus zijn **optimale** en **snelst**. |Nee |


>[!TIP]
>Alle bestanden in een map wilt kopiëren, geef **folderPath** alleen.<br>Als u één bestand met een bepaalde naam wilt kopiëren, geeft u **FolderPath** op met een mappen onderdeel en **filename** met een bestands naam.<br>Als u een subset van bestanden onder een map wilt kopiëren, geeft u **FolderPath** op met een deel van een map en een **Bestands naam** met een Joker teken filter. 

**Voorbeeld:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, Zie [pijp lijnen](concepts-pipelines-activities.md). In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Azure Data Lake Store bron en Sink.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store als bron

- Als u wilt kopiëren van **Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indeling**, raadpleegt u de sectie [Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indelings bron](#format-based-source) .
- Als u wilt kopiëren van andere indelingen, zoals de **indeling Orc**, raadpleegt u de sectie [andere indelings bron](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indelings bron

Als u gegevens wilt kopiëren uit **Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indeling**, raadpleegt u [Parquet-indeling](format-parquet.md), [tekst indeling met scheidings tekens](format-delimited-text.md), [Avro-indeling](format-avro.md) en [binaire indeling](format-binary.md) artikel op op indeling gebaseerde Kopieer activiteit bron en ondersteund Instellingen.  De volgende eigenschappen worden ondersteund voor Azure data Lake Store gen1 onder `storeSettings` instellingen in de indelings-gebaseerde Kopieer Bron:

| Eigenschap                 | Description                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| Type                     | De eigenschap type onder `storeSettings` moet worden ingesteld op **AzureDataLakeStoreReadSetting**. | Ja                                           |
| recursive                | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen voor de opgegeven map. Als recursief is ingesteld op True en de Sink een archief op basis van bestanden is, wordt een lege map of submap niet gekopieerd of gemaakt bij de sink. Toegestane waarden zijn **waar** (standaard) en **false**. | Nee                                            |
| wildcardFolderPath       | Het mappad met Joker tekens om de bron mappen te filteren. <br>Toegestane joker tekens zijn `*` (komt overeen met nul of meer tekens `?` ) en (komt overeen met nul of één teken). Gebruik `^` om te escapen als uw werkelijke mapnaam een Joker teken of escape-teken bevat. <br>Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Nee                                            |
| wildcardFileName         | De naam van het bestand met Joker tekens onder de opgegeven folderPath/wildcardFolderPath voor het filteren van bron bestanden. <br>Toegestane joker tekens zijn `*` (komt overeen met nul of meer tekens `?` ) en (komt overeen met nul of één teken). Gebruik `^` om te escapen als uw werkelijke mapnaam een Joker teken of escape-teken bevat. Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Ja als `fileName` niet is opgegeven in de gegevensset |
| modifiedDatetimeStart    | Bestanden filteren op basis van het kenmerk dat het laatst is gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik `modifiedDatetimeStart` ligt `modifiedDatetimeEnd`tussen en. De tijd wordt toegepast op de UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen bestands kenmerk filter op de gegevensset wordt toegepast. Wanneer `modifiedDatetimeStart` heeft een datum/tijd `modifiedDatetimeEnd` -waarde, maar null is, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter is dan of gelijk is aan de datum/tijd-waarde zijn geselecteerd. Wanneer `modifiedDatetimeEnd` heeft een datum/tijd `modifiedDatetimeStart` -waarde, maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is, kleiner zijn dan de waarde voor datum/tijd. | Nee                                            |
| modifiedDatetimeEnd      | Hetzelfde als hierboven.                                               | Nee                                            |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig met een opslag archief moet worden verbonden. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee                                            |

> [!NOTE]
> Voor Parquet of gescheiden tekst indeling wordt de bron van de Kopieer activiteit **AzureDataLakeStoreSource** die in de volgende sectie wordt vermeld, nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden dit nieuwe model verder te gebruiken. Deze nieuwe typen worden gegenereerd door de gebruikers interface van de Data Factory-ontwerp functie.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Andere indelings bron

Als u gegevens wilt kopiëren uit Azure Data Lake Store gen1 in **Orc-indeling**, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De `type` eigenschap van de bron van de Kopieer activiteit moet worden ingesteld op **AzureDataLakeStoreSource**. |Ja |
| recursive | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen voor de opgegeven map. Wanneer `recursive` is ingesteld op True en de Sink een archief op basis van bestanden is, wordt een lege map of submap niet gekopieerd of gemaakt bij de sink. Toegestane waarden zijn **waar** (standaard) en **false**. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met het gegevens archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store als sink

- Als u wilt kopiëren naar **Parquet, tekst met scheidings tekens, JSON, AVRO en binary**, verwijzen wij u naar [Parquet, gescheiden tekst, JSON, AVRO en de sectie Sink voor binaire indeling](#format-based-sink) .
- Als u wilt kopiëren naar andere indelingen, zoals de **Orc/JSON-indeling**, raadpleegt u de sectie een [andere indelings Sink](#other-format-sink) .

#### <a name="format-based-sink"></a>Parquet, tekst met scheidings tekens, JSON, AVRO en binaire indelings Sink

Als u gegevens wilt kopiëren naar **Parquet, tekst met scheidings tekens, JSON-, AVRO-en binaire indeling**, raadpleegt u [Parquet-indeling](format-parquet.md), [tekst indeling met scheidings tekens](format-delimited-text.md), [Avro-indeling](format-avro.md) en [binaire-indeling](format-binary.md) artikel op op indeling gebaseerde kopie van de Kopieer activiteit en wordt ondersteund Instellingen.  De volgende eigenschappen worden ondersteund voor Azure data Lake Store gen1 onder `storeSettings` instellingen in het op indeling gebaseerde Kopieer-Sink:

| Eigenschap                 | Description                                                  | Vereist |
| ------------------------ | ------------------------------------------------------------ | -------- |
| Type                     | De eigenschap type onder `storeSettings` moet worden ingesteld op **AzureDataLakeStoreWriteSetting**. | Ja      |
| copyBehavior             | Definieert het gedrag kopiëren wanneer de bron bestanden vanuit een bestandsgebaseerde gegevensarchief is.<br/><br/>Toegestane waarden zijn:<br/><b>-PreserveHierarchy (standaard)</b>: Hiermee behoudt u de bestands hiërarchie in de doelmap. Het relatieve pad van het bron bestand naar de bronmap is identiek aan het relatieve pad van het doel bestand naar de doelmap.<br/><b>-FlattenHierarchy</b>: Alle bestanden van de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>-MergeFiles</b>: Alle bestanden van de bronmap worden samengevoegd met één bestand. Als de bestandsnaam is opgegeven, is de naam van het samengevoegde de opgegeven naam. Anders is de naam van een automatisch gegenereerde bestand. | Nee       |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met het gegevens archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee       |

> [!NOTE]
> Voor een Parquet-of gescheiden tekst indeling wordt de Sink voor kopieer activiteit **AzureDataLakeStoreSink** die in de volgende sectie wordt vermeld, nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden dit nieuwe model verder te gebruiken. Deze nieuwe typen worden gegenereerd door de gebruikers interface van de Data Factory-ontwerp functie.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureDataLakeStoreWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Andere opmaak Sink

Als u gegevens wilt kopiëren naar Azure Data Lake Store gen1 in **Orc-indeling**, worden de volgende eigenschappen ondersteund in de sectie **sink** :

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De `type` eigenschap van de Sink voor kopieer activiteiten moet worden ingesteld op **AzureDataLakeStoreSink**. |Ja |
| copyBehavior | Definieert het gedrag kopiëren wanneer de bron bestanden vanuit een bestandsgebaseerde gegevensarchief is.<br/><br/>Toegestane waarden zijn:<br/><b>-PreserveHierarchy (standaard)</b>: Hiermee behoudt u de bestands hiërarchie in de doelmap. Het relatieve pad van het bron bestand naar de bronmap is identiek aan het relatieve pad van het doel bestand naar de doelmap.<br/><b>-FlattenHierarchy</b>: Alle bestanden van de bronmap bevinden zich in het eerste niveau van de doelmap. De doelbestanden hebben automatisch gegenereerde namen. <br/><b>-MergeFiles</b>: Alle bestanden van de bronmap worden samengevoegd met één bestand. Als de bestandsnaam is opgegeven, is de naam van het samengevoegde de opgegeven naam. Anders wordt de bestands naam automatisch gegenereerd. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met het gegevens archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Voor beelden van map-en bestands filter

In deze sectie wordt het resulterende gedrag van het mappad en de bestands naam met Joker teken filters beschreven.

| folderPath | fileName | recursive | De structuur van de bronmap en het filter resultaat ( **vetgedrukte** bestanden worden opgehaald)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Leeg, standaard instelling gebruiken) | false | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Leeg, standaard instelling gebruiken) | true | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Voor beelden van gedrag van de Kopieer bewerking

In deze sectie wordt het resulterende gedrag van de Kopieer bewerking voor verschillende combi `recursive` Naties `copyBehavior` van en waarden beschreven.

| recursive | copyBehavior | Structuur van de gegevensbron | Resulterende doel |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | De doel-Map1 is gemaakt met dezelfde structuur als de bron:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Het doel Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Het doel Map1 is gemaakt met de volgende structuur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1 + Bestand2 + File3 + File4 + File5 inhoud worden samengevoegd in één bestand, met een automatisch gegenereerde bestands naam. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Het doel Map1 is gemaakt met de volgende structuur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Het doel Map1 is gemaakt met de volgende structuur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor bestand2<br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Het doel Map1 is gemaakt met de volgende structuur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;De inhoud van bestand1 + Bestand2 wordt samengevoegd met een bestand met automatisch gegenereerde bestands naam. automatisch gegenereerde naam voor File1<br/><br/>Subfolder1 met File3, File4 en File5 worden niet opgehaald. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Acl's voor Data Lake Storage Gen2 behouden

Zie [acl's van data Lake Storage gen1 behouden](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)als u de acl's (toegangs beheer lijsten) samen met gegevens bestanden wilt repliceren wanneer u een upgrade uitvoert van Data Lake Storage Gen1 naar Data Lake Storage Gen2.

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Meer informatie over [bron transformatie](data-flow-source.md) en [sink-trans formatie](data-flow-sink.md) vindt u in de functie gegevens stroom toewijzen.

## <a name="next-steps"></a>Volgende stappen

Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md##supported-data-stores-and-formats).
