---
title: Getraceerde CPU in Apache HBase-cluster-Azure HDInsight
description: Problemen met vastgelegde CPU op regio server in Apache HBase-cluster in azure HDInsight oplossen
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 845307f24495090891812b4e945e202cdad47e71
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468331"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Scenario: CPU op regio server in Apache HBase-cluster in azure HDInsight getraceerd

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Het Server proces Apache HBase Region begint met het behalen van een CPU van 200%, waardoor waarschuwingen worden gestart op HBase Master proces en het cluster niet werken met volledige capaciteit.

## <a name="cause"></a>Oorzaak

Als u HBase cluster v 3.4 uitvoert, hebt u mogelijk een mogelijke fout veroorzaakt door een upgrade van JDK naar versie 1.7.0 _151. Het symptoom dat wordt weer gegeven, is een regio Server proces dat begint met een CPU van 200% (om de `top` opdracht uit te voeren. als er een proces is dat dicht bij 200% CPU bevindt, wordt de PID bereikt en wordt bevestigd dat de regio Server proces wordt uitgevoerd door `ps -aux | grep`).

## <a name="resolution"></a>Resolutie

1. Installeer JDK 1,8 op alle knoop punten van het cluster, zoals hieronder wordt beschreven:

    * Voer de script actie uit `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`. Zorg ervoor dat u de optie selecteert die op alle knoop punten moet worden uitgevoerd.

    * U kunt zich ook aanmelden bij elk afzonderlijk knoop punt en de opdracht `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`uitvoeren.

1. Ga naar de Ambari-gebruikers interface-`https://<clusterdnsname>.azurehdinsight.net`.

1. Navigeer naar **HBase->-configuraties-> Advanced-> advanced** `hbase-env configs` en wijzig de variabele `JAVA_HOME` in `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`. Sla de configuratie wijziging op.

1. [Optioneel, maar aanbevolen] [Alle tabellen op het cluster leegmaken](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

1. Start alle HBase-services die opnieuw moeten worden opgestart opnieuw vanuit de gebruikers interface van Ambari.

1. Afhankelijk van de gegevens op het cluster kan het enkele minuten duren voordat het cluster een stabiele status heeft bereikt. De manier waarop u bevestigt dat het cluster stabiel is, is door een HMaster-gebruikers interface te controleren (alle regio servers moeten actief zijn) van Ambari (vernieuwen) of van hoofd knooppunt HBase shell uit te voeren en vervolgens de status opdracht uit te voeren.

Als u wilt controleren of de upgrade is voltooid, controleert u of de relevante HBase-processen zijn gestart met behulp van de juiste Java-versie, bijvoorbeeld voor de regio server-controle als:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Zie [Een ondersteuningsaanvraag maken voor Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) voor meer informatie. Toegang tot abonnementsbeheer en factuurbeheer is in uw Microsoft Azure-abonnement inbegrepen, en technische ondersteuning wordt verstrekt via een van de [Azure-ondersteuningsplannen](https://azure.microsoft.com/support/plans/).
