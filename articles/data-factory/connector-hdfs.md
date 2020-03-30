---
title: Gegevens van HDFS kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit een cloud of on-premises HDFS-bron naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
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
ms.openlocfilehash: 2cd76afa9412e89c57cfb6c357eb164ce5d3d1c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75830425"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Gegevens van HDFS kopiëren met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-hdfs-connector.md)
> * [Huidige versie](connector-hdfs.md)

In dit artikel wordt beschreven hoe u gegevens van de HDFS-server kopiëren. Lees het [inleidende artikel](introduction.md)voor meer informatie over Azure Data Factory.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze HDFS-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

Met name deze HDFS-connector ondersteunt:

- Bestanden kopiëren met **Windows** (Kerberos) of **Anonieme** verificatie.
- Bestanden kopiëren met behulp van **webhdfs-protocol** of **ingebouwde DistCp-ondersteuning.**
- Bestanden kopiëren als is of ontleedt/genereert bestanden met de [ondersteunde bestandsindelingen en compressiecodecs.](supported-file-formats-and-compression-codecs.md)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Zorg ervoor dat de Integratieruntime toegang heeft tot **ALLE** [naamknooppuntserver]:[naamknooppuntpoort] en [gegevensknooppuntservers]:[datanodepoort] van het Hadoop-cluster. Standaard [naamknooppuntpoort] is 50070 en standaard [poort van het gegevensknooppunt] is 50075.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek die specifiek zijn voor HDFS te definiëren.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor HDFS-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **Hdfs**. | Ja |
| url |URL naar de HDFS |Ja |
| authenticationType | Toegestane waarden zijn: **Anoniem**of **Windows**. <br><br> Als u **Kerberos-verificatie** voor HDFS-connector wilt gebruiken, raadpleegt u [deze sectie](#use-kerberos-authentication-for-hdfs-connector) om uw on-premises omgeving dienovereenkomstig in te stellen. |Ja |
| userName |Gebruikersnaam voor Windows-verificatie. Geef voor Kerberos-verificatie op `<username>@<domain>.com`. |Ja (voor Windows-verificatie) |
| wachtwoord |Wachtwoord voor Windows-verificatie. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) |Ja (voor Windows-verificatie) |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

**Voorbeeld: anonieme verificatie gebruiken**

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

**Voorbeeld: Windows-verificatie gebruiken**

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

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden ondersteund `location` voor HDFS onder instellingen in op indeling gebaseerde gegevensset:

| Eigenschap   | Beschrijving                                                  | Vereist |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | De eigenschap `location` type onder in de gegevensset moet worden ingesteld op **HdfsLocatie**. | Ja      |
| folderPath | Het pad naar de map. Als u wildcard wilt gebruiken om de map te filteren, slaat u deze instelling over en geeft u op in de instellingen van de activiteitsbron. | Nee       |
| fileName   | De bestandsnaam onder de opgegeven mapPath. Als u wildcard wilt gebruiken om bestanden te filteren, slaat u deze instelling over en geeft u op in de instellingen van de activiteitsbron. | Nee       |

**Voorbeeld:**

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

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door HDFS-bron.

### <a name="hdfs-as-source"></a>HDFS als bron

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

De volgende eigenschappen worden voor `storeSettings` HDFS ondersteund onder instellingen in op indeling gebaseerde kopieerbron:

