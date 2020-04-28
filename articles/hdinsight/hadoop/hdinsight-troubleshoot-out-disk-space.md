---
title: Er is onvoldoende schijf ruimte voor het cluster knooppunt in azure HDInsight
description: Problemen met de schijf ruimte van Apache Hadoop cluster knooppunten in azure HDInsight oplossen.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: fbfd82473b68f5032d19834ac809191d498a5a67
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894134"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scenario: er is onvoldoende schijf ruimte beschikbaar voor het cluster knooppunt in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Een taak kan mislukken met een fout bericht van de volgende strekking:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Of u ontvangt een Apache Ambari-waarschuwing die vergelijkbaar `local-dirs usable space is below configured utilization percentage`is met:.

## <a name="cause"></a>Oorzaak

Apache garen-toepassings cache heeft mogelijk alle beschik bare schijf ruimte verbruikt. Uw Spark-toepassing wordt waarschijnlijk niet efficiënt uitgevoerd.

## <a name="resolution"></a>Oplossing

1. Gebruik de Ambari-gebruikers interface om te bepalen welk knoop punt bijna geen schijf ruimte meer heeft.

1. Bepaal welke map in het knoop punt zorgwekkend de meeste schijf ruimte draagt. SSH eerst naar het knoop punt en voer `df` vervolgens uit om het schijf gebruik voor alle koppels weer te geven. Meestal is `/mnt` dit een tijdelijke schijf die wordt gebruikt door OSS. U kunt een map invoeren en vervolgens typen `sudo du -hs` om de samenvatte bestands grootten in een map weer te geven. Als er een map met de volgende `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`strekking wordt weer gegeven, betekent dit dat de toepassing nog steeds wordt uitgevoerd. Dit kan het gevolg zijn van RDD-persistentie of tussenliggende bestanden met een wille keurige volg orde.

1. Als u het probleem wilt verhelpen, moet u de toepassing afbreken, waardoor de schijf ruimte die door de toepassing wordt gebruikt, wordt vrijgegeven.

1. Optimaliseer uw toepassing om het probleem uiteindelijk op te lossen.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
