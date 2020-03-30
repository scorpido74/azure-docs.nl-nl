---
title: Apache Ambari UI toont hosts en services in Azure HDInsight
description: Problemen met een Apache Ambari-gebruikersinterfaceoplossen wanneer hosts en services in Azure HDInsight worden weergegeven
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895640"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scenario: Apache Ambari UI toont down hosts and services in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Apache Ambari UI is toegankelijk, maar de gebruikersinterface toont bijna alle services zijn uitgeschakeld, alle hosts met hartslag verloren.

## <a name="cause"></a>Oorzaak

In de meeste scenario's is dit een probleem met de Ambari-server die niet op de actieve headnode wordt uitgevoerd. Controleer welke headnode de actieve headnode is en zorg ervoor dat uw ambari-server op de juiste server draait. Start de ambari-server niet handmatig, laat failovercontrollerservice verantwoordelijk zijn voor het starten van ambari-server op de juiste headnode. Start de actieve headnode opnieuw op om een failover te forceren.

Netwerkproblemen kunnen dit probleem ook veroorzaken. Kijk in elk clusterknooppunt of `headnodehost`u pingen. Er is een zeldzame situatie waarin geen `headnodehost`clusterknooppunt verbinding kan maken met:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Oplossing

Meestal opnieuw opstarten van de actieve headnode zal dit probleem te verzachten. Zo niet neem dan contact op met het hdInsight support team.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
