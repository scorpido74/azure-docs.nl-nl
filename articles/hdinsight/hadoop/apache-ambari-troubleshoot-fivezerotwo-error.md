---
title: Apache Ambari UI 502-fout in Azure HDInsight
description: Apache Ambari UI 502-fout wanneer u toegang probeert te krijgen tot uw Azure HDInsight-cluster
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895761"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Scenario: Apache Ambari UI 502-fout in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Wanneer u toegang probeert te krijgen tot de Apache Ambari-gebruikersinterface voor uw HDInsight-cluster, krijgt u een bericht dat vergelijkbaar is met: "502 - Webserver heeft een ongeldige reactie ontvangen terwijl u optreedt als gateway- of proxyserver."

## <a name="cause"></a>Oorzaak

In het algemeen betekent de HTTP 502-statuscode dat de Ambari-server niet correct op de actieve headnode wordt uitgevoerd. Er zijn een paar mogelijke oorzaken.

## <a name="resolution"></a>Oplossing

In de meeste gevallen u, om het probleem te beperken, de actieve headnode opnieuw starten. Of kies een grotere VM-maat voor uw headnode.

### <a name="ambari-server-failed-to-start"></a>Ambari-server kan niet worden gestart

U ambari-serverlogboeken controleren om erachter te komen waarom de Ambari-server niet is gestart. Een veel voorkomende reden is de fout van de databaseconsistentiecontrole. U dit vinden in `/var/log/ambari-server/ambari-server-check-database.log`dit log-bestand:.

Als u wijzigingen hebt aangebracht in het clusterknooppunt, u deze ongedaan maken. Gebruik ambari-gebruikersinterface altijd om hadoop/spark-gerelateerde configuraties te wijzigen.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari-server neemt 100% CPU-gebruik

In zeldzame situaties hebben we gezien ambari-server proces heeft bijna 100% CPU-gebruik voortdurend. Als een mitigatie, u ssh naar de actieve headnode, en dood de Ambari server proces en start het opnieuw.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Ambari server gedood door oom-killer

In sommige scenario's, je headnode loopt uit het geheugen, en de Linux oom-killer begint te kiezen processen om te doden. U deze situatie verifiëren door te zoeken op de AmbariServer-proces-ID, die niet mag worden gevonden. Kijk dan `/var/log/syslog`naar je , en kijk voor iets als dit:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Identificeer vervolgens welke processen herinneringen oproepen en probeer verder te achterhalen.

### <a name="other-issues-with-ambari-server"></a>Andere problemen met de Ambari-server

Zelden kan de Ambari-server het binnenkomende verzoek niet verwerken, u meer informatie vinden door te kijken naar de ambari-serverlogboeken voor een fout. Een dergelijk geval is een fout als deze:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
