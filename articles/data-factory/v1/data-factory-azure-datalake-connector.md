---
title: Gegevens kopiëren van en naar Azure Data Lake Storage Gen1
description: Meer informatie over het kopiëren van gegevens van en naar Data Lake Store met Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b6a60536bae6fbedf01eda7aa340e90ced58e004
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281598"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Gegevens van en naar Data Lake Storage Gen1 kopiëren met Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-azure-datalake-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Azure Data Lake Storage Gen1-connector in V2](../connector-azure-data-lake-store.md)als u de huidige versie van de Data Factory-service gebruikt.

In dit artikel wordt uitgelegd hoe u Activiteit kopiëren in Azure Data Factory gebruiken om gegevens van en naar Azure Data Lake Storage Gen1 (voorheen bekend als Azure Data Lake Store) te verplaatsen. Het bouwt voort op het artikel [Gegevensverplaatsingsactiviteiten,](data-factory-data-movement-activities.md) een overzicht van gegevensverplaatsing met Kopieeractiviteit.

## <a name="supported-scenarios"></a>Ondersteunde scenario's
U gegevens **uit azure data lake store** kopiëren naar de volgende gegevensarchieven:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

U gegevens uit de volgende gegevensarchieven kopiëren **naar Azure Data Lake Store:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Maak een Data Lake Store-account voordat u een pijplijn maakt met Kopieeractiviteit. Zie [Aan de slag met Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md)voor meer informatie.

## <a name="supported-authentication-types"></a>Ondersteunde verificatietypen
De Data Lake Store-connector ondersteunt deze verificatietypen:
* Verificatie van service-principal
* Verificatie van gebruikersreferenties (OAuth)