| Eigenschap                 | Beschrijving                                                  | Vereist                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | De eigenschap `storeSettings` type onder moet zijn ingesteld op **HdfsReadSettings**. | Ja                                           |
| Recursieve                | Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. Houd er rekening mee dat wanneer recursieve is ingesteld op true en de gootsteen een op bestanden gebaseerd e-bestand is, een lege map of submap niet wordt gekopieerd of gemaakt aan de gootsteen. Toegestane waarden zijn **waar** (standaard) en **onwaar.** | Nee                                            |
| wildcardFolderPath       | Het mappad met jokertekens om bronmappen te filteren. <br>Toegestane wildcards zijn: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft. <br>Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Nee                                            |
| wildcardFileName         | De bestandsnaam met jokertekens onder de opgegeven mapPath/wildcardFolderPath om bronbestanden te filteren. <br>Toegestane wildcards zijn: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft.  Zie meer voorbeelden in [voorbeelden van mappen en bestandenfilters](#folder-and-file-filter-examples). | Ja `fileName` als deze niet is opgegeven in de gegevensset |
| gewijzigdDatumtimeStart    | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br> De eigenschappen kunnen NULL zijn, wat betekent dat er geen filter voor bestandskenmerk wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd. | Nee                                            |
| gewijzigdDatumtimeEnd      | Hetzelfde als hierboven.                                               | Nee                                            |
| distcpInstellingen | Eigenschappengroep bij het gebruik van HDFS DistCp. | Nee |
| resourceManagerEindpunt | Het eindpunt van garenresourcebeheer | Ja als u DistCp gebruikt |
| tempScriptPath | Een mappad dat wordt gebruikt om het opdrachtscript temp DistCp op te slaan. Het scriptbestand wordt gegenereerd door Data Factory en wordt verwijderd nadat de taak Kopiëren is voltooid. | Ja als u DistCp gebruikt |
| distcpOptions | Extra opties voor de opdracht DistCp. | Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen dat tegelijkertijd verbinding moet maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee                                            |

**Voorbeeld:**

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

### <a name="folder-and-file-filter-examples"></a>Voorbeelden van mappen en bestandsfilters

In deze sectie wordt het resulterende gedrag van het mappad en de bestandsnaam met wildcardfilters beschreven.

| folderPath | fileName             | Recursieve | Structuur en filterresultaat voor bronmappen **(vetgedrukte** bestanden worden opgehaald) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (leeg, standaard gebruiken) | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (leeg, standaard gebruiken) | waar      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | waar      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Submap1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>DistCp gebruiken om gegevens van HDFS te kopiëren

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) is een Hadoop native command-line tool om gedistribueerde kopie te doen in een Hadoop cluster. Wanneer u een opdracht Distcp uitvoert, worden eerst alle bestanden vermeld die moeten worden gekopieerd, worden verschillende maptaken in het Hadoop-cluster gemaakt en zal elke map-taak binaire kopie van bron naar gootsteen doen.

Kopieer activiteitsondersteuning met DistCp om bestanden te kopiëren naar Azure Blob (inclusief [gefaseerde kopie)](copy-activity-performance.md)of Azure Data Lake Store, in welk geval het de kracht van uw cluster volledig kan benutten in plaats van te draaien op de runtime voor zelfgehoste integratie. Het zal zorgen voor een betere kopieerdoorvoer vooral als uw cluster is zeer krachtig. Op basis van uw configuratie in Azure Data Factory maakt kopieeractiviteit automatisch een distcp-opdracht, wordt u naar uw Hadoop-cluster verzonden en controleert u de kopieerstatus.

### <a name="prerequisites"></a>Vereisten

Als u DistCp wilt gebruiken om bestanden te kopiëren van HDFS naar Azure Blob (inclusief gefaseerde kopie) of Azure Data Lake Store, controleert u of uw Hadoop-cluster voldoet aan onderstaande vereisten:

1. MapReduce- en Yarn-services zijn ingeschakeld.
2. Garen versie is 2.5 of hoger.
3. HDFS-server is geïntegreerd met uw doelgegevensarchief - Azure Blob of Azure Data Lake Store:

    - Azure Blob FileSystem wordt native ondersteund sinds Hadoop 2.7. U hoeft alleen maar jar pad in Hadoop env config opgeven.
    - Azure Data Lake Store FileSystem is verpakt vanaf Hadoop 3.0.0-alpha1. Als uw Hadoop-cluster lager is dan die versie, moet u handmatig ADLS-gerelateerde jar-pakketten (azure-datalake-store.jar) vanaf [hier](https://hadoop.apache.org/releases.html)importeren in cluster en jarpad opgeven in Hadoop env config.

4. Een tijdelijke map voorbereiden in HDFS. Deze tijdelijke map wordt gebruikt om DistCp shell script op te slaan, dus het zal KB-niveau ruimte in beslag nemen.
5. Zorg ervoor dat het gebruikersaccount in HDFS Linked Service toestemming heeft om a) een aanvraag in Yarn in te dienen; b) hebben de toestemming om submap te maken, lezen / schrijven bestanden onder boven temp map.

### <a name="configurations"></a>Configuraties

