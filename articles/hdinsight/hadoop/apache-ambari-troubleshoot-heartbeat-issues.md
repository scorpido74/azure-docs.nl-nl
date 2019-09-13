---
title: Problemen met Apache Ambari heartbeat in azure HDInsight
description: Bekijk de verschillende redenen voor Apache Ambari heartbeat-problemen in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 09/11/2019
ms.openlocfilehash: 6b1c30ab218b47e080935d2959cb3f701806ac68
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918138"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemen met Apache Ambari heartbeat in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="scenario-high-cpu-utilization"></a>Scenario: Hoog CPU-gebruik

### <a name="issue"></a>Probleem

De Ambari-agent heeft een hoog CPU-gebruik, wat leidt tot waarschuwingen van de Ambari-gebruikers interface die voor bepaalde knoop punten de Ambari-agent heartbeat verloren zijn gegaan. De waarschuwing heartbeat verloren is doorgaans tijdelijk. 

### <a name="cause"></a>Oorzaak

Vanwege verschillende ambari-agent fouten kan uw ambari-agent in zeldzame gevallen een hoog CPU-gebruik hebben (dicht tot 100).

### <a name="resolution"></a>Oplossing

1. Proces-ID (PID) van ambari-agent identificeren:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Voer vervolgens de volgende opdrachten uit om het CPU-gebruik weer te geven:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Ambari-agent opnieuw starten om het probleem te verhelpen:

    ```bash
    service ambari-agent restart
    ```

1. Als opnieuw opstarten niet werkt, beëindigt u het proces ambari agent en start u het op:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Scenario: De Ambari-agent is niet gestart

### <a name="issue"></a>Probleem

De Ambari-agent is niet gestart, waardoor er waarschuwingen van de Ambari-gebruikers interface worden gegenereerd die voor bepaalde knoop punten de heartbeat van de Ambari-agent verloren zijn gegaan.

### <a name="cause"></a>Oorzaak

De waarschuwingen worden veroorzaakt door de Ambari-agent die niet wordt uitgevoerd.

### <a name="resolution"></a>Oplossing

1. Status van ambari-agent bevestigen:

    ```bash
    service ambari-agent status
    ```

1. Controleren of de failover-controller services worden uitgevoerd:

    ```bash
    ps -ef | grep failover
    ```

    Als failover controller-Services niet worden uitgevoerd, is er waarschijnlijk een probleem met het voor komen van hdinsight-agent om failover controller te starten. Controleer het logboek van hdinsight- `/var/log/hdinsight-agent/hdinsight-agent.out` agent van het bestand.

---

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
