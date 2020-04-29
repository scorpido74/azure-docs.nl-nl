---
title: Gegevens kopiëren van HDFS met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit een Cloud of een on-premises HDFS-bron naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 09c39c41b2d31f88fe2b19d8f20cd19e182c9214
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417273"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Gegevens kopiëren van HDFS met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-hdfs-connector.md)
> * [Huidige versie](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u gegevens kopieert van HDFS-server. Lees het [artikel Inleiding](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze HDFS-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Opzoek activiteit](control-flow-lookup-activity.md)

Deze HDFS-connector ondersteunt met name:

- Bestanden kopiëren met behulp van **Windows** (Kerberos) of **anonieme** verificatie.
- Bestanden kopiëren met behulp van **webhdfs** -protocol of **ingebouwde DistCp** -ondersteuning.
- Kopiëren van bestanden als-is of parseren/genereren van bestanden met de [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Zorg ervoor dat de Integration Runtime toegang heeft tot **alle** [naam knooppunt server]: [naam knooppunt poort] en [gegevens knooppunt Servers]: [Data knoop punt poort] van het Hadoop-cluster. De standaard poort voor [naam knooppunt] is 50070 en de standaard poort van het gegevens knooppunt is 50075.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor HDFS.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service van HDFS:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Hdfs**. | Ja |
| url |URL naar HDFS |Ja |
| authenticationType | Toegestane waarden zijn: **anoniem**of **Windows**. <br><br> Als u **Kerberos-verificatie** wilt gebruiken voor HDFS-connector, raadpleegt u [deze sectie](#use-kerberos-authentication-for-hdfs-connector) om uw on-premises omgeving dienovereenkomstig in te stellen. |Ja |
| userName |Gebruikers naam voor Windows-verificatie. Voor Kerberos-verificatie geeft `<username>@<domain>.com`u op. |Ja (voor Windows-verificatie) |
| wachtwoord |Wacht woord voor Windows-verificatie. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). |Ja (voor Windows-verificatie) |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |Nee |

**Voor beeld: anonieme verificatie gebruiken**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: Windows-verificatie gebruiken**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor HDFS onder `location` instellingen in gegevensset op basis van indeling:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap type onder `location` in DataSet moet worden ingesteld op **HdfsLocation**. | Ja      |
| folderPath | Het pad naar de map. Als u een Joker teken wilt gebruiken om de map te filteren, slaat u deze instelling over en geeft u de instellingen voor de activiteit bron op. | Nee       |
| fileName   | De bestands naam onder de opgegeven folderPath. Als u Joker tekens wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u de instellingen van de activiteit bron op. | Nee       |

**Hierbij**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
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

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron HDFS.

### <a name="hdfs-as-source"></a>HDFS als bron

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund voor HDFS onder `storeSettings` instellingen in op Format-gebaseerde Kopieer Bron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | De eigenschap type onder `storeSettings` moet worden ingesteld op **HdfsReadSettings**. | Ja                                           |
| recursieve                | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. Houd er rekening mee dat wanneer recursief is ingesteld op True en de Sink een archief op basis van bestanden is, een lege map of submap niet wordt gekopieerd of gemaakt bij de sink. Toegestane waarden zijn **True** (standaard) en **Onwaar**. | Nee                                            |
| wildcardFolderPath       | Het mappad met Joker tekens om de bron mappen te filteren. <br>Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens `?` ) en (komt overeen met nul of één teken); Gebruik `^` om te escapen als uw werkelijke mapnaam Joker teken of escape-teken bevat. <br>Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Nee                                            |
| wildcardFileName         | De naam van het bestand met Joker tekens onder de opgegeven folderPath/wildcardFolderPath voor het filteren van bron bestanden. <br>Toegestane joker tekens zijn: `*` (komt overeen met nul of meer tekens `?` ) en (komt overeen met nul of één teken); Gebruik `^` om te escapen als uw werkelijke mapnaam Joker teken of escape-teken bevat.  Bekijk meer voor beelden in [map-en bestands filter voorbeelden](#folder-and-file-filter-examples). | Ja als `fileName` niet is opgegeven in de gegevensset |
| modifiedDatetimeStart    | Bestanden filteren op basis van het kenmerk: laatst gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt `modifiedDatetimeStart` tussen `modifiedDatetimeEnd`en. De tijd wordt toegepast op UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestands kenmerken wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` heeft datetime-waarde `modifiedDatetimeEnd` , maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk is aan de datum/tijd-waarde wordt geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft datetime-waarde `modifiedDatetimeStart` , maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is kleiner is dan de datum/tijd-waarde wordt geselecteerd. | Nee                                            |
| modifiedDatetimeEnd      | Hetzelfde als hierboven.                                               | Nee                                            |
| distcpSettings | Eigenschaps groep bij gebruik van HDFS DistCp. | Nee |
| resourceManagerEndpoint | Het eind punt van het garen van een andere bron | Ja als u DistCp gebruikt |
| tempScriptPath | Een mappad dat wordt gebruikt voor het opslaan van het tijdelijke DistCp-opdracht script. Het script bestand wordt gegenereerd door Data Factory en wordt verwijderd nadat de Kopieer taak is voltooid. | Ja als u DistCp gebruikt |
| distcpOptions | Aanvullende opties voor de opdracht DistCp. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met opslag archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee                                            |

**Hierbij**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Voor beelden van map-en bestands filter

In deze sectie wordt het resulterende gedrag van het mappad en de bestands naam met Joker teken filters beschreven.

| folderPath | fileName             | recursieve | De structuur van de bronmap en het filter resultaat ( **vetgedrukte** bestanden worden opgehaald) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (leeg, standaard instelling gebruiken) | false     | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Bestand1. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Bestand2. json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. CSV<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. CSV<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. CSV |
| `Folder*`  | (leeg, standaard instelling gebruiken) | waar      | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Bestand1. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Bestand2. json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. CSV**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. CSV |
| `Folder*`  | `*.csv`              | false     | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Bestand1. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. CSV<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. CSV<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. CSV |
| `Folder*`  | `*.csv`              | waar      | Mapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Bestand1. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Bestand2. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. CSV**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. CSV**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. CSV |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>DistCp gebruiken om gegevens te kopiëren van HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) is een Hadoop native opdracht regel programma voor het uitvoeren van gedistribueerde kopieën in een Hadoop-cluster. Wanneer u een Distcp-opdracht uitvoert, worden eerst alle bestanden die moeten worden gekopieerd, in het Hadoop-cluster gemaakt en worden er met elke toewijzings taak binaire kopie van bron naar sink.

Ondersteuning voor kopieer activiteiten met behulp van DistCp voor het kopiëren van bestanden als-is naar Azure Blob (inclusief [gefaseerde kopie](copy-activity-performance.md)) of Azure data Lake Store, in dat geval kan de stroom van het cluster volledig worden benut in plaats van dat het zelf-hostende Integration runtime wordt uitgevoerd. Het biedt een betere Kopieer doorvoer, vooral als uw cluster zeer krachtig is. Kopieer activiteit maakt op basis van uw configuratie in Azure Data Factory automatisch een distcp-opdracht, verzendt deze naar uw Hadoop-cluster en bewaak de Kopieer status.

### <a name="prerequisites"></a>Vereisten

Als u DistCp wilt gebruiken om bestanden te kopiëren als-afkomstig van HDFS naar Azure Blob (inclusief gefaseerde kopie) of Azure Data Lake Store, moet u ervoor zorgen dat uw Hadoop-cluster voldoet aan onderstaande vereisten:

1. De services MapReduce en garens zijn ingeschakeld.
2. De versie van garen is 2,5 of hoger.
3. HDFS-server is geïntegreerd met uw doel gegevens opslag-Azure Blob of Azure Data Lake Store:

    - Het Azure Blob-bestands systeem wordt standaard ondersteund sinds Hadoop 2,7. U hoeft alleen jar-pad op te geven in de configuratie van Hadoop-env.
    - Azure Data Lake Store bestands systeem wordt verpakt vanaf Hadoop 3.0.0-alpha1. Als uw Hadoop-cluster lager is dan die versie, moet u de ADLS gerelateerde jar-pakketten (Azure-datalake-Store. jar) hand matig in het cluster importeren en het jar-pad opgeven in de [configuratie van de](https://hadoop.apache.org/releases.html)Hadoop-env.

4. Bereid een tijdelijke map voor in HDFS. Deze tijdelijke map wordt gebruikt voor het opslaan van het DistCp-shell script, waardoor er ruimte op KB-niveau in beslag neemt.
5. Zorg ervoor dat het gebruikers account dat is verstrekt in HDFS linked service gemachtigd is om een toepassing in garens te verzenden. b) de machtiging voor het maken van submap-, lees-en schrijf bestanden onder de map Temp.

### <a name="configurations"></a>Configuraties

Zie DistCp-gerelateerde configuraties en voor beelden in [HDFS als bron](#hdfs-as-source) sectie.

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Kerberos-verificatie gebruiken voor HDFS-connector

Er zijn twee opties om de on-premises omgeving in te stellen, zodat u Kerberos-verificatie kunt gebruiken in HDFS-connector. U kunt kiezen welk het beste past bij uw situatie.
* Optie 1: [zelf-hostende Integration runtime computer toevoegen in Kerberos-realm](#kerberos-join-realm)
* Optie 2: [wederzijdse vertrouwens relatie tussen Windows-domein en Kerberos-realm inschakelen](#kerberos-mutual-trust)

### <a name="option-1-join-self-hosted-integration-runtime-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Optie 1: zelf-hostende Integration Runtime computer toevoegen in Kerberos-realm

#### <a name="requirements"></a>Vereisten

* De zelf-hostende Integration Runtime computer moet worden toegevoegd aan de Kerberos-realm en kan niet worden toegevoegd aan een Windows-domein.

#### <a name="how-to-configure"></a>Configureren

**Op een zelf-hostende Integration Runtime computer:**

1.  Voer het hulp programma **Ksetup** uit om de Kerberos KDC-server en-realm te configureren.

    De machine moet worden geconfigureerd als lid van een werk groep, omdat een Kerberos-realm afwijkt van een Windows-domein. Dit kan worden bereikt door de Kerberos-realm in te stellen en een KDC-server als volgt toe te voegen. Vervang *realm.com* door uw eigen respectieve realm als dat nodig is.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Start** de computer opnieuw op nadat u deze twee opdrachten hebt uitgevoerd.

2.  Controleer de configuratie met de opdracht **Ksetup** . De uitvoer moet er als volgt uitzien:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**In Azure Data Factory:**

* Configureer de HDFS-connector met behulp van **Windows-verificatie** samen met uw Principal-naam en-wacht woord voor Kerberos om verbinding te maken met de HDFS-gegevens bron. Raadpleeg de sectie eigenschappen van koppelen aan de [gekoppelde service](#linked-service-properties) in configuratie details.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Optie 2: wederzijdse vertrouwens relatie tussen Windows-domein en Kerberos-realm inschakelen

#### <a name="requirements"></a>Vereisten

*   De zelf-hostende Integration Runtime computer moet lid worden van een Windows-domein.
*   U moet gemachtigd zijn om de instellingen van de domein controller bij te werken.

#### <a name="how-to-configure"></a>Configureren

> [!NOTE]
> Vervang REALM.COM en AD.COM in de volgende zelf studie met uw eigen respectievelijke realm en domein controller naar behoefte.

**Op KDC-server:**

1. Bewerk de KDC-configuratie in het bestand **krb5. conf** zodat het Windows-domein KDC kan vertrouwen naar de volgende configuratie sjabloon. De configuratie bevindt zich standaard op **/etc/krb5.conf**.

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

   **Start** de KDC-service na de configuratie opnieuw.

2. Bereid een principal met de naam **krbtgt/realm\@. com ad.com** in KDC server met de volgende opdracht:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. Voeg in **Hadoop. Security. auth_to_local** HDFS-service configuratie bestand `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`toe.

**Op domein controller:**

1.  Voer de volgende **Ksetup** -opdrachten uit om een realm-vermelding toe te voegen:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Een vertrouwens relatie tot stand brengen tussen Windows-domein en Kerberos-realm. [wacht woord] is het wacht woord voor de principal **krbtgt/realm\@. com-ad.com**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Selecteer het versleutelings algoritme dat in Kerberos wordt gebruikt.

    1. Ga naar Serverbeheer > groepsbeleid beheer > domein >-Groepsbeleid objecten > het standaard beleid of het actieve domein en bewerk.

    2. Ga in het pop-upvenster **Groepsbeleidsbeheer-editor** naar Computer configuratie > beleid > Windows-instellingen > beveiligings instellingen > lokale beleids regels > beveiligings opties en configureer **netwerk beveiliging: Configureer versleutelings typen die zijn toegestaan voor Kerberos**.

    3. Selecteer de versleutelings algoritme die u wilt gebruiken bij het maken van verbinding met KDC. Normaal gesp roken hoeft u alleen maar alle opties te selecteren.

        ![Configuratie versleutelings typen voor Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Gebruik de opdracht **Ksetup** om de versleutelings algoritme op te geven die moet worden gebruikt voor de specifieke realm.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Maak de toewijzing tussen het domein account en de Kerberos-principal, zodat u Kerberos principal in het Windows-domein kunt gebruiken.

    1. Start de beheer Programma's > **Active Directory gebruikers en computers**.

    2. Configureer geavanceerde functies door te klikken op**geavanceerde functies** **weer geven** > .

    3. Zoek het account waaraan u toewijzingen wilt maken en klik met de rechter muisknop om **naam toewijzingen** weer te geven > op het tabblad **Kerberos-namen** .

    4. Voeg een principal uit de realm toe.

        ![Beveiligings identiteit toewijzen](media/connector-hdfs/map-security-identity.png)

**Op een zelf-hostende Integration Runtime computer:**

* Voer de volgende **Ksetup** -opdrachten uit om een realm-vermelding toe te voegen.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**In Azure Data Factory:**

* Configureer de HDFS-connector met behulp van **Windows-verificatie** samen met uw domein account of Kerberos-principal om verbinding te maken met de HDFS-gegevens bron. Raadpleeg de sectie eigenschappen van koppelen aan de [gekoppelde service](#linked-service-properties) in configuratie details.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="legacy-models"></a>Verouderde modellen

>[!NOTE]
>De volgende modellen worden nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden het nieuwe model te gebruiken dat hierboven wordt beschreven en de gebruikers interface van de ADF-ontwerp functie is overgeschakeld op het genereren van het nieuwe model.

### <a name="legacy-dataset-model"></a>Verouderd gegevensset-model

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **file share** |Ja |
| folderPath | Pad naar de map. Het Joker teken filter wordt ondersteund, toegestane joker tekens `*` zijn: (komt overeen met nul of `?` meer tekens) en (komt overeen met nul of één teken); Gebruik `^` om te escapen als uw werkelijke bestands naam Joker teken of escape-teken bevat. <br/><br/>Voor beelden: root folder/submap/, zie voor beelden van [mappen en bestands filters](#folder-and-file-filter-examples)voor meer voor beelden. |Ja |
| fileName |  De **naam of het Joker teken** voor het bestand (en) onder de opgegeven folderPath. Als u geen waarde opgeeft voor deze eigenschap, wijst de gegevensset naar alle bestanden in de map. <br/><br/>Voor het filter zijn toegestane joker tekens: `*` (komt overeen met nul of meer tekens `?` ) en (komt overeen met nul of één teken).<br/>-Voor beeld 1:`"fileName": "*.csv"`<br/>-Voor beeld 2:`"fileName": "???20180427.txt"`<br/>Gebruik `^` om te escapen als uw werkelijke mapnaam Joker teken of escape-teken bevat. |Nee |
| modifiedDatetimeStart | Bestanden filteren op basis van het kenmerk: laatst gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt `modifiedDatetimeStart` tussen `modifiedDatetimeEnd`en. De tijd wordt toegepast op UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br/><br/> Houd er rekening mee dat de prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter van enorme hoeveel heden bestanden wilt uitvoeren. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestands kenmerken wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` heeft datetime-waarde `modifiedDatetimeEnd` , maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk is aan de datum/tijd-waarde wordt geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft datetime-waarde `modifiedDatetimeStart` , maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is kleiner is dan de datum/tijd-waarde wordt geselecteerd.| Nee |
| modifiedDatetimeEnd | Bestanden filteren op basis van het kenmerk: laatst gewijzigd. De bestanden worden geselecteerd als het tijdstip van de laatste wijziging binnen het tijds bereik ligt `modifiedDatetimeStart` tussen `modifiedDatetimeEnd`en. De tijd wordt toegepast op UTC-tijd zone in de notatie "2018-12-01T05:00:00Z". <br/><br/> Houd er rekening mee dat de prestaties van het verplaatsen van gegevens worden beïnvloed door deze instelling in te scha kelen wanneer u bestands filter van enorme hoeveel heden bestanden wilt uitvoeren. <br/><br/> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestands kenmerken wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` heeft datetime-waarde `modifiedDatetimeEnd` , maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is groter dan of gelijk is aan de datum/tijd-waarde wordt geselecteerd.  Wanneer `modifiedDatetimeEnd` heeft datetime-waarde `modifiedDatetimeStart` , maar is null, betekent dit dat de bestanden waarvan het kenmerk laatst gewijzigd is kleiner is dan de datum/tijd-waarde wordt geselecteerd.| Nee |
| formaat | Als u bestanden wilt **kopiëren als-zich bevindt** tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie indeling in de gegevensset voor invoer en uitvoer over.<br/><br/>Als u bestanden wilt parseren met een specifieke indeling, worden de volgende typen bestands indelingen ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie voor meer informatie secties [tekst indeling](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-indeling](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format)-indeling, [Orc-indeling](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en Parquet- [indeling](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nee (alleen voor het scenario binair kopiëren) |
| compressie | Geef het type en compressie niveau voor de gegevens op. Zie [ondersteunde bestands indelingen en compressie-codecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2**en **ZipDeflate**.<br/>Ondersteunde niveaus zijn: **optimaal** en **snelst**. |Nee |

>[!TIP]
>Als u alle bestanden in een map wilt kopiëren, geeft u alleen **FolderPath** op.<br>Als u één bestand met een bepaalde naam wilt kopiëren, geeft u **FolderPath** op met een map en een **Bestands** naam met een bestand.<br>Als u een subset van bestanden onder een map wilt kopiëren, geeft u **FolderPath** op met een deel van de map en de **Bestands naam** met het Joker teken filter.

**Hierbij**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
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

### <a name="legacy-copy-activity-source-model"></a>Bron model van verouderde Kopieer activiteit

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **HdfsSource** |Ja |
| recursieve | Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. Opmerking Wanneer recursief is ingesteld op True en Sink is op bestanden gebaseerd archief, wordt lege map/submap niet gekopieerd/gemaakt bij sink.<br/>Toegestane waarden zijn: **True** (standaard), **False** | Nee |
| distcpSettings | Eigenschaps groep bij gebruik van HDFS DistCp. | Nee |
| resourceManagerEndpoint | Het eind punt van het garen van een andere bron | Ja als u DistCp gebruikt |
| tempScriptPath | Een mappad dat wordt gebruikt voor het opslaan van het tijdelijke DistCp-opdracht script. Het script bestand wordt gegenereerd door Data Factory en wordt verwijderd nadat de Kopieer taak is voltooid. | Ja als u DistCp gebruikt |
| distcpOptions | Aanvullende opties voor de opdracht DistCp. | Nee |
| maxConcurrentConnections | Het aantal verbindingen dat gelijktijdig verbinding maakt met opslag archief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevens archief wilt beperken. | Nee |

**Voor beeld: HDFS-bron in Kopieer activiteit met behulp van DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
