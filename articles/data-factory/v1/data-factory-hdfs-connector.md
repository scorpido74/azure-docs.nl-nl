---
title: Gegevens verplaatsen van on-premises HDFS
description: Meer informatie over het verplaatsen van gegevens van on-premises HDFS met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7652ab72fb972230d98913c2d7e2601737982532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924352"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Gegevens verplaatsen van on-premises HDFS met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-hdfs-connector.md)
> * [Versie 2 (huidige versie)](../connector-hdfs.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [HDFS-connector in V2](../connector-hdfs.md)als u de huidige versie van de service Data Factory gebruikt.

In dit artikel wordt uitgelegd hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van een on-premises HDFS te verplaatsen. Het bouwt voort op het artikel [Data Movement Activities,](data-factory-data-movement-activities.md) dat een algemeen overzicht geeft van gegevensverplaatsing met de kopieeractiviteit.

U gegevens van HDFS kopiëren naar elk ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die als sinks worden ondersteund door de kopieeractiviteit. Datafabriek ondersteunt momenteel alleen het verplaatsen van gegevens van een on-premises HDFS naar andere gegevensopslag, maar niet voor het verplaatsen van gegevens van andere gegevensopslag naar een on-premises HDFS.

> [!NOTE]
> Copy Activity verwijdert het bronbestand niet nadat het naar de bestemming is gekopieerd. Als u het bronbestand na een geslaagde kopie moet verwijderen, maakt u een aangepaste activiteit om het bestand te verwijderen en de activiteit in de pijplijn te gebruiken. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Connectiviteit inschakelen
Data Factory-service ondersteunt het maken van verbinding met on-premises HDFS met behulp van de Data Management Gateway. Bekijk [het verplaatsen van gegevens tussen on-premises locaties en een cloudartikel](data-factory-move-data-between-onprem-and-cloud.md) voor meer informatie over Data Management Gateway en stapsgewijze instructies voor het instellen van de gateway. Gebruik de gateway om verbinding te maken met HDFS, zelfs als deze wordt gehost in een Azure IaaS VM.

> [!NOTE]
> Zorg ervoor dat de Data Management Gateway toegang heeft tot **ALLE** server [naamknooppuntserver]:[naamknooppuntpoort] en [gegevensknooppuntservers]:[datanodepoort] van het Hadoop-cluster. Standaard [naamknooppuntpoort] is 50070 en standaard [poort van het gegevensknooppunt] is 50075.

Hoewel u gateway installeren op dezelfde on-premises machine of de Azure VM als de HDFS, raden we u aan de gateway te installeren op een aparte machine/Azure IaaS VM. Het hebben van gateway op een aparte machine vermindert de onenigheid tussen bronnen en verbetert de prestaties. Wanneer u de gateway op een aparte machine installeert, moet de machine toegang hebben tot de machine met de HDFS.

## <a name="getting-started"></a>Aan de slag
U een pijplijn maken met een kopieeractiviteit die gegevens van een HDFS-bron verplaatst met behulp van verschillende hulpprogramma's/API's.

De eenvoudigste manier om een pijplijn te maken, is door de **wizard Kopiëren**te gebruiken. Zie [Zelfstudie: Maak een pijplijn met wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough voor het maken van een pijplijn met de wizard Gegevens kopiëren.

U ook de volgende hulpprogramma's gebruiken om een pijplijn te maken: **Azure-portal,** **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-sjabloon**, **.NET API**en REST **API**. Zie [Zelfstudie voor activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies om een pijplijn met een kopieeractiviteit te maken.

Of u nu de hulpprogramma's of API's gebruikt, u voert de volgende stappen uit om een pijplijn te maken die gegevens van een brongegevensarchief naar een sink-gegevensarchief verplaatst:

1. Maak **gekoppelde services** om invoer- en uitvoergegevensopslag te koppelen aan uw gegevensfabriek.
2. Maak **gegevenssets** om invoer- en uitvoergegevens voor de kopieerbewerking weer te geven.
3. Maak een **pijplijn** met een kopieeractiviteit die een gegevensset als invoer en een uitvoerset als uitvoer neemt.

Wanneer u de wizard gebruikt, worden JSON-definities voor deze gegevensfabrieksentiteiten (gekoppelde services, gegevenssets en de pijplijn) automatisch voor u gemaakt. Wanneer u tools/API's (behalve .NET API) gebruikt, definieert u deze entiteiten in de Data Factory met behulp van de JSON-indeling.  Zie JSON-voorbeeld voor een voorbeeld met JSON-definities voor entiteiten in gegevensfabriek die worden gebruikt om gegevens uit een HDFS-gegevensarchief te [kopiëren: Gegevens kopiëren van on-premises HDFS naar](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) azure blob.

In de volgende secties vindt u informatie over JSON-eigenschappen die worden gebruikt om entiteiten in gegevensfabriek die specifiek zijn voor HDFS te definiëren:

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen
Een gekoppelde service koppelt een gegevensarchief aan een gegevensfabriek. U maakt een gekoppelde service van type **Hdfs** om een on-premises HDFS aan uw gegevensfabriek te koppelen. In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor hdfs-gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet zijn ingesteld op: **Hdfs** |Ja |
| url |URL naar de HDFS |Ja |
| authenticationType |Anoniem, of Windows. <br><br> Als u **Kerberos-verificatie** voor HDFS-connector wilt gebruiken, raadpleegt u [deze sectie](#use-kerberos-authentication-for-hdfs-connector) om uw on-premises omgeving dienovereenkomstig in te stellen. |Ja |
| userName |Gebruikersnaam voor Windows-verificatie. Geef voor Kerberos-verificatie op `<username>@<domain>.com`. |Ja (voor Windows-verificatie) |
| wachtwoord |Wachtwoord voor Windows-verificatie. |Ja (voor Windows-verificatie) |
| gatewayNaam |Naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de HDFS. |Ja |
| versleuteldCredential |[Nieuw-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) uitvoer van de toegangsreferenties. |Nee |

### <a name="using-anonymous-authentication"></a>Anonieme verificatie gebruiken

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Windows-verificatie gebruiken

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [Gegevenssets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. Secties zoals structuur, beschikbaarheid en beleid van een gegevensset JSON zijn vergelijkbaar voor alle gegevenssettypen (Azure SQL, Azure blob, Azure table, etc.).

De sectie **typeEigenschappen** is verschillend voor elk type gegevensset en geeft informatie over de locatie van de gegevens in het gegevensarchief. De sectie typeEigenschappen voor de gegevensset van type **FileShare** (inclusief HDFS-gegevensset) heeft de volgende eigenschappen

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Pad naar de map. Voorbeeld: `myfolder`<br/><br/>Gebruik escape character ' \ ' voor speciale tekens in de tekenreeks. Geef bijvoorbeeld voor map\submap\\\\de submap map op en geef\\\\voor d:\samplemap d: samplemap op.<br/><br/>U deze eigenschap combineren met **partitieDoor** mappaden te hebben op basis van begin- en einddatums van segmenten. |Ja |
| fileName |Geef de naam op van het bestand in de **mapPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, verwijst de tabel naar alle bestanden in de map.<br/><br/>Wanneer bestandsnaam niet is opgegeven voor een uitvoergegevensset, wordt de naam van het gegenereerde bestand in de volgende deze indeling weergegeven: <br/><br/>`Data.<Guid>.txt`(bijvoorbeeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nee |
| partitionedBy |partitionedBy kan worden gebruikt om een dynamische mapPath op te geven, bestandsnaam voor tijdreeksgegevens. Voorbeeld: mapPath parameterized voor elk uur aan gegevens. |Nee |
| formaat | De volgende formattypes worden ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), Avro [Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)en [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) voor meer informatie. <br><br> Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets. |Nee |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**. Ondersteunde niveaus zijn: **Optimaal** en **snelste**. Zie [Bestands- en compressieindelingen in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |Nee |

> [!NOTE]
> bestandsnaam en fileFilter kunnen niet tegelijkertijd worden gebruikt.

### <a name="using-partionedby-property"></a>PartionedBy gebruiken, eigenschap
Zoals in de vorige sectie is vermeld, u een dynamische mapPath en bestandsnaam opgeven voor tijdreeksgegevens met de eigenschap **partitionedBy,** [de functies Gegevensfabriek en de systeemvariabelen](data-factory-functions-variables.md).

Zie [Gegevenssets maken,](data-factory-create-datasets.md) [plannen & uitvoeren](data-factory-scheduling-and-execution.md)en [Pijplijnen maken](data-factory-create-pipelines.md) voor meer informatie over gegevenssets voor tijdreeksen, planning en segmenten.

#### <a name="sample-1"></a>Voorbeeld 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
In dit voorbeeld wordt {Slice} vervangen door de waarde van Data Factory-systeemvariabele SliceStart in de opgegeven indeling (YYYYMMDDHH). De SliceStart verwijst naar de begintijd van het segment. De mapPath is verschillend voor elk segment. Bijvoorbeeld: wikidatagateway/wikisampledataout/2014100103 of wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Voorbeeld 2:

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
In dit voorbeeld worden jaar, maand, dag en tijd van SliceStart geëxtraheerd in afzonderlijke variabelen die worden gebruikt door mapPath- en fileName-eigenschappen.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [Pijplijnmaken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer- en uitvoertabellen en beleidsregels zijn beschikbaar voor alle soorten activiteiten.

Overwegende dat de eigenschappen die beschikbaar zijn in de sectie typeEigenschappen van de activiteit per activiteitstype verschillen. Voor Kopieeractiviteit variëren ze afhankelijk van de soorten bronnen en putten.

Voor Kopieeractiviteit zijn, wanneer de bron van type **FileSystemSource** is, de volgende eigenschappen beschikbaar in de sectie typeEigenschappen:

**FileSystemSource** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| Recursieve |Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. |True, False (standaard) |Nee |

## <a name="supported-file-and-compression-formats"></a>Ondersteunde indelingen voor bestanden en compressie
Zie [Bestands- en compressie-indelingen in](data-factory-supported-file-and-compression-formats.md) het artikel van Azure Data Factory over details.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>JSON-voorbeeld: gegevens van on-premises HDFS kopiëren naar Azure Blob
In dit voorbeeld ziet u hoe u gegevens van een on-premises HDFS naar Azure Blob Storage kopieert. Gegevens kunnen echter **rechtstreeks** worden gekopieerd naar een van de putten die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) zijn vermeld met behulp van de kopieeractiviteit in Azure Data Factory.  

Het voorbeeld bevat JSON-definities voor de volgende entiteiten in gegevensfabrieken. U deze definities gebruiken om een pijplijn te maken om gegevens van HDFS naar Azure Blob Storage te kopiëren met [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md)

1. Een gekoppelde service van het type [OnPremisesHdfs](#linked-service-properties).
2. Een gekoppelde service van het type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een [invoergegevensset](data-factory-create-datasets.md) van het type [FileShare](#dataset-properties).
4. Een [uitvoergegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijplijn](data-factory-create-pipelines.md) met kopieeractiviteit die [Bestandssysteembron](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)gebruikt.

Het voorbeeld kopieert elk uur gegevens van een on-premises HDFS naar een Azure-blob. De JSON-eigenschappen die in deze monsters worden gebruikt, worden beschreven in secties die de monsters volgen.

Stel als eerste stap de datamanagementgateway in. De instructies in de [verhuisgegevens tussen on-premises locaties en cloudartikelen.](data-factory-move-data-between-onprem-and-cloud.md)

**HDFS gekoppelde service:** In dit voorbeeld wordt de Windows-verificatie gebruikt. Zie [hdfs-gekoppelde servicesectie](#linked-service-properties) voor verschillende soorten verificatie die u gebruiken.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Gekoppelde Azure Storage-service:**

```JSON
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

**HDFS-invoergegevensset:** Deze gegevensset verwijst naar de HDFS-map DataTransfer/UnitTest/. De pijplijn kopieert alle bestanden in deze map naar de bestemming.

Als u "extern" instelt: "true" informeert de datafabriekservice dat de gegevensset zich buiten de gegevensfabriek bevindt en niet wordt geproduceerd door een activiteit in de gegevensfabriek.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Azure Blob-uitvoergegevensset:**

Gegevens worden elk uur naar een nieuwe blob geschreven (frequentie: uur, interval: 1). Het mappad voor de blob wordt dynamisch geëvalueerd op basis van de begintijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van de begintijd van jaar, maand, dag en uur.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Een kopieeractiviteit in een pijplijn met bestandssysteembron en Blob-sink:**

De pijplijn bevat een kopieeractiviteit die is geconfigureerd om deze invoer- en uitvoergegevenssets te gebruiken en die elk uur moet worden uitgevoerd. In de JSON-definitie van pijplijn is het **brontype** ingesteld op **FileSystemSource** en is **het sinktype** ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de **eigenschap query** selecteert de gegevens in het afgelopen uur die u wilt kopiëren.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Kerberos-verificatie gebruiken voor HDFS-connector
Er zijn twee opties om de on-premises omgeving in te stellen om Kerberos Authentication in HDFS-connector te gebruiken. U kiezen voor de een beter past bij uw zaak.
* Optie 1: [Word lid van gateway machine in Kerberos rijk](#kerberos-join-realm)
* Optie 2: [Wederzijds vertrouwen tussen Windows-domein en Kerberos-realm inschakelen](#kerberos-mutual-trust)

### <a name="option-1-join-gateway-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Optie 1: Word lid van gateway machine in Kerberos rijk

#### <a name="requirement"></a>Vereiste:

* De gatewaymachine moet lid worden van het Kerberos-rijk en kan niet deelnemen aan een Windows-domein.

#### <a name="how-to-configure"></a>Configureren:

**Op gateway machine:**

1.  Voer het **Ksetup-hulpprogramma** uit om de Kerberos KDC-server en -realm te configureren.

    De machine moet zijn geconfigureerd als lid van een werkgroep, omdat een Kerberos-realm verschilt van een Windows-domein. Dit kan worden bereikt door het Kerberos-rijk in te stellen en een KDC-server als volgt toe te voegen. Vervang *REALM.COM* met je eigen eigen rijk als dat nodig is.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Start** de machine opnieuw op na het uitvoeren van deze 2 opdrachten.

2.  Controleer de configuratie met de opdracht **Ksetup.** De uitvoer moet zijn als:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**In Azure Data Factory:**

* Configureer de HDFS-connector met **Windows-verificatie** samen met uw Kerberos-hoofdnaam en wachtwoord om verbinding te maken met de HDFS-gegevensbron. Controleer de sectie [HDFS Linked Service-eigenschappen](#linked-service-properties) op configuratiegegevens.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Optie 2: Wederzijds vertrouwen tussen Windows-domein en Kerberos-realm inschakelen

#### <a name="requirement"></a>Vereiste:
*   De gatewaymachine moet lid worden van een Windows-domein.
*   U hebt toestemming nodig om de instellingen van de domeincontroller bij te werken.

#### <a name="how-to-configure"></a>Configureren:

> [!NOTE]
> Vervang REALM.COM en AD.COM in de volgende zelfstudie naar eigen huis en domeincontroller.

**Op de KDC-server:**

1. Bewerk de KDC-configuratie in **het krb5.conf-bestand** om KDC te laten vertrouwen op Windows Domain, verwijzend naar de volgende configuratiesjabloon. Standaard bevindt de configuratie zich op **/etc/krb5.conf.**

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log

           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true

           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }

           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM

           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   **Start** de KDC-service opnieuw na configuratie.

2. Bereid een hoofdmet de naam **\@krbtgt/REALM.COM AD.COM** in de KDC-server voor met de volgende opdracht:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. Voeg in **hadoop.security.auth_to_local** HDFS-serviceconfiguratiebestand . `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`

**Op domeincontroller:**

1.  Voer de volgende **Ksetup-opdrachten** uit om een realm-item toe te voegen:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Vestig vertrouwen van Windows Domain naar Kerberos Realm. [wachtwoord] is het wachtwoord voor de belangrijkste **\@krbtgt/REALM.COM AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Selecteer versleutelingsalgoritme dat wordt gebruikt in Kerberos.

    1. Ga naar Serverbeheer > Groepsbeleidsbeheer > Domein > groepsbeleidsobjecten > standaard- of actief domeinbeleid en Bewerken.

    2. Ga in het pop-upvenster van de **groepsbeleidsbeheereditor** naar > Beleid voor computerconfiguratie > Windows-instellingen > beveiligingsinstellingen > lokale beleidsopties > beveiligingsopties **configureren: Coderingstypen configureren die zijn toegestaan voor Kerberos**.

    3. Selecteer het versleutelingsalgoritme dat u wilt gebruiken wanneer u verbinding maakt met KDC. Vaak u gewoon alle opties selecteren.

        ![Config-versleutelingstypen voor Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Gebruik de opdracht **Ksetup** om het versleutelingsalgoritme op te geven dat op de specifieke REALM moet worden gebruikt.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Maak de toewijzing tussen het domeinaccount en kerberos-principal om Kerberos principal in Windows Domain te gebruiken.

    1. Start de hulpprogramma's voor beheer > **Active Directory: gebruikers en computers**.

    2. Geavanceerde functies configureren door op**Geavanceerde functies** **weergeven** > te klikken.

    3. Zoek het account waarop u toewijzingen wilt maken en klik met de rechtermuisknop om **naamtoewijzingen** weer te geven > klik op het tabblad **Kerberos-namen.**

    4. Voeg een opdrachtgever van het rijk toe.

        ![Identiteit kaartbeveiliging](media/data-factory-hdfs-connector/map-security-identity.png)

**Op gateway machine:**

* Voer de volgende **Ksetup-opdrachten** uit om een realm-item toe te voegen.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**In Azure Data Factory:**

* Configureer de HDFS-connector met **Windows-verificatie** samen met uw domeinaccount of Kerberos Principal om verbinding te maken met de HDFS-gegevensbron. Controleer de sectie [HDFS Linked Service-eigenschappen](#linked-service-properties) op configuratiegegevens.

> [!NOTE]
> Zie Kolommen van [gegevensset toewijzing in Azure Data Factory](data-factory-map-columns.md)als u kolommen wilt toewijzen van brongegevensset naar kolommen uit de sink-gegevensset.


## <a name="performance-and-tuning"></a>Prestaties en tuning
Zie [Handleiding activiteitsprestaties kopiëren & tuningom](data-factory-copy-activity-performance.md) meer te weten te komen over de belangrijkste factoren die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory en op verschillende manieren om deze te optimaliseren.
