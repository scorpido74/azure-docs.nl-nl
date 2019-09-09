---
title: CPU op regio server in Apache HBase-cluster vastgelegd in azure HDInsight
description: Problemen met vastgelegde CPU op regio server in Apache HBase-cluster in azure HDInsight oplossen
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: b1fba84e7841713b005e387bcd806344f6a9e7a1
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810791"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Scenario: CPU op regio server in Apache HBase-cluster vastgelegd in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Het Server proces Apache HBase Region begint met het behalen van een CPU van 200%, waardoor waarschuwingen worden gestart op HBase Master proces en het cluster niet werken met volledige capaciteit.

## <a name="cause"></a>Oorzaak

Als u HBase cluster v 3.4 uitvoert, hebt u mogelijk een mogelijke fout veroorzaakt door een upgrade van JDK naar versie 1.7.0 _151. Het symptoom dat wordt weer gegeven, is een regio Server proces dat begint met een CPU van 200% (om `top` de opdracht uit te voeren. als er een proces is dat dicht bij 200% CPU bevindt, wordt de PID bereikt en `ps -aux | grep` wordt gecontroleerd of het Server proces regio wordt uitgevoerd).

## <a name="resolution"></a>Oplossing

1. Installeer JDK 1,8 op alle knoop punten van het cluster, zoals hieronder wordt beschreven:

    * Voer de script actie `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`uit. Zorg ervoor dat u de optie selecteert die op alle knoop punten moet worden uitgevoerd.

    * U kunt zich ook aanmelden bij elk afzonderlijk knoop punt en de opdracht `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`uitvoeren.

1. Ga naar Ambari-gebruikers `https://<clusterdnsname>.azurehdinsight.net`interface-.

1. Navigeer naar **HBase->-configuraties-> Geavanceerd-> Geavanceerd** `hbase-env configs` en wijzig de variabele `JAVA_HOME` in `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`. Sla de configuratie wijziging op.

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

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
