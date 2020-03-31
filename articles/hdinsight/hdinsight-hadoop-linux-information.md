---
title: Tips voor het gebruik van Hadoop op Linux-gebaseerde HDInsight - Azure
description: Ontvang implementatietips voor het gebruik van HDInsight-clusters (Hadoop) op basis van Linux op een vertrouwde Linux-omgeving die wordt uitgevoerd in de Azure-cloud.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 3d9dec0065bb62821fcedcbc4f6e5b578c061caf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272459"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informatie over het gebruik van HDInsight in Linux

Azure HDInsight-clusters bieden Apache Hadoop op een vertrouwde Linux-omgeving, uitgevoerd in de Azure-cloud. Voor de meeste dingen, het moet precies werken als elke andere Hadoop-on-Linux installatie. In dit document worden specifieke verschillen opgeroepen die u moet weten.

## <a name="prerequisites"></a>Vereisten

Veel van de stappen in dit document maken gebruik van de volgende hulpprogramma's, die mogelijk op uw systeem moeten worden geïnstalleerd.

* [cURL](https://curl.haxx.se/) - gebruikt om te communiceren met web-based services.
* **jq**, een command-line JSON processor.  Zie [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) - wordt gebruikt om Azure-services op afstand te beheren.
* **Een SSH-client.** Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Gebruikers

Tenzij [het domein is aangesloten,](./domain-joined/hdinsight-security-overview.md)moet HDInsight worden beschouwd als een systeem voor één **gebruiker.** Er wordt één SSH-gebruikersaccount gemaakt met het cluster, met machtigingen op beheerdersniveau. Er kunnen extra SSH-accounts worden gemaakt, maar ze hebben ook beheerderstoegang tot het cluster.

HdInsight met domein wordt verbonden, ondersteunt meerdere gebruikers en meer gedetailleerde machtigings- en rolinstellingen. Zie Clusters [van domeinverbonden HDInsight](./domain-joined/apache-domain-joined-manage.md)beheren voor meer informatie.

## <a name="domain-names"></a>Domeinnamen

De volledig gekwalificeerde domeinnaam (FQDN) te gebruiken bij het `CLUSTERNAME.azurehdinsight.net` `CLUSTERNAME-ssh.azurehdinsight.net` aansluiten op het cluster vanaf het internet is of (alleen voor SSH).

Intern heeft elk knooppunt in het cluster een naam die is toegewezen tijdens de clusterconfiguratie. Zie de pagina Hosts op de gebruikersinterface **van** Ambari Web om de clusternamen te vinden. U het volgende ook gebruiken om een lijst met hosts terug te sturen uit de Ambari REST API:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Vervang `CLUSTERNAME` door de naam van uw cluster. Voer het wachtwoord voor het beheerdersaccount in wanneer u daarom wordt gevraagd. Met deze opdracht wordt een JSON-document geretourneerd met een lijst met de hosts in het cluster. [jq](https://stedolan.github.io/jq/) wordt gebruikt `host_name` om de elementwaarde voor elke host te extraheren.

Als u de naam van het knooppunt voor een specifieke service wilt vinden, u Ambari voor dat onderdeel opvragen. Als u bijvoorbeeld de hosts voor het HDFS-naamknooppunt wilt vinden, gebruikt u de volgende opdracht:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Met deze opdracht wordt een JSON-document geretourneerd waarin de `host_name` service wordt beschreven en [haalt jq](https://stedolan.github.io/jq/) alleen de waarde voor de hosts eruit.

## <a name="remote-access-to-services"></a>Toegang op afstand tot services

* **Ambari (web)** - `https://CLUSTERNAME.azurehdinsight.net`

    Verifieer met de gebruiker en het wachtwoord van de clusterbeheerder en meld u vervolgens aan bij Ambari.

    Verificatie is plaintext - gebruik altijd HTTPS om ervoor te zorgen dat de verbinding veilig is.

    > [!IMPORTANT]  
    > Sommige van de web-UI's die beschikbaar zijn via Ambari-toegangsknooppunten met behulp van een interne domeinnaam. Interne domeinnamen zijn niet openbaar toegankelijk via het internet. Mogelijk ontvangt u fouten als u "server niet gevonden" fouten ontvangt wanneer u toegang probeert te krijgen tot bepaalde functies via internet.
    >
    > Als u de volledige functionaliteit van de Ambari-webgebruikersinterface wilt gebruiken, gebruikt u een SSH-tunnel om webverkeer naar het clusterhoofdknooppunt te proxyn. Zie [SSH-tunneling gebruiken om toegang te krijgen tot de UI, ResourceManager, JobHistory, NameNode, Oozie en andere web-UI's](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (RUST)** - `https://CLUSTERNAME.azurehdinsight.net/ambari`

    > [!NOTE]  
    > Verifieer met behulp van de gebruiker en het wachtwoord van de clusterbeheerder.
    >
    > Verificatie is plaintext - gebruik altijd HTTPS om ervoor te zorgen dat de verbinding veilig is.

* **WebHCat (Templeton)** - `https://CLUSTERNAME.azurehdinsight.net/templeton`

    > [!NOTE]  
    > Verifieer met behulp van de gebruiker en het wachtwoord van de clusterbeheerder.
    >
    > Verificatie is plaintext - gebruik altijd HTTPS om ervoor te zorgen dat de verbinding veilig is.

* **SSH** - CLUSTERNAME-ssh.azurehdinsight.net op poort 22 of 23. Poort 22 wordt gebruikt om verbinding te maken met de primaire headnode, terwijl 23 wordt gebruikt om verbinding te maken met de secundaire. Zie [Beschikbaarheid en betrouwbaarheid van Apache Hadoop-clusters in HDInsight](hdinsight-high-availability-linux.md)voor meer informatie over de hoofdknooppunten.

    > [!NOTE]  
    > U hebt alleen toegang tot de clusterkopknooppunten via SSH vanaf een clientmachine. Eenmaal aangesloten, u vervolgens toegang krijgen tot de werknemersknooppunten met behulp van SSH vanaf een headnode.

Zie voor meer informatie de [poorten die worden gebruikt door Apache Hadoop-services op HDInsight-document.](hdinsight-hadoop-port-settings-for-services.md)

## <a name="file-locations"></a>Bestandslocaties

Hadoop-gerelateerde bestanden zijn te vinden `/usr/hdp`op de clusterknooppunten op . Deze map bevat de volgende submappen:

* **2.6.5.3009-43**: De directorynaam is de versie van het Hadoop-platform dat door HDInsight wordt gebruikt. Het nummer op uw cluster kan afwijken van het nummer dat hier wordt vermeld.
* **actueel**: Deze map bevat koppelingen naar submappen onder de map **2.6.5.3009-43.** Deze map bestaat zodat u het versienummer niet hoeft te onthouden.

Voorbeeldgegevens en JAR-bestanden zijn te vinden op `/example` Hadoop Distributed File System op en `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Azure Storage en Data Lake Storage

In de meeste Hadoop-distributies worden de gegevens opgeslagen in HDFS, dat wordt ondersteund door lokale opslag op de machines in het cluster. Het gebruik van lokale opslag kan duur zijn voor een cloudoplossing waarbij u per uur of per minuut wordt opgeladen voor rekenbronnen.

Bij gebruik van HDInsight worden de gegevensbestanden op een schaalbare en veerkrachtige manier opgeslagen in de cloud met Azure Blob Storage en optioneel Azure Data Lake Storage. Deze diensten bieden de volgende voordelen:

* Goedkope opslag op lange termijn.
* Toegankelijkheid van externe services zoals websites, hulpprogramma's voor het uploaden/downloaden van bestanden, verschillende taal-SDK's en webbrowsers.
* Grote bestandscapaciteit en grote schaalbare opslag.

Zie [Blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) en Data [Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)begrijpen voor meer informatie.

Wanneer u Azure Storage of Data Lake Storage gebruikt, hoeft u niets speciaals te doen van HDInsight om toegang te krijgen tot de gegevens. In de volgende opdracht worden `/example/data` bijvoorbeeld bestanden in de map weergegeven, ongeacht of deze zijn opgeslagen in Azure Storage of Data Lake Storage:

    hdfs dfs -ls /example/data

In HDInsight worden de bronnen voor gegevensopslag (Azure Blob Storage en Azure Data Lake Storage) losgekoppeld van rekenbronnen. Daarom u HDInsight-clusters maken om berekeningen te doen wanneer u dat nodig hebt, en het cluster later verwijderen wanneer het werk is voltooid, zodat uw gegevensbestanden veilig in cloudopslag blijven bestaan zolang als u nodig hebt.

### <a name="uri-and-scheme"></a><a name="URI-and-scheme"></a>URI en schema

Voor sommige opdrachten moet u het schema mogelijk opgeven als onderdeel van de URI wanneer u een bestand opent. Voor de component Storm-HDFS moet u bijvoorbeeld het schema opgeven. Wanneer u niet-standaardopslag gebruikt (opslag toegevoegd als "extra" opslag aan het cluster), moet u het schema altijd gebruiken als onderdeel van de URI.

Gebruik bij het gebruik van [**Azure Storage**](./hdinsight-hadoop-use-blob-storage.md)een van de volgende URI-schema's:

* `wasb:///`: Toegang tot standaardopslag met onversleutelde communicatie.

* `wasbs:///`: Toegang tot standaardopslag met versleutelde communicatie.  De wasbs-regeling wordt alleen ondersteund vanaf HDInsight versie 3.6.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Wordt gebruikt bij het communiceren met een niet-standaard opslagaccount. Bijvoorbeeld wanneer u een extra opslagaccount hebt of wanneer u toegang hebt tot gegevens die zijn opgeslagen in een openbaar toegankelijk opslagaccount.

Wanneer u [**Azure Data Lake Storage Gen2**](./hdinsight-hadoop-use-data-lake-storage-gen2.md)gebruikt, gebruikt u het volgende URI-schema:

* `abfs://`: Toegang tot standaardopslag met versleutelde communicatie.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Wordt gebruikt bij het communiceren met een niet-standaard opslagaccount. Bijvoorbeeld wanneer u een extra opslagaccount hebt of wanneer u toegang hebt tot gegevens die zijn opgeslagen in een openbaar toegankelijk opslagaccount.

Wanneer u [**Azure Data Lake Storage Gen1**](./hdinsight-hadoop-use-data-lake-store.md)gebruikt, gebruikt u een van de volgende URI-schema's:

* `adl:///`: Toegang tot de standaard gegevensopslag voor het cluster.

* `adl://<storage-name>.azuredatalakestore.net/`: Gebruikt bij het communiceren met een niet-standaard Data Lake Storage. Ook gebruikt om toegang te krijgen tot gegevens buiten de hoofdmap van uw HDInsight-cluster.

> [!IMPORTANT]  
> Wanneer u Data Lake Storage gebruikt als het standaardarchief voor HDInsight, moet u een pad binnen de winkel opgeven dat u wilt gebruiken als de hoofdmap van HDInsight-opslag. Het standaardpad `/clusters/<cluster-name>/`is .
>
> Wanneer `/` u `adl:///` gegevens gebruikt of toegang hebt tot gegevens, hebt `/clusters/<cluster-name>/`u alleen toegang tot gegevens die zijn opgeslagen in de hoofdmap (bijvoorbeeld ) van het cluster. Als u overal in de `adl://<storage-name>.azuredatalakestore.net/` winkel toegang wilt krijgen tot gegevens, gebruikt u de indeling.

### <a name="what-storage-is-the-cluster-using"></a>Welke opslag gebruikt het cluster

U Ambari gebruiken om de standaardopslagconfiguratie voor het cluster op te halen. Gebruik de volgende opdracht om HDFS-configuratiegegevens op te halen met behulp van krul en deze te filteren met [jq:](https://stedolan.github.io/jq/)

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Met deze opdracht wordt de eerste`service_config_version=1`configuratie geretourneerd die is toegepast op de server ( ), die deze informatie bevat. Mogelijk moet u alle configuratieversies weergeven om de nieuwste versie te vinden.

Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende URI's:

* `wasb://<container-name>@<account-name>.blob.core.windows.net`als u een Azure Storage-account gebruikt.

    De accountnaam is de naam van het Azure Storage-account. De containernaam is de blobcontainer die de hoofdmap van de clusteropslag is.

* `adl://home`als u Azure Data Lake Storage gebruikt. Als u de naam Data Lake Storage wilt krijgen, gebruikt u de volgende REST-aanroep:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Met deze opdracht wordt `<data-lake-store-account-name>.azuredatalakestore.net`de volgende hostnaam geretourneerd: .

    Als u de map in de winkel wilt krijgen die de hoofdmap voor HDInsight is, gebruikt u de volgende REST-aanroep:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Met deze opdracht wordt een pad `/clusters/<hdinsight-cluster-name>/`geretourneerd dat vergelijkbaar is met het volgende pad: .

U de opslaggegevens ook vinden via de Azure-portal met behulp van de volgende stappen:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)uw HDInsight-cluster.

2. Selecteer **opslagaccounts**in de sectie **Eigenschappen** . De opslaggegevens voor het cluster worden weergegeven.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Hoe krijg ik toegang tot bestanden van buiten HDInsight

Er zijn verschillende manieren om toegang te krijgen tot gegevens van buiten het HDInsight-cluster. Hieronder volgen enkele links naar hulpprogramma's en SDK's die kunnen worden gebruikt om met uw gegevens te werken:

Als u __Azure Storage gebruikt,__ raadpleegt u de volgende koppelingen voor manieren waarop u toegang hebt tot uw gegevens:

* [Azure CLI:](https://docs.microsoft.com/cli/azure/install-az-cli2)Command-Line-interfaceopdrachten voor het werken met Azure. Gebruik na het `az storage` installeren de opdracht voor `az storage blob` hulp bij het gebruik van opslag of voor blobspecifieke opdrachten.
* [blobxfer.py:](https://github.com/Azure/blobxfer)een python-script voor het werken met blobs in Azure Storage.
* Diverse SDK's:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [Php](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Storage REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Als u __Azure Data Lake Storage gebruikt,__ raadpleegt u de volgende koppelingen voor manieren waarop u toegang hebt tot uw gegevens:

* [Webbrowser](../data-lake-store/data-lake-store-get-started-portal.md)
* [Powershell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure-CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake-hulpprogramma's voor Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling-your-cluster"></a><a name="scaling"></a>Uw cluster schalen

Met de functie clusterschaling u dynamisch het aantal gegevensknooppunten wijzigen dat door een cluster wordt gebruikt. U schaalbewerkingen uitvoeren terwijl andere taken of processen op een cluster worden uitgevoerd.  Zie ook, [Scale HDInsight clusters](./hdinsight-scaling-best-practices.md)

De verschillende clustertypen worden als volgt beïnvloed door schaalvergroting:

* **Hadoop**: Wanneer het aantal knooppunten in een cluster wordt afschaald, worden sommige services in het cluster opnieuw gestart. Schaalbewerkingen kunnen ertoe leiden dat taken die worden uitgevoerd of in behandeling zijn, mislukken bij het voltooien van de schaalbewerking. U de taken opnieuw indienen zodra de bewerking is voltooid.
* **HBase**: Regionale servers worden automatisch binnen een paar minuten in evenwicht gebracht, zodra de schalingbewerking is voltooid. Als u regionale servers handmatig wilt in evenwicht brengen, gebruikt u de volgende stappen:

    1. Maak verbinding met het HDInsight-cluster via SSH. Zie [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie.

    2. Gebruik het volgende om de HBase-shell te starten:

            hbase shell

    3. Zodra de HBase-shell is geladen, gebruikt u het volgende om de regionale servers handmatig in evenwicht te brengen:

            balancer

* **Storm:** U moet alle lopende Stormtopologieën opnieuw in evenwicht brengen nadat een schaalbewerking is uitgevoerd. Met rebalancing kan de topologie parallellisme-instellingen aanpassen op basis van het nieuwe aantal knooppunten in het cluster. Als u de uitvoerende topologieën opnieuw wilt in evenwicht brengen, gebruikt u een van de volgende opties:

    * **SSH**: Maak verbinding met de server en gebruik de volgende opdracht om een topologie opnieuw in evenwicht te brengen:

            storm rebalance TOPOLOGYNAME

        U ook parameters opgeven om de parallelle hints die oorspronkelijk door de topologie werden verstrekt, te overschrijven. Hiermee `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` wordt de topologie bijvoorbeeld opnieuw geconfigureerd tot 5 werkprocessen, 3 uitvoerders voor de component blauw-uitloop en 10 uitvoerders voor de component geelbout.

    * **Storm gebruikersinterface:** gebruik de volgende stappen om een topologie opnieuw in evenwicht te brengen met behulp van de gebruikersinterface Storm.

        1. Open `https://CLUSTERNAME.azurehdinsight.net/stormui` in uw webbrowser, waar `CLUSTERNAME` is de naam van uw Stormcluster. Voer de naam en het wachtwoord van de HDInsight-clusterbeheerder (admin) in die u hebt opgegeven bij het maken van het cluster.
        2. Selecteer de topologie die u opnieuw in evenwicht wilt brengen en selecteer vervolgens de knop **Opnieuw in evenwicht** brengen. Voer de vertraging in voordat de bewerking opnieuw in evenwicht wordt gebracht.

* **Kafka:** U moet partitiereplica's opnieuw in evenwicht brengen na het schalen van bewerkingen. Zie voor meer informatie de [hoge beschikbaarheid van gegevens met Apache Kafka op HDInsight-document.](./kafka/apache-kafka-high-availability.md)

Zie voor specifieke informatie over het schalen van uw HDInsight-cluster:

* [Apache Hadoop-clusters beheren in HDInsight met behulp van de Azure-portal](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Apache Hadoop-clusters beheren in HDInsight met Azure CLI](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hoe installeer ik Hue (of een ander Hadoop-onderdeel)?

HDInsight is een managed service. Als Azure een probleem met het cluster detecteert, kan het het falende knooppunt worden verwijderd en een knooppunt worden gemaakt om het te vervangen. Als u handmatig dingen op het cluster installeert, worden deze niet volgehouden wanneer deze bewerking plaatsvindt. Gebruik in plaats daarvan [HDInsight Script-acties](hdinsight-hadoop-customize-cluster-linux.md). Een scriptactie kan worden gebruikt om de volgende wijzigingen aan te brengen:

* Een service of website installeren en configureren.
* Installeer en configureer een component waarvoor configuratiewijzigingen nodig zijn op meerdere knooppunten in het cluster.

Scriptacties zijn Bash-scripts. De scripts worden uitgevoerd tijdens het maken van het cluster en worden gebruikt om extra componenten te installeren en te configureren. Zie [Ontwikkeling van scriptacties met HDInsight](hdinsight-hadoop-script-actions-linux.md) voor informatie over het ontwikkelen van uw eigen scriptacties.

### <a name="jar-files"></a>Jar-bestanden

Sommige Hadoop-technologieën worden geleverd in op zichzelf staande jarbestanden die functies bevatten die worden gebruikt als onderdeel van een MapReduce-taak, of van binnenuit Pig of Hive. Ze vereisen vaak geen setup en kunnen na het maken naar het cluster worden geüpload en direct worden gebruikt. Als u ervoor wilt zorgen dat de component reimaging van het cluster overleeft, u het jar-bestand opslaan in de standaardopslag voor uw cluster (WASB of ADL).

Als u bijvoorbeeld de nieuwste versie van [Apache DataFu](https://datafu.incubator.apache.org/)wilt gebruiken, u een pot met het project downloaden en uploaden naar het HDInsight-cluster. Volg dan de DataFu documentatie over hoe het te gebruiken van Pig of Hive.

> [!IMPORTANT]  
> Sommige componenten die standalone jar-bestanden zijn, zijn voorzien van HDInsight, maar zijn niet in het pad. Als u op zoek bent naar een specifieke component, u de follow gebruiken om deze te zoeken op uw cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Met deze opdracht wordt het pad van alle overeenkomende jarbestanden geretourneerd.

Als u een andere versie van een component wilt gebruiken, uploadt u de versie die u nodig hebt en gebruikt u deze in uw taken.

> [!IMPORTANT]
> Onderdelen die zijn geleverd met het HDInsight-cluster worden volledig ondersteund en Microsoft Support helpt bij het isoleren en oplossen van problemen met betrekking tot deze componenten.
>
> Aangepaste componenten ontvangen commercieel redelijke ondersteuning om u te helpen het probleem verder op te lossen. Dit kan resulteren in het oplossen van het probleem of u vragen om beschikbare kanalen in te schakelen voor de open source-technologieën waar diepgaande expertise voor die technologie wordt gevonden. Er zijn bijvoorbeeld veel communitysites die kunnen worden gebruikt, zoals: [https://stackoverflow.com](https://stackoverflow.com) [MSDN-forum voor HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), . Ook Apache projecten hebben [https://apache.org](https://apache.org)projectsites op , bijvoorbeeld: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met behulp van de Apache Ambari REST API](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [MapReduce-taken gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)
