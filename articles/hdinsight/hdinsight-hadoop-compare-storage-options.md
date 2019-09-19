---
title: Opslag opties vergelijken voor gebruik met Azure HDInsight-clusters
description: Biedt een overzicht van opslag typen en hoe deze werken met Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: ced2c888a412a72643bb0c12fceb7e83d39936cf
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098732"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Opslag opties vergelijken voor gebruik met Azure HDInsight-clusters

U kunt kiezen tussen een aantal verschillende Azure Storage-services bij het maken van HDInsight-clusters:

* Azure Storage
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

Dit artikel bevat een overzicht van deze opslag typen en de bijbehorende unieke functies.

De volgende tabel bevat een overzicht van de Azure Storage services die worden ondersteund door verschillende versies van HDInsight:

| Opslagservice | Accounttype | Type naam ruimte | Ondersteunde services | Ondersteunde prestatie lagen | Ondersteunde toegangs lagen | HDInsight-versie | Clustertype |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Voor algemeen gebruik v2 | Hiërarchisch (bestands systeem) | Blob | Standard | Hot, cool, Archive | 3.6 + | Alle |
|Azure Storage| Voor algemeen gebruik v2 | Object | Blob | Standard | Hot, cool, Archive | 3.6 + | Alle |
|Azure Storage| Algemeen v1 | Object | Blob | Standard | N/A | Alle | Alle |
|Azure Storage| Blob Storage * * | Object | Blok-blob | Standard | Hot, cool, Archive | Alle | Alle |
|Azure Data Lake Storage Gen1| N/A | Hiërarchisch (bestands systeem) | N/A | N/A | N/A | alleen 3,6 | Alle behalve HBase |

\* * Voor HDInsight-clusters kunnen alleen secundaire opslag accounts van het type BlobStorage zijn.

Zie [overzicht van Azure Storage-accounts](../storage/common/storage-account-overview.md) voor meer informatie over Azure Storage-account typen

Zie [Azure Blob-opslag voor meer informatie over Azure Storage toegangs lagen: Premium (preview), warme, cool en archief opslag lagen](../storage/blobs/storage-blob-storage-tiers.md)

U kunt een cluster maken met verschillende combi Naties van services voor primaire en optionele secundaire opslag. De volgende tabel bevat een overzicht van de configuraties voor cluster opslag die momenteel worden ondersteund in HDInsight:

| HDInsight-versie | Primaire opslag | Secundaire opslag | Ondersteund |
|---|---|---|---|
| 3,6 & 4,0 | Algemeen v1, Algemeen v2 | Algemeen v1, Algemeen v2, BlobStorage (blok-blobs) | Ja |
| 3,6 & 4,0 | Algemeen v1, Algemeen v2 | Data Lake Storage Gen2 | Nee |
| 3,6 & 4,0 | Algemeen v1, Algemeen v2 | Data Lake Storage Gen1 | Ja |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | Ja |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Algemeen v1, Algemeen v2, BlobStorage (blok-blobs) | Ja |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Nee |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ja |
| 3.6 | Data Lake Storage Gen1 | Algemeen v1, Algemeen v2, BlobStorage (blok-blobs) | Ja |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Nee |
| 4.0 | Data Lake Storage Gen1 | Any | Nee |

\* = Dit kan een of meer Data Lake Storage Gen2 accounts zijn, zolang deze allemaal zijn ingesteld om dezelfde beheerde identiteit te gebruiken voor toegang tot het cluster.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Azure Data Lake Storage Gen2 gebruiken met Apache Hadoop in azure HDInsight

Azure Data Lake Storage Gen2 maakt kern functies van Azure Data Lake Storage Gen1 en integreert deze in Azure Blob-opslag. Deze functies omvatten een bestands systeem dat compatibel is met Hadoop, Azure Active Directory (Azure AD) en Acl's (toegangs beheer lijsten) op basis van POSIX. Met deze combi natie kunt u profiteren van de prestaties van Azure Data Lake Storage Gen1 en ook het beheer van de levens cyclus voor lagen en gegevens van Blob-opslag gebruiken.

