---
title: Apache Ambari-gebruikers interface toont hosts en services in azure HDInsight
description: Problemen met een Apache Ambari-UI oplossen wanneer de hosts en services in azure HDInsight worden weer gegeven
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895640"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scenario: Apache Ambari-gebruikers interface toont hosts en services in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Apache Ambari-gebruikers interface is toegankelijk, maar in de gebruikers interface worden bijna alle services weer gegeven, worden alle hosts met heartbeat verloren gegaan.

## <a name="cause"></a>Oorzaak

In de meeste gevallen is dit een probleem met Ambari-server die niet wordt uitgevoerd op de actieve hoofd knooppunt. Controleer welk hoofd knooppunt de actieve hoofd knooppunt is en zorg ervoor dat uw ambari-server op de juiste wordt uitgevoerd. Start ambari-server niet hand matig, laat de failover controller-service verantwoordelijk voor het starten van ambari-server op de juiste hoofd knooppunt. Start de actieve hoofd knooppunt opnieuw op om een failover af te dwingen.

Dit probleem kan ook worden veroorzaakt door netwerk problemen. Ga vanuit elk cluster knooppunt naar of u kunt pingen `headnodehost`. Er is een zeldzame situatie waarbij er geen verbinding kan worden gemaakt `headnodehost`met een cluster knooppunt:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Oplossing

Normaal gesp roken wordt de actieve hoofd knooppunt door het opnieuw opstarten van dit probleem verminderd. Neem contact op met het ondersteunings team van HDInsight

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
