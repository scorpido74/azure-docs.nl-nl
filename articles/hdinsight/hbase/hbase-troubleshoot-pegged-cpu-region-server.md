---
title: Gekoppelde CPU in Apache HBase-cluster - Azure HDInsight
description: Problemen met gekoppelde CPU op regioserver in Apache HBase-cluster in Azure HDInsight oplossen
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887305"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Scenario: Gekoppelde CPU op regioserver in Apache HBase-cluster in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Apache HBase-regioserverproces begint bijna 200% CPU in beslag te nemen, waardoor waarschuwingen worden gestart op het HBase Master-proces en het cluster niet op volledige capaciteit functioneren.

## <a name="cause"></a>Oorzaak

Als u HBase-cluster v3.4 uitvoert, bent u mogelijk getroffen door een potentiële bug die is veroorzaakt door een upgrade van jdk naar versie 1.7.0_151. Het symptoom dat we zien is regio server proces begint bezetten dicht `top` bij 200% CPU (om te controleren of dit de opdracht uit te `ps -aux | grep` voeren, als er een proces bezetten bijna 200% CPU krijgen zijn pid en bevestigen dat het regio server proces door het uitvoeren van).

## <a name="resolution"></a>Oplossing

1. Installeer jdk 1.8 op alle knooppunten van het cluster zoals hieronder:

    * Voer de `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`scriptactie uit . Zorg ervoor dat u de optie selecteert die op alle knooppunten moet worden uitgevoerd.

    * U zich ook aanmelden bij elk afzonderlijk `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`knooppunt en de opdracht uitvoeren.

1. Ga naar Ambari `https://<clusterdnsname>.azurehdinsight.net`UI - .

1. Navigeer naar **HBase->Configs->Advanced->** `hbase-env configs` `JAVA_HOME` Advanced `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`en wijzig de variabele in . Sla de config verandering.

1. [Optioneel maar aanbevolen] [Alle tabellen op het cluster doorspoelen](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

1. Start vanaf Ambari UI opnieuw alle HBase-services die opnieuw moeten worden opgestart.

1. Afhankelijk van de gegevens op het cluster kan het enkele minuten tot maximaal een uur duren voordat het cluster een stabiele status bereikt. De manier waarop u bevestigt dat het cluster de stabiele status bereikt, is door HMaster UI (alle regioservers moeten actief zijn) te controleren vanuit Ambari (refresh) of vanaf headnode de HBase-shell uit te voeren en vervolgens de statusopdracht uit te voeren.

Als u wilt controleren of uw upgrade is geslaagd, controleert u of de relevante HBase-processen zijn gestart met de juiste java-versie, bijvoorbeeld voor regioservercontrole als:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
