---
title: Apache Spark traag wanneer Azure HDInsight-opslag veel bestanden heeft
description: Apache Spark-taak wordt langzaam uitgevoerd wanneer de Azure-opslagcontainer veel bestanden in Azure HDInsight bevat
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: e389c05a6de85287bc86eff510e137f470837e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894331"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Apache Spark-taken worden langzaam uitgevoerd wanneer de Azure-opslagcontainer veel bestanden bevat in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-componenten in Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Wanneer u een HDInsight-cluster uitvoert, wordt de Apache Spark-taak die naar Azure-opslagcontainer wordt geschreven, traag wanneer er veel bestanden/submappen zijn. Bijvoorbeeld, het duurt 20 seconden bij het schrijven naar een nieuwe container, maar ongeveer 2 minuten bij het schrijven naar een container die 200k bestanden heeft.

## <a name="cause"></a>Oorzaak

Dit is een bekend Spark-probleem. De traagheid komt `ListBlob` van `GetBlobProperties` de en operaties tijdens Spark taakuitvoering.

Om partities bij te houden, moet Spark een `FileStatusCache` informatie over directorystructuur onderhouden. Met deze cache kan Spark de paden ontken en zich bewust zijn van beschikbare partities. Het voordeel van het bijhouden van partities is dat Spark alleen de benodigde bestanden raakt wanneer u gegevens leest. Om deze informatie up-to-date te houden, moet Spark bij het schrijven van nieuwe gegevens alle bestanden onder de map vermelden en deze cache bijwerken.

In Spark 2.1, terwijl we de cache niet na elke schrijven hoeven bij te werken, controleert Spark of een bestaande partitiekolom overeenkomt met de voorgestelde kolom in de huidige schrijfaanvraag, dus het zal ook leiden tot aanbiedingsbewerkingen aan het begin van elke schrijfbewerking.

In Spark 2.2 moet dit prestatieprobleem worden opgelost bij het schrijven van gegevens met de toewetmodus.

## <a name="resolution"></a>Oplossing

Wanneer u een partitiegegevensset maakt, is het belangrijk om een partitieschema te gebruiken dat het `FileStatusCache`aantal bestanden beperkt dat spark moet aanbieden om de .

Voor elke Nth-microbatch waarbij N % 100 == 0 (100 slechts een voorbeeld is), verplaatst u bestaande gegevens naar een andere map, die door Spark kan worden geladen.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
