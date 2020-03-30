---
title: Hoge beschikbaarheid voor Hadoop - Azure HDInsight
description: Ontdek hoe HDInsight-clusters de betrouwbaarheid en beschikbaarheid verbeteren met behulp van een extra hoofdknooppunt. Ontdek hoe dit gevolgen heeft voor Hadoop-services zoals Ambari en Hive en hoe u individueel verbinding maken met elk hoofdknooppunt met Behulp van SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop hoge beschikbaarheid
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 085933f9a74ee37779ce63ce499d89ea53a9f7d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198936"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Beschikbaarheid en betrouwbaarheid van Apache Hadoop clusters in HDInsight

HDInsight-clusters bieden twee hoofdknooppunten om de beschikbaarheid en betrouwbaarheid van Apache Hadoop-services en -taken te verhogen.

Hadoop bereikt hoge beschikbaarheid en betrouwbaarheid door services en gegevens te repliceren over meerdere knooppunten in een cluster. Nochtans hebben de standaarddistributies van Hadoop typisch slechts één enkel hoofdknooppunt. Elke uitval van het enkelhoofdknooppunt kan ertoe leiden dat het cluster niet meer werkt. HDInsight biedt twee headnodes om de beschikbaarheid en betrouwbaarheid van Hadoop te verbeteren.

## <a name="availability-and-reliability-of-nodes"></a>Beschikbaarheid en betrouwbaarheid van knooppunten

Knooppunten in een HDInsight-cluster worden geïmplementeerd met Azure Virtual Machines. In de volgende secties worden de afzonderlijke knooppunttypen besproken die met HDInsight worden gebruikt.

