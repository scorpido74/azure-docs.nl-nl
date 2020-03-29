---
title: Toegang tot Apache Hadoop YARN-toepassingslogboeken - Azure HDInsight
description: Meer informatie over het openen van YARN-toepassingslogboeken op een HDInsight-cluster (Linux- gebaseerde HDInsight)-cluster met behulp van zowel de opdrachtregel als een webbrowser.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/23/2020
ms.openlocfilehash: 2a7d71c6d751d4a48ec93f020e657a4d43114cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764385"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Toegang Apache Hadoop YARN applicatie logs op Linux-gebaseerde HDInsight

Meer informatie over het openen van de logboeken voor [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Yet Another Resource Negotiator) toepassingen op een [Apache Hadoop](https://hadoop.apache.org/) cluster in Azure HDInsight.

## <a name="what-is-apache-yarn"></a>Wat is Apache YARN?

YARN ondersteunt meerdere programmeermodellen[(Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) is een van hen) door resourcemanagement te ontkoppelen van toepassingsplanning/monitoring. YARN maakt gebruik van een globale *ResourceManager* (RM), *nodemanagers* (NMs) per werknemer en *ApplicationMasters* (AMs) per toepassing. De AM per toepassing onderhandelt over resources (CPU, geheugen, schijf, netwerk) voor het uitvoeren van uw toepassing met de RM. De RM werkt samen met NMs om deze middelen, die worden toegekend als *containers*. De AM is verantwoordelijk voor het bijhouden van de voortgang van de containers toegewezen aan het door de RM. Een toepassing kan veel containers vereisen, afhankelijk van de aard van de toepassing.

Elke toepassing kan bestaan uit meerdere *toepassingspogingen.* Als een toepassing mislukt, kan deze opnieuw worden geprobeerd als een nieuwe poging. Elke poging wordt uitgevoerd in een container. In zekere zin biedt een container de context voor de basiseenheid van het werk uitgevoerd door een YARN applicatie. Al het werk dat binnen de context van een container wordt uitgevoerd, wordt uitgevoerd op het knooppunt voor één werknemer waarop de container is toegewezen. Zie [Hadoop: Writing YARN Applications](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html), of Apache [Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) for further reference.

Als u uw cluster wilt schalen om een grotere verwerkingsdoorvoer te ondersteunen, u [Autoscale](hdinsight-autoscale-clusters.md) of Schaal uw clusters handmatig gebruiken [met behulp van een paar verschillende talen.](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters)

## <a name="yarn-timeline-server"></a>GARENtijdlijnserver

De [Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) biedt algemene informatie over voltooide toepassingen

YARN Timeline Server bevat het volgende type gegevens:

* De toepassings-ID, een unieke id van een toepassing
* De gebruiker die de toepassing heeft gestart
* Informatie over pogingen om de aanvraag te voltooien
* De containers die door een bepaalde toepassingspoging worden gebruikt

## <a name="yarn-applications-and-logs"></a>YARN-toepassingen en -logboeken

YARN ondersteunt meerdere programmeermodellen[(Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) is een van hen) door resourcemanagement te ontkoppelen van toepassingsplanning/monitoring. YARN maakt gebruik van een globale *ResourceManager* (RM), *nodemanagers* (NMs) per werknemer en *ApplicationMasters* (AMs) per toepassing. De AM per toepassing onderhandelt over resources (CPU, geheugen, schijf, netwerk) voor het uitvoeren van uw toepassing met de RM. De RM werkt samen met NMs om deze middelen, die worden toegekend als *containers*. De AM is verantwoordelijk voor het bijhouden van de voortgang van de containers toegewezen aan het door de RM. Een toepassing kan veel containers vereisen, afhankelijk van de aard van de toepassing.

Elke toepassing kan bestaan uit meerdere *toepassingspogingen.* Als een toepassing mislukt, kan deze opnieuw worden geprobeerd als een nieuwe poging. Elke poging wordt uitgevoerd in een container. In zekere zin biedt een container de context voor de basiseenheid van het werk uitgevoerd door een YARN applicatie. Al het werk dat binnen de context van een container wordt uitgevoerd, wordt uitgevoerd op het knooppunt voor één werknemer waarop de container is toegewezen. Zie [Apache Hadoop YARN Concepts](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) voor verdere referentie.

Toepassingslogboeken (en de bijbehorende containerlogboeken) zijn van cruciaal belang bij het debuggen van problematische Hadoop-toepassingen. YARN biedt een mooi kader voor het verzamelen, aggregeren en opslaan van applicatielogboeken met de [logaggregatiefunctie.](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) De functie Logboekaggregatie maakt de toegang tot toepassingslogboeken deterministischer. Het verzamelt logboeken over alle containers op een werknemersknooppunt en slaat ze op als één geaggregeerd logboekbestand per werknemersknooppunt. Het logboek wordt opgeslagen in het standaardbestandssysteem nadat een toepassing is voltooid. Uw toepassing kan honderden of duizenden containers gebruiken, maar logboeken voor alle containers die op één werknemersknooppunt worden uitgevoerd, worden altijd samengevoegd tot één bestand. Er is dus slechts 1 logboek per medewerkerknooppunt dat door uw toepassing wordt gebruikt. Log Aggregatie is standaard ingeschakeld op HDInsight-clusters versie 3.0 en hoger. Geaggregeerde logboeken bevinden zich in de standaardopslag voor het cluster. Het volgende pad is het HDFS-pad naar de logboeken:

```
/app-logs/<user>/logs/<applicationId>
```

In het `user` pad is de naam van de gebruiker die de toepassing heeft gestart. Het `applicationId` is de unieke id die is toegewezen aan een toepassing door de YARN RM.

De geaggregeerde logboeken zijn niet direct leesbaar, omdat ze zijn geschreven in een [TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf), [binaire indeling](https://issues.apache.org/jira/browse/HADOOP-3315) geïndexeerd door container. Gebruik de YARN ResourceManager-logboeken of CLI-hulpprogramma's om deze logboeken te bekijken als platte tekst voor toepassingen of containers die van belang zijn.

## <a name="yarn-logs-in-an-esp-cluster"></a>Garenlogboeken in een ESP-cluster

Twee configuraties moeten worden toegevoegd `mapred-site` aan de aangepaste in Ambari.

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net`naar `CLUSTERNAME` , waar is de naam van uw cluster.

1. Navigeer vanuit de Ambari-gebruikersinterface naar **MapReduce2** > **Configs** > **Advanced** > Custom**mapred-site.**

1. Voeg *een* van de volgende sets eigenschappen toe:

    **Stel 1 in**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Stel 2 in**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Sla wijzigingen op en start alle betrokken services opnieuw.

## <a name="yarn-cli-tools"></a>GAREN CLI-gereedschappen

1. Gebruik [de ssh-opdracht](./hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Vermeld alle toepassings-id's van de momenteel draaiende garentoepassingen met de volgende opdracht:

    ```bash
    yarn top
    ```

    Let op de `APPLICATIONID` toepassings-id van de kolom waarvan de logboeken moeten worden gedownload.

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. U deze logboeken als platte tekst bekijken door een van de volgende opdrachten uit te voeren:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Geef &lt;de, de &lt;>, de &lt;> en> informatie over &lt;het uitvoeren van deze opdrachten op>, gebruiker-wie-de-toepassing, containerId-> en>-adres voor werknemers.

### <a name="other-sample-commands"></a>Andere voorbeeldopdrachten

1. Download Garen containers logs voor alle applicatie masters met de opdracht hieronder. Hiermee wordt het logboekbestand gemaakt met de naam `amlogs.txt` tekstnotatie.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Download Garencontainerlogboeken voor alleen de nieuwste toepassingsmaster met de volgende opdracht:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Download YARN-containerlogboeken voor de eerste twee toepassingsmasters met de volgende opdracht:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Download alle garencontainerlogboeken met de volgende opdracht:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Download garen container log voor een bepaalde container met de volgende opdracht:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>GAREN ResourceManager-gebruikersinterface

De YARN ResourceManager UI wordt uitgevoerd op de clusterheadnode. Het is toegankelijk via de Ambari web UI. Gebruik de volgende stappen om de GARENlogboeken weer te geven:

1. Navigeer in uw webbrowser `https://CLUSTERNAME.azurehdinsight.net`naar . Vervang CLUSTERNAME door de naam van uw HDInsight-cluster.

2. Selecteer **GAREN**in de lijst met services aan de linkerkant.

    ![Apache Ambari Garen service geselecteerd](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Selecteer in de vervolgkeuzelijst **Snelle koppelingen** een van de knooppunten van de clusterkop en selecteer **vervolgens ResourceManager-logboek**.

    ![Apache Ambari Garen snelle links](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Je krijgt een lijst met links naar YARN logs.
