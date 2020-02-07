---
title: Problemen met Apache Ambari heartbeat in azure HDInsight
description: Bekijk de verschillende redenen voor Apache Ambari heartbeat-problemen in azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057070"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemen met Apache Ambari heartbeat in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="scenario-high-cpu-utilization"></a>Scenario: hoog CPU-gebruik

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

## <a name="scenario-ambari-agent-not-started"></a>Scenario: Ambari-agent is niet gestart

### <a name="issue"></a>Probleem

De Ambari-agent is nog niet gestart, waardoor er waarschuwingen zijn van de Ambari-gebruikers interface die voor bepaalde knoop punten de heartbeat van de Ambari-agent is verbroken.

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

    Als failover controller-Services niet worden uitgevoerd, is er waarschijnlijk een probleem met het niet toestaan van hdinsight-agent om failover controller te starten. Controleer het logboek van hdinsight-agent van `/var/log/hdinsight-agent/hdinsight-agent.out` bestand.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Scenario: heartbeat verloren voor Ambari

### <a name="issue"></a>Probleem

De Ambari heartbeat-agent is verloren gegaan.

### <a name="cause"></a>Oorzaak

OMS-logboeken veroorzaken een hoog CPU-gebruik.

### <a name="resolution"></a>Oplossing

* Schakel Azure Monitor logboek registratie uit met de Power shell [-cmdlet Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) .
* Het `mdsd.warn` logboek bestand verwijderen

---

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
