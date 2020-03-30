---
title: Apache Ambari-gebruik in Azure HDInsight
description: Discussie over hoe Apache Ambari wordt gebruikt in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77067394"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Apache Ambari-gebruik in Azure HDInsight

HDInsight gebruikt Apache Ambari voor clusterimplementatie en -beheer. Ambari-agenten draaien op elk knooppunt (headnode, werkknooppunt, zookeeper en edgenode als ze bestaan). Ambari server draait alleen op headnode (hn0 of hn1). Er wordt slechts één exemplaar van de Ambari-server tegelijk uitgevoerd. Dit wordt geregeld door HDInsight failover controller. Wanneer een van de headnodes is uitgeschakeld voor opnieuw opstarten of onderhoud, wordt de andere headnode actief en wordt de Ambari-server op de tweede headnode gestart.

Alle clusterconfiguratie moet worden uitgevoerd via de [Ambari-gebruikersinterface,](./hdinsight-hadoop-manage-ambari.md)elke lokale wijziging wordt overschreven wanneer het knooppunt opnieuw wordt gestart.

## <a name="failover-controller-services"></a>Failovercontrollerservices

De HDInsight failover controller is ook verantwoordelijk voor het bijwerken van het IP-adres van headnode host, wat wijst op de huidige actieve hoofdnode. Alle Ambari-agents zijn geconfigureerd om de status en hartslag te rapporteren aan de host van headnode. De failovercontroller is een set services die op elk knooppunt in het cluster worden uitgevoerd, als deze niet wordt uitgevoerd, werkt de failover van de headnode mogelijk niet goed en komt u in de AANLOOP naar de HTTP 502-server.

Om te controleren welke headnode actief is, een manier is om ssh naar een van de knooppunten in het cluster, dan uitvoeren `ping headnodehost` en vergelijken van de IP met die van de twee headnodes.

Als failovercontrollerservices niet worden uitgevoerd, kan de failover van headnode niet correct plaatsvinden, waardoor de Ambari-server mogelijk niet wordt uitgevoerd. Voer het selectievakje uit om te controleren of failovercontrollerservices worden uitgevoerd:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Logboeken

Op de actieve headnode u de Ambari-serverlogboeken controleren op:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

Op elk knooppunt in het cluster u de ambari-agentlogboeken controleren op:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Startreeksen voor service

Dit is de volgorde van de service start bij het opstarten:

1. Hdinsight-agent start failover controller diensten.
1. Failovercontrollerservices starten Ambari-agent op elke node- en Ambari-server op actieve headnode.

## <a name="ambari-database"></a>Ambari-database

HDInsight maakt SQL Azure Database onder de motorkap om te dienen als de database voor Ambari-server. De [standaardservicelaag is S0](../sql-database/sql-database-elastic-pool-scale.md).

Voor elk cluster met het aantal werknemersdaten groter is dan 16 bij het maken van het cluster, is S2 de databaseservicelaag.

## <a name="takeaway-points"></a>Takeaway punten

Nooit handmatig start/stop ambari-server of ambari-agent services, tenzij u probeert om de service opnieuw op te starten om een probleem te omzeilen. Als u een failover wilt forceren, u de actieve headnode opnieuw opstarten.

Wijzig nooit handmatig configuratiebestanden op een clusterknooppunt, laat Ambari UI het werk voor u doen.

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met behulp van de Apache Ambari-webinterface](hdinsight-hadoop-manage-ambari.md)
* [HDInsight-clusters beheren met behulp van de Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