Zie [Inleiding tot Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)voor meer informatie over Azure data Lake Storage Gen2.

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Kern functionaliteit van Azure Data Lake Storage Gen2

* **Toegang die compatibel is met Hadoop:** In Azure Data Lake Storage Gen2 kunt u gegevens beheren en benaderen net zoals u dat zou doen met een Hadoop Distributed File System (HDFS). Het ABFS-stuur programma (Azure Blob File System) is beschikbaar in alle Apache Hadoop omgevingen, waaronder Azure HDInsight en Azure Databricks. Gebruik ABFS om toegang te krijgen tot gegevens die zijn opgeslagen in Data Lake Storage Gen2.

* **Een superset van POSIX-machtigingen:** Het beveiligings model voor Data Lake Gen2 ondersteunt ACL-en POSIX-machtigingen, samen met een extra granulariteit die specifiek is voor Data Lake Storage Gen2. Instellingen kunnen worden geconfigureerd via beheer hulpprogramma's of Frameworks als Apache Hive en Apache Spark.

* **Kosten effectiviteit:** Data Lake Storage Gen2 biedt goedkope opslag capaciteit en-trans acties. Met functies zoals Azure Blob Storage-levens cyclus kunt u de kosten verlagen door de facturerings tarieven aan te passen naarmate de levens cyclus van gegevens verloopt.

* **Compatibiliteit met hulpprogram ma's, frameworks en apps voor Blob-opslag:** Data Lake Storage Gen2 blijft werken met een breed scala aan hulpprogram ma's, frameworks en toepassingen voor Blob Storage.

* **Geoptimaliseerd stuur programma:** Het ABFS-stuur programma is specifiek geoptimaliseerd voor big data Analytics. De bijbehorende REST-Api's worden opgehaald via het DFS-eind punt (Distributed File System), dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Wat is er nieuw voor Azure Data Lake Storage gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Beheerde identiteiten voor beveiligde bestands toegang

Azure HDInsight maakt gebruik van beheerde identiteiten voor het beveiligen van de cluster toegang tot bestanden in Azure Data Lake Storage Gen2. Beheerde identiteiten zijn een functie van Azure Active Directory die Azure-Services biedt met een set automatisch beheerde referenties. Deze referenties kunnen worden gebruikt om te verifiëren bij elke service die ondersteuning biedt voor Active Directory-verificatie. Voor het gebruik van beheerde identiteiten is het niet nodig om referenties op te slaan in code of configuratie bestanden.

