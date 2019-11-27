---
title: Toegang tot de toepassings logboeken van Apache Hadoop garen-Azure HDInsight
description: Meer informatie over het openen van toepassings logboeken op basis van een op Linux gebaseerd HDInsight-cluster (Apache Hadoop) met behulp van de opdracht regel en een webbrowser.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 883e1007b35991c1f5d8f0c6e949efcb48c27a1d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327224"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Toegang tot Apache Hadoop GARENs van toepassings logboeken op HDInsight op basis van Linux

Meer informatie over toegang tot de logboeken voor [Apache HADOOP garens](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (nog een andere resource-onderhandelings toepassing) op een [Apache Hadoop](https://hadoop.apache.org/) cluster in azure HDInsight.

## <a name="what-is-apache-yarn"></a>Wat is Apache GARENs?

GARENs ondersteunt meerdere programmeer modellen ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) zijn) door het ontkoppelen van resource beheer vanuit toepassings planning/-bewaking. GARENs maakt gebruik van een globale *Resource Manager* (RM), per worker-knoop punt *NodeManagers* (NMs) en per toepassings *ApplicationMasters* (AMS). De per-toepassing onderhandelt bronnen (CPU, geheugen, schijf, netwerk) voor het uitvoeren van uw toepassing met de RM. De RM werkt met NMs voor het verlenen van deze resources, die als *containers*worden verleend. De AM is verantwoordelijk voor het volgen van de voortgang van de containers die hieraan zijn toegewezen door de RM. Een toepassing kan veel containers vereisen, afhankelijk van de aard van de toepassing.

Elke toepassing kan bestaan uit meerdere *toepassings pogingen*. Als een toepassing mislukt, wordt deze mogelijk opnieuw geprobeerd als een nieuwe poging. Elke poging wordt uitgevoerd in een container. In een zin bevat een container de context voor de basis eenheid van het werk dat door een toepassing met een garen is uitgevoerd. Alle werkzaamheden die in de context van een container worden uitgevoerd, worden uitgevoerd op het knoop punt voor één werk nemer waarop de container is toegewezen. Zie [Hadoop: garens schrijven](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)of [Apache Hadoop garens](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) voor meer informatie.

Als u uw cluster wilt schalen om een grotere verwerking door voer te ondersteunen, kunt u automatisch [schalen](hdinsight-autoscale-clusters.md) gebruiken of [uw clusters hand matig schalen met een aantal verschillende talen](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="YARNTimelineServer"></a>Tijdlijn server-garen

De [tijdlijn server van Apache HADOOP garen](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) biedt algemene informatie over voltooide toepassingen

De lijn van een garen tijdlijn server bevat het volgende type gegevens:

* De toepassings-ID, een unieke id van een toepassing
* De gebruiker die de toepassing heeft gestart
* Informatie over pogingen om de toepassing te volt ooien
* De containers die worden gebruikt door een bepaalde toepassings poging

## <a name="YARNAppsAndLogs"></a>Toepassingen en logboeken van garen

GARENs ondersteunt meerdere programmeer modellen ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) zijn) door het ontkoppelen van resource beheer vanuit toepassings planning/-bewaking. GARENs maakt gebruik van een globale *Resource Manager* (RM), per worker-knoop punt *NodeManagers* (NMs) en per toepassings *ApplicationMasters* (AMS). De per-toepassing onderhandelt bronnen (CPU, geheugen, schijf, netwerk) voor het uitvoeren van uw toepassing met de RM. De RM werkt met NMs voor het verlenen van deze resources, die als *containers*worden verleend. De AM is verantwoordelijk voor het volgen van de voortgang van de containers die hieraan zijn toegewezen door de RM. Een toepassing kan veel containers vereisen, afhankelijk van de aard van de toepassing.

Elke toepassing kan bestaan uit meerdere *toepassings pogingen*. Als een toepassing mislukt, wordt deze mogelijk opnieuw geprobeerd als een nieuwe poging. Elke poging wordt uitgevoerd in een container. In een zin bevat een container de context voor de basis eenheid van het werk dat door een toepassing met een garen is uitgevoerd. Alle werkzaamheden die in de context van een container worden uitgevoerd, worden uitgevoerd op het knoop punt voor één werk nemer waarop de container is toegewezen. Zie [Apache HADOOP garens](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) voor meer informatie.

Toepassings Logboeken (en de bijbehorende container Logboeken) zijn van cruciaal belang bij het opsporen van fouten in problemen met Hadoop-toepassingen. GARENs bieden een goed kader voor het verzamelen, samen voegen en opslaan van toepassings logboeken met de functie voor het [samen voegen van Logboeken](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) . Met de functie voor het samen voegen van Logboeken wordt toegang tot toepassings logboeken meer deterministisch. Hiermee worden logboeken samengevoegd over alle containers op een werk knooppunt en worden deze opgeslagen als één geaggregeerd logboek bestand per worker-knoop punt. Het logboek wordt opgeslagen op het standaard bestandssysteem nadat een toepassing is voltooid. Uw toepassing kan honderden of duizenden containers gebruiken, maar logboeken voor alle containers die worden uitgevoerd op één worker-knoop punt worden altijd geaggregeerd naar één bestand. Er is slechts één logboek per werk knooppunt dat door uw toepassing wordt gebruikt. Logboek aggregatie is standaard ingeschakeld op HDInsight-clusters versie 3,0 en hoger. Samengevoegde logboeken bevinden zich in de standaard opslag voor het cluster. Het volgende pad is het HDFS-pad naar de logboeken:

    /app-logs/<user>/logs/<applicationId>

In het pad is `user` de naam van de gebruiker die de toepassing heeft gestart. De `applicationId` is de unieke id die is toegewezen aan een toepassing door de GARENs RM.

De geaggregeerde logboeken zijn niet direct leesbaar, omdat ze zijn geschreven in een [TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf), [binaire indeling](https://issues.apache.org/jira/browse/HADOOP-3315) geïndexeerd door container. Gebruik de indelings-of CLI-hulpprogram ma's van de garen om deze logboeken als tekst zonder opmaak te bekijken voor toepassingen of containers die van belang zijn.

## <a name="yarn-cli-tools"></a>Hulp middelen voor garen-CLI

Als u de hulpprogram ma's voor garen-CLI wilt gebruiken, moet u eerst met SSH verbinding maken met het HDInsight-cluster. Zie [SSH-sleutels gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

U kunt deze logboeken als tekst zonder opmaak weer geven door een van de volgende opdrachten uit te voeren:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Geef de &lt;applicationId > op, &lt;gebruiker-who-started-Application >, &lt;containerId > en &lt;informatie over worker-node-Address > bij het uitvoeren van deze opdrachten.

## <a name="yarn-resourcemanager-ui"></a>De gebruikers interface van garen

De interface van de garen-WebResource Manager wordt uitgevoerd op het cluster hoofd knooppunt. Het is toegankelijk via de Ambari-webgebruikersinterface. Voer de volgende stappen uit om de GARENs-logboeken weer te geven:

1. Navigeer in uw webbrowser naar https://CLUSTERNAME.azurehdinsight.net. Vervang CLUSTERNAME door de naam van uw HDInsight-cluster.
2. Selecteer in de lijst met services aan de linkerkant de optie **garens**.

    ![Apache Ambari garen-service geselecteerd](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Selecteer in de vervolg keuzelijst **snelle koppelingen** een van de cluster hoofd knooppunten en selecteer vervolgens het **bestand Manager-logboek**.

    ![Snelle koppelingen voor Apache Ambari-garens](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Er wordt een lijst weer gegeven met koppelingen naar GARENs in de logboeken.
