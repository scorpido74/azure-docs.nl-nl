---
title: Apache ZooKeeper-server vormt geen quorum in Azure HDInsight
description: Apache ZooKeeper-server vormt geen quorum in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 4e46efaf17ae9bad5df6f1f61f401d3e6de58a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250240"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper-server vormt geen quorum in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Apache ZooKeeper server is ongezond, symptomen kunnen zijn: beide Resource Managers / Naam Knooppunten zijn `zkFailoverController` in stand-by-modus, eenvoudige HDFS operaties werken niet, wordt gestopt en kan niet worden gestart, Garen / Spark / Livy banen mislukken als gevolg van Zookeeper fouten. LLAP Daemons kunnen ook niet starten op Secure Spark- of Interactive Hive-clusters. Mogelijk ziet u een foutbericht dat vergelijkbaar is met:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

In de Zookeeper Server logs op een Zookeeper host op / var /\*log / zookeeper / zookeeper-zookeeper-server- .out, u ook de volgende fout te zien:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Oorzaak

Wanneer het volume van momentopnamebestanden groot is of momentopnamebestanden beschadigd zijn, vormt de ZooKeeper-server geen quorum, waardoor ZooKeeper-gerelateerde services niet in orde zijn. ZooKeeper-server verwijdert geen oude momentopnamebestanden uit de gegevensmap, in plaats daarvan is het een periodieke taak die gebruikers moeten uitvoeren om de gezondheid van ZooKeeper te behouden. Zie [ZooKeeper Sterke punten en beperkingen voor](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations)meer informatie.

## <a name="resolution"></a>Oplossing

Controleer zookeeper `/hadoop/zookeeper/version-2` gegevensdirectory en `/hadoop/hdinsight-zookeeper/version-2` om erachter te komen of de snapshots bestandsgrootte is groot. Neem de volgende stappen als er grote momentopnamen bestaan:

1. Back-upmomentopnamen `/hadoop/zookeeper/version-2` `/hadoop/hdinsight-zookeeper/version-2`in en .

1. Maak snapshots `/hadoop/zookeeper/version-2` schoon `/hadoop/hdinsight-zookeeper/version-2`in en.

1. Start alle ZooKeeper-servers van Apache Ambari UI opnieuw.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

- Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

- Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
