---
title: Watchdog BUG soft lockup CPU-fout van Azure HDInsight-cluster
description: Watchdog BUG soft lockup CPU verschijnt in kernel syslogs van Azure HDInsight cluster
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894110"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scenario: "watchdog: BUG: soft lockup - CPU" fout van een Azure HDInsight cluster

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De kernel syslogs bevatten `watchdog: BUG: soft lockup - CPU`de foutmelding: .

## <a name="cause"></a>Oorzaak

Een [bug](https://bugzilla.kernel.org/show_bug.cgi?id=199437) in Linux Kernel veroorzaakt CPU zachte lockups.

## <a name="resolution"></a>Oplossing

Kernel patch toepassen. Het script hieronder upgrades van de linux kernel en herstart de machines op verschillende tijdstippen meer dan 24 uur. Voer de scriptactie in twee batches uit. De eerste batch is op alle knooppunten, behalve hoofdknooppunt. De tweede batch is op hoofdknooppunt. Niet tegelijkertijd op hoofdknooppunt en andere knooppunten draaien.

1. Navigeer vanuit azure-portal naar uw HDInsight-cluster.

1. Ga naar scriptacties.

1. Selecteer **Nieuw verzenden** en voer de invoer als volgt in

    | Eigenschap | Waarde |
    | --- | --- |
    | Scripttype | -Aangepast |
    | Name |Oplossing voor probleem met de zachte vergrendeling van de kernel |
    | Bash script URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Knooppunttype(s) |Arbeider, Zookeeper |
    | Parameters |N.v.t. |

    Selecteer **Deze scriptactie blijven bestaan...** als u het script wilt uitvoeren wanneer nieuwe knooppunten worden toegevoegd.

1. Selecteer **Maken**.

1. Wacht tot de uitvoering slaagt.

1. Voer de scriptactie uit op Hoofdknooppunt door dezelfde stappen te volgen als stap 3, maar dit keer met knooppunttypen: Hoofd.

1. Wacht tot de uitvoering slaagt.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
