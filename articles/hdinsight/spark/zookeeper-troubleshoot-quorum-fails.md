---
title: Apache ZooKeeper server kan geen quorum vormen in azure HDInsight
description: Apache ZooKeeper server kan geen quorum vormen in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 41ac109e5c5379e6085dd57a3fcd8119915558fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133278"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper server kan geen quorum vormen in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Apache ZooKeeper server niet in orde is, kunnen symptomen het volgende omvatten: zowel resource managers/naam knooppunten bevinden zich in de stand-bymodus, eenvoudige `zkFailoverController` HDFS-bewerkingen werken niet, is gestopt en kan niet worden gestart, garens/Spark/livy mislukken vanwege ZooKeeper fouten. LLAP-daemons kunnen ook niet worden gestart op beveiligde Spark-of interactieve Hive-clusters. Er wordt mogelijk een fout bericht met de volgende strekking weer gegeven:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

In de Zookeeper-server logboeken op een Zookeeper-\*host op/var/log/Zookeeper/Zookeeper-Zookeeper-server-. out ziet u mogelijk ook de volgende fout:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Oorzaak

Wanneer het volume van de momentopname bestanden groot is of momentopname bestanden zijn beschadigd, kan de ZooKeeper-server geen quorum vormen, waardoor ZooKeeper gerelateerde services beschadigd raken. De ZooKeeper-server verwijdert geen oude momentopname bestanden uit de bijbehorende gegevens Directory, maar het is een periodieke taak die door gebruikers moet worden uitgevoerd om de healthiness van ZooKeeper te onderhouden. Zie [ZooKeeper-sterke punten en beperkingen](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations)voor meer informatie.

## <a name="resolution"></a>Oplossing

Controleer de ZooKeeper- `/hadoop/zookeeper/version-2` gegevens `/hadoop/hdinsight-zookeeper/version-2` Directory en zoek uit of de bestands grootte van de moment opnamen groot is. Voer de volgende stappen uit als er grote moment opnamen bestaan:

1. Controleer de status van andere ZooKeeper-servers in hetzelfde quorum om er zeker van te zijn dat ze goed werken met`echo stat | nc {zk_host_ip} 2181 (or 2182)`de opdracht.  

1. Meld de problematische ZooKeeper-host, back-upmomentopnamen `/hadoop/zookeeper/version-2` en `/hadoop/hdinsight-zookeeper/version-2`transactie logboeken aan en en opruim deze bestanden in de twee directory's. 

1. Start de problematische ZooKeeper-server in Ambari of de ZooKeeper-host opnieuw. Start de service met problemen opnieuw op.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

- Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

- Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
