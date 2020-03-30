---
title: Clusterprestaties bewaken - Azure HDInsight
description: Hoe de status en prestaties van Apache Hadoop-clusters in Azure HDInsight te controleren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 75ac5a7fc352f877573d79a004d8da761c6f1cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082877"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Clusterprestaties bewaken in Azure HDInsight

Het bewaken van de status en prestaties van een HDInsight-cluster is essentieel voor het behoud van optimale prestaties en resourcegebruik. Met monitoring u ook clusterconfiguratiefouten en problemen met de gebruikerscode detecteren en oplossen.

In de volgende secties wordt beschreven hoe u de belasting van uw clusters controleren en optimaliseren, Apache Hadoop YARN-wachtrijen en opslagbeperkingsproblemen detecteren.

## <a name="monitor-cluster-load"></a>Clusterbelasting bewaken

Hadoop-clusters kunnen de meest optimale prestaties leveren wanneer de belasting op het cluster gelijkmatig over alle knooppunten is verdeeld. Hierdoor kunnen de verwerkingstaken worden uitgevoerd zonder te worden beperkt door RAM-, CPU- of schijfbronnen op afzonderlijke knooppunten.

Als u de knooppunten van uw cluster en het laden ervan op hoog niveau wilt bekijken, meldt u zich aan bij de [Gebruikersinterface van Ambari Web](hdinsight-hadoop-manage-ambari.md)en selecteert u het tabblad **Hosts.** Uw hosts worden vermeld door hun volledig gekwalificeerde domeinnamen. De bedrijfsstatus van elke host wordt weergegeven door een gekleurde gezondheidsindicator:

| Kleur | Beschrijving |
| --- | --- |
| Rood | Ten minste één hoofdcomponent op de host is uitgeschakeld. Plaats de plaats van de knop om een knopinfo te zien met de betreffende onderdelen. |
| Orange | Ten minste één secundaironderdeel op de host is uitgeschakeld. Plaats de plaats van de knop om een knopinfo te zien met de betreffende onderdelen. |
| Geel | Ambari Server heeft al meer dan 3 minuten geen heartbeat van de host ontvangen. |
| Groen | Normale looptoestand. |

U ziet ook kolommen met het aantal cores en de hoeveelheid RAM voor elke host en het schijfgebruik en het gemiddelde belasting.

