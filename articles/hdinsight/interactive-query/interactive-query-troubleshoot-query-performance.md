---
title: Slechte prestaties in Apache Hive LLAP-query's in Azure HDInsight
description: Query's in Apache Hive LLAP worden trager uitgevoerd dan verwacht in Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895128"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scenario: Slechte prestaties in Apache Hive LLAP-query's in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactive query-componenten in Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De standaardclusterconfiguraties zijn onvoldoende afgestemd op uw werkbelasting. Query's in Hive LLAP worden trager uitgevoerd dan verwacht.

## <a name="cause"></a>Oorzaak

Dit kan gebeuren als gevolg van een verscheidenheid van redenen.

## <a name="resolution"></a>Oplossing

LLAP is geoptimaliseerd voor query's waarbij joins en aggregaten betrokken zijn. Query's zoals de volgende presteren niet goed in een cluster van Interactive Hive:

```
select * from table where column = "columnvalue"
```

Als u de prestaties van puntquery's in Hive LLAP wilt verbeteren, stelt u de volgende configuraties in:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

U ook het gebruik van de LLAP-cache verhogen om de prestaties te verbeteren met de volgende configuratiewijziging:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