We raden u aan servicehoofdverificatie te gebruiken, vooral voor een geplande gegevenskopie. Tokenverloopgedrag kan optreden met verificatie van gebruikersreferenties. Zie de sectie [Gekoppelde serviceeigenschappen](#linked-service-properties) voor configuratiedetails.

## <a name="get-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens van/naar een Azure Data Lake Store verplaatst met behulp van verschillende hulpprogramma's/API's.

De eenvoudigste manier om een pijplijn te maken om gegevens te kopiëren, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Een pijplijn maken met wizard Kopiëren voor](data-factory-copy-data-wizard-tutorial.md)een zelfstudie over het maken van een pijplijn.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak een **gegevensfabriek**. Een gegevensfabriek kan een of meer pijplijnen bevatten.
2. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek. Als u bijvoorbeeld gegevens kopieert van een Azure blob-opslag naar een Azure Data Lake Store, maakt u twee gekoppelde services om uw Azure-opslagaccount en Azure Data Lake-opslag te koppelen aan uw gegevensfabriek. Zie [sectie gekoppelde service-eigenschappen](#linked-service-properties) die specifiek zijn voor Azure Data Lake Store.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven. In het voorbeeld dat in de laatste stap wordt genoemd, maakt u een gegevensset om de blobcontainer en -map op te geven die de invoergegevens bevat. En u maakt een andere gegevensset om de map en het bestandspad op te geven in het Data Lake-archief met de gegevens die zijn gekopieerd uit de blob-opslag. Zie sectie [gegevensseteigenschappen](#dataset-properties) voor gegevensseteigenschappen die specifiek zijn voor Azure Data Lake Store.
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt. In het eerder genoemde voorbeeld gebruikt u BlobSource als bron en AzureDataLakeStoreSink als een sink voor de kopieeractiviteit. Als u van Azure Data Lake Store naar Azure Blob Storage kopieert, gebruikt u AzureDataLakeStoreSource en BlobSink in de kopieeractiviteit. Zie [sectie activiteitseigenschappen kopiëren](#copy-activity-properties) voor eigenschappen voor kopiëren die specifiek zijn voor Azure Data Lake Store. Klik op de koppeling in de vorige sectie voor uw gegevensarchief voor meer informatie over het gebruik van een gegevensarchief als bron of gootsteen.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling. Zie [JSON-voorbeelden](#json-examples-for-copying-data-to-and-from-data-lake-store) sectie van dit artikel voor voorbeelden van JSON-instellingen voor entiteiten in gegevensfabrieken die worden gebruikt om gegevens naar/vanuit een Azure Data Lake Store te kopiëren.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de Data Lake Store.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
Een gekoppelde service koppelt een gegevensarchief aan een gegevensfabriek. U maakt een gekoppelde service van het type **AzureDataLakeStore** om uw Data Lake Store-gegevens te koppelen aan uw gegevensfabriek. In de volgende tabel worden JSON-elementen beschreven die specifiek zijn voor gekoppelde services in data lake store. U kiezen tussen serviceprincipal en verificatie van gebruikersreferenties.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| **Type** | De eigenschap type moet zijn ingesteld op **AzureDataLakeStore**. | Ja |
| **dataLakeStoreUri** | Informatie over het Azure Data Lake Store-account. Deze informatie neemt een van `https://[accountname].azuredatalakestore.net/webhdfs/v1` `adl://[accountname].azuredatalakestore.net/`de volgende formaten: of . | Ja |
| **abonnementId** | Azure-abonnements-ID waartoe het Data Lake Store-account behoort. | Vereist voor gootsteen |
| **resourceGroupName** | De naam van de Azure-brongroep waartoe het Data Lake Store-account behoort. | Vereist voor gootsteen |

### <a name="service-principal-authentication-recommended"></a>Serviceprincipal-verificatie (aanbevolen)
Als u servicehoofdverificatie wilt gebruiken, registreert u een toepassingsentiteit in Azure Active Directory (Azure AD) en verleent u deze toegang tot De Gegevenslake store. Zie [Service-to-service-verificatie voor](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)gedetailleerde stappen . Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:
* Toepassings-id
* Toepassingssleutel
* Tenant-id

> [!IMPORTANT]
> Zorg ervoor dat u de serviceprincipal de juiste toestemming verleent in Azure Data Lake Store:
>- **Als u Data Lake Store als bron wilt gebruiken,** verleent u ten minste toestemming voor **lees + uitvoergegevens** om de inhoud van een map te vermelden en te kopiëren, of **Geeft u** toestemming om één bestand te kopiëren. Geen vereiste voor toegangsbeheer op accountniveau.
>- **Als u Data Lake Store als gootsteen wilt gebruiken,** geeft u ten minste toestemming voor **gegevenstoegang voor schrijven + uitvoeren** om onderliggende items in de map te maken. En als u Azure IR gebruikt om kopiëren (zowel bron als gootsteen in de cloud) te machtigen, om Data Factory de regio van Data Lake Store te laten detecteren, verleent u ten minste de rol **van Reader** in account access control (IAM). Als u deze IAM-rol wilt vermijden, [geeft u uitvoering opLocatie](data-factory-data-movement-activities.md#global) met de locatie van uw Data Lake Store in kopieeractiviteit.
>- Als u **Wizard Kopiëren gebruikt om pijplijnen te maken,** verleent u ten minste een **Reader-rol** in accounttoegangsbeheer (IAM). Geef ook ten minste **Lees + Voer** toestemming toe aan uw Data Lake Store-root ("/") en zijn kinderen. Anders ziet u mogelijk het bericht 'De opgegeven referenties zijn ongeldig'.

Gebruik serviceprincipal-verificatie door de volgende eigenschappen op te geven:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| **servicePrincipalId** | Geef de client-id van de toepassing op. | Ja |
| **servicePrincipalKey** | Geef de sleutel van de toepassing op. | Ja |
| **Huurder** | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. U deze ophalen door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Ja |

**Voorbeeld: Serviceprincipal-verificatie**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Verificatie van gebruikersreferenties
U ook verificatie van gebruikersreferenties gebruiken om van of naar Data Lake Store te kopiëren door de volgende eigenschappen op te geven:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| **Vergunning** | Klik op de knop **Autoriseren** in de editor voor gegevensfabrieksonderdelen en voer uw referentie in die de automatisch gegenereerde autorisatie-URL aan deze eigenschap toewijst. | Ja |
| **Sessionid** | OAuth-sessie-ID van de OAuth-autorisatiesessie. Elke sessie-ID is uniek en kan slechts één keer worden gebruikt. Deze instelling wordt automatisch gegenereerd wanneer u de Data Factory Editor gebruikt. | Ja |

> [!IMPORTANT]
> Zorg ervoor dat u de gebruiker de juiste toestemming geeft in Azure Data Lake Store:
>- **Als u Data Lake Store als bron wilt gebruiken,** verleent u ten minste toestemming voor **lees + uitvoergegevens** om de inhoud van een map te vermelden en te kopiëren, of **Geeft u** toestemming om één bestand te kopiëren. Geen vereiste voor toegangsbeheer op accountniveau.
>- **Als u Data Lake Store als gootsteen wilt gebruiken,** geeft u ten minste toestemming voor **gegevenstoegang voor schrijven + uitvoeren** om onderliggende items in de map te maken. En als u Azure IR gebruikt om kopiëren (zowel bron als gootsteen in de cloud) te machtigen, om Data Factory de regio van Data Lake Store te laten detecteren, verleent u ten minste de rol **van Reader** in account access control (IAM). Als u deze IAM-rol wilt vermijden, [geeft u uitvoering opLocatie](data-factory-data-movement-activities.md#global) met de locatie van uw Data Lake Store in kopieeractiviteit.
>- Als u **Wizard Kopiëren gebruikt om pijplijnen te maken,** verleent u ten minste een **Reader-rol** in accounttoegangsbeheer (IAM). Geef ook ten minste **Lees + Voer** toestemming toe aan uw Data Lake Store-root ("/") en zijn kinderen. Anders ziet u mogelijk het bericht 'De opgegeven referenties zijn ongeldig'.

**Voorbeeld: Verificatie van gebruikersreferenties**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Token vervaldatum
De autorisatiecode die u genereert met de knop **Autoriseren,** vervalt na een bepaalde tijd. Het volgende bericht betekent dat het verificatietoken is verlopen:

Fout in de referentiebewerking: invalid_grant - AADSTS70002: Fout bij het valideren van referenties. AADSTS70008: De verstrekte toegangssubsidie is verlopen of ingetrokken. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlatie-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z.

In de volgende tabel worden de vervaldatums van verschillende typen gebruikersaccounts weergegeven:

| Gebruikerstype | Verloopt na |
|:--- |:--- |
| Gebruikersaccounts *worden niet* beheerd door Azure @hotmail.com @live.comActive Directory (bijvoorbeeld of ) |12 uur |
| Gebruikersaccounts beheerd door Azure Active Directory |14 dagen na de laatste slice run <br/><br/>90 dagen, als een segment op basis van een oauth-gebaseerde gekoppelde service ten minste eenmaal per 14 dagen wordt uitgevoerd |

Als u uw wachtwoord wijzigt vóór de vervaldatum van het token, vervalt het token onmiddellijk. U ziet het bericht eerder vermeld in deze sectie.

U het account opnieuw autoriseren met de knop **Autoriseren** wanneer het token verloopt om de gekoppelde service opnieuw te implementeren. U ook programmatisch waarden genereren voor de **sessieId-** en **autorisatie-eigenschappen** met behulp van de volgende code:


```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Zie de onderwerpen [AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)en [AuthorizationSessionGetResponse Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) voor meer informatie over de klassen Gegevensfabriek die in de code worden gebruikt. Voeg een verwijzing `2.9.10826.1824` `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` toe `WindowsFormsWebAuthenticationDialog` naar de versie van voor de klasse die in de code wordt gebruikt.

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

**Symptoom:** Wanneer u gegevens kopieert **naar** Azure Data Lake Store, mislukt uw kopieeractiviteit met de volgende fout:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Oorzaak:** Er zijn 2 mogelijke redenen:

1. De `resourceGroupName` gekoppelde `subscriptionId` service voor en/of opgegeven in azure data lake store is onjuist.
2. De gebruiker of de serviceprincipal heeft niet de benodigde toestemming.

**Resolutie:**

1. Zorg ervoor `subscriptionId` `resourceGroupName` dat de en `typeProperties` u opgeven in de gekoppelde dienst zijn inderdaad degenen die uw data lake account behoort tot.

2. Zorg ervoor dat u ten minste **Reader** rol toe te kennen aan de gebruiker of service principal op de data lake account. Hier is hoe het te maken:

    1. Ga naar de Azure-portal > uw Data Lake Store-account
    2. Klik op **Toegangsbesturingselement (IAM)** op het blad van de Data Lake Store
    3. Klik **op Roltoewijzing toevoegen**
    4. **Rol instellen** als **lezer**en selecteer de gebruiker of de serviceprincipal die u gebruikt voor kopiëren om toegang te verlenen

3. Als u reader-rol **Reader** niet wilt toekennen aan de gebruiker of serviceprincipal, is het alternatief om [expliciet een uitvoeringslocatie](data-factory-data-movement-activities.md#global) op te geven in kopieeractiviteit met de locatie van uw Data Lake Store. Voorbeeld:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Als u een gegevensset wilt opgeven om invoergegevens in een Data Lake Store weer te geven, stelt u de **eigenschap type** van de gegevensset in op **AzureDataLakeStore**. Stel de eigenschap **linkedServiceName** van de gegevensset in op de naam van de gekoppelde service van de Data Lake Store. Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met JSON-secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties van een gegevensset in JSON, zoals **structuur,** **beschikbaarheid**en **beleid,** zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL-database, Azure blob en Azure-tabel, bijvoorbeeld). De sectie **typeEigenschappen** is verschillend voor elk type gegevensset en biedt informatie zoals locatie en indeling van de gegevens in het gegevensarchief.

De sectie **typeEigenschappen** voor een gegevensset van het type **AzureDataLakeStore** bevat de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| **folderPath** |Pad naar de container en map in Data Lake Store. |Ja |
| **fileName** |Naam van het bestand in Azure Data Lake Store. De eigenschap **fileName** is optioneel en hoofdlettergevoelig. <br/><br/>Als u **bestandsnaam**opgeeft, werkt de activiteit (inclusief Kopiëren) in het specifieke bestand.<br/><br/>Wanneer **bestandsnaam** niet is opgegeven, bevat Kopiëren alle bestanden in **mapPath** in de invoergegevensset.<br/><br/>Wanneer **bestandsnaam** niet is opgegeven voor een uitvoergegevensset en **behoudenHiërarchie** niet is opgegeven in `Data._Guid_.txt`activiteitssink, is de naam van het gegenereerde bestand in de indeling . Bijvoorbeeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Nee |
| **partitionedBy** |De eigenschap **partitionedBy** is optioneel. U het gebruiken om een dynamisch pad en bestandsnaam op te geven voor tijdreeksgegevens. **MapPath** kan bijvoorbeeld worden geparameteriseerd voor elk uur aan gegevens. Zie De eigenschap partitionedBy voor meer informatie en voorbeelden. |Nee |
| **Formaat** | De volgende formattypes worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**en **ParquetFormat**. Stel de **eigenschap type** onder **opmaak** in op een van deze waarden. Zie voor meer informatie de secties [Tekstindeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-indeling,](data-factory-supported-file-and-compression-formats.md#avro-format) [ORC-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parketindeling](data-factory-supported-file-and-compression-formats.md#parquet-format) in de [bestands- en compressieindelingen die worden ondersteund door](data-factory-supported-file-and-compression-formats.md) het artikel Azure Data Factory. <br><br> Als u bestanden 'as-is' wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de `format` sectie over in definities van zowel invoer- als uitvoergegevenssets. |Nee |
| **Compressie** | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen zijn **GZip,** **Deflate,** **BZip2**en **ZipDeflate**. Ondersteunde niveaus zijn **optimaal** en **snelst.** Zie [Bestands- en compressieindelingen die worden ondersteund door Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee |

### <a name="the-partitionedby-property"></a>De eigenschap partitionedBy
U dynamische **mapPath-** en **fileName-eigenschappen** opgeven voor tijdreeksgegevens met de eigenschap **partitionedBy,** de functies Data Factory en systeemvariabelen. Zie het artikel [Azure Data Factory - functies en systeemvariabelen](data-factory-functions-variables.md) voor meer informatie.


In het volgende `{Slice}` voorbeeld wordt vervangen door de `SliceStart` waarde van de`yyyyMMddHH`systeemvariabele Data Factory in de opgegeven indeling ( ). De `SliceStart` naam verwijst naar de begintijd van het segment. De `folderPath` eigenschap is verschillend voor `wikidatagateway/wikisampledataout/2014100103` elk `wikidatagateway/wikisampledataout/2014100104`segment, zoals in of .

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

In het volgende voorbeeld worden het jaar, `SliceStart` de maand, de dag en de `folderPath` `fileName` tijd ervan geëxtraheerd in afzonderlijke variabelen die worden gebruikt door de eigenschappen en eigenschappen:
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Zie de [gegevenssets in azure datafabriek](data-factory-create-datasets.md) en [gegevensfabriek planning](data-factory-scheduling-and-execution.md) en uitvoering voor meer informatie over gegevenssets, planningen en segmenten in de tijdreeksen.


## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleid zijn beschikbaar voor alle soorten activiteiten.

De eigenschappen die beschikbaar zijn in de sectie **typeEigenschappen** van een activiteit, variëren per activiteitstype. Voor een kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

**AzureDataLakeStoreSource** ondersteunt de volgende eigenschap in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| **Recursieve** |Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. |True (standaardwaarde), Onwaar |Nee |

**AzureDataLakeStoreSink** ondersteunt de volgende eigenschappen in de sectie **typeEigenschappen:**

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| **copyBehavior** |Hiermee geeft u het kopieergedrag op. |<b>Hiërarchie behouden:</b>hiermee blijft de bestandshiërarchie in de doelmap behouden. Het relatieve pad van bronbestand naar bronmap is identiek aan het relatieve pad van doelbestand naar doelmap.<br/><br/><b>AfvlakkingHiërarchie:</b>Alle bestanden uit de bronmap worden gemaakt in het eerste niveau van de doelmap. De doelbestanden worden gemaakt met automatisch gegenereerde namen.<br/><br/><b>MergeFiles</b>: hiermee worden alle bestanden van de bronmap samengevoegd tot één bestand. Als de bestands- of blobnaam is opgegeven, is de samengevoegde bestandsnaam de opgegeven naam. Anders wordt de bestandsnaam automatisch gegenereerd. |Nee |

### <a name="recursive-and-copybehavior-examples"></a>recursieve en copyBehavior voorbeelden
In deze sectie wordt het resulterende gedrag van de bewerking Kopiëren beschreven voor verschillende combinaties van recursieve en copyBehavior-waarden.

| Recursieve | copyBehavior | Resulterend gedrag |
| --- | --- | --- |
| waar |behoudenHiërarchie |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap Map1 wordt gemaakt met dezelfde structuur als de bron<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5. |
| waar |afvlakkenHiërarchie |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand5 |
| waar |mergeFiles |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap1 wordt gemaakt met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5-inhoud wordt samengevoegd tot één bestand met automatisch gegenereerde bestandsnaam |
| false |behoudenHiërarchie |Voor een bronmap Map1 met de volgende structuur: <br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/><br/><br/>Submap1 met Bestand3, File4 en File5 worden niet opgehaald. |
| false |afvlakkenHiërarchie |Voor een bronmap Map1 met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch gegenereerde naam voor Bestand2<br/><br/><br/>Submap1 met Bestand3, File4 en File5 worden niet opgehaald. |
| false |mergeFiles |Voor een bronmap Map1 met de volgende structuur:<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bestand5<br/><br/>de doelmap Map1 wordt gemaakt met de volgende structuur<br/><br/>Map1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2-inhoud wordt samengevoegd tot één bestand met automatisch gegenereerde bestandsnaam. automatisch gegenereerde naam voor Bestand1<br/><br/>Submap1 met Bestand3, File4 en File5 worden niet opgehaald. |

## <a name="supported-file-and-compression-formats"></a>Ondersteunde indelingen voor bestanden en compressie
Zie de [bestands- en compressieindelingen in](data-factory-supported-file-and-compression-formats.md) het artikel Azure Data Factory voor meer informatie.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>JSON-voorbeelden voor het kopiëren van gegevens van en naar Data Lake Store
In de volgende voorbeelden worden voorbeeld-JSON-definities gegeven. U deze voorbeelddefinities gebruiken om een pijplijn te maken met [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) In de voorbeelden wordt uitgelegd hoe u gegevens kopieert van en naar Data Lake Store en Azure Blob-opslag. Gegevens kunnen echter _rechtstreeks_ worden gekopieerd van een van de bronnen naar een van de ondersteunde putten. Zie voor meer informatie de sectie 'Ondersteunde gegevensopslag en -indelingen' in de gegevens verplaatsen met het artikel [Activiteit kopiëren.](data-factory-data-movement-activities.md)

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Voorbeeld: Gegevens uit Azure Blob Storage kopiëren naar Azure Data Lake Store
De voorbeeldcode in deze sectie geeft het volgende aan:

* Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Een gekoppelde service van het type [AzureDataLakeStore](#linked-service-properties).
* Een [invoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureDataLakeStore](#dataset-properties).
* Een [pijplijn](data-factory-create-pipelines.md) met een kopieeractiviteit die [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) en [AzureDataLakeStoreSink](#copy-activity-properties)gebruikt.

De voorbeelden laten zien hoe tijdreeksgegevens uit Azure Blob Storage elk uur worden gekopieerd naar Data Lake Store.

**Gekoppelde Azure Storage-service**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Gekoppelde Azure Data Lake Store-service**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Zie de sectie [Gekoppelde serviceeigenschappen](#linked-service-properties) voor configuratiedetails.
>

**Azure Blob-invoergegevensset**

In het volgende voorbeeld worden gegevens elk uur opgehaald`"frequency": "Hour", "interval": 1`uit een nieuwe blob (). Het mappad en de bestandsnaam voor de blob worden dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt het jaar-, maand- en daggedeelte van de begintijd. De bestandsnaam gebruikt het uurgedeelte van de begintijd. De `"external": true` instelling informeert de service Gegevensfabriek dat de tabel zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
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

**Uitvoergegevensset Azure Data Lake Store**

In het volgende voorbeeld worden gegevens naar Data Lake Store gekopieerd. Elk uur worden nieuwe gegevens gekopieerd naar Data Lake Store.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Activiteit in een pijplijn kopiëren met een blobbron en een gegevensarchief**

In het volgende voorbeeld bevat de pijplijn een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken. De kopieeractiviteit wordt elk uur uitgevoerd. In de JSON-definitie `source` van de `BlobSource`pijplijn `sink` wordt het `AzureDataLakeStoreSink`type ingesteld op , en wordt het type ingesteld op .

```json
{
    "name":"SamplePipeline",
    "properties":
    {
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [
            {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink"
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

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Voorbeeld: Gegevens uit Azure Data Lake Store kopiëren naar een Azure-blob
De voorbeeldcode in deze sectie geeft het volgende aan:

* Een gekoppelde service van het type [AzureDataLakeStore](#linked-service-properties).
* Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Een [invoergegevensset](data-factory-create-datasets.md) van het type [AzureDataLakeStore](#dataset-properties).
* Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [pijplijn](data-factory-create-pipelines.md) met een kopieeractiviteit die [AzureDataLakeStoreSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

De code kopieert elk uur tijdreeksgegevens uit Data Lake Store naar een Azure-blob.

**Gekoppelde Azure Data Lake Store-service**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Zie de sectie [Gekoppelde serviceeigenschappen](#linked-service-properties) voor configuratiedetails.
>

**Gekoppelde Azure Storage-service**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Invoergegevensset Azure Data Lake**

Stel in dit `"external"` `true` voorbeeld de gegevensfabriekservice in om te informeren dat de tabel zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```json
{
    "name": "AzureDataLakeStoreInput",
    "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
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
**Gegevensset Azure blob-uitvoer**

In het volgende voorbeeld worden gegevens elk uur`"frequency": "Hour", "interval": 1`naar een nieuwe blob geschreven ( ). Het mappad voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt het gedeelte jaar, maand, dag en uren van de begintijd.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Een kopieeractiviteit in een pijplijn met een Azure Data Lake Store-bron en een blobsink**

In het volgende voorbeeld bevat de pijplijn een kopieeractiviteit die is geconfigureerd om de invoer- en uitvoergegevenssets te gebruiken. De kopieeractiviteit wordt elk uur uitgevoerd. In de JSON-definitie `source` van de `AzureDataLakeStoreSource`pijplijn `sink` wordt het `BlobSink`type ingesteld op , en wordt het type ingesteld op .

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureDataLakeStoreInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
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

In de definitie van kopieeractiviteit u kolommen van de brongegevensset ook toewijzen aan kolommen in de gegevensset sink. Zie Kolommen [met gegevenssetstoewijzen in Azure Data Factory](data-factory-map-columns.md)voor meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
Zie het artikel [Activiteits- en stemgids kopiëren](data-factory-copy-activity-performance.md) voor meer informatie over de factoren die van invloed zijn op de prestaties van kopieeractiviteit en hoe u deze optimaliseren.
