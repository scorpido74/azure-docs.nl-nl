---
title: 'Opslag: On-premises Apache Hadoop migreren naar Azure HDInsight'
description: Lees aanbevolen procedures voor opslag voor het migreren van on-premises Hadoop-clusters naar Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: b68e438a01f9f771c16fc712597308089f628f62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409470"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>On-premises Apache Hadoop-clusters migreren naar Azure HDInsight

In dit artikel worden aanbevelingen gegeven voor gegevensopslag in Azure HDInsight-systemen. Het maakt deel uit van een serie die best practices biedt om te helpen bij het migreren van on-premises Apache Hadoop-systemen naar Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Kies het juiste opslagsysteem voor HDInsight-clusters

De hdfs-mapstructuur (Apache Hadoop File System) kan opnieuw worden gemaakt in Azure Storage of Azure Data Lake Storage. U hdinsight-clusters die worden gebruikt voor berekeningen veilig verwijderen zonder gebruikersgegevens te verliezen. Beide services kunnen worden gebruikt als zowel het standaardbestandssysteem als een extra bestandssysteem voor een HDInsight-cluster. Het HDInsight-cluster en het opslagaccount moeten in dezelfde regio worden gehost.

### <a name="azure-storage"></a>Azure Storage

HDInsight-clusters kunnen de blobcontainer in Azure Storage gebruiken als het standaardbestandssysteem of een extra bestandssysteem.Het standaardopslagaccount wordt ondersteund voor gebruik met HDInsight-clusters. De Premier-laag wordt niet ondersteund. In de standaard blobcontainer worden clusterspecifieke gegevens opgeslagen, zoals taakgeschiedenis en logboeken.Het delen van één blobcontainer als het standaardbestandssysteem voor meerdere clusters wordt niet ondersteund.

De opslagaccounts die zijn gedefinieerd in het creatieproces `%HADOOP_HOME%/conf/core-site.xml` en hun respectievelijke sleutels, worden opgeslagen in de clusterknooppunten. Ze zijn ook toegankelijk onder de sectie 'Aangepaste kernsite' in HDFS-configuratie in de Ambari-gebruikersinterface. De opslagaccountsleutel wordt standaard versleuteld en een aangepast decryptiescript wordt gebruikt om de sleutels te decoderen voordat deze wordt doorgegeven aan Hadoop-daemons. De vacatures, waaronder Hive, MapReduce, Hadoop streaming en Pig, bevatten een beschrijving van opslagaccounts en metadata.

Azure Storage kan geo-gerepliceerd worden. Hoewel geo-replicatie geografische herstel- en gegevensredundantie biedt, heeft een fail-over naar de geo-gerepliceerde locatie een ernstige invloed op de prestaties en kan dit extra kosten met zich meebrengen. De aanbeveling is om de geo-replicatie verstandig te kiezen en alleen als de waarde van de gegevens de extra kosten waard is.

Een van de volgende indelingen kan worden gebruikt om toegang te krijgen tot gegevens die zijn opgeslagen in Azure Storage:

|Gegevenstoegangsindeling |Beschrijving |
|---|---|
|`wasb:///`|Toegang tot standaardopslag met onversleutelde communicatie.|
|`wasbs:///`|Toegang tot standaardopslag met behulp van versleutelde communicatie.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Wordt gebruikt bij het communiceren met een niet-standaard opslagaccount. |

[Schaalbaarheidsdoelen voor standaardopslagaccounts](../../storage/common/scalability-targets-standard-account.md) bevatten de huidige limieten voor Azure Storage-accounts. Als de behoeften van de toepassing de schaalbaarheidsdoelen van één opslagaccount overschrijden, kan de toepassing worden gebouwd om meerdere opslagaccounts te gebruiken en vervolgens gegevensobjecten in die opslagaccounts te partitioneren.

