---
title: Fout bericht niet weer gegeven in Apache Hive weer gave-Azure HDInsight
description: De query mislukt in Apache Hive weergave zonder enige details van Azure HDInsight-cluster.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 38dd064fe8365e6661ce7ea7e1077a4e1e32dbf5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494260"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Scenario: query-fout bericht wordt niet weer gegeven in Apache Hive weer gave in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactieve query onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Het Apache Hive weergave query fout bericht ziet er ongeveer als volgt uit, zonder verdere informatie:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Oorzaak

Soms is het fout bericht van een query fout te groot om te worden weer gegeven op de hoofd pagina van de Hive-weer gave.

## <a name="resolution"></a>Resolutie

Ga naar het tabblad meldingen in de rechter bovenhoek van de Hive_view om de volledige stacktrace en het fout bericht te zien.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Zie [Een ondersteuningsaanvraag maken voor Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) voor meer informatie. Toegang tot abonnementsbeheer en factuurbeheer is in uw Microsoft Azure-abonnement inbegrepen, en technische ondersteuning wordt verstrekt via een van de [Azure-ondersteuningsplannen](https://azure.microsoft.com/support/plans/).
