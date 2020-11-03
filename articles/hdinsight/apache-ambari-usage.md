---
title: Apache Ambari-gebruik in azure HDInsight
description: Bespreking van de manier waarop Apache Ambari wordt gebruikt in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: cfb83c4ec9972fda3813d414583bc73edeef3229
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285974"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Apache Ambari-gebruik in azure HDInsight

HDInsight gebruikt Apache Ambari voor cluster implementatie en-beheer. Ambari-agents worden uitgevoerd op elk knoop punt (hoofd knooppunt, Worker node, Zookeeper en de, indien aanwezig). De Ambari-server wordt alleen uitgevoerd op hoofd knooppunt (hn0 of HN1). Er mag slechts één exemplaar van de Ambari-server tegelijk worden uitgevoerd. Dit wordt bepaald door de HDInsight-failover-controller. Wanneer een van de hoofd knooppunten niet beschikbaar is voor opnieuw opstarten of onderhoud, wordt de andere hoofd knooppunt actief en wordt de Ambari-server op de tweede hoofd knooppunt gestart.

Alle cluster configuraties moeten worden uitgevoerd via de [Ambari-gebruikers interface](./hdinsight-hadoop-manage-ambari.md). alle lokale wijzigingen worden overschreven wanneer het knoop punt opnieuw wordt opgestart.

## <a name="failover-controller-services"></a>Failover-controller Services

De HDInsight-failover-controller is ook verantwoordelijk voor het bijwerken van het IP-adres van de hoofd knooppunt-host, die verwijst naar het huidige actieve hoofd knooppunt. Alle Ambari-agents zijn geconfigureerd om de status en heartbeat te rapporteren aan de hoofd knooppunt-host. De failover-controller is een set services die worden uitgevoerd op elk knoop punt in het cluster. als deze niet worden uitgevoerd, werkt de failover van hoofd knooppunt mogelijk niet naar behoren en wordt er een fout opgetreden met HTTP 502 bij het openen van de Ambari-server.

Als u wilt controleren welke hoofd knooppunt actief is, kunt u een van de knoop punten in het cluster sshiseren en vervolgens `ping headnodehost` het IP-adres uitvoeren en vergelijken met dat van de twee hoofd knooppunten.

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

HDInsight maakt een data base in SQL Database onder de motorkap die als de Data Base voor Ambari-server fungeert. De [standaardlaag van de service is S0](../azure-sql/database/elastic-pool-scale.md).

Voor een cluster met een aantal worker-knoop punten dat groter is dan 16 tijdens het maken van het cluster, is S2 de data base-servicelaag.

## <a name="takeaway-points"></a>Maakt punten

Ambari nooit hand matig starten/stoppen, tenzij u de service opnieuw probeert te starten om een probleem te omzeilen. Als u een failover wilt forceren, kunt u de actieve hoofd knooppunt opnieuw opstarten.

Nooit hand matig configuratie bestanden wijzigen op een cluster knooppunt, laat Ambari gebruikers interface de taak voor u doen.

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met behulp van de Apache Ambari-webinterface](hdinsight-hadoop-manage-ambari.md)
* [HDInsight-clusters beheren met behulp van de Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]