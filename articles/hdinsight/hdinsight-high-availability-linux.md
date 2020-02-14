---
title: Hoge Beschik baarheid voor Hadoop-Azure HDInsight
description: Meer informatie over hoe HDInsight-clusters de betrouw baarheid en beschik baarheid verbeteren met behulp van een extra hoofd knooppunt. Lees hoe dit van invloed is op Hadoop-services zoals Ambari en Hive, en hoe u afzonderlijk verbinding maakt met elk hoofd knooppunt met behulp van SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: Hadoop hoge Beschik baarheid
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 085933f9a74ee37779ce63ce499d89ea53a9f7d6
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198936"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Beschik baarheid en betrouw baarheid van Apache Hadoop clusters in HDInsight

HDInsight-clusters bieden twee hoofd knooppunten voor het verg Roten van de beschik baarheid en betrouw baarheid van Apache Hadoop Services en taken die worden uitgevoerd.

Hadoop behaalt hoge Beschik baarheid en betrouw baarheid door services en gegevens te repliceren op meerdere knoop punten in een cluster. Standaard distributies van Hadoop hebben doorgaans slechts één hoofd knooppunt. Elke onderbreking van het hoofd knooppunt kan ertoe leiden dat het cluster niet meer werkt. HDInsight biedt twee hoofd knooppunten voor het verbeteren van de beschik baarheid en betrouw baarheid van Hadoop.

## <a name="availability-and-reliability-of-nodes"></a>Beschik baarheid en betrouw baarheid van knoop punten

Knoop punten in een HDInsight-cluster worden geïmplementeerd met Azure Virtual Machines. In de volgende secties worden de afzonderlijke knooppunt typen besproken die worden gebruikt met HDInsight.

> [!NOTE]  
> Niet alle knooppunt typen worden gebruikt voor een cluster type. Een Hadoop-cluster type heeft bijvoorbeeld geen Nimbus-knoop punten. Zie de sectie cluster typen in het document op [Linux gebaseerde Hadoop-clusters maken in hdinsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-type) voor meer informatie over knoop punten die worden gebruikt door HDInsight-cluster typen.

### <a name="head-nodes"></a>Hoofdknooppunten

HDInsight biedt twee hoofd knooppunten om te zorgen voor hoge Beschik baarheid van Hadoop-Services. Beide hoofd knooppunten zijn actief en worden uitgevoerd binnen het HDInsight-cluster tegelijk. Sommige services, zoals Apache HDFS of Apache Hadoop GARENs, zijn op een bepaald moment alleen ' actief ' op één hoofd knooppunt. Andere services, zoals HiveServer2 of Hive-meta Store, zijn gelijktijdig actief op beide hoofd knooppunten.