Zie DistCp-gerelateerde configuraties en voorbeelden in [HDFS als bronsectie.](#hdfs-as-source)

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Kerberos-verificatie gebruiken voor HDFS-connector

Er zijn twee opties om de on-premises omgeving in te stellen om Kerberos Authentication in HDFS-connector te gebruiken. U kiezen voor de een beter past bij uw zaak.
* Optie 1: [Word lid van self-hosted Integration Runtime machine in Kerberos rijk](#kerberos-join-realm)
* Optie 2: [Wederzijds vertrouwen tussen Windows-domein en Kerberos-realm inschakelen](#kerberos-mutual-trust)

### <a name="option-1-join-self-hosted-integration-runtime-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Optie 1: Word lid van self-hosted Integration Runtime machine in Kerberos rijk

#### <a name="requirements"></a>Vereisten

* De self-hosted Integration Runtime-machine moet lid worden van de Kerberos-wereld en kan niet deelnemen aan een Windows-domein.

#### <a name="how-to-configure"></a>Configureren

**Op self-hosted Integration Runtime machine:**

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

#### <a name="requirements"></a>Vereisten

*   De self-hosted Integration Runtime-machine moet lid worden van een Windows-domein.
*   U hebt toestemming nodig om de instellingen van de domeincontroller bij te werken.

#### <a name="how-to-configure"></a>Configureren

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

        ![Config-versleutelingstypen voor Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Gebruik de opdracht **Ksetup** om het versleutelingsalgoritme op te geven dat op de specifieke REALM moet worden gebruikt.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Maak de toewijzing tussen het domeinaccount en kerberos-principal om Kerberos principal in Windows Domain te gebruiken.

    1. Start de hulpprogramma's voor beheer > **Active Directory: gebruikers en computers**.

    2. Geavanceerde functies configureren door op**Geavanceerde functies** **weergeven** > te klikken.

    3. Zoek het account waarop u toewijzingen wilt maken en klik met de rechtermuisknop om **naamtoewijzingen** weer te geven > klik op het tabblad **Kerberos-namen.**

    4. Voeg een opdrachtgever van het rijk toe.

        ![Identiteit kaartbeveiliging](media/connector-hdfs/map-security-identity.png)

**Op self-hosted Integration Runtime machine:**

* Voer de volgende **Ksetup-opdrachten** uit om een realm-item toe te voegen.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**In Azure Data Factory:**

* Configureer de HDFS-connector met **Windows-verificatie** samen met uw domeinaccount of Kerberos Principal om verbinding te maken met de HDFS-gegevensbron. Controleer de sectie [HDFS Linked Service-eigenschappen](#linked-service-properties) op configuratiegegevens.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Verouderde modellen

>[!NOTE]
>De volgende modellen worden nog steeds ondersteund als-is voor achterwaartse compatibiliteit. U wordt voorgesteld om het nieuwe model te gebruiken dat in bovengenoemde secties wordt vermeld die vooruit gaan, en ADF authoring UI is op het produceren van het nieuwe model overgestapt.

### <a name="legacy-dataset-model"></a>Verouderd gegevenssetmodel

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **FileShare** |Ja |
| folderPath | Pad naar de map. Wildcard filter wordt ondersteund, toegestane wildcards zijn: `*` (komt `?` overeen met nul of meer tekens) en (komt overeen met nul of een enkel teken); gebruiken `^` om te ontsnappen als uw werkelijke bestandsnaam wildcard of deze ontsnapping char binnen heeft. <br/><br/>Voorbeelden: rootmap/submap/, zie meer voorbeelden in [voorbeelden van mappen en bestandsfilters](#folder-and-file-filter-examples). |Ja |
| fileName |  **Naam- of wildcardfilter** voor het bestand(en) onder de opgegeven 'mapPath'. Als u geen waarde voor deze eigenschap opgeeft, verwijst de gegevensset naar alle bestanden in de map. <br/><br/>Voor filter zijn toegestane jokertekens: `*` (komt overeen `?` met nul of meer tekens) en (komt overeen met nul of één teken).<br/>- Voorbeeld 1:`"fileName": "*.csv"`<br/>- Voorbeeld 2:`"fileName": "???20180427.txt"`<br/>Gebruik `^` om te ontsnappen als uw werkelijke map naam wildcard of deze ontsnapping char binnen heeft. |Nee |
| gewijzigdDatumtimeStart | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br/><br/> Houd er rekening mee dat de algehele prestaties van gegevensverplaatsing worden beïnvloed door deze instelling in te schakelen wanneer u bestandsfilter wilt doen uit enorme hoeveelheden bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, zodat er geen filter voor bestandskenmerk wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd.| Nee |
| gewijzigdDatumtimeEnd | Bestandenfilter op basis van het kenmerk: Laatst gewijzigd. De bestanden worden geselecteerd als hun laatste gewijzigde `modifiedDatetimeStart` tijd `modifiedDatetimeEnd`binnen het tijdsbereik tussen en . De tijd wordt toegepast op utc-tijdzone in de indeling "2018-12-01T05:00:00Z". <br/><br/> Houd er rekening mee dat de algehele prestaties van gegevensverplaatsing worden beïnvloed door deze instelling in te schakelen wanneer u bestandsfilter wilt doen uit enorme hoeveelheden bestanden. <br/><br/> De eigenschappen kunnen NULL zijn, zodat er geen filter voor bestandskenmerk wordt toegepast op de gegevensset.  Wanneer `modifiedDatetimeStart` de datumtijdwaarde is, maar `modifiedDatetimeEnd` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk groter is dan of gelijk is aan de datumtijdwaarde, worden geselecteerd.  Wanneer `modifiedDatetimeEnd` de datumtijdwaarde is, maar `modifiedDatetimeStart` NULL is, betekent dit dat de bestanden waarvan het laatst gewijzigde kenmerk kleiner is dan de datumtijdwaarde, worden geselecteerd.| Nee |
| formaat | Als u **bestanden** wilt kopiëren tussen bestandsopslag (binaire kopie), slaat u de sectie opmaak over in definities van zowel invoer- als uitvoergegevenssets.<br/><br/>Als u bestanden met een specifieke indeling wilt ontleden, worden de volgende bestandsindelingstypen ondersteund: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Stel de **eigenschap type** onder opmaak in op een van deze waarden. Zie [Secties Tekstformaat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Json Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), Avro [Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)en [Parquet Format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) voor meer informatie. |Nee (alleen voor binaire kopie scenario) |
| compressie | Geef het type en het compressieniveau voor de gegevens op. Zie [Ondersteunde bestandsindelingen en compressiecodecs](supported-file-formats-and-compression-codecs-legacy.md#compression-support)voor meer informatie.<br/>Ondersteunde typen zijn: **GZip,** **Deflate,** **BZip2**en **ZipDeflate**.<br/>Ondersteunde niveaus zijn: **Optimaal** en **snelste**. |Nee |

>[!TIP]
>Als u alle bestanden onder een map wilt kopiëren, geeft u **alleen mapPath** op.<br>Als u één bestand met een bepaalde naam wilt kopiëren, geeft u **mapPath** op met maponderdeel en **bestandsnaam.**<br>Als u een subset van bestanden onder een map wilt kopiëren, geeft u **mapPath** op met maponderdeel en **bestandsnaam** met wildcardfilter.

**Voorbeeld:**

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

### <a name="legacy-copy-activity-source-model"></a>Bronmodel voor verouderde kopieeractiviteit

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van kopieeractiviteit moet zijn ingesteld op: **HdfsSource** |Ja |
| Recursieve | Hiermee geeft u aan of de gegevens recursief worden gelezen vanuit de submappen of alleen vanuit de opgegeven map. Opmerking wanneer recursieve is ingesteld op true en sink is file-based store, lege map / sub-map zal niet worden gekopieerd / gemaakt op gootsteen.<br/>Toegestane waarden zijn: **true** (default), **false** | Nee |
| distcpInstellingen | Eigenschappengroep bij het gebruik van HDFS DistCp. | Nee |
| resourceManagerEindpunt | Het eindpunt van garenresourcebeheer | Ja als u DistCp gebruikt |
| tempScriptPath | Een mappad dat wordt gebruikt om het opdrachtscript temp DistCp op te slaan. Het scriptbestand wordt gegenereerd door Data Factory en wordt verwijderd nadat de taak Kopiëren is voltooid. | Ja als u DistCp gebruikt |
| distcpOptions | Extra opties voor de opdracht DistCp. | Nee |
| maxConcurrentVerbindingen | Het aantal verbindingen dat tegelijkertijd verbinding moet maken met het opslagarchief. Geef alleen op wanneer u de gelijktijdige verbinding met het gegevensarchief wilt beperken. | Nee |

**Voorbeeld: HDFS-bron in kopieeractiviteit met DistCp**

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
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