[Azure Storage Analytics](../../storage/storage-analytics.md) biedt statistieken voor alle opslagservices en Azure-portal kan worden geconfigureerd voor verzamelstatistieken die via grafieken kunnen worden gevisualiseerd. Waarschuwingen kunnen worden gemaakt om te melden wanneer er drempelwaarden zijn bereikt voor statistieken over opslagbronnen.

Azure Storage biedt [soft delete voor blobobjecten](../../storage/blobs/storage-blob-soft-delete.md) om gegevens te herstellen wanneer deze per ongeluk worden gewijzigd of verwijderd door een toepassing of andere gebruiker van een opslagaccount.

U [blobmomentopnamen](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)maken. Een momentopname is een alleen-lezen versie van een blob die op een bepaald moment is gemaakt en biedt een manier om een back-up van een blob te maken. Zodra een momentopname is gemaakt, kan deze worden gelezen, gekopieerd of verwijderd, maar niet gewijzigd.

> [!Note]
> Voor oudere versies van on-premises Hadoop-distributies die niet over het "wasbs"-certificaat beschikken, moeten ze worden geïmporteerd naar de Java-vertrouwenswinkel.

De volgende methoden kunnen worden gebruikt om certificaten in te voeren in de Java-vertrouwenswinkel:

Het Azure Blob SSL-cert downloaden naar een bestand

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Het bovenstaande bestand importeren in het Java-vertrouwensarchief op alle knooppunten

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Controleren of het toegevoegde certificaat zich in de vertrouwenswinkel bevindt

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Raadpleeg voor meer informatie de volgende artikelen:

