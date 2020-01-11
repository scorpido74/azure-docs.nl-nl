---
title: Lockup CPU-fout met watchdog-fouten van Azure HDInsight-cluster
description: Er wordt een tijdelijke Lockup-CPU voor een watchdog-fout weer gegeven in kernel syslogs van Azure HDInsight-cluster
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894110"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scenario: fout ' watchdog: BUG: zacht Lockup-CPU ' van een Azure HDInsight-cluster

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De kernel-syslogs bevatten het volgende fout bericht: `watchdog: BUG: soft lockup - CPU`.

## <a name="cause"></a>Oorzaak

Een [fout](https://bugzilla.kernel.org/show_bug.cgi?id=199437) in de Linux-kernel veroorzaakt CPU-Soft-Lockups.

## <a name="resolution"></a>Resolutie

Kernel-patch Toep assen. In het onderstaande script wordt de Linux-kernel bijgewerkt en worden de machines op verschillende tijdstippen in 24 uur opnieuw opgestart. Voer de script actie uit in twee batches. De eerste batch bevindt zich op alle knoop punten, behalve het hoofd knooppunt. De tweede batch bevindt zich op het hoofd knooppunt. Voer niet op het hoofd knooppunt en andere knoop punten tegelijk uit.

1. Ga vanuit Azure Portal naar uw HDInsight-cluster.

1. Ga naar script acties.

1. Selecteer **nieuwe verzenden** en voer de invoer als volgt in

    | Eigenschap | Waarde |
    | --- | --- |
    | Script type | -Aangepast |
    | Name |Oplossing voor een probleem met de kernel-zachte vergrendeling |
    | Bash-script-URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Knooppunt type (n) |Werk nemer, Zookeeper |
    | Parameters |N/A |

    Selecteer **Deze script actie persistent maken...** als u het script wilt uitvoeren wanneer er nieuwe knoop punten worden toegevoegd.

1. Selecteer **Maken**.

1. Wacht totdat de uitvoering is geslaagd.

1. Voer de script actie uit op het hoofd knooppunt door dezelfde stappen te volgen als stap 3, maar deze keer met knooppunt typen: Head.

1. Wacht totdat de uitvoering is geslaagd.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
