---
title: Cluster prestaties bewaken-Azure HDInsight
description: De status en prestaties van Apache Hadoop clusters in azure HDInsight bewaken.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 706f0333f1cca984b44e0fc96a5b014d4157ce4c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879691"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Cluster prestaties in azure HDInsight bewaken

Het bewaken van de status en prestaties van een HDInsight-cluster is essentieel voor optimale prestaties en optimaal gebruik van resources. Bewaking kan u ook helpen bij het detecteren en adresseren van cluster configuratie fouten en problemen met de gebruikers code.

In de volgende secties wordt beschreven hoe u de belasting van uw clusters bewaken en optimaliseert, Apache Hadoop GARENs in de werk ruimte en problemen met de opslag beperking kunt detecteren.

## <a name="monitor-cluster-load"></a>Cluster belasting bewaken

Hadoop-clusters kunnen de meest optimale prestaties leveren wanneer de belasting van het cluster gelijkmatig over alle knoop punten wordt verdeeld. Hierdoor kunnen de verwerkings taken worden uitgevoerd zonder te worden beperkt door RAM-, CPU-of schijf bronnen op afzonderlijke knoop punten.

Meld u aan bij de [Ambari-webgebruikersinterface](hdinsight-hadoop-manage-ambari.md)en selecteer vervolgens het tabblad **hosts** om een hoog niveau te krijgen voor de knoop punten van uw cluster en het laden ervan. Uw hosts worden weer gegeven op basis van de volledig gekwalificeerde domein namen. De operationele status van elke host wordt weer gegeven met een gekleurde status indicator:

| Kleur | Description |
| --- | --- |
| Rood | Ten minste één hoofd onderdeel op de host is niet beschikbaar. Beweeg de muis aanwijzer om een knop Info weer te geven waarin de betrokken onderdelen worden weer gegeven. |
| Oranje | Ten minste één secundair onderdeel op de host is niet beschikbaar. Beweeg de muis aanwijzer om een knop Info weer te geven waarin de betrokken onderdelen worden weer gegeven. |
| Geel | De Ambari-server heeft meer dan drie minuten geen heartbeat van de host ontvangen. |
| Groen | Normale status. |

U ziet ook kolommen met het aantal kernen en de hoeveelheid RAM-geheugen voor elke host, en het schijf gebruik en de gemiddelde belasting.