Zie [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie.

#### <a name="azure-blob-file-system-driver"></a>Azure Blob File System-stuur programma

Apache Hadoop toepassingen verwachten een systeem eigen gegevens te lezen uit en te schrijven naar de lokale schijf opslag. Met een Hadoop File System-stuur programma zoals ABFS kunnen Hadoop-toepassingen werken met Cloud opslag door regel matige Hadoop-bestandssysteem bewerkingen te emuleren. Het stuur programma converteert die opdrachten die zijn ontvangen van de toepassing, naar bewerkingen die het daad werkelijke platform voor Cloud opslag begrijpt.

Voorheen heeft het Hadoop File System-stuur programma alle bestandssysteem bewerkingen geconverteerd naar Azure Storage REST API-aanroepen aan de client zijde en vervolgens de REST API aangeroepen. Deze conversie aan de client zijde heeft echter geresulteerd in meerdere REST API-aanroepen voor één bestandssysteem bewerking, zoals het wijzigen van de naam van een bestand. ABFS heeft een deel van de Hadoop-bestandssysteem logica verplaatst van de client aan de server zijde. De Azure Data Lake Storage Gen2-API wordt nu parallel uitgevoerd met de BLOB API. Deze migratie verbetert de prestaties omdat u nu algemene Hadoop-bestandssysteem bewerkingen kunt uitvoeren met één REST API aanroep.

Voor meer informatie raadpleegt [u het Azure Blob-bestandssysteem stuur programma (ABFS): Een speciaal Azure Storage stuur programma voor](../storage/blobs/data-lake-storage-abfs-driver.md)Hadoop.

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>URI-schema voor Azure Data Lake Storage gen 2 

Azure Data Lake Storage Gen2 gebruikt een nieuw URI-schema voor toegang tot bestanden in Azure Storage van HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Het URI-schema biedt SSL-versleutelde toegang (`abfss://` voor voegsel) en niet-versleutelde toegang (`abfs://` voor voegsel). Gebruik `abfss` waar mogelijk, zelfs bij het openen van gegevens die zich in dezelfde regio bevinden in Azure.

`<FILE_SYSTEM_NAME>`Hiermee wordt het pad van het bestands systeem Data Lake Storage Gen2 geïdentificeerd.

`<ACCOUNT_NAME>`Hiermee wordt de naam van het Azure Storage-account aangeduid. Een FQDN (Fully Qualified Domain Name) is vereist.

`<PATH>`is de naam van het bestand of de map HDFS-pad.

Als waarden voor `<FILE_SYSTEM_NAME>` en `<ACCOUNT_NAME>` niet worden opgegeven, wordt het standaard bestandssysteem gebruikt. Voor de bestanden in het standaard bestandssysteem gebruikt u een relatief pad of een absoluut pad. U kunt bijvoorbeeld een `hadoop-mapreduce-examples.jar` van de volgende paden naar het bestand met HDInsight-clusters verwijzen:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> De bestands naam bevindt zich `hadoop-examples.jar` in de HDInsight-versies 2,1 en 1,6. Wanneer u werkt met bestanden buiten HDInsight, zult u zien dat de meeste hulpprogram ma's de ABFS-indeling niet herkennen, maar verwacht u in plaats daarvan een elementaire `example/jars/hadoop-mapreduce-examples.jar`pad-indeling, zoals.

Zie [de Azure data Lake Storage GEN2 URI gebruiken](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)voor meer informatie.

## <a name="azure-storage"></a>Azure Storage

Azure Storage is een robuuste, algemene opslag oplossing die naadloos kan worden geïntegreerd met HDInsight. HDInsight kan een blobcontainer in Azure Storage gebruiken als het standaardbestandssysteem voor het cluster. Via een HDFS-interface kan de volledige set onderdelen in HDInsight rechtstreeks worden toegepast op gestructureerde of ongestructureerde gegevens die zijn opgeslagen als blobs.

We raden u aan om afzonderlijke opslag containers te gebruiken voor uw standaard cluster opslag en uw bedrijfs gegevens, om de HDInsight-logboeken en tijdelijke bestanden van uw eigen zakelijke gegevens te isoleren. We raden u ook aan de standaard-BLOB-container te verwijderen, die toepassings-en systeem Logboeken bevat, na elk gebruik om de opslag kosten te verlagen. Breng de logboeken over naar een andere locatie voordat u de container verwijdert.

Als u ervoor kiest om uw opslag account te beveiligen met de **firewalls en beperkingen voor virtuele netwerken** op **geselecteerde netwerken**, moet u de uitzonde ring inschakelen **vertrouwde micro soft-Services toestaan...** zodat HDInsight toegang kan krijgen tot uw opslag account.

### <a name="hdinsight-storage-architecture"></a>HDInsight-opslagarchitectuur

Het volgende diagram biedt een abstracte weer gave van de HDInsight-architectuur van Azure Storage:

![HDInsight Storage architectuur](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "HDInsight Storage architectuur")

HDInsight biedt toegang tot het Distributed File System dat lokaal wordt gekoppeld aan de rekenknooppunten. Dit bestandssysteem is toegankelijk via de volledig gekwalificeerde URI, bijvoorbeeld:

    hdfs://<namenodehost>/<path>

Via HDInsight kunt u ook toegang krijgen tot gegevens in Azure Storage. De syntaxis is als volgt:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Houd rekening met de volgende principes wanneer u een Azure Storage-account gebruikt met HDInsight-clusters:

* **Containers in de opslag accounts die zijn verbonden met een cluster:** Omdat de account naam en-sleutel zijn gekoppeld aan het cluster tijdens het maken, hebt u volledige toegang tot de blobs in deze containers.

* **Open bare containers of open bare blobs in opslag accounts die *niet* zijn verbonden met een cluster:** U hebt alleen-lezen-machtiging voor de blobs in de containers.
  
  > [!NOTE]  
  > Open bare containers bieden u de mogelijkheid een lijst op te halen met alle blobs die beschikbaar zijn in die container en om meta gegevens van de container op te halen. Openbare blobs zijn alleen toegankelijk als u de exacte URL weet. Zie [Anonieme leestoegang tot containers en blobs beheren](../storage/blobs/storage-manage-access-to-resources.md) voor meer informatie.

* **Persoonlijke containers in opslag accounts die *niet* zijn verbonden met een cluster:** U hebt geen toegang tot de blobs in de containers, tenzij u het opslag account definieert wanneer u de WebHCat-taken verzendt. 

De opslagaccounts die worden gedefinieerd tijdens het creatieproces en de bijbehorende sleutels worden opgeslagen in %HADOOP_HOME%/conf/core-site.xml op de clusterknooppunten. HDInsight maakt standaard gebruik van de opslag accounts die zijn gedefinieerd in het bestand bestand core-site. XML. U kunt deze instelling wijzigen met behulp van [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Meerdere WebHCat-taken, waaronder Apache Hive, MapReduce, Apache Hadoop streaming en Apache varken, kunnen een beschrijving van opslag accounts en meta gegevens bevatten. (Dit geldt op dit moment voor varkens met opslag accounts, maar niet voor meta gegevens.) Zie [een HDInsight-cluster gebruiken met alternatieve opslag accounts en meta Stores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)voor meer informatie.

Blobs kunnen worden gebruikt voor gestructureerde en ongestructureerde gegevens. BLOB-containers slaan gegevens op als sleutel/waarde-paren en hebben geen Directory-hiërarchie. De naam van de sleutel kan echter een slash (/) bevatten om deze weer te geven als een bestand wordt opgeslagen in een mapstructuur. De sleutel van een BLOB kan bijvoorbeeld zijn `input/log1.txt`. Er bestaat `input` geen daad werkelijke map, maar vanwege het slash-teken in de naam van de sleutel ziet de sleutel eruit als een bestandspad.

### <a id="benefits"></a>Voordelen van Azure Storage
Reken clusters en opslag resources die niet naast zichzelf zijn ondergebracht, hebben impliciete prestatie kosten. Deze kosten worden verholpen door de manier waarop de berekenings clusters worden gemaakt dicht bij de resources van het opslag account in de Azure-regio. In deze regio kunnen de reken knooppunten efficiënt toegang krijgen tot de gegevens via het netwerk met hoge snelheid in Azure Storage.

Wanneer u de gegevens opslaat in Azure Storage in plaats van HDFS, profiteert u van verschillende voor delen:

* **Hergebruik en delen van gegevens:** De gegevens in HDFS bevinden zich in het berekenings cluster. Alleen de toepassingen die toegang tot het rekencluster hebben, kunnen de gegevens met HDFS API's gebruiken. De gegevens in Azure Storage daarentegen kunnen worden geopend via de HDFS-Api's of de Blob Storage REST-Api's. Vanwege deze indeling kan een grotere set toepassingen (met inbegrip van andere HDInsight-clusters) en hulpprogram ma's worden gebruikt om de gegevens te produceren en te gebruiken.

* **Gegevens archivering:** Wanneer gegevens worden opgeslagen in Azure Storage, kunnen de HDInsight-clusters die worden gebruikt voor berekeningen, veilig worden verwijderd zonder dat er gebruikers gegevens verloren gaan.

* **Kosten voor gegevens opslag:** Het opslaan van gegevens in DFS voor lange termijn is kostbaarer dan het opslaan van de gegevens in Azure Storage, omdat de kosten van een reken cluster hoger zijn dan de kosten van Azure Storage. Omdat de gegevens voor elke generatie van het berekenings cluster niet opnieuw hoeven te worden geladen, bespaart u ook de kosten voor het laden van gegevens.

* **Elastisch uitschalen:** Hoewel HDFS u een scale-out bestands systeem biedt, wordt de schaal bepaald door het aantal knoop punten dat u voor uw cluster maakt. Het wijzigen van de schaal kan ingewik kelder zijn dan de mogelijkheden voor elastisch schalen die u automatisch in Azure Storage krijgt.

* **Geo-replicatie:** Uw Azure Storage kan geografisch worden gerepliceerd. Hoewel geo-replicatie een geografisch herstel en gegevens redundantie biedt, is een failover naar de geo-gerepliceerde locatie ernstig van invloed op de prestaties en kunnen er extra kosten in rekening worden gebracht. Kies dus geo-replicatie voorzichtig en alleen als de waarde van de gegevens de extra kosten rechtvaardigt.

Bepaalde MapReduce-taken en-pakketten kunnen tussenliggende resultaten maken die u niet in Azure Storage wilt opslaan. In dat geval kunt u ervoor kiezen om de gegevens op te slaan in de lokale HDFS. HDInsight gebruikt DFS voor verschillende van deze tussenliggende resultaten in Hive-taken en andere processen.

> [!NOTE]  
> De meeste HDFS-opdrachten (bijvoorbeeld `ls` `copyFromLocal`, en `mkdir`) werken zoals verwacht in azure Storage. Alleen de opdrachten die specifiek zijn voor de systeem eigen HDFS-implementatie (aangeduid als DFS), zoals `fschk` en `dfsadmin`, tonen een ander gedrag in azure Storage.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Overzicht van Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 is een bedrijfsbrede grootschalige-opslag plaats voor big data analytische werk belastingen. Met Azure Data Lake kunt u gegevens van elke grootte, type en opname snelheid op één locatie vastleggen voor operationele en experimentele analyses.

Toegang tot Data Lake Storage Gen1 vanuit Hadoop (beschikbaar met een HDInsight-cluster) met behulp van de WebHDFS-compatibele REST-Api's. Data Lake Storage Gen1 is ontworpen om analyses in te scha kelen voor de opgeslagen gegevens en is afgestemd op de prestaties in scenario's voor gegevens analyse. In het vak zijn de mogelijkheden opgenomen die essentieel zijn voor praktijk cases van het bedrijf. Tot deze mogelijkheden behoren beveiliging, beheer baarheid, schalen, betrouw baarheid en beschik baarheid.

Zie het gedetailleerde [overzicht van Azure data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md)voor meer informatie over Azure data Lake Storage gen1.

De belangrijkste mogelijkheden van Data Lake Storage Gen1 zijn:

### <a name="compatibility-with-hadoop"></a>Compatibiliteit met Hadoop

Data Lake Storage Gen1 is een Apache Hadoop bestands systeem dat compatibel is met HDFS en werkt met het Hadoop-ecosysteem.  Uw bestaande HDInsight-toepassingen of-services die gebruikmaken van de WebHDFS-API kunnen eenvoudig worden geïntegreerd met Data Lake Storage Gen1. Data Lake Storage Gen1 biedt ook een WebHDFS-compatibele REST-interface voor toepassingen.

Gegevens die zijn opgeslagen in Data Lake Storage Gen1 kunnen eenvoudig worden geanalyseerd met behulp van Hadoop analytische frameworks zoals MapReduce of Hive. Azure HDInsight-clusters kunnen worden ingericht en geconfigureerd om rechtstreeks toegang te krijgen tot gegevens die zijn opgeslagen in Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Onbeperkte opslag, bestanden ter grootte van petabytes

Data Lake Storage Gen1 biedt onbeperkte opslag en is geschikt voor het opslaan van een verscheidenheid aan gegevens voor analyse. Er gelden geen limieten voor account grootten, bestands grootten of de hoeveelheid gegevens die kan worden opgeslagen in een Data Lake. De grootte van afzonderlijke bestanden kan variëren van KB tot PETA bytes, waardoor Data Lake Storage Gen1 een fantastische keuze maakt om elk type gegevens op te slaan. Gegevens worden opgeslagen blijvend door meerdere kopieën te maken, en er zijn geen beperkingen voor de duur van de gegevens die in de data Lake kunnen worden opgeslagen.

### <a name="performance-tuning-for-big-data-analytics"></a>Prestaties afstemmen voor big data Analytics

Data Lake Storage Gen1 is gebouwd voor het uitvoeren van grootschalige analyse systemen die een enorme door voer nodig hebben om grote hoeveel heden gegevens te doorzoeken en te analyseren. De data Lake breidt delen van een bestand uit op verschillende afzonderlijke opslag servers. Wanneer u gegevens analyseert, wordt de Lees doorvoer door deze setup verbeterd wanneer het bestand parallel wordt gelezen.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Gereedheid voor Enter prise: Maxi maal beschikbaar en veilig

Data Lake Storage Gen1 biedt een industrie standaard Beschik baarheid en betrouw baarheid. Gegevensassets worden opgeslagen blijvend: de beveiliging van redundante kopieën tegen onverwachte fouten. Ondernemingen kunnen Data Lake Storage Gen1 in hun oplossingen gebruiken als een belang rijk onderdeel van hun bestaande gegevens platform.

Data Lake Storage Gen1 biedt ook beveiliging op ondernemings niveau voor opgeslagen gegevens. Zie [gegevens beveiligen in azure data Lake Storage gen1](#DataLakeStoreSecurity)voor meer informatie.

### <a name="flexible-data-structures"></a>Flexibele gegevens structuren

Data Lake Storage Gen1 kunnen alle gegevens in de oorspronkelijke indeling opslaan, op voor waarde dat dit zo is, zonder dat hiervoor eerdere trans formaties zijn vereist. Data Lake Storage Gen1 hoeft geen schema te worden gedefinieerd voordat de gegevens worden geladen. Het afzonderlijke analytische Framework interpreteert de gegevens en definieert een schema op het moment van de analyse. Omdat deze bestanden met wille keurige grootte en indelingen kan opslaan, kan Data Lake Storage Gen1 gestructureerde, semigestructureerde en ongestructureerde gegevens verwerken.

Data Lake Storage Gen1 containers voor gegevens zijn in wezen mappen en bestanden. U werkt met de opgeslagen gegevens met behulp van Sdk's, het Azure Portal en Azure PowerShell. Zolang u uw gegevens in de Store hebt geplaatst met behulp van deze interfaces en de juiste containers, kunt u elk type gegevens opslaan. Data Lake Storage Gen1 voert geen speciale verwerking van gegevens uit op basis van het type gegevens dat wordt opgeslagen.

## <a name="DataLakeStoreSecurity"></a>Gegevens beveiliging in Data Lake Storage Gen1
Data Lake Storage Gen1 gebruikt Azure Active Directory voor verificatie en maakt gebruik van toegangs beheer lijsten (Acl's) voor het beheren van de toegang tot uw gegevens.

| **Functie** | **Beschrijving** |
| --- | --- |
| Authentication |Data Lake Storage Gen1 integreert met Azure Active Directory (Azure AD) voor identiteits-en toegangs beheer voor alle gegevens die zijn opgeslagen in Data Lake Storage Gen1. Vanwege de integratie Data Lake Storage Gen1 de voor delen van alle Azure AD-functies. Deze functies omvatten multi-factor Authentication, voorwaardelijke toegang, op rollen gebaseerd toegangs beheer, bewaking van toepassings gebruik, beveiligings bewaking en waarschuwingen, enzovoort. Data Lake Storage Gen1 ondersteunt het OAuth 2,0-protocol voor verificatie binnen de REST-interface. Bekijk [verificatie binnen Azure data Lake Storage gen1 met behulp van Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Toegangsbeheer |Data Lake Storage Gen1 biedt toegangs beheer door de machtigingen voor POSIX-stijlen te ondersteunen die door het WebHDFS-protocol worden weer gegeven. ACL's kunnen worden ingeschakeld voor de hoofdmap, submappen en afzonderlijke bestanden. Zie [toegangs beheer in data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md)voor meer informatie over de werking van acl's in de context van data Lake Storage gen1. |
| Versleuteling |Data Lake Storage Gen1 biedt ook versleuteling voor gegevens die in het account zijn opgeslagen. U geeft de versleutelings instellingen op tijdens het maken van een Data Lake Storage Gen1-account. U kunt ervoor kiezen om uw gegevens te versleutelen of te kiezen voor geen versleuteling. Zie [versleuteling in data Lake Storage gen1](../data-lake-store/data-lake-store-encryption.md)voor meer informatie. Zie [aan de slag met Azure data Lake Storage gen1 met behulp van de Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md)voor instructies over het bieden van een configuratie met betrekking tot versleuteling. |

Zie [gegevens beveiligen die zijn opgeslagen in azure data Lake Storage gen1](../data-lake-store/data-lake-store-secure-data.md)voor meer informatie over het beveiligen van gegevens in data Lake Storage gen1.

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Toepassingen die compatibel zijn met Data Lake Storage Gen1
Data Lake Storage Gen1 is compatibel met de meeste open source-onderdelen in het Hadoop-ecosysteem. Het kan ook goed worden geïntegreerd in andere Azure-services.  Volg de onderstaande koppelingen voor meer informatie over hoe Data Lake Storage Gen1 kan worden gebruikt met open source-onderdelen, evenals andere Azure-Services.

* Zie [open-source Big Data-toepassingen die met Azure data Lake Storage gen1 werken](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) voor een lijst van open-source toepassingen die samen werken met data Lake Storage gen1.
* Zie [Azure data Lake Storage gen1 integreren met andere Azure-Services](../data-lake-store/data-lake-store-integrate-with-other-services.md) om te begrijpen hoe u data Lake Storage gen1 met andere Azure-Services kunt gebruiken om een breder scala aan scenario's mogelijk te maken.
* Zie [Azure data Lake Storage gen1 gebruiken voor Big Data vereisten](../data-lake-store/data-lake-store-data-scenarios.md) voor meer informatie over het gebruik van data Lake Storage gen1 in scenario's zoals het opnemen van gegevens, het verwerken van gegevens, het downloaden van gegevens en het visualiseren van gegevens.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1-bestands systeem (adl://)
In Hadoop-omgevingen (beschikbaar met een HDInsight-cluster) hebt u toegang tot Data Lake Storage Gen1 via het nieuwe bestands systeem, de AzureDataLakeFilesystem (adl://). De prestaties van toepassingen en services die gebruikmaken van adl://kunnen worden geoptimaliseerd op manieren die momenteel niet beschikbaar zijn in WebHDFS. Als u Data Lake Storage Gen1 gebruikt, krijgt u de flexibiliteit om de beste prestaties te behalen door gebruik te maken van de aanbevolen adl://of door de bestaande code te hand haven door de WebHDFS-API direct te blijven gebruiken. Azure HDInsight maakt optimaal gebruik van de AzureDataLakeFilesystem om de beste prestaties op Data Lake Storage Gen1 te bieden.

Gebruik het volgende om toegang te krijgen tot uw gegevens in Data Lake Storage Gen1:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Zie [acties die beschikbaar zijn voor de opgeslagen gegevens](../data-lake-store/data-lake-store-get-started-portal.md#properties)voor meer informatie over het openen van de gegevens in data Lake Storage gen1.



## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Kennismaking met Azure Storage](../storage/common/storage-introduction.md)
