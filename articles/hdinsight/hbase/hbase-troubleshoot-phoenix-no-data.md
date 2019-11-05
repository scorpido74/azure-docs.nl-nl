---
title: HDP-upgrade & geen gegevens in Apache Phoenix weergaven in azure HDInsight
description: HDP-upgrade veroorzaakt geen gegevens in Apache Phoenix weergaven in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: 148c7ea4bb3eef3fff7c1a8fd3b865791613704f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498037"
---
# <a name="scenario-hdp-upgrade-causes-no-data-in-apache-phoenix-views-in-azure-hdinsight"></a>Scenario: de upgrade van HDP veroorzaakt geen gegevens in Apache Phoenix weergaven in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Een Apache Phoenix weergave bevat geen datum na een upgrade van HDP 2,4 naar HDP 2,5.

## <a name="cause"></a>Oorzaak

De index tabel voor weer gaven (alle indexen voor weer gave zijn opgeslagen in één fysieke Apache HBase-tabel) wordt tijdens de upgrade afgekapt

## <a name="resolution"></a>Resolutie

Verwijder alle weergave indexen en maak deze opnieuw nadat de upgrade is uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie. Toegang tot abonnementsbeheer en factuurbeheer is in uw Microsoft Azure-abonnement inbegrepen, en technische ondersteuning wordt verstrekt via een van de [Azure-ondersteuningsplannen](https://azure.microsoft.com/support/plans/).