> [!NOTE]  
> Niet alle knooppunttypen worden gebruikt voor een clustertype. Een Hadoop-clustertype heeft bijvoorbeeld geen Nimbus-knooppunten. Zie de sectie Clustertypen van de [Hadoop-clusters in HDInsight voor](hdinsight-hadoop-provision-linux-clusters.md#cluster-type) meer informatie over knooppunten die worden gebruikt door HDInsight-clustertypen.

### <a name="head-nodes"></a>Hoofdknooppunten

Om een hoge beschikbaarheid van Hadoop-services te garanderen, biedt HDInsight twee hoofdknooppunten. Beide hoofdknooppunten zijn actief en worden tegelijkertijd uitgevoerd binnen het HDInsight-cluster. Sommige services, zoals Apache HDFS of Apache Hadoop YARN, zijn slechts 'actief' op één hoofdknooppunt op een bepaald moment. Andere services zoals HiveServer2 of Hive MetaStore zijn tegelijkertijd actief op beide hoofdknooppunten.

Als u de hostnamen voor verschillende knooppunttypen in uw cluster wilt verkrijgen, gebruikt u de [Ambari REST API.](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes)

> [!IMPORTANT]  
> Koppel de numerieke waarde niet aan de vraag of een knooppunt primair of secundair is. De numerieke waarde is alleen aanwezig om een unieke naam voor elk knooppunt op te geven.

### <a name="nimbus-nodes"></a>Nimbus-knooppunten

Nimbus-knooppunten zijn beschikbaar met Apache Storm-clusters. De Nimbus-knooppunten bieden vergelijkbare functionaliteit als de Hadoop JobTracker door verwerking over werknemersknooppunten te distribueren en te bewaken. HDInsight biedt twee Nimbus-knooppunten voor Storm-clusters

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper knooppunten

[ZooKeeper](https://zookeeper.apache.org/) knooppunten worden gebruikt voor de leider verkiezing van master diensten op hoofdknooppunten. Ze worden ook gebruikt om te verzekeren dat services, gegevens (werknemer) knooppunten en gateways weten op welk hoofdknooppunt een hoofdservice actief is. HDInsight biedt standaard drie ZooKeeper-knooppunten.

### <a name="worker-nodes"></a>Werkknooppunten

Werknemersknooppunten voeren de werkelijke gegevensanalyse uit wanneer een taak in het cluster wordt verzonden. Als een werknemersknooppunt mislukt, wordt de taak die het heeft uitgevoerd, ingediend bij een ander werknemersknooppunt. HDInsight maakt standaard vier werkknooppunten. U dit nummer aanpassen aan uw behoeften, zowel tijdens als na het maken van het cluster.

### <a name="edge-node"></a>randknooppunt

Een randknooppunt neemt niet actief deel aan gegevensanalyse binnen het cluster. Het wordt gebruikt door ontwikkelaars of data scientists bij het werken met Hadoop. Het randknooppunt leeft in hetzelfde Azure Virtual Network als de andere knooppunten in het cluster en heeft rechtstreeks toegang tot alle andere knooppunten. Het randknooppunt kan worden gebruikt zonder resources weg te halen bij kritieke Hadoop-services of analysetaken.

Momenteel is ML Services op HDInsight het enige clustertype dat standaard een randknooppunt biedt. Voor ML Services op HDInsight wordt het randknooppunt lokaal gebruikt testR-code op het knooppunt voordat deze wordt ingediend bij het cluster voor gedistribueerde verwerking.

Zie het document [Randknooppunten gebruiken in HDInsight](hdinsight-apps-use-edge-node.md) voor informatie over het gebruik van een randknooppunt met andere clustertypen.

## <a name="accessing-the-nodes"></a>Toegang tot de knooppunten

Toegang tot het cluster via het internet wordt geboden via een openbare gateway. Toegang is beperkt tot het verbinden met de hoofdknooppunten en, als er een bestaat, het randknooppunt. Toegang tot services die op de hoofdknooppunten worden uitgevoerd, wordt niet beïnvloed door het hebben van meerdere hoofdknooppunten. De openbare gateway leidt aanvragen naar het hoofdknooppunt dat de gevraagde service host. Als Apache Ambari momenteel wordt gehost op het secundaire hoofdknooppunt, worden de binnenkomende aanvragen voor Ambari naar dat knooppunt gerouteerd.

Toegang via de openbare gateway is beperkt tot poorten 443 (HTTPS), 22 en 23.

|Poort |Beschrijving |
|---|---|
|443|Wordt gebruikt om toegang te krijgen tot Ambari en andere web-ui- of REST-API's die worden gehost op de hoofdknooppunten.|
|22|Wordt gebruikt om toegang te krijgen tot het primaire hoofdknooppunt of het randknooppunt met SSH.|
|23|Wordt gebruikt om toegang te krijgen tot het secundaire hoofdknooppunt met SSH. Maakt bijvoorbeeld `ssh username@mycluster-ssh.azurehdinsight.net` verbinding met het primaire hoofdknooppunt van het cluster met de naam **mycluster**.|

Zie het document [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie over het gebruik van SSH.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Interne volledig gekwalificeerde domeinnamen (FQDN)

Knooppunten in een HDInsight-cluster hebben een intern IP-adres en FQDN dat alleen vanuit het cluster toegankelijk is. Wanneer u toegang krijgt tot services op het cluster met behulp van het interne FQDN- of IP-adres, moet u Ambari gebruiken om het IP of FQDN te verifiëren dat u gebruiken bij de toegang tot de service.

De Apache Oozie-service kan bijvoorbeeld slechts op één hoofdknooppunt worden uitgevoerd en voor het gebruik van de `oozie` opdracht van een SSH-sessie is de URL naar de service vereist. Deze URL kan uit Ambari worden opgehaald met de volgende opdracht:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is `oozie` met de volgende waarde, die de interne URL bevat die met de opdracht moet worden gebruikt:

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Zie [HDInsight controleren en beheren met behulp van de Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)voor meer informatie over het werken met de Ambari REST API.

### <a name="accessing-other-node-types"></a>Toegang tot andere knooppunttypen

U verbinding maken met knooppunten die niet rechtstreeks toegankelijk zijn via internet met behulp van de volgende methoden:

|Methode |Beschrijving |
|---|---|
|SSH|Eenmaal verbonden met een hoofdknooppunt met SSH, u Vervolgens SSH vanaf het hoofdknooppunt gebruiken om verbinding te maken met andere knooppunten in het cluster. Zie het document [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.|
|SSH-tunnel|Als u toegang moet krijgen tot een webservice die wordt gehost op een van de knooppunten die niet zijn blootgesteld aan internet, moet u een SSH-tunnel gebruiken. Zie het document [Gebruik een SSH-tunnel met HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) voor meer informatie.|
|Azure Virtual Network|Als uw HDInsight-cluster deel uitmaakt van een Azure Virtual Network, heeft elke bron op hetzelfde virtuele netwerk rechtstreeks toegang tot alle knooppunten in het cluster. Zie Het document [Plan een virtueel netwerk voor HDInsight voor](hdinsight-plan-virtual-network-deployment.md) meer informatie.|

## <a name="how-to-check-on-a-service-status"></a>Een servicestatus controleren

Als u de status wilt controleren van services die op de hoofdknooppunten worden uitgevoerd, gebruikt u de Ambari Web UI of de Ambari REST-API.

### <a name="ambari-web-ui"></a>Ambari Web UI

De Ambari Web UI `https://CLUSTERNAME.azurehdinsight.net`is zichtbaar op . Vervang **CLUSTERNAME** door de naam van uw cluster. Voer de http-gebruikersreferenties voor uw cluster in als u daarom wordt gevraagd. De standaard HTTP-gebruikersnaam is **beheerder** en het wachtwoord is het wachtwoord dat u hebt ingevoerd bij het maken van het cluster.

Wanneer u op de Ambari-pagina aankomt, worden de geïnstalleerde services links van de pagina weergegeven.

![Apache Ambari geïnstalleerde services](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Er zijn een reeks pictogrammen die naast een service kunnen worden weergegeven om de status aan te geven. Alle waarschuwingen met betrekking tot een service kunnen worden bekeken via de link **Waarschuwingen** boven aan de pagina.  Ambari biedt verschillende vooraf gedefinieerde waarschuwingen.

Met de volgende waarschuwingen u de beschikbaarheid van een cluster controleren:

| Naam van waarschuwing                               | Beschrijving                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Status van metrische monitor                    | Deze waarschuwing geeft de status aan van het proces Met statistiekenmonitor, zoals bepaald door het statusscript van de monitor.                                                                                   |
| Ambari Agent Heartbeat                   | Deze waarschuwing wordt geactiveerd als de server het contact met een agent heeft verloren.                                                                                                                        |
| ZooKeeper-serverproces                 | Deze waarschuwing op hostniveau wordt geactiveerd als het ZooKeeper-serverproces niet kan worden bepaald dat het op het netwerk moet worden beluisterd.                                                               |
| Status iocache-metagegevensserver           | Deze waarschuwing op hostniveau wordt geactiveerd als de IOCache Metadata Server niet kan worden vastgesteld dat deze up is en reageert op clientverzoeken                                                            |
| Gebruikersinterface van JournalNode Web                       | Deze waarschuwing op hostniveau wordt geactiveerd als de Gebruikersinterface van JournalNode Web onbereikbaar is.                                                                                                                 |
| Spark2-kringloopserver                     | Deze waarschuwing op hostniveau wordt geactiveerd als de Spark2 Thrift Server niet kan worden bepaald dat deze hoger is.                                                                                                |
| Geschiedenisserverproces                   | Deze waarschuwing op hostniveau wordt geactiveerd als het history server-proces niet kan worden ingesteld om te worden weergegeven en te luisteren op het netwerk.                                                                |
| Gebruikersinterface geschiedenisserver                    | Deze waarschuwing op hostniveau wordt geactiveerd als de webgebruikers van de geschiedenisserver onbereikbaar is.                                                                                                              |
| `ResourceManager`Web-gebruikersinterface                   | Deze waarschuwing op hostniveau `ResourceManager` wordt geactiveerd als de webgebruikersinterface onbereikbaar is.                                                                                                             |
| Overzicht van de status nodemanager               | Deze waarschuwing op serviceniveau wordt geactiveerd als er ongezonde NodeManagers zijn                                                                                                                    |
| Web-gebruikersinterface van app-tijdlijn                      | Deze waarschuwing op hostniveau wordt geactiveerd als de webgebruikers van de webinterface van de app-tijdlijnserver onbereikbaar zijn.                                                                                                         |
| Samenvatting van de status gegevensnode                  | Deze waarschuwing op serviceniveau wordt geactiveerd als er ongezonde DataNodes zijn                                                                                                                       |
| Gebruikersinterface NameNode                          | Deze waarschuwing op hostniveau wordt geactiveerd als de gebruikersinterface namenode web onbereikbaar is.                                                                                                                    |
| ZooKeeper Failover Controller Proces    | Deze waarschuwing op hostniveau wordt geactiveerd als het ZooKeeper Failover Controller-proces niet kan worden bevestigd dat deze up en luisteren op het netwerk is.                                                   |
| Oozie Server-webgebruikersinterface                      | Deze waarschuwing op hostniveau wordt geactiveerd als de Oozie-serverwebgebruikersinterface onbereikbaar is.                                                                                                                |
| Oozie-serverstatus                      | Deze waarschuwing op hostniveau wordt geactiveerd als de Oozie-server niet kan worden bepaald dat deze up is en reageert op clientverzoeken.                                                                      |
| Hive Metastore-proces                   | Deze waarschuwing op hostniveau wordt geactiveerd als het Hive Metastore-proces niet kan worden bepaald dat het op het netwerk wordt beluisteren.                                                                 |
| HiveServer2-proces                      | Deze waarschuwing op hostniveau wordt geactiveerd als de HiveServer niet kan worden vastgesteld dat deze wordt weergegeven en reageert op clientverzoeken.                                                                        |
| WebhCat-serverstatus                    | Deze waarschuwing op hostniveau `templeton` wordt geactiveerd als de serverstatus niet in orde is.                                                                                                            |
| Percentage ZooKeeper servers beschikbaar      | Deze waarschuwing wordt geactiveerd als het aantal down ZooKeeper-servers in het cluster groter is dan de geconfigureerde kritieke drempelwaarde. Het verzamelt de resultaten van ZooKeeper procescontroles.     |
| Spark2 Livy-server                       | Deze waarschuwing op hostniveau wordt geactiveerd als de Livy2-server niet kan worden bepaald dat deze hoger is.                                                                                                        |
| Spark2-geschiedenisserver                    | Deze waarschuwing op hostniveau wordt geactiveerd als de Spark2 History Server niet kan worden bepaald dat deze hoger is.                                                                                               |
| Verzamelproces voor statistieken                | Deze waarschuwing wordt geactiveerd als het verzamelprogramma voor metrische gegevens niet kan worden bevestigd dat deze is ingeschakeld en dat deze gedurende het aantal seconden dat gelijk is aan de drempelwaarde, op de geconfigureerde poort wordt weergegeven.                                 |
| Metrics Collector - HBase Master Process | Deze waarschuwing wordt geactiveerd als de HBase-hoofdprocessen van Metrics Collector niet kunnen worden bevestigd als up-to-up en luisteren op het netwerk voor de geconfigureerde kritieke drempel, gegeven in seconden. |
| Percentage Metrics Monitors beschikbaar       | Deze waarschuwing wordt geactiveerd als een percentage van de processen van de monitor met statistieken niet is ingeschakeld en niet wordt geluisterd naar de geconfigureerde waarschuwings- en kritieke drempelwaarden.                             |
| Percentage NodeManagers beschikbaar           | Deze waarschuwing wordt geactiveerd als het aantal down NodeManagers in het cluster groter is dan de geconfigureerde kritieke drempelwaarde. Hiermee worden de resultaten van NodeManager-procescontroles samengevoegd.        |
| NodeManager-status                       | Met deze waarschuwing op hostniveau wordt de eigenschap nodehealth gecontroleerd die beschikbaar is in het onderdeel NodeManager.                                                                                              |
| Gebruikersinterface van NodeManager                       | Deze waarschuwing op hostniveau wordt geactiveerd als de gebruikersinterface van het Web NodeManager onbereikbaar is.                                                                                                                 |
| NameNode High Availability Health        | Deze waarschuwing op serviceniveau wordt geactiveerd als de Active NameNode of Standby NameNode niet worden uitgevoerd.                                                                                     |
| DataNode-proces                         | Deze waarschuwing op hostniveau wordt geactiveerd als de afzonderlijke DataNode-processen niet kunnen worden ingesteld om te luisteren en te luisteren op het netwerk.                                                         |
| Gebruikersinterface van DataNode                          | Deze waarschuwing op hostniveau wordt geactiveerd als de gebruikersinterface van Het Web gegevensnode onbereikbaar is.                                                                                                                    |
| Percentage JournalNodes beschikbaar           | Deze waarschuwing wordt geactiveerd als het aantal down-JournalNodes in het cluster groter is dan de geconfigureerde kritieke drempelwaarde. Het verzamelt de resultaten van JournalNode procescontroles.        |
| Percenten dataknooppunten beschikbaar              | Deze waarschuwing wordt geactiveerd als het aantal down DataNodes in het cluster groter is dan de geconfigureerde kritieke drempelwaarde. Het verzamelt de resultaten van DataNode procescontroles.              |
| Zeppelinserverstatus                   | Deze waarschuwing op hostniveau wordt geactiveerd als de Zeppelin-server niet kan worden bepaald dat deze is weergegeven en reageert op clientverzoeken.                                                                   |
| Interactief HiveServer2-proces          | Deze waarschuwing op hostniveau wordt geactiveerd als de HiveServerInteractive niet kan worden vastgesteld dat deze up is en reageert op clientverzoeken.                                                             |
| LLAP-toepassing                         | Deze waarschuwing wordt geactiveerd als de LLAP-toepassing niet kan worden vastgesteld dat deze wordt geactiveerd en op verzoeken reageert.                                                                                    |

U elke service selecteren om meer informatie over deze service weer te geven.

Hoewel de servicepagina informatie geeft over de status en configuratie van elke service, geeft deze geen informatie over het hoofdknooppunt waarop de service wordt uitgevoerd. Als u deze informatie wilt bekijken, gebruikt u de koppeling **Hosts** boven aan de pagina. Op deze pagina worden hosts in het cluster weergegeven, inclusief de hoofdknooppunten.

![Apache Ambari headnode hosts lijst](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Als u de koppeling voor een van de hoofdknooppunten selecteert, worden de services en onderdelen weergegeven die op dat knooppunt worden uitgevoerd.

![Status apache Ambari-component](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Zie [HDInsight controleren en beheren met behulp van de Apache Ambari Web UI voor](hdinsight-hadoop-manage-ambari.md)meer informatie over het gebruik van Ambari.

### <a name="ambari-rest-api"></a>Ambari REST API

De Ambari REST API is beschikbaar via het internet. De HDInsight public gateway verwerkt routeringsaanvragen naar het hoofdknooppunt dat momenteel de REST API host.

U de volgende opdracht gebruiken om de status van een service te controleren via de Ambari REST API:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* Vervang **WACHTWOORD door** het wachtwoord van het HTTP-gebruikersaccount (admin).
* Vervang **CLUSTERNAME** door de naam van uw cluster.
* Vervang **SERVICENAME** door de naam van de service waarvan u de status wilt controleren.

Als u bijvoorbeeld de status van de **HDFS-service** op een cluster met de naam **mycluster**wilt controleren, met een wachtwoord van **wachtwoord,** gebruikt u de volgende opdracht:

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

Het antwoord is vergelijkbaar met de volgende JSON:

```json
{
    "href" : "http://mycluster.wutj3h4ic1zejluqhxzvckxq0g.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
    "ServiceInfo" : {
    "cluster_name" : "mycluster",
    "service_name" : "HDFS",
    "state" : "STARTED"
    }
}
```

De URL vertelt ons dat de dienst momenteel draait op een hoofdknooppunt genaamd **mycluster.wutj3h4ic1zejluqhxzvckxq0g**.

De status vertelt ons dat de service momenteel wordt uitgevoerd of **GESTART**.

Als u niet weet welke services op het cluster zijn geïnstalleerd, u de volgende opdracht gebruiken om een lijst op te halen:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Zie [HDInsight controleren en beheren met behulp van de Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)voor meer informatie over het werken met de Ambari REST API.

#### <a name="service-components"></a>Servicecomponenten

Services kunnen onderdelen bevatten waarvan u de status van afzonderlijk wilt controleren. HDFS bevat bijvoorbeeld de component NameNode. Als u informatie over een component wilt weergeven, moet u het als:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Als u niet weet welke onderdelen door een service worden geleverd, u de volgende opdracht gebruiken om een lijst op te halen:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Hoe toegang te krijgen tot logbestanden op de hoofdknooppunten

### <a name="ssh"></a>SSH

Terwijl aangesloten op een hoofdknooppunt via SSH, log bestanden kunnen worden gevonden onder **/ var / log**. Bijvoorbeeld, **/ var / log / hadoop-garen / garen** bevatten logs voor GAREN.

Elk hoofdknooppunt kan unieke logboekvermeldingen hebben, dus u moet de logboeken op beide controleren.

### <a name="sftp"></a>SFTP

U ook verbinding maken met het hoofdknooppunt via het SSH File Transfer Protocol of Secure File Transfer Protocol (SFTP) en de logbestanden rechtstreeks downloaden.

Net als bij het gebruik van een SSH-client, moet u bij het maken van verbinding met het cluster de naam van het SSH-gebruikersaccount en het SSH-adres van het cluster opgeven. Bijvoorbeeld `sftp username@mycluster-ssh.azurehdinsight.net`. Geef het wachtwoord voor het account op wanneer daarom `-i` wordt gevraagd, of geef een openbare sleutel op met behulp van de parameter.

Eenmaal aangesloten, krijg je `sftp>` een prompt te zien. Vanaf deze prompt u mappen wijzigen, bestanden uploaden en downloaden. De volgende opdrachten wijzigen bijvoorbeeld mappen in de **map /var/log/hadoop/hdfs** en downloaden vervolgens alle bestanden in de map.

    cd /var/log/hadoop/hdfs
    get *

Voer bij de `help` `sftp>` prompt een lijst met beschikbare opdrachten in.

> [!NOTE]  
> Er zijn ook grafische interfaces waarmee u het bestandssysteem visualiseren wanneer u verbinding maakt met SFTP. Met [MobaXTerm](https://mobaxterm.mobatek.net/) u bijvoorbeeld door het bestandssysteem bladeren met een interface die vergelijkbaar is met Windows Explorer.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Als u toegang wilt krijgen tot logbestanden met Ambari, moet u een SSH-tunnel gebruiken. De webinterfaces voor de afzonderlijke services worden niet openbaar op het internet weergegeven. Zie het [SSH-tunnelingdocument gebruiken](hdinsight-linux-ambari-ssh-tunnel.md) voor informatie over het gebruik van een SSH-tunnel.

Selecteer in de Ambari Web UI de service waarvoor u logboeken wilt weergeven (bijvoorbeeld GAREN). Gebruik vervolgens **Snelle koppelingen** om te selecteren voor welk hoofdknooppunt u de logboeken wilt weergeven.

![Snelle koppelingen gebruiken om logboeken weer te geven](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>De grootte van het knooppunt configureren

De grootte van een knooppunt kan alleen worden geselecteerd tijdens het maken van het cluster. Op de prijspagina van [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/)vindt u een lijst met de verschillende VM-formaten die beschikbaar zijn voor HDInsight.

Wanneer u een cluster maakt, u de grootte van de knooppunten opgeven. De volgende informatie geeft richtlijnen voor het opgeven van de grootte met behulp van de [Azure-portal,](https://portal.azure.com/) [Azure PowerShell-module Az](/powershell/azureps-cmdlets-docs)en de [Azure CLI:](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

* **Azure-portal:** Wanneer u een cluster maakt, u de grootte instellen van de knooppunten die door het cluster worden gebruikt:

    ![Afbeelding van wizard clustercreatie met selectie van knooppuntgrootte](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI:** Wanneer [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) u de opdracht gebruikt, u de grootte van `--headnode-size`de `--workernode-size`knooppunten `--zookeepernode-size` Hoofd, werknemer en ZooKeeper instellen met behulp van de, en parameters.

* **Azure PowerShell:** Wanneer u de cmdlet [Nieuw-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) gebruikt, u de grootte van `-HeadNodeSize` `-WorkerNodeSize`de `-ZookeeperNodeSize` knooppunten Hoofd, werknemer en ZooKeeper instellen met behulp van de, en parameters.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de onderwerpen die in dit artikel worden besproken:

* [Apache Ambari REST Referentie](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [De Azure CLI installeren en configureren](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure PowerShell-module Az installeren en configureren](/powershell/azure/overview)
* [HDInsight beheren met Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Inrichten van HDInsight clusters op basis van Linux](hdinsight-hadoop-provision-linux-clusters.md)
