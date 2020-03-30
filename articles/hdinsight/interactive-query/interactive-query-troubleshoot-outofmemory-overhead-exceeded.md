---
title: Deelnemen aan Apache Hive leidt tot OutOfMemory-fout - Azure HDInsight
description: Omgaan met OutOfMemory-fouten "GC overheadlimiet overschreden fout"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ab334dfb15044fd0734a107c12003ca2c1f86906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895173"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Scenario: Deelnemen aan Apache Hive leidt tot een OutOfMemory-fout in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactive query-componenten in Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Het standaardgedrag voor Apache Hive joins is om de volledige inhoud van een tabel in het geheugen te laden, zodat een join kan worden uitgevoerd zonder dat u een map/reduce-stap hoeft uit te voeren. Als de Hive-tabel te groot is om in het geheugen te passen, kan de query mislukken.

## <a name="cause"></a>Oorzaak

Bij het uitvoeren van joins in bijenkorf van voldoende grootte, wordt de volgende fout aangetroffen:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Oplossing

Voorkomen dat Hive tabellen laadt in geheugen bij joins (in plaats daarvan een kaart/stap verkleinen) door de volgende Hive-configuratiewaarde in te stellen:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Volgende stappen

Als u deze waarde niet hebt opgelost, gaat u naar een van de volgende...

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