![Apache Ambari host tabbladoverzicht](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

Selecteer een van de hostnamen voor een gedetailleerde blik op onderdelen die op die host en hun statistieken worden uitgevoerd. De statistieken worden weergegeven als een selecteerbare tijdlijn van CPU-gebruik, belasting, schijfgebruik, geheugengebruik, netwerkgebruik en aantal processen.

![Overzicht van Apache Ambari-hostdetails](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

Zie [HDInsight-clusters beheren met de Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) voor meer informatie over het instellen van waarschuwingen en het weergeven van statistieken.

## <a name="yarn-queue-configuration"></a>YARN-wachtrijconfiguratie

Hadoop heeft verschillende diensten die over het gedistribueerde platform lopen. YARN (Yet Another Resource Negotiator) coördineert deze services en wijst clusterresources toe om ervoor te zorgen dat elke belasting gelijkmatig over het cluster wordt verdeeld.

YARN verdeelt de twee verantwoordelijkheden van de JobTracker, resource management en job scheduling/monitoring, in twee daemons: een global Resource Manager en een ApplicationMaster per applicatie (AM).

De Resource Manager is een *pure planner*en bearbitreert alleen de beschikbare resources tussen alle concurrerende toepassingen. De Resource Manager zorgt ervoor dat alle resources altijd in gebruik zijn, optimaliseren voor verschillende constanten zoals SLA's, capaciteitsgaranties, enzovoort. De ApplicationMaster onderhandelt over resources van resourcemanager en werkt samen met de NodeManager(s) om de containers en het verbruik van de resources uit te voeren en te controleren.

Wanneer meerdere tenants een groot cluster delen, is er concurrentie voor de bronnen van het cluster. De CapacityScheduler is een pluggable scheduler die helpt bij het delen van resources door aanvragen in de wachtrij te plaatsen. De Capaciteitsplanner ondersteunt ook *hiërarchische wachtrijen* om ervoor te zorgen dat resources worden gedeeld tussen de subwachtrijen van een organisatie, voordat wachtrijen van andere toepassingen gratis resources mogen gebruiken.

YARN stelt ons in staat om resources toe te wijzen aan deze wachtrijen en laat u zien of al uw beschikbare resources zijn toegewezen. Als u informatie over uw wachtrijen wilt weergeven, meldt u zich aan bij de Ambari Web UI en selecteert u **YARN Queue Manager** in het bovenste menu.

![Apache Ambari YARN Queue Manager](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

Op de pagina YARN Queue Manager ziet u een lijst met uw wachtrijen aan de linkerkant, samen met het percentage capaciteit dat aan elk nummer is toegewezen.

![Detailspagina YARN Queue Manager](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Selecteer de **YARN-service** in de lijst aan de linkerkant voor een meer gedetailleerde blik op uw wachtrijen in het Dashboard Van Ambari. Selecteer vervolgens onder het vervolgkeuzemenu **Snelle koppelingen** **de gebruikersinterface van Resourcemanager** onder uw actieve knooppunt.

![Koppelingen in het uI-menu van Resource Manager](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

Selecteer **Scheduler** in de gebruikersinterface van Resourcemanager in het linkermenu. U ziet een lijst met wachtrijen onder *Toepassingswachtrijen*. Hier ziet u de capaciteit die wordt gebruikt voor elk van uw wachtrijen, hoe goed de taken tussen deze rijen worden verdeeld en of taken beperkt zijn voor resources.

![Apache HAdoop Resource Manager UI menu](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>Opslagbeperking

Het prestatieknelpunt van een cluster kan optreden op opslagniveau. Dit type knelpunt is meestal het gevolg van *het blokkeren* van input/output (IO)-bewerkingen, die plaatsvinden wanneer uw actieve taken meer IO verzenden dan de opslagservice aankan. Met deze blokkering wordt een wachtrij met IO-aanvragen veroorzaakt die wachten om te worden verwerkt totdat de huidige IOs zijn verwerkt. De blokken zijn vanwege *opslagbeperking*, wat geen fysieke limiet is, maar eerder een limiet die door de opslagservice wordt opgelegd door een servicelevelovereenkomst (SLA). Deze limiet zorgt ervoor dat geen enkele klant of huurder de service kan monopoliseren. De SLA beperkt het aantal IOs per seconde (IOPS) voor Azure Storage - zie [Schaalbaarheid en prestatiedoelen voor standaardopslagaccounts](../storage/common/scalability-targets-standard-account.md)voor meer informatie.

Als u Azure Storage gebruikt, raadpleegt u Voor informatie over het bewaken van opslaggerelateerde problemen, waaronder beperking, [controleren, diagnosticeren en problemen oplossen van Microsoft Azure Storage.](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting)

Als de back-upopslag van uw cluster Azure Data Lake Storage (ADLS) is, is uw beperking waarschijnlijk vanwege bandbreedtelimieten. Beperking in dit geval kan worden geïdentificeerd door het observeren van throttling fouten in taaklogboeken. Zie voor ADLS de sectie beperking voor de juiste service in deze artikelen:

* [Richtlijnen voor prestatieafstemming voor Apache Hive op HDInsight en Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Richtlijnen voor prestatieafstemming voor mapreduce op HDInsight en Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Richtlijnen voor prestatieafstemming voor Apache Storm op HDInsight en Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>Trage knooppuntprestaties oplossen

In sommige gevallen kan traagheid optreden als gevolg van lage schijfruimte op het cluster. Onderzoeken met de volgende stappen:

1. Gebruik [de ssh-opdracht](./hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met elk van de knooppunten.

1. Controleer het schijfgebruik door een van de volgende opdrachten uit te voeren:

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. Controleer de uitvoer en controleer op de aanwezigheid `mnt` van grote bestanden in de map of andere mappen. Typisch, de `usercache`, `appcache` en (mnt / resource / hadoop / garen / lokale / usercache / hive / appcache /) mappen bevatten grote bestanden.

1. Als er grote bestanden zijn, veroorzaakt een huidige taak de bestandsgroei of heeft een mislukte vorige taak mogelijk bijgedragen aan dit probleem. Voer de volgende opdracht uit om te controleren of dit gedrag wordt veroorzaakt door een huidige taak:

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. Als deze opdracht een specifieke taak aangeeft, u ervoor kiezen de taak te beëindigen met behulp van een opdracht die lijkt op de volgende:

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    Vervang `application_id` door de toepassings-id. Als er geen specifieke taken zijn aangegeven, gaat u naar de volgende stap.

1. Nadat de bovenstaande opdracht is voltooid of als er geen specifieke taken zijn aangegeven, verwijdert u de grote bestanden die u hebt geïdentificeerd door een opdracht uit te voeren die lijkt op de volgende:

    ```bash
    rm -rf filecache usercache
    ```

Zie [Out of diskspace](./hadoop/hdinsight-troubleshoot-out-disk-space.md)voor meer informatie over schijfruimte.

> [!NOTE]  
> Als u grote bestanden hebt die u wilt bewaren, maar die bijdragen aan het probleem met de lage schijfruimte, moet u uw HDInsight-cluster opschalen en uw services opnieuw opstarten. Nadat u deze procedure hebt voltooid en enkele minuten hebt gewacht, zult u merken dat de opslag is vrijgemaakt en dat de gebruikelijke prestaties van het knooppunt worden hersteld.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende links voor meer informatie over het oplossen van problemen en het bewaken van uw clusters:

* [HDInsight-logboeken analyseren](hdinsight-debug-jobs.md)
* [Foutopsporings-apps met Apache Hadoop YARN-logboeken](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Heap dumps inschakelen voor Apache Hadoop services op Linux-gebaseerde HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
