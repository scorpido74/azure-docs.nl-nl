---
title: Apache HBase Master kan niet starten in Azure HDInsight
description: Apache HBase Master (HMaster) kan niet starten in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887203"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) kan niet starten in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="scenario-atomic-renaming-failure"></a>Scenario: Atomaire hernoemen van mislukte

### <a name="issue"></a>Probleem

Onverwachte bestanden die tijdens het opstartproces zijn geïdentificeerd.

### <a name="cause"></a>Oorzaak

Tijdens het opstartproces voert HMaster veel initialisatiestappen uit, waaronder het verplaatsen van gegevens van de map scratch (.tmp) naar de gegevensmap. HMaster kijkt ook naar de map write-ahead logs (WAL) om te zien of er niet-reagerende regioservers zijn.

HMaster doet een basislijstopdracht op de WAL-mappen. Als HMaster op enig moment een onverwacht bestand in een van deze mappen ziet, wordt er een uitzondering gemaakt en wordt er niet gestart.

### <a name="resolution"></a>Oplossing

Controleer de oproepstack en probeer te bepalen welke map het probleem kan veroorzaken (het kan bijvoorbeeld de WAL-map of de .tmp-map zijn). Probeer vervolgens in Cloud Explorer of met HDFS-opdrachten het probleembestand te zoeken. Meestal is dit `*-renamePending.json` een bestand. (Het `*-renamePending.json` bestand is een logboekbestand dat wordt gebruikt om de atomaire hernaambewerking in het WASB-stuurprogramma te implementeren. Als gevolg van bugs in deze implementatie, kunnen deze bestanden worden overgelaten na proces crashes, en ga zo maar door.) Verwijder dit bestand in Cloud Explorer of met HDFS-opdrachten.

Soms kan er ook een tijdelijk `$$$.$$$` bestand met de naam iets als op deze locatie. U moet de `ls` opdracht HDFS gebruiken om dit bestand te zien; u het bestand niet zien in Cloud Explorer. Als u dit bestand wilt `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`verwijderen, gebruikt u de opdracht HDFS .

Nadat u deze opdrachten hebt uitgevoerd, moet HMaster onmiddellijk beginnen.

---

## <a name="scenario-no-server-address-listed"></a>Scenario: Geen serveradres vermeld

### <a name="issue"></a>Probleem

Mogelijk ziet u een bericht `hbase: meta` dat aangeeft dat de tabel niet online is. Als `hbck` u `hbase: meta table replicaId 0 is not found on any region.` dit uitvoert, wordt mogelijk gemeld dat `No server address listed in hbase: meta for region hbase: backup <region name>`u in de HMaster-logboeken het bericht zien:.  

### <a name="cause"></a>Oorzaak

HMaster kon niet initialiseren na het opnieuw opstarten van HBase.

### <a name="resolution"></a>Oplossing

1. Voer in de dop HBase de volgende opdrachten in (wijzig de werkelijke waarden indien van toepassing):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Verwijder `hbase: namespace` de vermelding. Dit item kan dezelfde fout zijn die `hbase: namespace` wordt gerapporteerd wanneer de tabel wordt gescand.

1. Start de actieve HMaster opnieuw van Ambari UI om HBase in actieve status weer te geven.

1. Voer in de dop HBase de volgende opdracht uit om alle offline tabellen weer te geven:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scenario: java.io.IOException: Timedout

### <a name="issue"></a>Probleem

HMaster times out met fatale `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`uitzondering vergelijkbaar met: .

### <a name="cause"></a>Oorzaak

U dit probleem ondervinden als u veel tabellen en regio's hebt die niet zijn doorgespoeld wanneer u uw HMaster-services opnieuw start. De time-out is een bekend defect met HMaster. Algemene clusteropstarttaken kunnen lang duren. HMaster wordt afgesloten als de tabel met naamruimte nog niet is toegewezen. De lange opstarttaken vinden plaats wanneer er een grote hoeveelheid niet-gespoelde gegevens bestaat en een time-out van vijf minuten niet voldoende is.

### <a name="resolution"></a>Oplossing

1. Ga vanaf de Apache Ambari UI naar **HBase** > **Configs.** Voeg in `hbase-site.xml` het aangepaste bestand de volgende instelling toe:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Start de vereiste services opnieuw (HMaster en mogelijk andere HBase-services).

---

## <a name="scenario-frequent-region-server-restarts"></a>Scenario: Frequente regioserver wordt opnieuw opgestart

### <a name="issue"></a>Probleem

Knooppunten worden periodiek opnieuw opgestart. In de logboeken van de regioserver ziet u mogelijk items die vergelijkbaar zijn met:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Oorzaak

Lange `regionserver` JVM GC pauze. De pauze `regionserver` zal ervoor zorgen dat niet reageert en niet in staat om hartslag te sturen naar HMaster binnen de zk sessie time-out 40s. HMaster zal `regionserver` geloven dat het `regionserver` dood is en zal de afbreken en opnieuw opstarten.

### <a name="resolution"></a>Oplossing

Verander de Zookeeper sessie time-out, `hbase-site` niet alleen instelling, `zookeeper.session.timeout` maar ook Zookeeper `zoo.cfg` instelling `maxSessionTimeout` moeten worden gewijzigd.

1. Toegang tot Ambari UI, ga naar **HBase-> Configs-> Instellingen**, in time-outs sectie, wijzig de waarde van Zookeeper Session Timeout.

1. Toegang tot Ambari UI, ga naar **Zookeeper -> Configs -> Custom,** `zoo.cfg`voeg/wijzig de volgende instelling. Zorg ervoor dat de waarde `zookeeper.session.timeout`gelijk is aan HBase.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Start vereiste services opnieuw op.

---

## <a name="scenario-log-splitting-failure"></a>Scenario: Fout bij het splitsen van logboeken

### <a name="issue"></a>Probleem

HMasters is er niet in geslaagd om op een HBase-cluster te komen.

### <a name="cause"></a>Oorzaak

Verkeerd geconfigureerde HDFS- en HBase-instellingen voor een secundair opslagaccount.

### <a name="resolution"></a>Oplossing

hbase.rootdir instellen: wasb://@.blob.core.windows.net/hbase services opnieuw opstarten op Ambari.

---

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