- [Azure Storage gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-blob-storage.md)
- [Schaalbaarheidsdoelen voor standaardopslagaccounts](../../storage/common/scalability-targets-standard-account.md)
- [Schaalbaarheid en prestatiedoelen voor Blob-opslag](../../storage/blobs/scalability-targets.md)
- [Controlelijst voor prestaties en schaalbaarheid van Microsoft Azure Storage](../../storage/common/storage-performance-checklist.md)
- [Microsoft Azure Storage bewaken, problemen opsporen en oplossen](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Een Storage-account bewaken in de Azure-portal](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage implementeert HDFS- en POSIX-model voor toegangscontrole. Het biedt eersteklas integratie met AAD voor fijnkorrelige toegangscontrole. Er zijn geen grenzen aan de grootte van gegevens die het kan opslaan, of de mogelijkheid om massaal parallelle analyses uit te voeren.

Raadpleeg voor meer informatie de volgende artikelen:

- [HDInsight-clusters maken met Data Lake Storage met behulp van de Azure-portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Data Lake Storage gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-store.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 is het nieuwste opslagaanbod. Het maakt de kernmogelijkheden van de eerste generatie Azure Data Lake Storage met een Hadoop-compatibel bestandssysteemeindpunt dat rechtstreeks is geïntegreerd in Azure Blob Storage, wordt gedeifeerd. Deze verbetering combineert de schaal- en kostenvoordelen van objectopslag met de betrouwbaarheid en prestaties die doorgaans alleen worden geassocieerd met on-premises bestandssystemen.

ADLS Gen 2 is gebouwd bovenop [Azure Blob-opslag](../../storage/blobs/storage-blobs-introduction.md) en stelt u in staat om te communiceren met gegevens met behulp van zowel bestandssysteem- als objectopslagparadigma's. Functies van [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml), zoals semantiek van bestandssystemen, beveiliging op bestandsniveau en schaal, worden gecombineerd met goedkope, gelaagde opslag, mogelijkheden voor hoge beschikbaarheid/noodherstel en een groot SDK/tooling-ecosysteem van Azure [Blob-opslag](../../storage/blobs/storage-blobs-introduction.md). In Data Lake Storage Gen2 blijven alle kwaliteiten van objectopslag bestaan en voegen ze de voordelen toe van een bestandssysteeminterface die is geoptimaliseerd voor analytics-workloads.

Een fundamenteel kenmerk van Data Lake Storage Gen2 is de toevoeging van een [hiërarchische naamruimte](../../storage/data-lake-storage/namespace.md) aan de Blob-opslagservice, die objecten/bestanden organiseert in een hiërarchie van mappen voor performante gegevenstoegang.Met de hiërarchische structuur kunnen bewerkingen zoals het hernoemen of verwijderen van een map één atomaire metagegevensbewerkingen op de map zijn in plaats van alle objecten die het naamvoorvoegsel van de map delen, op te sommen en te verwerken.

In het verleden moesten cloudgebaseerde analyses compromissen sluiten op het gebied van prestaties, beheer en beveiliging. De belangrijkste functies van ADLS-opslag (Azure Data Lake Storage) Gen2 zijn als volgt:

- **Hadoop-compatibele toegang:** met Azure Data Lake Storage Gen2 u gegevens beheren en openen, net zoals u dat zou doen met een [Hadoop Distributed File System (HDFS).](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) Het nieuwe [ABFS-stuurprogramma](../../storage/data-lake-storage/abfs-driver.md) is beschikbaar in alle Apache Hadoop-omgevingen die zijn opgenomen in [Azure HDInsight.](../index.yml) Met dit stuurprogramma hebt u toegang tot gegevens die zijn opgeslagen in Data Lake Storage Gen2.

- **Een superset van POSIX-machtigingen**: Het beveiligingsmodel voor Data Lake Gen2 ondersteunt volledig ACL- en POSIX-machtigingen, samen met een extra granulariteit die specifiek is voor Data Lake Storage Gen2. Instellingen kunnen worden geconfigureerd via beheertools of via frameworks zoals Hive en Spark.

- **Kosteneffectief**: Data Lake Storage Gen2 beschikt over goedkope opslagcapaciteit en transacties. Naarmate gegevens de volledige levenscyclus doorlopen, veranderen de factureringspercentages om de kosten te minimaliseren via ingebouwde functies zoals [de levenscyclus van Azure Blob-opslag.](../../storage/common/storage-lifecycle-management-concepts.md)

- **Werkt met Blob-opslagtools, frameworks en apps:** Data Lake Storage Gen2 blijft werken met een breed scala aan tools, frameworks en toepassingen die vandaag de dag bestaan voor Blob-opslag.

- **Geoptimaliseerdstuurprogramma**: Het Azure Blob Filesystem-stuurprogramma (ABFS) is [speciaal](../../storage/data-lake-storage/abfs-driver.md) geoptimaliseerd voor big data-analyses. De bijbehorende REST API's worden opgedoken via het dfs-eindpunt, dfs.core.windows.net.

Een van de volgende indelingen kan worden gebruikt om toegang te krijgen tot gegevens die zijn opgeslagen in ADLS Gen2:
- `abfs:///`: Toegang tot de standaard gegevensopslag voor het cluster.
- `abfs://file_system@account_name.dfs.core.windows.net`: Gebruikt bij het communiceren met een niet-standaard Data Lake Storage.

Raadpleeg voor meer informatie de volgende artikelen:

- [Inleiding tot Azure Data Lake Storage Gen2](../../storage/data-lake-storage/introduction.md)
- [Het Azure Blob Filesystem-stuurprogramma (ABFS.md)](../../storage/data-lake-storage/abfs-driver.md)
- [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Azure-opslagsleutels beveiligen in de on-premises Hadoop-clusterconfiguratie

De Azure Storage-sleutels die worden toegevoegd aan de Hadoop-configuratiebestanden, zorgen voor connectiviteit tussen on-premises HDFS- en Azure Blob-opslag. Deze sleutels kunnen worden beschermd door ze te versleutelen met de Hadoop credential provider framework. Eenmaal versleuteld, kunnen ze veilig worden opgeslagen en geopend.

**Ga als u de referenties indient:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Ga als volgt te werk om het bovenstaande providerpad toe te voegen aan de core-site.xml of aan de Ambari-configuratie onder aangepaste kernsite:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> De eigenschap van het providerpad kan ook als volgt worden toegevoegd aan de opdrachtregel distcp in plaats van de sleutel op clusterniveau op core-site.xml op te slaan:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Toegang tot Azure Storage-gegevens beperken met SAS

HDInsight heeft standaard volledige toegang tot gegevens in de Azure Storage-accounts die aan het cluster zijn gekoppeld. Shared Access Signatures (SAS) op de blobcontainer kan worden gebruikt om de toegang tot de gegevens te beperken, zoals gebruikers alleen-lezen toegang tot de gegevens bieden.

### <a name="using-the-sas-token-created-with-python"></a>Het SAS-token gebruiken dat is gemaakt met python

1. Open het [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) bestand en wijzig de volgende waarden:

    |Token, eigenschap|Beschrijving|
    |---|---|
    |policy_name|De naam die u wilt gebruiken voor het opgeslagen beleid om te maken.|
    |storage_account_name|De naam van uw opslagaccount.|
    |storage_account_key|De sleutel voor de opslagrekening.|
    |storage_container_name|De container in het opslagaccount waartoe u de toegang wilt beperken.|
    |example_file_path|Het pad naar een bestand dat naar de container wordt geüpload.|

2. Het SASToken.py bestand `ContainerPermissions.READ + ContainerPermissions.LIST` wordt geleverd met de machtigingen en kan worden aangepast op basis van de use case.

3. Voer het script als volgt uit:`python SASToken.py`

4. Het sas-token wordt weergegeven die vergelijkbaar is met de volgende tekst wanneer het script is voltooid:`sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Als u de toegang tot een container met Gedeelde toegangshandtekening wilt beperken, voegt u een aangepaste vermelding toe aan de kernsiteconfiguratie voor het cluster onder de eigenschap Geavanceerde toepassing van de kernsite van Ambari HDFS Configs.

6. Gebruik de volgende waarden voor de velden **Sleutel** en **Waarde:**

    **Sleutel:** `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **Waarde**: De SAS KEY geretourneerd door de Python-toepassing VAN stap 4 hierboven.

7. Klik **op** de knop Toevoegen om deze sleutel en waarde op te slaan en klik vervolgens op de knop **Opslaan** om de configuratiewijzigingen op te slaan. Voeg desgevraagd een beschrijving van de wijziging toe ('SAS-opslagtoegang toevoegen' bijvoorbeeld) en klik op **Opslaan**.

8. Selecteer in de gebruikersinterface van het Web Ambari HDFS in de lijst aan de linkerkant en selecteer **Vervolgens Alle betrokkenen opnieuw starten** in de vervolgkeuzelijst Serviceacties aan de rechterkant. Wanneer u daarom wordt gevraagd, selecteert u **Alles opnieuw starten bevestigen**.

9. Herhaal dit proces voor MapReduce2 en YARN.

Er zijn drie belangrijke dingen om te onthouden over het gebruik van SAS-tokens in Azure:

1. Wanneer SAS-tokens worden gemaakt met machtigingen voor LEZEN + LIJST, kunnen gebruikers die toegang hebben tot de Blob-container met dat SAS-token niet in staat zijn om gegevens te "schrijven en verwijderen". Gebruikers die toegang hebben tot de Blob-container met dat SAS-token `"This request is not authorized to perform this operation"`en een schrijf- of verwijderingsbewerking proberen, ontvangen een bericht als .

2. Wanneer de SAS-tokens `READ + LIST + WRITE` worden gegenereerd `DELETE` met machtigingen (alleen te beperken), schrijven opdrachten zoals `hadoop fs -put` eerst naar een `\_COPYING\_` bestand en proberen vervolgens de naam van het bestand te wijzigen. Deze HDFS-bewerking `copy+delete` wordt toegewezen aan een voor WASB. Aangezien `DELETE` de toestemming niet werd verstrekt, zou de "put" mislukken. De `\_COPYING\_` bewerking is een Hadoop-functie die bedoeld is om enige gelijktijdigheidscontrole te bieden. Momenteel is er geen manier om alleen de "DELETE" operatie te beperken zonder invloed op "WRITE" operaties ook.

3. Helaas, de hadoop credential provider en decryptie sleutel provider (ShellDecryptionKeyProvider) werkt momenteel niet met de SAS tokens en dus het kan momenteel niet worden beschermd tegen zichtbaarheid.

Zie [Azure Storage Shared Access Signatures gebruiken om de toegang tot gegevens in HDInsight te beperken](../hdinsight-storage-sharedaccesssignature-permissions.md)voor meer informatie.

## <a name="use-data-encryption-and-replication"></a>Gegevensversleuteling en -replicatie gebruiken

Alle gegevens die naar Azure Storage zijn verzonden, worden automatisch versleuteld met [Storage Service Encryption (SSE).](../../storage/common/storage-service-encryption.md) De gegevens in het Azure Storage-account worden altijd gerepliceerd voor hoge beschikbaarheid.Wanneer u een opslagaccount maakt, u een van de volgende replicatieopties kiezen:

- [Lokaal redundante opslag (LRS)](../../storage/common/storage-redundancy-lrs.md)
- [Zone-redundante opslag (ZRS)](../../storage/common/storage-redundancy-zrs.md)
- [Georedundante opslag (GRS)](../../storage/common/storage-redundancy-grs.md)
- [Geografisch redundante opslag met leestoegang (RA-GRS)](../../storage/common/storage-redundancy.md)

Azure Data Lake Storage biedt lokaal redundante opslag (LRS), maar u moet ook kritieke gegevens kopiëren naar een ander Data Lake Storage-account in een andere regio met een frequentie die is afgestemd op de behoeften van het noodherstelplan.Er zijn verschillende methoden om gegevens te kopiëren, waaronder [ADLCopy,](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) [DistCp,](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html) [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)of [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md).Het wordt ook aanbevolen om toegangsbeleid voor het Data Lake Storage-account af te dwingen om onbedoelde verwijdering te voorkomen.

Raadpleeg voor meer informatie de volgende artikelen:

- [Azure Storage-replicatie](../../storage/common/storage-redundancy.md)
- [Richtlijnen voor rampen voor Azure Data Lake Storage (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Extra Azure Storage-accounts toevoegen aan cluster

Tijdens het maakproces van HDInsight wordt een Azure Storage-account of Azure Data Lake Storage-account gekozen als het standaardbestandssysteem. Naast dit standaardopslagaccount kunnen extra opslagaccounts worden toegevoegd vanuit hetzelfde Azure-abonnement of verschillende Azure-abonnementen tijdens het proces voor het maken van het cluster of nadat een cluster is gemaakt.

Extra opslagaccount kan op de volgende manieren worden toegevoegd:
- Ambari HDFS Config Advanced Custom-kernsite Voeg de naam van het opslagaccount en de sleutel toe Om de services opnieuw op te starten
- [Script-actie gebruiken](../hdinsight-hadoop-add-storage.md) door de naam en sleutel van het opslagaccount door te geven

> [!Note]
> In geldige use-cases kunnen de limieten voor de Azure-opslag worden verhoogd via een aanvraag voor [Azure Support.](https://azure.microsoft.com/support/faq/)

Zie [Extra opslagaccounts toevoegen aan HDInsight](../hdinsight-hadoop-add-storage.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel in deze serie: [Best practices voor gegevensmigratie voor on-premises naar Azure HDInsight Hadoop-migratie](apache-hadoop-on-premises-migration-best-practices-data-migration.md).
