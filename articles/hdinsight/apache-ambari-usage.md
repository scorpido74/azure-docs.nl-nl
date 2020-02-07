---
title: Apache Ambari-gebruik in azure HDInsight
description: Bespreking van de manier waarop Apache Ambari wordt gebruikt in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067394"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Apache Ambari-gebruik in azure HDInsight

HDInsight gebruikt Apache Ambari voor cluster implementatie en-beheer. Ambari-agents worden uitgevoerd op elk knoop punt (hoofd knooppunt, Worker node, Zookeeper en de, indien aanwezig). De Ambari-server wordt alleen uitgevoerd op hoofd knooppunt (hn0 of HN1). Er mag slechts één exemplaar van de Ambari-server tegelijk worden uitgevoerd. Dit wordt bepaald door de HDInsight-failover-controller. Wanneer een van de hoofd knooppunten niet beschikbaar is voor opnieuw opstarten of onderhoud, wordt de andere hoofd knooppunt actief en wordt de Ambari-server op de tweede hoofd knooppunt gestart.

Alle cluster configuraties moeten worden uitgevoerd via de [Ambari-gebruikers interface](./hdinsight-hadoop-manage-ambari.md). alle lokale wijzigingen worden overschreven wanneer het knoop punt opnieuw wordt opgestart.

## <a name="failover-controller-services"></a>Failover-controller Services

De HDInsight-failover-controller is ook verantwoordelijk voor het bijwerken van het IP-adres van de hoofd knooppunt-host, die verwijst naar het huidige actieve hoofd knooppunt. Alle Ambari-agents zijn geconfigureerd om de status en heartbeat te rapporteren aan de hoofd knooppunt-host. De failover-controller is een set services die worden uitgevoerd op elk knoop punt in het cluster. als deze niet worden uitgevoerd, werkt de failover van hoofd knooppunt mogelijk niet naar behoren en wordt er een fout opgetreden met HTTP 502 bij het openen van de Ambari-server.

Als u wilt controleren welke hoofd knooppunt actief is, kunt u een van de knoop punten in het cluster SSHen en vervolgens `ping headnodehost` uitvoeren en het IP-adres van de twee hoofd knooppunten vergelijken.

Als failover controller-Services niet worden uitgevoerd, wordt de hoofd knooppunt-failover mogelijk niet correct uitgevoerd. Dit kan ertoe leiden dat de Ambari-server niet actief is. Als u wilt controleren of de failover-controller services worden uitgevoerd, voert u de volgende handelingen uit:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Logboeken

Op de actieve hoofd knooppunt kunt u de logboeken van de Ambari-server controleren op:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

Op elk knoop punt in het cluster kunt u de logboeken van de Ambari-agent controleren op:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Start reeksen van service

Dit is de volg orde waarin de service wordt gestart bij het opstarten:

1. Hdinsight-agent start failover controller-Services.
1. Failover controller-Services starten Ambari-agent op elk knoop punt en Ambari-server op actieve hoofd knooppunt.

## <a name="ambari-database"></a>Ambari-data base

HDInsight maakt SQL Azure data base aan de achtergrond die als de Data Base voor Ambari-server fungeert. De [standaardlaag van de service is S0](../sql-database/sql-database-elastic-pool-scale.md).

Voor een cluster met een aantal worker-knoop punten dat groter is dan 16 tijdens het maken van het cluster, is S2 de data base-servicelaag.

## <a name="takeaway-points"></a>Maakt punten

Ambari nooit hand matig starten/stoppen, tenzij u de service opnieuw probeert te starten om een probleem te omzeilen. Als u een failover wilt forceren, kunt u de actieve hoofd knooppunt opnieuw opstarten.

Nooit hand matig configuratie bestanden wijzigen op een cluster knooppunt, laat Ambari gebruikers interface de taak voor u doen.

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met behulp van de Apache Ambari-webinterface](hdinsight-hadoop-manage-ambari.md)
* [HDInsight-clusters beheren met behulp van de Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
