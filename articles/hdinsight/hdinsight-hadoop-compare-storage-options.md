---
title: Opslagopties vergelijken voor gebruik met Azure HDInsight-clusters
description: Biedt een overzicht van opslagtypen en hoe ze werken met Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 98d71434ac9e3f712be0cbd8c505b7d5a537e7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79095544"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Opslagopties vergelijken voor gebruik met Azure HDInsight-clusters

U kiezen uit een aantal verschillende Azure-opslagservices bij het maken van HDInsight-clusters:

* Azure Storage
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

Dit artikel geeft een overzicht van deze opslagtypen en hun unieke functies.

In de volgende tabel worden de Azure Storage-services samengevat die worden ondersteund met verschillende versies van HDInsight:

| Opslagservice | Accounttype | Type naamruimte | Ondersteunde services | Ondersteunde prestatielagen | Ondersteunde toegangsniveaus | HDInsight-versie | Clustertype |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| V2 voor algemene doeleinden | Hiërarchisch (bestandssysteem) | Blob | Standard | Hot, Cool, Archief | 3,6+ | Alle behalve Spark 2.1 en 2.2|
|Azure Storage| V2 voor algemene doeleinden | Object | Blob | Standard | Hot, Cool, Archief | 3,6+ | Alle |
|Azure Storage| V1 voor algemeen gebruik | Object | Blob | Standard | N.v.t. | Alle | Alle |
|Azure Storage| Blob-opslag** | Object | Blob blokkeren | Standard | Hot, Cool, Archief | Alle | Alle |
|Azure Data Lake Storage Gen1| N.v.t. | Hiërarchisch (bestandssysteem) | N.v.t. | N.v.t. | N.v.t. | 3.6 Alleen | Alles behalve HBase |

**Voor HDInsight-clusters kunnen alleen secundaire opslagaccounts van het type BlobStorage zijn en is Page Blob geen ondersteunde opslagoptie.

Zie Overzicht van [Azure Storage-account voor](../storage/common/storage-account-overview.md) meer informatie over Azure Storage-accounttypen

Zie [Azure Blob-opslag: Premium (voorbeeld), Hot, Cool en Archive storagelagen](../storage/blobs/storage-blob-storage-tiers.md) voor meer informatie over azure storage-toegangslagen.

U een cluster maken met verschillende combinaties van services voor primaire en optionele secundaire opslag. In de volgende tabel worden de clusteropslagconfiguraties samengevat die momenteel worden ondersteund in HDInsight:

| HDInsight-versie | Primaire opslag | Secundaire opslag | Ondersteund |
|---|---|---|---|
| 3.6 & 4.0 | Algemeen doel V1 , Algemeen Doel V2 | Algemeen doel V1 , Algemeen doel V2, BlobStorage (Blokblobs) | Ja |
| 3.6 & 4.0 | Algemeen doel V1 , Algemeen Doel V2 | Data Lake Storage Gen2 | Nee |
| 3.6 & 4.0 | Data Lake Storage Gen2* | Data Lake Storage Gen2 | Ja |
| 3.6 & 4.0 | Data Lake Storage Gen2* | Algemeen doel V1 , Algemeen doel V2, BlobStorage (Blokblobs) | Ja |
| 3.6 & 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Nee |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ja |
| 3.6 | Data Lake Storage Gen1 | Algemeen doel V1 , Algemeen doel V2, BlobStorage (Blokblobs) | Ja |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Nee |
| 4,0 | Data Lake Storage Gen1 | Alle | Nee |
| 4,0 | Algemeen doel V1 , Algemeen Doel V2 | Data Lake Storage Gen1 | Nee |

*=Dit kunnen een of meerdere Data Lake Storage Gen2-accounts zijn, zolang ze allemaal zijn ingesteld om dezelfde beheerde identiteit te gebruiken voor clustertoegang.

> [!Note] 
> Primaire opslag van Data Lake Storage Gen2 wordt niet ondersteund voor Spark 2.1- of 2.2-clusters. 

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Azure Data Lake Storage Gen2 gebruiken met Apache Hadoop in Azure HDInsight

