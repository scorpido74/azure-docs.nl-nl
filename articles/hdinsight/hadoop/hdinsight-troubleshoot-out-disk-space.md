---
title: Clusterknooppunt heeft geen schijfruimte meer in Azure HDInsight
description: Problemen met de problemen met de schijfruimte van Apache Hadoop-clusterclients in Azure HDInsight oplossen.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: fbfd82473b68f5032d19834ac809191d498a5a67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894134"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scenario: Clusterknooppunt heeft geen schijfruimte meer in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Een taak kan mislukken met een foutmelding die vergelijkbaar is met:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Of u ontvangt Apache Ambari `local-dirs usable space is below configured utilization percentage`alert vergelijkbaar met: .

## <a name="cause"></a>Oorzaak

De cache van apache garentoepassingen heeft mogelijk alle beschikbare schijfruimte verbruikt. Uw Spark-toepassing wordt waarschijnlijk inefficiënt uitgevoerd.

## <a name="resolution"></a>Oplossing

1. Gebruik de Ambari-gebruikersinterface om te bepalen welk knooppunt geen schijfruimte meer heeft.

1. Bepaal welke map in het verontrustende knooppunt bijdraagt aan het grootste deel van de schijfruimte. SSH naar het knooppunt eerst, dan uitvoeren `df` om schijfgebruik lijst voor alle mounts. Meestal is `/mnt` het dat is een temp schijf gebruikt door OSS. U een map invoeren `sudo du -hs` en vervolgens typen om samengevatte bestandsformaten onder een map weer te geven. Als u een map `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`ziet die vergelijkbaar is met , betekent dit dat de toepassing nog steeds wordt uitgevoerd. Dit kan te wijten zijn aan RDD-persistentie- of tussentijdse shuffle-bestanden.

1. Als u het probleem wilt beperken, doodt u de toepassing, waardoor schijfruimte die door die toepassing wordt gebruikt, wordt vrijgegeven.

1. Om het probleem uiteindelijk op te lossen, optimaliseert u uw toepassing.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
