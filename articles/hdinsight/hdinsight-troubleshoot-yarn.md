---
title: Problemen met YARN in Azure HDInsight oplossen
description: Krijg antwoorden op veelgestelde vragen over het werken met Apache Hadoop YARN en Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: f0c7b966b9fa7580809d2df0f4d05a7146ca0fd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272199"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Problemen met Apache Hadoop YARN oplossen met behulp van Azure HDInsight

Meer informatie over de belangrijkste problemen en hun resoluties bij het werken met Apache Hadoop YARN payloads in Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Hoe maak ik een nieuwe GAREN-wachtrij op een cluster?

### <a name="resolution-steps"></a>Oplossingen

Gebruik de volgende stappen in Ambari om een nieuwe GAREN-wachtrij te maken en vervolgens de capaciteitstoewijzing tussen alle wachtrijen in evenwicht te brengen.

In dit voorbeeld worden twee bestaande wachtrijen **(standaard** en **thriftsvr)** beide gewijzigd van 50% capaciteit naar 25% capaciteit, waardoor de nieuwe wachtrij (spark) 50% capaciteit krijgt.

| Wachtrij | Capaciteit | Maximale capaciteit |
| --- | --- | --- |
| standaardinstelling | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. Selecteer het pictogram **Ambari-weergaven** en selecteer vervolgens het rasterpatroon. Selecteer vervolgens **YARN Queue Manager**.

    ![Apache Ambari dashboard YARN Queue Manager](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. Selecteer de **standaardwachtrij.**

    ![Apache Ambari YARN selecteert standaardwachtrij](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. Wijzig voor de **standaardwachtrij** de **capaciteit** van 50% naar 25%. Voor de **thriftsvr** wachtrij, verander de **capaciteit** naar 25%.

    ![De capaciteit wijzigen in 25% voor de standaard- en spaarzame vr-wachtrijen](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. Als u een nieuwe wachtrij wilt maken, selecteert u **Wachtrij toevoegen**.

    ![Apache Ambari YARN-dashboard Wachtrij toevoegen](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. Geef de nieuwe wachtrij een naam.

    ![Apache Ambari YARN-dashboardnaam Wachtrij](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. Laat de **capaciteitswaarden** op 50% staan en selecteer vervolgens de knop **Handelingen.**

    ![Apache Ambari YARN selecteer actie](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. Selecteer **Wachtrijen opslaan en vernieuwen**.

    ![Wachtrijen opslaan en vernieuwen selecteren](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

Deze wijzigingen zijn direct zichtbaar op de YARN Scheduler UI.

### <a name="additional-reading"></a>Aanvullende lezing

- [Apache Hadoop YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Hoe download ik YARN-logboeken van een cluster?

### <a name="resolution-steps"></a>Oplossingen

1. Maak verbinding met het HDInsight-cluster via een Secure Shell -client (SSH). Zie [Aanvullende lectuur](#additional-reading-2)voor meer informatie.

1. Voer de volgende opdracht uit om alle toepassings-in-het-d-toepassingen van de YARN-toepassingen aan te geven die momenteel worden uitgevoerd:

    ```apache
    yarn top
    ```

    De id's worden vermeld in de kolom **APPLICATIONID.** U logboeken downloaden uit de **kolom APPLICATIONID.**

    ```apache
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

1. Als u YARN-containerlogboeken voor alle toepassingsmodellen wilt downloaden, gebruikt u de volgende opdracht:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Met deze opdracht wordt een logboekbestand met de naam amlogs.txt ge.

1. Als u YARN-containerlogboeken wilt downloaden voor alleen de nieuwste toepassingsmaster, gebruikt u de volgende opdracht:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Met deze opdracht wordt een logboekbestand met de naam latestamlogs.txt aangenomen.

1. Als u YARN-containerlogboeken wilt downloaden voor de eerste twee toepassingsmodellen, gebruikt u de volgende opdracht:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Met deze opdracht wordt een logboekbestand met de naam first2amlogs.txt.

1. Als u alle YARN-containerlogboeken wilt downloaden, gebruikt u de volgende opdracht:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Met deze opdracht wordt een logboekbestand met de naam logs.txt.

1. Als u het YARN-containerlogboek voor een specifieke container wilt downloaden, gebruikt u de volgende opdracht:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Met deze opdracht wordt een logboekbestand met de naam containerlogs.txt.

### <a name="additional-reading"></a><a name="additional-reading-2"></a>Aanvullende lezing

- [Verbinding maken met HDInsight (Apache Hadoop) met Behulp van SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN concepten en toepassingen](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

- Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

- Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
