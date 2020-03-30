---
title: Log in Azure HDInsight niet te lezen
description: Problemen oplossen met stappen en mogelijke oplossingen voor problemen bij interactie met Azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776194"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Scenario: Kan apache garenlogboek niet lezen in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De Apache Garen logs gevonden van opslag account is niet menselijk leesbaar. De bestandsparser werkt niet en produceert het volgende foutbericht:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Oorzaak

Het apachegarenlogboek wordt `IndexFile` samengevoegd tot opmaak, die niet wordt ondersteund door de sprolietener.

## <a name="resolution"></a>Oplossing

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net`naar `CLUSTERNAME` , waar is de naam van uw cluster.

1. Navigeer vanaf ambari-gebruikersinterface naar **YARN** > **Configs** > **Advanced** > Advanced**yarn-site.**

1. Voor WASB-opslag: de `yarn.log-aggregation.file-formats` `IndexedFormat,TFile`standaardwaarde voor is . De waarde `TFile`wijzigen in .

1. Voor ADLS-opslag: de `yarn.nodemanager.log-aggregation.compression-type` `gz`standaardwaarde voor is . De waarde `none`wijzigen in .

1. Sla de wijziging op en start alle betrokken services opnieuw.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
