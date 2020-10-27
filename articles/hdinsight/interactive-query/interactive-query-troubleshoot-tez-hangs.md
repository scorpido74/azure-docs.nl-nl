---
title: Apache TEZ-toepassing is vastgelopen in azure HDInsight
description: Apache TEZ-toepassing is vastgelopen in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 4e4d1ec77d989fd354e8b3938a052be94ff4ba34
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539629"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>Scenario: Apache TEZ toepassing loopt vast in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Na het verzenden van Apache Hive taak, van de TEZ weer geven, is de taak status actief, maar lijkt het niet om een voortgang te maken

## <a name="cause"></a>Oorzaak

Er zijn te veel taken verzonden; lange garen wachtrij.

## <a name="resolution"></a>Oplossing

Het cluster omhoog schalen of alleen wachten tot de garen wachtrij is leeg gemaakt.

Standaard `yarn.scheduler.capacity.maximum-applications` bepaalt het maximum aantal toepassingen dat wordt uitgevoerd of in behandeling is en standaard ingesteld op `10000` .

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).