![Tabblad hosts](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Selecteer een van de hostnamen voor een gedetailleerde weer gave van de onderdelen die worden uitgevoerd op die host en de metrische gegevens. De metrische gegevens worden weer gegeven als een selecteerbaar tijd lijn van CPU-gebruik, belasting, schijf gebruik, geheugen gebruik, netwerk gebruik en aantal processen.

![Details van host](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

Zie [HDInsight-clusters beheren met de Web-UI van Apache Ambari](hdinsight-hadoop-manage-ambari.md) voor meer informatie over het instellen van waarschuwingen en het weer geven van metrische gegevens.

## <a name="yarn-queue-configuration"></a>Configuratie van de garen wachtrij

Hadoop heeft verschillende services die worden uitgevoerd op het gedistribueerde platform. GARENs (nog een andere resource-onderhandel) coördineert deze services en wijst cluster bronnen toe om ervoor te zorgen dat elke belasting gelijkmatig over het cluster wordt verdeeld.

GARENs delen de twee verantwoordelijkheden van de JobTracker, resource beheer en taak planning/-bewaking in twee daemons: een globale Resource Manager en een ApplicationMaster (AM) per toepassing.

De Resource Manager is een *pure planner*en alleen arbitrates beschik bare bronnen tussen alle concurrerende toepassingen. De Resource Manager zorgt ervoor dat alle resources altijd in gebruik zijn, met optimalisatie voor diverse constanten, zoals service overeenkomsten, capaciteits garanties enzovoort. De ApplicationMaster onderhandelt resources van Resource Manager en werkt samen met de NodeManager (s) voor het uitvoeren en controleren van de containers en het Resource verbruik.

Wanneer meerdere tenants een groot cluster delen, is er een competitie voor de resources van het cluster. De CapacityScheduler is een pluggable scheduler die helpt bij het delen van resources door aanvragen in de wachtrij te plaatsen. De CapacityScheduler biedt ook ondersteuning voor *hiërarchische wacht rijen* om ervoor te zorgen dat bronnen worden gedeeld tussen de subwachtrijen van een organisatie, voordat de wachtrij van andere toepassingen vrije bronnen mag gebruiken.

Met GARENs kunnen we resources toewijzen aan deze wacht rijen en kunt u zien of al uw beschik bare resources zijn toegewezen. Als u informatie over uw wacht rijen wilt weer geven, meldt u zich aan bij de Ambari-webgebruikersinterface en selecteert u vervolgens **garen wachtrij beheer** in het bovenste menu.

![GAREN van de wachtrij](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

Op de pagina GARENs Queue Manager wordt aan de linkerkant een lijst met uw wacht rijen weer gegeven, samen met het percentage toegewezen capaciteit.

![Pagina Details van de garen-wachtrij beheerder](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Voor een gedetailleerdere weer gave van uw wacht rijen, in het Ambari-dash board selecteert u de service **garen** in de lijst aan de linkerkant. Selecteer vervolgens in het vervolg keuzemenu **snelle koppelingen** de **gebruikers interface van Resource Manager** onder het actieve knoop punt.

![Menu-koppeling voor Resource Manager-gebruikers interface](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

Selecteer in de gebruikers interface van Resource Manager **scheduler** in het menu aan de linkerkant. U ziet een lijst met uw wacht rijen onder *toepassings wachtrijen*. Hier ziet u de capaciteit die voor elk van uw wacht rijen wordt gebruikt, hoe goed de taken worden gedistribueerd en of alle taken zijn beperkt tot een resource.

![Menu gebruikers interface van Resource Manager](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Opslag beperking

Het prestatie knelpunt van een cluster kan zich voordoen op het opslag niveau. Dit type knel punt wordt meestal veroorzaakt door het *blok keren* van invoer/uitvoer-bewerkingen (i/o), wat er gebeurt wanneer uw actieve taken meer io verzenden dan de opslag service kan verwerken. Deze blok kering maakt een wachtrij met i/o-aanvragen die nog moeten worden verwerkt totdat de huidige IOs is verwerkt. De blokken worden veroorzaakt door *beperking*van de opslag, wat geen fysieke limiet is, maar een limiet die door de opslag service wordt opgelegd door een Service Level Agreement (Sla). Deze limiet zorgt ervoor dat er geen enkele client of Tenant de service in beslag kan nemen. De SLA beperkt het aantal IOs per seconde (IOPS) voor Azure Storage-Zie [Azure Storage schaal baarheid en prestatie doelen](https://docs.microsoft.com/azure/storage/storage-scalability-targets)voor meer informatie.

Als u Azure Storage gebruikt, raadpleegt u voor meer informatie over het bewaken van problemen met betrekking tot opslag, zoals beperking, het [controleren, diagnosticeren en probleem oplossing van Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Als de back-upopslag van uw cluster Azure Data Lake Storage is (ADLS), is uw beperking waarschijnlijk het gevolg van bandbreedte limieten. Beperking in dit geval kan worden geïdentificeerd door beperkings fouten in taak logboeken te observeren. Voor ADLS raadpleegt u de sectie beperking voor de betreffende service in de volgende artikelen:

* [Richt lijnen voor het afstemmen van de prestaties van Apache Hive op HDInsight en Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Richt lijnen voor het afstemmen van de prestaties voor MapReduce in HDInsight en Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Richt lijnen voor het afstemmen van de prestaties van Apache Storm op HDInsight en Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende koppelingen voor meer informatie over het oplossen van problemen en het bewaken van uw clusters:

* [HDInsight-logboeken analyseren](hdinsight-debug-jobs.md)
* [Apps opsporen met Apache Hadoop GARENs logboeken](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Heap-dumps inschakelen voor Apache Hadoop Services op HDInsight op basis van Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