Azure Data Lake Storage Gen2 neemt kernfuncties over van Azure Data Lake Storage Gen1 en integreert deze in Azure Blob-opslag. Deze functies omvatten een bestandssysteem dat compatibel is met Hadoop, Azure Active Directory (Azure AD) en POSIX-gebaseerde toegangscontrolelijsten (ACL's). Met deze combinatie u profiteren van de prestaties van Azure Data Lake Storage Gen1 en tegelijkertijd gebruik maken van het tiering- en datalifebeheer van Blob-opslag.

Zie Inleiding tot Azure Data Lake Storage Gen2 voor meer informatie over Azure Data Lake Storage [Gen2.](../storage/blobs/data-lake-storage-introduction.md)

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Kernfunctionaliteit van Azure Data Lake Storage Gen2

* **Toegang die compatibel is met Hadoop:** In Azure Data Lake Storage Gen2 u gegevens beheren en openen, net zoals u dat zou doen met een Hadoop Distributed File System (HDFS). Het ABFS-stuurprogramma (Azure Blob File System) is beschikbaar binnen alle Apache Hadoop-omgevingen, inclusief Azure HDInsight en Azure Databricks. Gebruik ABFS om toegang te krijgen tot gegevens die zijn opgeslagen in Data Lake Storage Gen2.

* **Een superset van POSIX-machtigingen:** Het beveiligingsmodel voor Data Lake Gen2 ondersteunt ACL- en POSIX-machtigingen, samen met een extra granulariteit die specifiek is voor Data Lake Storage Gen2. Instellingen kunnen worden geconfigureerd via beheertools of frameworks zoals Apache Hive en Apache Spark.

* **Kosteneffectiviteit:** Data Lake Storage Gen2 biedt goedkope opslagcapaciteit en transacties. Functies zoals de levenscyclus van Azure Blob-opslag helpen de kosten te verlagen door factureringstarieven aan te passen wanneer gegevens door de levenscyclus worden verplaatst.

* **Compatibiliteit met blob-opslaghulpprogramma's, frameworks en apps:** Data Lake Storage Gen2 blijft werken met een breed scala aan tools, frameworks en toepassingen voor Blob-opslag.

* **Geoptimaliseerde driver:** De ABFS driver is speciaal geoptimaliseerd voor big data analytics. De bijbehorende REST API's worden opgedoken via het DFS-eindpunt (distributed file system), dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Nieuwe informatie voor Azure Data Lake Storage Gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Beheerde identiteiten voor beveiligde bestandstoegang

Azure HDInsight gebruikt beheerde identiteiten om clustertoegang tot bestanden in Azure Data Lake Storage Gen2 te beveiligen. Beheerde identiteiten zijn een functie van Azure Active Directory die Azure-services voorziet van een set automatisch beheerde referenties. Deze referenties kunnen worden gebruikt om te verifiëren voor elke service die Active Directory-verificatie ondersteunt. Als u beheerde identiteiten gebruikt, hoeft u referenties niet op te slaan in code- of configuratiebestanden.

Zie [Beheerde identiteiten voor Azure-bronnen voor](../active-directory/managed-identities-azure-resources/overview.md)meer informatie.

#### <a name="azure-blob-file-system-driver"></a>Azure Blob-bestandssysteemstuurprogramma

Apache Hadoop-toepassingen verwachten native gegevens uit lokale schijfopslag te lezen en te schrijven. Met een Hadoop-bestandssysteemstuurprogramma zoals ABFS kunnen Hadoop-toepassingen werken met cloudopslag door reguliere Hadoop-bestandssysteembewerkingen na te bootsen. Het stuurprogramma zet de opdrachten die van de toepassing zijn ontvangen om in bewerkingen die het werkelijke cloudopslagplatform begrijpt.

Voorheen heeft het hadoop-bestandssysteemstuurprogramma alle bestandssysteembewerkingen geconverteerd naar Azure Storage REST API-aanroepen aan de clientzijde en vervolgens de REST-API aangeroepen. Deze client-side conversie resulteerde echter in meerdere REST API-oproepen voor één bestandssysteembewerking, zoals de naamswijziging van een bestand. ABFS heeft een deel van de Hadoop-bestandssysteemlogica verplaatst van de clientkant naar de serverzijde. De Azure Data Lake Storage Gen2 API wordt nu parallel uitgevoerd met de Blob API. Deze migratie verbetert de prestaties omdat nu veelvoorkomende Hadoop-bestandssysteembewerkingen kunnen worden uitgevoerd met één REST API-aanroep.

Zie [Het Azure Blob Filesystem-stuurprogramma (ABFS): een speciaal Azure Storage-stuurprogramma voor Hadoop voor](../storage/blobs/data-lake-storage-abfs-driver.md)meer informatie.

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>URI-schema voor Azure Data Lake Storage Gen 2 

Azure Data Lake Storage Gen2 maakt gebruik van een nieuw URI-schema om toegang te krijgen tot bestanden in Azure Storage vanuit HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Het URI-schema biedt SSL-versleutelde toegang.

`<FILE_SYSTEM_NAME>`identificeert het pad van het bestandssysteem Data Lake Storage Gen2.

`<ACCOUNT_NAME>`hiermee wordt de naam van het Azure Storage-account geïdentificeerd. Een FQDN (Fully Qualified Domain Name) is vereist.

`<PATH>`is de naam van het bestand of de map HDFS-pad.

Als waarden `<FILE_SYSTEM_NAME>` `<ACCOUNT_NAME>` voor en niet zijn opgegeven, wordt het standaardbestandssysteem gebruikt. Gebruik voor de bestanden op het standaardbestandssysteem een relatief pad of een absoluut pad. Het `hadoop-mapreduce-examples.jar` bestand dat wordt geleverd met HDInsight-clusters kan bijvoorbeeld worden verwezen met behulp van een van de volgende paden:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> De bestandsnaam `hadoop-examples.jar` bevindt zich in HDInsight-versies 2.1 en 1.6 clusters. Wanneer u werkt met bestanden buiten HDInsight, zult u merken dat de meeste hulpprogramma's het ABFS-formaat `example/jars/hadoop-mapreduce-examples.jar`niet herkennen, maar in plaats daarvan een basispadindeling verwachten, zoals .

Zie [De Azure Data Lake Storage Gen2 URI gebruiken](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)voor meer informatie.

## <a name="azure-storage"></a>Azure Storage

Azure Storage is een robuuste opslagoplossing voor algemene doeleinden die naadloos integreert met HDInsight. HDInsight kan een blobcontainer in Azure Storage gebruiken als het standaardbestandssysteem voor het cluster. Via een HDFS-interface kan de volledige set componenten in HDInsight direct werken op gestructureerde of ongestructureerde gegevens die als blobs zijn opgeslagen.

We raden u aan afzonderlijke opslagcontainers te gebruiken voor uw standaardclusteropslag en uw bedrijfsgegevens om de HDInsight-logboeken en tijdelijke bestanden te isoleren van uw eigen bedrijfsgegevens. We raden ook aan om de standaardblobcontainer, die toepassings- en systeemlogboeken bevat, na elk gebruik te verwijderen om de opslagkosten te verlagen. Breng de logboeken over naar een andere locatie voordat u de container verwijdert.

Als u ervoor kiest uw opslagaccount te beveiligen met de beperkingen **firewalls en virtuele netwerken** op **geselecteerde netwerken,** moet u de uitzondering **Vertrouwde Microsoft-services toestaan inschakelen...** zodat HDInsight toegang heeft tot uw opslagaccount.

### <a name="hdinsight-storage-architecture"></a>HDInsight-opslagarchitectuur

Het volgende diagram biedt een abstracte weergave van de HDInsight-architectuur van Azure Storage:

![HDInsight-opslagarchitectuur](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "HDInsight-opslagarchitectuur")

HDInsight biedt toegang tot het Distributed File System dat lokaal wordt gekoppeld aan de rekenknooppunten. Dit bestandssysteem is toegankelijk via de volledig gekwalificeerde URI, bijvoorbeeld:

    hdfs://<namenodehost>/<path>

Via HDInsight hebt u ook toegang tot gegevens in Azure Storage. De syntaxis ziet er als volgt uit:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Houd rekening met de volgende beginselen bij het gebruik van een Azure Storage-account met HDInsight-clusters:

* **Containers in de opslagaccounts die zijn verbonden met een cluster:** omdat de accountnaam en de sleutel tijdens het maken worden gekoppeld aan het cluster, hebt u volledige toegang tot de blobs in deze containers.

* **Openbare containers of openbare blobs in opslagaccounts die *niet* zijn verbonden met een cluster:** U hebt alleen-lezen toestemming voor de blobs in de containers.
  
  > [!NOTE]  
  > Met openbare containers u een lijst krijgen van alle blobs die beschikbaar zijn in die container en containermetagegevens krijgen. Openbare blobs zijn alleen toegankelijk als u de exacte URL weet. Zie [Anonieme leestoegang tot containers en blobs beheren](../storage/blobs/storage-manage-access-to-resources.md)voor meer informatie.

* **Privécontainers in opslagaccounts die *niet* zijn verbonden met een cluster:** U hebt geen toegang tot de blobs in de containers, tenzij u het opslagaccount definieert wanneer u de WebHCat-taken verzendt. 

De opslagaccounts die worden gedefinieerd tijdens het creatieproces en de bijbehorende sleutels worden opgeslagen in %HADOOP_HOME%/conf/core-site.xml op de clusterknooppunten. HDInsight maakt standaard gebruik van de opslagaccounts die zijn gedefinieerd in het bestand core-site.xml. U deze instelling wijzigen met [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Meerdere WebHCat-taken, waaronder Apache Hive, MapReduce, Apache Hadoop streaming en Apache Pig, kunnen een beschrijving van opslagaccounts en metadata met zich meebrengen. (Dit geldt momenteel voor Pig met opslagaccounts, maar niet voor metagegevens.) Zie [Een HDInsight-cluster gebruiken met alternatieve opslagaccounts en metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)voor meer informatie.

Blobs kunnen worden gebruikt voor gestructureerde en ongestructureerde gegevens. Blob-containers slaan gegevens op als sleutel-/waardeparen en hebben geen directoryhiërarchie. De sleutelnaam kan echter een slash-teken (/ ) bevatten om het te laten lijken alsof een bestand is opgeslagen in een mapstructuur. De sleutel van een blob `input/log1.txt`kan bijvoorbeeld . Er `input` bestaat geen werkelijke map, maar vanwege het slash-teken in de sleutelnaam lijkt de sleutel op een bestandspad.

### <a name="benefits-of-azure-storage"></a><a id="benefits"></a>Voordelen van Azure Storage
Compute clusters en opslagresources die niet zijn ondergebracht, hebben impliciete prestatiekosten. Deze kosten worden beperkt door de manier waarop de compute clusters worden gemaakt in de buurt van de opslagaccountbronnen binnen het Azure-gebied. In deze regio hebben de compute nodes efficiënt toegang tot de gegevens via het hogesnelheidsnetwerk in Azure Storage.

Wanneer u de gegevens opslaat in Azure Storage in plaats van HDFS, krijgt u verschillende voordelen:

* **Hergebruik en delen van gegevens:** de gegevens in HDFS bevinden zich in het rekencluster. Alleen de toepassingen die toegang tot het rekencluster hebben, kunnen de gegevens met HDFS API's gebruiken. De gegevens in Azure Storage kunnen daarentegen worden geopend via de HDFS-API's of de REST-API's voor Blob-opslag. Door deze opstelling kan een grotere set toepassingen (waaronder andere HDInsight-clusters) en tools worden gebruikt om de gegevens te produceren en te consumeren.

* **Gegevensarchivering:** Wanneer gegevens worden opgeslagen in Azure Storage, kunnen de HDInsight-clusters die worden gebruikt voor berekening veilig worden verwijderd zonder gebruikersgegevens te verliezen.

* **Kosten voor gegevensopslag:** Het opslaan van gegevens in DFS voor de lange termijn is duurder dan het opslaan van de gegevens in Azure Storage, omdat de kosten van een compute cluster hoger zijn dan de kosten van Azure Storage. Omdat de gegevens niet opnieuw hoeven te worden geladen voor elke compute cluster generatie, bespaart u ook kosten voor het laden van gegevens.

* **Elastisch uitbreiden:** hoewel HDFS u een uitgebreid bestandssysteem biedt, wordt de schaal bepaald door het aantal knooppunten dat u voor het cluster maakt. Het wijzigen van de schaal kan ingewikkelder zijn dan vertrouwen op de elastische schalingmogelijkheden die u automatisch in Azure Storage krijgt.

* **Geo-replicatie:** Uw Azure Storage kan geo-gerepliceerd worden. Hoewel geo-replicatie u geografische herstel- en gegevensredundantie biedt, heeft een fail-over naar de geo-gerepliceerde locatie ernstige gevolgen voor uw prestaties en kan dit extra kosten met zich meebrengen. Kies dus voorzichtig en alleen voor geo-replicatie als de waarde van de gegevens de extra kosten rechtvaardigt.

Bepaalde mapWerk verminderen, taken en pakketten verminderen, kunnen tussentijdse resultaten opleveren die u niet in Azure Storage wilt opslaan. In dat geval u ervoor kiezen om de gegevens op te slaan in de lokale HDFS. HDInsight gebruikt DFS voor een aantal van deze tussentijdse resultaten in Hive-taken en andere processen.

> [!NOTE]  
> De meeste HDFS-opdrachten `ls`(bijvoorbeeld , en `copyFromLocal` `mkdir`) werken zoals verwacht in Azure Storage. Alleen de opdrachten die specifiek zijn voor de oorspronkelijke HDFS-implementatie `fschk` (die wordt aangeduid als DFS), zoals en `dfsadmin`tonen ander gedrag in Azure Storage.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Overzicht van Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 is een bedrijfsbrede hyperscale repository voor big data analytische workloads. Met Azure Data Lake u gegevens van elke grootte, elk type en opnamesnelheid op één plaats vastleggen voor operationele en verkennende analyses.

Toegang tot Data Lake Storage Gen1 vanuit Hadoop (beschikbaar met een HDInsight-cluster) met behulp van de WebHDFS-compatibele REST API's. Data Lake Storage Gen1 is ontworpen om analyses op de opgeslagen gegevens mogelijk te maken en is afgestemd op prestaties in scenario's voor gegevensanalyse. Out of the box, het bevat de mogelijkheden die essentieel zijn voor real-world enterprise use cases. Deze mogelijkheden omvatten beveiliging, beheerbaarheid, schaalbaarheid, betrouwbaarheid en beschikbaarheid.

Zie het gedetailleerde overzicht van Azure Data [Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)voor meer informatie over Azure Data Lake Storage Gen1.

De belangrijkste mogelijkheden van Data Lake Storage Gen1 zijn het volgende.

### <a name="compatibility-with-hadoop"></a>Compatibiliteit met Hadoop

Data Lake Storage Gen1 is een Apache Hadoop bestandssysteem dat compatibel is met HDFS en werkt met het Hadoop ecosysteem.  Uw bestaande HDInsight-toepassingen of -services die gebruik maken van de WebHDFS API kunnen eenvoudig worden geïntegreerd met Data Lake Storage Gen1. Data Lake Storage Gen1 onthult ook een WebHDFS-compatibele REST-interface voor toepassingen.

Gegevens die zijn opgeslagen in Data Lake Storage Gen1 kunnen eenvoudig worden geanalyseerd met behulp van Hadoop analytische frameworks zoals MapReduce of Hive. Azure HDInsight-clusters kunnen worden ingericht en geconfigureerd om rechtstreeks toegang te krijgen tot gegevens die zijn opgeslagen in Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Onbeperkte opslag, bestanden ter grootte van petabytes

Data Lake Storage Gen1 biedt onbeperkte opslag en is geschikt voor het opslaan van een verscheidenheid aan gegevens voor analytics. Het legt geen beperkingen op aan accountgroottes, bestandsgroottes of de hoeveelheid gegevens die in een gegevensmeer kunnen worden opgeslagen. Afzonderlijke bestanden kunnen variëren in grootte van kilobytes tot petabytes, waardoor Data Lake Storage Gen1 een geweldige keuze is om elk type gegevens op te slaan. Gegevens worden blijvend opgeslagen door het maken van meerdere kopieën, en er zijn geen grenzen aan hoe lang de gegevens kunnen worden opgeslagen in de gegevens meer.

### <a name="performance-tuning-for-big-data-analytics"></a>Prestatieafstemming voor big data-analyses

Data Lake Storage Gen1 is gebouwd om grootschalige analytische systemen uit te voeren die een enorme doorvoer vereisen om grote hoeveelheden gegevens op te vragen en te analyseren. Het datalake verspreidt delen van een bestand over verschillende afzonderlijke opslagservers. Wanneer u gegevens analyseert, verbetert deze instelling de leesdoorvoer wanneer het bestand parallel wordt gelezen.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Gereedheid voor ondernemingen: zeer beschikbaar en veilig

Data Lake Storage Gen1 biedt standaard beschikbaarheid en betrouwbaarheid. Gegevensactiva worden blijvend opgeslagen: overbodige kopieën beschermen tegen onverwachte storingen. Ondernemingen kunnen Data Lake Storage Gen1 in hun oplossingen gebruiken als een belangrijk onderdeel van hun bestaande dataplatform.

Data Lake Storage Gen1 biedt ook beveiliging op bedrijfsniveau voor opgeslagen gegevens. Zie [Gegevens beveiligen in Azure Data Lake Storage Gen1](#DataLakeStoreSecurity)voor meer informatie.

### <a name="flexible-data-structures"></a>Flexibele gegevensstructuren

Data Lake Storage Gen1 kan alle gegevens opslaan in zijn oorspronkelijke formaat, zoals het is, zonder voorafgaande transformaties. Data Lake Storage Gen1 vereist geen schema dat moet worden gedefinieerd voordat de gegevens worden geladen. Het individuele analytische kader interpreteert de gegevens en definieert een schema op het moment van de analyse. Omdat het bestanden met willekeurige groottes en indelingen kan opslaan, kan Data Lake Storage Gen1 gestructureerde, semigestructureerde en ongestructureerde gegevens verwerken.

Data Lake Storage Gen1 containers voor gegevens zijn in wezen mappen en bestanden. U werkt op de opgeslagen gegevens met behulp van SDKs, de Azure-portal en Azure PowerShell. Zolang u uw gegevens in de winkel plaatst met behulp van deze interfaces en de juiste containers, u elk type gegevens opslaan. Data Lake Storage Gen1 voert geen speciale verwerking van gegevens uit op basis van het type gegevens dat het opslaat.

## <a name="data-security-in-data-lake-storage-gen1"></a><a name="DataLakeStoreSecurity"></a>Gegevensbeveiliging in Data Lake Storage Gen1
Data Lake Storage Gen1 gebruikt Azure Active Directory voor verificatie en gebruikt toegangscontrolelijsten (ACL's) om de toegang tot uw gegevens te beheren.

| **Functie** | **Beschrijving** |
| --- | --- |
| Authentication |Data Lake Storage Gen1 integreert met Azure Active Directory (Azure AD) voor identiteits- en toegangsbeheer voor alle gegevens die zijn opgeslagen in Data Lake Storage Gen1. Dankzij de integratie profiteert Data Lake Storage Gen1 van alle Azure AD-functies. Deze functies omvatten multifactorauthenticatie, voorwaardelijke toegang, op rollen gebaseerdtoegangscontrole, bewaking van het gebruik van toepassingen, beveiligingsbewaking en waarschuwingen, enzovoort. Data Lake Storage Gen1 ondersteunt het OAuth 2.0-protocol voor verificatie binnen de REST-interface. Zie [Verificatie in Azure Data Lake Storage Gen1 met Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Toegangsbeheer |Data Lake Storage Gen1 biedt toegangscontrole door posix-achtige machtigingen te ondersteunen die worden blootgesteld door het WebHDFS-protocol. ACL's kunnen worden ingeschakeld voor de hoofdmap, submappen en afzonderlijke bestanden. Zie [Toegangscontrole in Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md)voor meer informatie over hoe ACL's werken in de context van Data Lake Storage Gen1. |
| Versleuteling |Data Lake Storage Gen1 biedt ook versleuteling voor gegevens die in het account zijn opgeslagen. U geeft de versleutelingsinstellingen op tijdens het maken van een Data Lake Storage Gen1-account. U ervoor kiezen om uw gegevens te versleutelen of te kiezen voor geen versleuteling. Zie [Versleuteling in Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md)voor meer informatie. Zie [Aan de slag met Azure Data Lake Storage Gen1 met behulp van de Azure-portal](../data-lake-store/data-lake-store-get-started-portal.md)voor instructies over het bieden van een versleutelingsgerelateerde configuratie. |

Zie Gegevens beveiligen die zijn opgeslagen in [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md)voor meer informatie over het beveiligen van gegevens in Data Lake Storage Gen1.

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Toepassingen die compatibel zijn met Data Lake Storage Gen1
Data Lake Storage Gen1 is compatibel met de meeste open-source componenten in het Hadoop-ecosysteem. Het kan ook goed worden geïntegreerd in andere Azure-services.  Volg de onderstaande links voor meer informatie over hoe Data Lake Storage Gen1 kan worden gebruikt, zowel met open-source componenten als met andere Azure-services.

* Zie [Open-source big data-toepassingen die werken met Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) voor een lijst met open-source toepassingen die samenwerken met Data Lake Storage Gen1.
* Zie [Azure Data Lake Storage Gen1 integreren met andere Azure-services](../data-lake-store/data-lake-store-integrate-with-other-services.md) om te begrijpen hoe u Data Lake Storage Gen1 gebruiken met andere Azure-services om een breder scala aan scenario's mogelijk te maken.
* Zie [Azure Data Lake Storage Gen1 gebruiken voor vereisten voor big data](../data-lake-store/data-lake-store-data-scenarios.md) voor meer informatie over het gebruik van Data Lake Storage Gen1 in scenario's zoals het innemen van gegevens, het verwerken van gegevens, het downloaden van gegevens en het visualiseren van gegevens.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Gegevens lake storage Gen1-bestandssysteem (adl://)
In Hadoop-omgevingen (beschikbaar met een HDInsight-cluster) hebt u toegang tot Data Lake Storage Gen1 via het nieuwe bestandssysteem, het AzureDataLakeFilesystem (adl://). De prestaties van toepassingen en services die adl:// gebruiken, kunnen worden geoptimaliseerd op manieren die momenteel niet beschikbaar zijn in WebHDFS. Als gevolg hiervan krijgt u bij het gebruik van Data Lake Storage Gen1 de flexibiliteit om gebruik te maken van de beste prestaties door gebruik te maken van de aanbevolen adl:// of bestaande code te onderhouden door de WebHDFS API rechtstreeks te blijven gebruiken. Azure HDInsight maakt optimaal gebruik van het AzureDataLakeFilesystem om de beste prestaties te leveren op Data Lake Storage Gen1.

Krijg toegang tot uw gegevens in Data Lake Storage Gen1 met het volgende:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Zie [Acties die beschikbaar zijn op de opgeslagen gegevens](../data-lake-store/data-lake-store-get-started-portal.md#properties)voor meer informatie over hoe u toegang krijgt tot de gegevens in Data Lake Storage Gen1.



## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Kennismaking met Azure Storage](../storage/common/storage-introduction.md)
