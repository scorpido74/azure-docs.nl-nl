---
title: Kan Apache-garen logboek niet lezen in azure HDInsight
description: Probleemoplossings stappen en mogelijke oplossingen voor problemen bij interactie met Azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76776194"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Scenario: kan het Apache-garen logboek in azure HDInsight niet lezen

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De Apache-garens logboeken die zijn gevonden vanuit het opslag account zijn niet leesbaar voor mensen. De parser van het bestand werkt niet en levert het volgende fout bericht op:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Oorzaak

Het Apache-garen logboek wordt samengevoegd in `IndexFile` de indeling. dit wordt niet ondersteund door de bestands-parser.

## <a name="resolution"></a>Oplossing

1. Ga in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`, waarbij `CLUSTERNAME` de naam van het cluster is.

1. Ga vanuit de Ambari-gebruikers interface naar **garens** > **Configs** > **Geavanceerd** > **Geavanceerd garen-site**.

1. Voor WASB-opslag: de standaard waarde `yarn.log-aggregation.file-formats` voor `IndexedFormat,TFile`is. Wijzig de waarde in `TFile`.

1. Voor ADLS-opslag: de standaard waarde `yarn.nodemanager.log-aggregation.compression-type` voor `gz`is. Wijzig de waarde in `none`.

1. Sla de wijziging op en start alle betrokken services opnieuw op.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