Als u de hostnamen voor verschillende knooppunt typen in uw cluster wilt ophalen, gebruikt u de [Ambari-rest API](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

> [!IMPORTANT]  
> Koppel de numerieke waarde niet aan de vraag of een knoop punt primair of secundair is. De numerieke waarde is alleen aanwezig om een unieke naam op te geven voor elk knoop punt.

### <a name="nimbus-nodes"></a>Nimbus-knoop punten

Nimbus-knoop punten zijn beschikbaar met Apache Storm clusters. De Nimbus-knoop punten bieden vergelijk bare functionaliteit aan de Hadoop-JobTracker door verwerking te distribueren en te bewaken op werk knooppunten. HDInsight biedt twee Nimbus-knoop punten voor Storm-clusters

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper-knoop punten

[ZooKeeper](https://zookeeper.apache.org/) -knoop punten worden gebruikt voor de Leader verkiezing van Master Services op hoofd knooppunten. Ze worden ook gebruikt om ervoor te zorgen dat services, gegevens (worker) knoop punten en gateways weten op welke hoofd knooppunt een Master-service actief is. HDInsight biedt standaard drie ZooKeeper-knoop punten.

### <a name="worker-nodes"></a>Worker-knooppunten

Worker-knoop punten voeren de werkelijke gegevens analyse uit wanneer een taak wordt verzonden naar het cluster. Als een werk knooppunt mislukt, wordt de taak die het heeft uitgevoerd, verzonden naar een ander worker-knoop punt. Standaard maakt HDInsight vier worker-knoop punten. U kunt dit nummer tijdens en na het maken van het cluster aanpassen aan uw behoeften.

### <a name="edge-node"></a>Edge-knoop punt

Een Edge-knoop punt neemt niet actief deel aan de gegevens analyse binnen het cluster. Het wordt gebruikt door ontwikkel aars of gegevens wetenschappers bij het werken met Hadoop. Het Edge-knoop punt bevindt zich in dezelfde Azure-Virtual Network als de andere knoop punten in het cluster en heeft rechtstreeks toegang tot alle andere knoop punten. Het Edge-knoop punt kan worden gebruikt zonder resources te hoeven weghalen uit kritieke Hadoop-Services of analyse taken.

Momenteel is ML van de services op HDInsight het enige cluster type dat standaard een Edge-knoop punt biedt. Voor ML-Services op HDInsight wordt het Edge-knoop punt gebruikt om de R-code lokaal op het knoop punt te testen voordat deze naar het cluster wordt verzonden voor gedistribueerde verwerking.

Zie het document [Edge-knoop punten gebruiken in HDInsight](hdinsight-apps-use-edge-node.md) voor meer informatie over het gebruik van een Edge-knoop punt met andere cluster typen.

## <a name="accessing-the-nodes"></a>Toegang tot de knoop punten

Toegang tot het cluster via internet wordt via een open bare gateway gegeven. De toegang is beperkt tot het maken van verbinding met de hoofd knooppunten en, als er een bestaat, het Edge-knoop punt. De toegang tot services die worden uitgevoerd op de hoofd knooppunten heeft geen invloed op meerdere hoofd knooppunten. De open bare gateway routeert aanvragen naar het hoofd knooppunt dat als host fungeert voor de aangevraagde service. Als Apache Ambari momenteel wordt gehost op het secundaire hoofd knooppunt, routeert de gateway inkomende aanvragen voor Ambari naar dat knoop punt.

Toegang via de open bare gateway is beperkt tot poorten 443 (HTTPS), 22 en 23.

|Poort |Beschrijving |
|---|---|
|443|Wordt gebruikt voor toegang tot Ambari en andere web-UI of REST-Api's die worden gehost op de hoofd knooppunten.|
|22|Wordt gebruikt om toegang te krijgen tot het primaire knoop punt of Edge-knoop punt met SSH.|
|23|Wordt gebruikt voor toegang tot het secundaire hoofd knooppunt met SSH. `ssh username@mycluster-ssh.azurehdinsight.net` maakt bijvoorbeeld verbinding met het primaire hoofd knooppunt van het cluster met de naam **mycluster**.|

Zie het document [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie over het gebruik van SSH.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Interne volledig gekwalificeerde domein namen (FQDN)

Knoop punten in een HDInsight-cluster hebben een intern IP-adres en een FQDN-naam die alleen toegankelijk is vanuit het cluster. Bij het openen van services op het cluster met behulp van de interne FQDN of het IP-adres, moet u Ambari gebruiken om te controleren of de IP of FQDN voor gebruik bij het openen van de service.

De Apache Oozie-service kan bijvoorbeeld alleen worden uitgevoerd op één hoofd knooppunt en met behulp van de `oozie` opdracht vanuit een SSH-sessie moet de URL voor de service worden gebruikt. Deze URL kan worden opgehaald van Ambari met behulp van de volgende opdracht:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende, die de interne URL bevat die moet worden gebruikt met de opdracht `oozie`:

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Zie [HDInsight controleren en beheren met behulp van de Apache Ambari rest API](hdinsight-hadoop-manage-ambari-rest-api.md)voor meer informatie over het werken met de Ambari-rest API.

### <a name="accessing-other-node-types"></a>Toegang tot andere knooppunt typen

U kunt verbinding maken met knoop punten die niet rechtstreeks toegankelijk zijn via internet met behulp van de volgende methoden:

|Methode |Beschrijving |
|---|---|
|SSH|Wanneer u met SSH verbinding hebt gemaakt met een hoofd knooppunt, kunt u vervolgens SSH van het hoofd knooppunt gebruiken om verbinding te maken met andere knoop punten in het cluster. Zie het document [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.|
|SSH-tunnel|Als u toegang wilt krijgen tot een webservice die wordt gehost op een van de knoop punten die niet beschikbaar zijn op internet, moet u een SSH-tunnel gebruiken. Zie het document [een SSH-tunnel gebruiken met HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) voor meer informatie.|
|Azure Virtual Network|Als uw HDInsight-cluster deel uitmaakt van een Azure-Virtual Network, heeft elke resource op dezelfde Virtual Network rechtstreeks toegang tot alle knoop punten in het cluster. Zie het document [een virtueel netwerk voor HDInsight plannen](hdinsight-plan-virtual-network-deployment.md) voor meer informatie.|

## <a name="how-to-check-on-a-service-status"></a>De status van een service controleren

Als u de status wilt controleren van services die worden uitgevoerd op de hoofd knooppunten, gebruikt u de Ambari-webgebruikersinterface of de Ambari-REST API.

### <a name="ambari-web-ui"></a>Ambari-webgebruikersinterface

De Web-UI van Ambari wordt weer gegeven op `https://CLUSTERNAME.azurehdinsight.net`. Vervang **CLUSTERNAME** door de naam van uw cluster. Als u hierom wordt gevraagd, voert u de HTTP-gebruikers referenties voor uw cluster in. De standaard-HTTP-gebruikers naam is **beheerder** en het wacht woord is het wacht woord dat u hebt ingevoerd bij het maken van het cluster.

Wanneer u op de pagina Ambari, worden de geïnstalleerde services weer gegeven aan de linkerkant van de pagina.

![Geïnstalleerde Apache Ambari-Services](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Er kan een reeks pictogrammen naast een service worden weer gegeven om de status aan te geven. U kunt waarschuwingen met betrekking tot een service weer geven met behulp van de koppeling **waarschuwingen** boven aan de pagina.  Ambari biedt verschillende vooraf gedefinieerde waarschuwingen.

De volgende waarschuwingen helpen bij het bewaken van de beschik baarheid van een cluster:

| Naam van waarschuwing                               | Beschrijving                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Status van metrische monitor                    | Deze waarschuwing geeft de status van het bewakings proces voor metrische gegevens aan, zoals bepaald door het controle status script.                                                                                   |
| Heartbeat van de Ambari-agent                   | Deze waarschuwing wordt geactiveerd als de server contact met een agent heeft verloren.                                                                                                                        |
| ZooKeeper-Server proces                 | Deze waarschuwing op hostniveau wordt geactiveerd als het ZooKeeper-Server proces niet kan worden vastgesteld om op het netwerk te worden geluisterd.                                                               |
| IOCache-status van meta gegevens server           | Deze waarschuwing op hostniveau wordt geactiveerd als de IOCache-meta gegevens server niet kan worden vastgesteld om te reageren op client aanvragen                                                            |
| JournalNode-webgebruikersinterface                       | Deze waarschuwing op hostniveau wordt geactiveerd als de JournalNode-webgebruikersinterface niet bereikbaar is.                                                                                                                 |
| Spark2 Thrift-server                     | Deze waarschuwing op hostniveau wordt geactiveerd als de Spark2 Thrift-server niet kan worden vastgesteld.                                                                                                |
| Proces van geschiedenis server                   | Deze waarschuwing op hostniveau wordt geactiveerd als het geschiedenis Server proces niet kan worden ingesteld om te worden geluisterd naar het netwerk.                                                                |
| Web-UI van geschiedenis server                    | Deze waarschuwing op hostniveau wordt geactiveerd als de Web-UI van de geschiedenis server niet bereikbaar is.                                                                                                              |
| webgebruikersinterface `ResourceManager`                   | Deze waarschuwing op hostniveau wordt geactiveerd als de webgebruikersinterface van `ResourceManager` niet bereikbaar is.                                                                                                             |
| NodeManager status overzicht               | Deze waarschuwing op service niveau wordt geactiveerd als er sprake is van een slechte NodeManagers                                                                                                                    |
| Web-UI voor app-tijd lijn                      | Deze waarschuwing op hostniveau wordt geactiveerd als de Web-UI van de app-tijdlijn server niet bereikbaar is.                                                                                                         |
| DataNode status overzicht                  | Deze waarschuwing op service niveau wordt geactiveerd als er sprake is van een slechte DataNodes                                                                                                                       |
| NameNode-webgebruikersinterface                          | Deze waarschuwing op hostniveau wordt geactiveerd als de NameNode-webgebruikersinterface niet bereikbaar is.                                                                                                                    |
| ZooKeeper failover-controller proces    | Deze waarschuwing op hostniveau wordt geactiveerd als het ZooKeeper failover controller-proces niet kan worden bevestigd om op het netwerk te kunnen worden geluisterd.                                                   |
| Oozie server-webinterface                      | Deze waarschuwing op hostniveau wordt geactiveerd als de Web-UI van de Oozie-server niet bereikbaar is.                                                                                                                |
| Oozie-server status                      | Deze waarschuwing op hostniveau wordt geactiveerd als de Oozie-server niet kan worden vastgesteld om te reageren op client aanvragen.                                                                      |
| Hive-meta Store-proces                   | Deze waarschuwing op hostniveau wordt geactiveerd als het proces van de Hive-meta Store niet kan worden vastgesteld om op het netwerk te worden geluisterd.                                                                 |
| HiveServer2-proces                      | Deze waarschuwing op hostniveau wordt geactiveerd als de HiveServer niet kan worden vastgesteld om te reageren op client aanvragen.                                                                        |
| WebHCat-server status                    | Deze waarschuwing op hostniveau wordt geactiveerd als de `templeton` server status niet in orde is.                                                                                                            |
| Percentage beschik bare ZooKeeper-servers      | Deze waarschuwing wordt geactiveerd als het aantal ZooKeeper-servers in het cluster hoger is dan de geconfigureerde kritieke drempel waarde. Hiermee worden de resultaten van ZooKeeper-proces controles geaggregeerd.     |
| Spark2 livy-server                       | Deze waarschuwing op hostniveau wordt geactiveerd als de Livy2-server niet kan worden vastgesteld.                                                                                                        |
| Spark2-geschiedenis server                    | Deze waarschuwing op hostniveau wordt geactiveerd als de Spark2-geschiedenis server niet kan worden vastgesteld.                                                                                               |
| Verzamelaar proces voor metrische gegevens                | Deze waarschuwing wordt geactiveerd als de metrische Collector niet kan worden bevestigd om de geconfigureerde poort te laten Luis teren voor een aantal seconden dat gelijk is aan de drempel waarde.                                 |
| Collector van metrische gegevens-HBase Master proces | Deze waarschuwing wordt geactiveerd als de HBase-Master processen van de metrische Collector niet kunnen worden bevestigd om de geconfigureerde kritieke drempel waarde in het netwerk te bekijken, uitgedrukt in een paar seconden. |
| Beschik bare monitors voor percentage metrische gegevens       | Deze waarschuwing wordt geactiveerd als een percentage van de metrische gegevens monitor processen niet actief is en op het netwerk luistert naar de geconfigureerde waarschuwings-en kritieke drempel waarden.                             |
| Percentage NodeManagers beschikbaar           | Deze waarschuwing wordt geactiveerd als het aantal NodeManagers in het cluster hoger is dan de geconfigureerde kritieke drempel waarde. Hiermee worden de resultaten van NodeManager-proces controles geaggregeerd.        |
| NodeManager-status                       | Deze waarschuwing op hostniveau controleert de knooppunt status eigenschap die beschikbaar is via het onderdeel NodeManager.                                                                                              |
| NodeManager-webgebruikersinterface                       | Deze waarschuwing op hostniveau wordt geactiveerd als de NodeManager-webgebruikersinterface niet bereikbaar is.                                                                                                                 |
| Status van hoge Beschik baarheid van NameNode        | Deze waarschuwing op service niveau wordt geactiveerd als de actieve NameNode of stand-NameNode niet actief zijn.                                                                                     |
| DataNode-proces                         | Deze waarschuwing op hostniveau wordt geactiveerd als de afzonderlijke DataNode-processen niet kunnen worden ingesteld om te worden geluisterd naar het netwerk.                                                         |
| DataNode-webgebruikersinterface                          | Deze waarschuwing op hostniveau wordt geactiveerd als de DataNode-webgebruikersinterface niet bereikbaar is.                                                                                                                    |
| Percentage beschik journalnodes beschikbaar           | Deze waarschuwing wordt geactiveerd als het aantal beschik journalnodes in het cluster hoger is dan de geconfigureerde kritieke drempel waarde. Hiermee worden de resultaten van JournalNode-proces controles geaggregeerd.        |
| Percentage DataNodes beschikbaar              | Deze waarschuwing wordt geactiveerd als het aantal DataNodes in het cluster hoger is dan de geconfigureerde kritieke drempel waarde. Hiermee worden de resultaten van DataNode-proces controles geaggregeerd.              |
| Zeppelin-server status                   | Deze waarschuwing op hostniveau wordt geactiveerd als de Zeppelin-server niet kan worden vastgesteld om te reageren op client aanvragen.                                                                   |
| HiveServer2 interactief proces          | Deze waarschuwing op hostniveau wordt geactiveerd als de HiveServerInteractive niet kan worden vastgesteld om te reageren op client aanvragen.                                                             |
| LLAP-toepassing                         | Deze waarschuwing wordt geactiveerd als de LLAP-toepassing niet kan worden vastgesteld om aanvragen te ontvangen en erop te reageren.                                                                                    |

U kunt elke service selecteren om meer informatie weer te geven.

De service pagina bevat informatie over de status en configuratie van elke service, maar biedt geen informatie over het hoofd knooppunt waarop de service wordt uitgevoerd. Als u deze informatie wilt weer geven, gebruikt u de koppeling **hosts** boven aan de pagina. Op deze pagina worden de hosts in het cluster weer gegeven, met inbegrip van de hoofd knooppunten.

![Ambari hoofd knooppunt hosts-lijst](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Als u de koppeling selecteert voor een van de hoofd knooppunten, worden de services en onderdelen weer gegeven die op dat knoop punt worden uitgevoerd.

![Status van Apache Ambari-onderdeel](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Zie [HDInsight bewaken en beheren met behulp van de Apache Ambari-webgebruikersinterface](hdinsight-hadoop-manage-ambari.md)voor meer informatie over het gebruik van Ambari.

### <a name="ambari-rest-api"></a>Ambari REST API

De Ambari-REST API is beschikbaar via internet. De open bare HDInsight-gateway verwerkt routerings aanvragen naar het hoofd knooppunt dat momenteel de REST API host.

U kunt de volgende opdracht gebruiken om de status van een service te controleren via de Ambari-REST API:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* Vervang het **wacht woord** door het wacht woord van de http-gebruiker (Administrator).
* Vervang **CLUSTERNAME** door de naam van uw cluster.
* Vervang **ServiceName** door de naam van de service waarvan u de status wilt controleren.

Als u bijvoorbeeld de status van de service **HDFS** wilt controleren op een cluster met de naam **mycluster**, met een wacht woord van het **wacht woord**, gebruikt u de volgende opdracht:

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

De URL geeft aan dat de service momenteel wordt uitgevoerd op een hoofd knooppunt met de naam **mycluster. wutj3h4ic1zejluqhxzvckxq0g**.

De status geeft aan dat de service momenteel wordt uitgevoerd of is **gestart**.

Als u niet weet welke services zijn geïnstalleerd op het cluster, kunt u de volgende opdracht gebruiken om een lijst op te halen:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Zie [HDInsight controleren en beheren met behulp van de Apache Ambari rest API](hdinsight-hadoop-manage-ambari-rest-api.md)voor meer informatie over het werken met de Ambari-rest API.

#### <a name="service-components"></a>Service onderdelen

Services kunnen onderdelen bevatten waarvan u de status afzonderlijk wilt controleren. Bijvoorbeeld, HDFS bevat het onderdeel NameNode. Als u informatie wilt weer geven over een onderdeel, zou de opdracht er als volgt uitzien:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Als u niet weet welke onderdelen door een service worden aangeboden, kunt u de volgende opdracht gebruiken om een lijst op te halen:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Toegang tot logboek bestanden op de hoofd knooppunten

### <a name="ssh"></a>SSH

Terwijl u via SSH verbinding hebt met een hoofd knooppunt, kunt u logboek bestanden vinden onder **/var/log**. **/Var/log/Hadoop-Yarn/yarn** bevatten bijvoorbeeld Logboeken voor garens.

Elk hoofd knooppunt kan unieke logboek vermeldingen hebben, dus controleer de logboeken op beide.

### <a name="sftp"></a>SFTP

U kunt ook verbinding maken met het hoofd knooppunt met behulp van de SSH-File Transfer Protocol of beveiligde File Transfer Protocol (SFTP) en de logboek bestanden rechtstreeks downloaden.

Net als bij het gebruik van een SSH-client, moet u bij het maken van verbinding met het cluster de naam van het SSH-gebruikers account en het SSH-adres van het cluster opgeven. Bijvoorbeeld `sftp username@mycluster-ssh.azurehdinsight.net`. Geef het wacht woord voor het account op wanneer u daarom wordt gevraagd of geef een open bare sleutel op met behulp van de para meter `-i`.

Zodra de verbinding is gemaakt, wordt er een `sftp>` prompt weer gegeven. Vanuit deze prompt kunt u mappen wijzigen, bestanden uploaden en downloaden. Met de volgende opdrachten worden bijvoorbeeld directory's gewijzigd in de **/var/log/Hadoop/hdfs** -map en worden alle bestanden in de map gedownload.

    cd /var/log/hadoop/hdfs
    get *

Voer `help` op de `sftp>` prompt in voor een lijst met beschik bare opdrachten.

> [!NOTE]  
> Er zijn ook grafische interfaces waarmee u het bestands systeem kunt visualiseren wanneer u verbinding maakt via SFTP. Met [MobaXTerm](https://mobaxterm.mobatek.net/) kunt u bijvoorbeeld bladeren in het bestands systeem met behulp van een interface die lijkt op Windows Verkenner.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Als u toegang wilt tot logboek bestanden met behulp van Ambari, moet u een SSH-tunnel gebruiken. De webinterfaces voor de afzonderlijke services worden niet openbaar op internet weer gegeven. Zie het document [ssh-tunneling gebruiken](hdinsight-linux-ambari-ssh-tunnel.md) voor meer informatie over het gebruik van een SSH-tunnel.

Selecteer in de Ambari-webgebruikersinterface de service waarvoor u logboeken wilt weer geven (bijvoorbeeld GARENs). Gebruik vervolgens **snelle koppelingen** om te selecteren welk hoofd knooppunt voor de logboeken moet worden weer gegeven.

![Snelle koppelingen gebruiken om logboeken weer te geven](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>De knooppunt grootte configureren

De grootte van een knoop punt kan alleen worden geselecteerd tijdens het maken van het cluster. Op de [pagina met hdinsight-prijzen](https://azure.microsoft.com/pricing/details/hdinsight/)vindt u een overzicht van de verschillende VM-grootten die beschikbaar zijn voor hdinsight.

Wanneer u een cluster maakt, kunt u de grootte van de knoop punten opgeven. De volgende informatie bevat richt lijnen voor het opgeven van de grootte met behulp van de [Azure Portal](https://portal.azure.com/), [Azure PowerShell module AZ](/powershell/azureps-cmdlets-docs)en de [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

* **Azure Portal**: wanneer u een cluster maakt, kunt u de grootte van de knoop punten instellen die door het cluster worden gebruikt:

    ![Afbeelding van de wizard voor het maken van een cluster met een knooppunt grootte selectie](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure cli**: wanneer u de [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) -opdracht gebruikt, kunt u de grootte van de hoofd-, werk-en ZooKeeper-knoop punten instellen met behulp van de para meters `--headnode-size`, `--workernode-size`en `--zookeepernode-size`.

* **Azure PowerShell**: wanneer u de cmdlet [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) gebruikt, kunt u de grootte van de hoofd-, werk-en ZooKeeper-knoop punten instellen met behulp van de para meters `-HeadNodeSize`, `-WorkerNodeSize`en `-ZookeeperNodeSize`.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de items die in dit artikel worden besproken:

* [Naslag informatie voor Apache Ambari-REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [De Azure CLI installeren en configureren](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure PowerShell-module AZ installeren en configureren](/powershell/azure/overview)
* [HDInsight beheren met Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [HDInsight-clusters op basis van Linux inrichten](hdinsight-hadoop-provision-linux-clusters.md)
