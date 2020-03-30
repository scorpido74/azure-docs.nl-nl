---
title: Problemen met de Apache Ambari-heartbeat in Azure HDInsight
description: Bekijk verschillende redenen voor Apache Ambari heartbeat problemen in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057070"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemen met de Apache Ambari-heartbeat in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="scenario-high-cpu-utilization"></a>Scenario: Hoog CPU-gebruik

### <a name="issue"></a>Probleem

Ambari-agent heeft een hoog CPU-gebruik, wat resulteert in waarschuwingen van Ambari UI dat voor sommige knooppunten de Hartslag van de Ambari-agent verloren gaat. De hartslag verloren waarschuwing is meestal van voorbijgaande aard.

### <a name="cause"></a>Oorzaak

Als gevolg van verschillende ambari-agent bugs, in zeldzame gevallen, kan uw ambari-agent hebben een hoog (bijna 100) percentage CPU-gebruik.

### <a name="resolution"></a>Oplossing

1. Identificeer proces-ID (pid) van ambari-agent:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Voer vervolgens de volgende opdrachten uit om het CPU-gebruik weer te geven:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Start ambari-agent opnieuw om problemen te beperken:

    ```bash
    service ambari-agent restart
    ```

1. Als herstart niet werkt, doodt u het ambari-agentproces en start u het op:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Scenario: Ambari-agent niet gestart

### <a name="issue"></a>Probleem

Ambari agent is niet begonnen wat resulteert in waarschuwingen van Ambari UI dat voor sommige knooppunten de Ambari agent heartbeat verloren gaat.

### <a name="cause"></a>Oorzaak

De waarschuwingen worden veroorzaakt doordat de Ambari-agent niet wordt uitgevoerd.

### <a name="resolution"></a>Oplossing

1. Bevestig de status van ambari-agent:

    ```bash
    service ambari-agent status
    ```

1. Controleer of failovercontrollerservices worden uitgevoerd:

    ```bash
    ps -ef | grep failover
    ```

    Als failover controller services niet worden uitgevoerd, is het waarschijnlijk te wijten aan een probleem te voorkomen hdinsight-agent starten failover controller. Controleer hdinsight-agent `/var/log/hdinsight-agent/hdinsight-agent.out` log uit het bestand.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Scenario: Heartbeat verloren voor Ambari

### <a name="issue"></a>Probleem

Ambari hartslagagent is verloren gegaan.

### <a name="cause"></a>Oorzaak

OMS-logboeken veroorzaken een hoog CPU-gebruik.

### <a name="resolution"></a>Oplossing

* Azure Monitor-logboekregistratie uitschakelen met de powerdlet [Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) PowerShell.
* Het `mdsd.warn` logboekbestand verwijderen

---

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
