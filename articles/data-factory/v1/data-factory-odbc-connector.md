---
title: Gegevens verplaatsen van ODBC-gegevensarchieven
description: Meer informatie over het verplaatsen van gegevens uit ODBC-gegevensarchieven met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e1735c2d2ed107f7ec65d68a6826267ee83a93f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281390"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Gegevens verplaatsen van ODBC-gegevensarchieven met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-odbc-connector.md)
> * [Versie 2 (huidige versie)](../connector-odbc.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [ODBC-connector in V2](../connector-odbc.md)als u de huidige versie van de datafabriekservice gebruikt.


In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens te verplaatsen vanuit een on-premises ODBC-gegevensarchief. Het bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit.

U gegevens uit een ODBC-gegevensarchief kopiëren naar een ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die als sinks worden ondersteund door de kopieeractiviteit. Datafactory ondersteunt momenteel alleen het verplaatsen van gegevens van een ODBC-gegevensarchief naar andere gegevensopslag, maar niet voor het verplaatsen van gegevens van andere gegevensopslag naar een ODBC-gegevensarchief.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Connectiviteit inschakelen
Data Factory-service ondersteunt het verbinden met on-premises ODBC-bronnen via de Data Management Gateway. Bekijk [het verplaatsen van gegevens tussen on-premises locaties en een cloudartikel](data-factory-move-data-between-onprem-and-cloud.md) voor meer informatie over Data Management Gateway en stapsgewijze instructies voor het instellen van de gateway. Gebruik de gateway om verbinding te maken met een ODBC-gegevensarchief, zelfs als deze wordt gehost in een Azure IaaS-vm.

U de gateway installeren op dezelfde on-premises machine of de Azure VM als het ODBC-gegevensarchief. We raden u echter aan de gateway op een aparte machine/Azure IaaS VM te installeren om bronconflicten en betere prestaties te voorkomen. Wanneer u de gateway op een aparte machine installeert, moet de machine toegang hebben tot de machine met het ODBC-gegevensarchief.

Naast de Data Management Gateway moet u ook het ODBC-stuurprogramma voor het gegevensarchief op de gatewaymachine installeren.

> [!NOTE]
> Zie [Problemen met de gateway oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor tips over het oplossen van verbindings-/gatewaygerelateerde problemen.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens uit een ODBC-gegevensarchief verplaatst met behulp van verschillende hulpprogramma's/API's.

De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon,** **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven.
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling. Zie JSON-voorbeeld voor een voorbeeld van JSON met JSON-definities voor entiteiten in gegevensfabriek die worden gebruikt om gegevens uit een ODBC-gegevensarchief te kopiëren: [Gegevens kopiëren van ODBC-gegevensarchief naar](#json-example-copy-data-from-odbc-data-store-to-azure-blob) azure blob.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor ODBC-gegevensarchief:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor odbc-gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **OnPremisesOdbc** |Ja |
| Connectionstring |Het gedeelte van de niet-toegangsreferenties van de verbindingstekenreeks en een optionele versleutelde referentie. Zie voorbeelden in de volgende secties. <br/><br/>U de verbindingstekenreeks `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`met patroon als, of het systeem DSN (Gegevensbronnaam) gebruiken waarmee u op de gatewaymachine hebt ingesteld `"DSN=<name of the DSN>;"` (u moet nog steeds het referentiegedeelte in gekoppelde service opgeven). |Ja |
| referenties |Het gedeelte toegangsreferenties van de verbindingstekenreeks die is opgegeven in de waardenotatie van stuurprogramma's. Bijvoorbeeld: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |Nee |
| authenticationType |Type verificatie wordt gebruikt om verbinding te maken met het ODBC-gegevensarchief. Mogelijke waarden zijn: Anoniem en Basic. |Ja |
| userName |Geef de gebruikersnaam op als u Basisverificatie gebruikt. |Nee |
| wachtwoord |Geef het wachtwoord op voor het gebruikersaccount dat u voor de userName hebt opgegeven. |Nee |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met het ODBC-gegevensarchief. |Ja |

### <a name="using-basic-authentication"></a>Basisverificatie gebruiken

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Basisverificatie gebruiken met versleutelde referenties
U de referenties versleutelen met de cmdlet [Nieuw-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) (1.0-versie van Azure PowerShell) of [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0,9 of eerdere versie van de Azure PowerShell).

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Anonieme verificatie gebruiken

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL, Azure blob, Azure table, etc.).

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie typeEigenschappen voor de gegevensset van type **RelationalTable** (die ODBC-gegevensset bevat) heeft de volgende eigenschappen

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |Naam van de tabel in het ODBC-gegevensarchief. |Ja |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleidsregels zijn beschikbaar voor alle soorten activiteiten.

Eigenschappen die beschikbaar zijn in de sectie **typeEigenschappen** van de activiteit daarentegen, variëren per activiteitstype. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

In kopieeractiviteit, wanneer de bron van type **RelationalSource** is (dat ODBC bevat), zijn de volgende eigenschappen beschikbaar in de sectie typeEigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-querytekenreeks. Selecteer bijvoorbeeld * in MyTable. |Ja |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>JSON-voorbeeld: Gegevens uit ODBC-gegevensarchief kopiëren naar Azure Blob
In dit voorbeeld worden JSON-definities gegeven die u gebruiken om een pijplijn te maken met Behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Hierin wordt weergegeven hoe u gegevens uit een ODBC-bron kopiëren naar een Azure Blob Storage. Gegevens kunnen echter worden gekopieerd naar een van de putten die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) zijn vermeld met behulp van de kopieeractiviteit in Azure Data Factory.

Het voorbeeld heeft de volgende gegevensfabriekentiteiten:

1. Een gekoppelde service van het type [OnPremisesOdbc](#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van type [RelationalTable](#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [RelationalSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert gegevens van een queryresultaat in een ODBC-gegevensarchief elk uur naar een blob. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

Stel als eerste stap de datamanagementgateway in. De instructies bevinden zich in de [verhuisgegevens tussen on-premises locaties en cloudartikelen.](data-factory-move-data-between-onprem-and-cloud.md)

**ODBC-gekoppelde service** In dit voorbeeld wordt de basisverificatie gebruikt. Zie [ODBC-sectie met gekoppelde service](#linked-service-properties) voor verschillende soorten verificatie die u gebruiken.

```json
{
    "name": "OnPremOdbcLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
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

**ODBC-invoergegevensset**

In het voorbeeld wordt ervan uitgegaan dat u een tabel "MyTable" hebt gemaakt in een ODBC-database en dat deze kolom "tijdstempelkolom" bevat voor tijdreeksgegevens.

Als u "extern" instelt: "true" informeert de datafabriekservice dat de gegevensset zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremOdbcLinkedService",
        "typeProperties": {},
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

**Azure Blob-uitvoergegevensset**

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1). Het mappad voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van de begintijd van jaar, maand, dag en uur.

```json
{
    "name": "AzureBlobOdbcDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Activiteit in een pijplijn kopiëren met ODBC-bron (RelationalSource) en Blob-sink (BlobSink)**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om deze invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn wordt het **brontype** ingesteld op **RelationalSource** en wordt **het gootsteentype** ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de **eigenschap query** selecteert de gegevens in het afgelopen uur die u wilt kopiëren.

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "OdbcDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOdbcDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "OdbcToBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-odbc"></a>Typetoewijzing voor ODBC
Zoals vermeld in het artikel [gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) voert kopieeractiviteit automatische typeconversies uit van brontypen naar sinktypen met de volgende benadering in twee stappen:

1. Converteren van native brontypen naar .NET-type
2. Converteren van .NET-type naar native sinktype

Bij het verplaatsen van gegevens uit ODBC-gegevensopslag worden ODBC-gegevenstypen toegewezen aan .NET-typen zoals vermeld in het [odbc-onderwerp voor toewijzingen van gegevenstypen.](https://msdn.microsoft.com/library/cc668763.aspx)

## <a name="map-source-to-sink-columns"></a>Kaartbron om kolommen te laten zinken
Zie Kolommen van [gegevenssetsin Azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in brongegevensset naar kolommen in sink dataset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaalbaar lezen uit relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevensopslag rekening met herhaalbaarheid om onbedoelde resultaten te voorkomen. In Azure Data Factory u een segment handmatig opnieuw uitvoeren. U ook het beleid voor een wijziging opnieuw configureren, zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment in beide richtingen wordt opnieuw uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht hoe vaak een segment wordt uitgevoerd. Zie [Herhaalbaar lezen uit relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="troubleshoot-connectivity-issues"></a>Verbindingsproblemen oplossen
Als u verbindingsproblemen wilt oplossen, gebruikt u het tabblad **Diagnostische gegevens** van **Configuratiebeheer voor gegevensbeheergateway**.

1. Configuratiebeheer **voor gegevensbeheergateway starten**. U 'C:\Program Files\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe' direct (of) naar **Gateway** zoeken om een koppeling naar **de Microsoft Data Management Gateway-toepassing** te zoeken zoals weergegeven in de volgende afbeelding.

    ![Zoekgateway](./media/data-factory-odbc-connector/search-gateway.png)
2. Ga naar het tabblad **Diagnostische gegevens.**

    ![Diagnostische gegevens van de gateway](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Selecteer het **type** gegevensarchief (gekoppelde service).
4. Geef **verificatie** op en voer **referenties** in (of voer **verbindingstekenreeks in** die wordt gebruikt om verbinding te maken met het gegevensarchief.
5. Klik **op Verbinding testen** om de verbinding met het gegevensarchief te testen.

## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Handleiding activiteitsprestaties kopiëren & tuningom](data-factory-copy-activity-performance.md) meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.
