---
title: Apache ZooKeeper server kan geen quorum vormen in azure HDInsight
description: Apache ZooKeeper server kan geen quorum vormen in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 7091e638743fb8cd1488fe7e332378bf89304af1
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087072"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper server kan geen quorum vormen in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Apache ZooKeeper server niet in orde is, kunnen symptomen het volgende omvatten: zowel resource managers/naam knooppunten bevinden zich in de stand-bymodus, eenvoudige `zkFailoverController` HDFS-bewerkingen werken niet, is gestopt en kan niet worden gestart, garens/Spark/livy mislukken vanwege ZooKeeper fouten. Er wordt mogelijk een fout bericht met de volgende strekking weer gegeven:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

## <a name="cause"></a>Oorzaak

Wanneer het volume van de momentopname bestanden groot is of momentopname bestanden zijn beschadigd, kan de ZooKeeper-server geen quorum vormen, waardoor ZooKeeper gerelateerde services beschadigd raken. De ZooKeeper-server verwijdert geen oude momentopname bestanden uit de bijbehorende gegevens Directory, maar het is een periodieke taak die door gebruikers moet worden uitgevoerd om de healthiness van ZooKeeper te onderhouden. Zie [ZooKeeper-sterke punten en beperkingen](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations)voor meer informatie.

## <a name="resolution"></a>Oplossing

Controleer de ZooKeeper- `/hadoop/zookeeper/version-2` gegevens `/hadoop/hdinsight-zookeepe/version-2` Directory en zoek uit of de bestands grootte van de moment opnamen groot is. Voer de volgende stappen uit als er grote moment opnamen bestaan:

1. Back-ups maken van `/hadoop/zookeeper/version-2` moment `/hadoop/hdinsight-zookeepe/version-2`opnamen in en.

1. Moment opnamen opschonen `/hadoop/zookeeper/version-2` in `/hadoop/hdinsight-zookeepe/version-2`en.

1. Start alle ZooKeeper-servers opnieuw op in Apache Ambari-gebruikers interface.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

- Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

- Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
