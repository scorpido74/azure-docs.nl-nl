---
title: Reducer is traag in Azure HDInsight
description: Reducer is traag in Azure HDInsight door mogelijke gegevensscheeftrekking
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895165"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Scenario: Reducer is traag in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactive query-componenten in Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Bij het uitvoeren `insert into table1 partition(a,b) select a,b,c from table2` van een query, zoals het queryplan, wordt een aantal reducers gestart, maar de gegevens van elke partitie gaan naar één reducer. Dit zorgt ervoor dat de query net zo traag is als de tijd die wordt genomen door de reducer van de grootste partitie.

## <a name="cause"></a>Oorzaak

Open [beeline](../hadoop/apache-hadoop-use-hive-beeline.md) en controleer `hive.optimize.sort.dynamic.partition`de waarde van de set .

De waarde van deze variabele is bedoeld om te worden ingesteld op true /false op basis van de aard van de gegevens.

Als de partities in de invoertabel minder zijn (bijvoorbeeld minder dan 10), en het `true`aantal uitvoerpartities ook, en de variabele is ingesteld op , zorgt dit ervoor dat gegevens wereldwijd worden gesorteerd en geschreven met één reducer per partitie. Zelfs als het aantal beschikbare reducers groter is, kunnen een paar reducers achterblijven als gevolg van gegevens scheeftrekken en de maximale parallellisme kan niet worden bereikt. Wanneer gewijzigd `false`in , meer dan een reducer kan omgaan met een partitie en meerdere kleinere bestanden zullen worden uitgeschreven, wat resulteert in een snellere insert. Dit kan gevolgen hebben voor verdere query's vanwege de aanwezigheid van kleinere bestanden.

Een waarde `true` van zinvol wanneer het aantal partities groter is en de gegevens niet scheef. In dergelijke gevallen zal het resultaat van de kaartfase zodanig worden uitgeschreven dat elke partitie wordt behandeld door een enkele reducer, wat resulteert in betere vervolgqueryprestaties.

## <a name="resolution"></a>Oplossing

1. Probeer de gegevens opnieuw te verdelen om te normaliseren in meerdere partities.

1. Als #1 niet mogelijk is, stelt u de waarde van de config in op false in de beelinesessie en probeert u de query opnieuw. `set hive.optimize.sort.dynamic.partition=false`. Het wordt afgeraden de waarde in te stellen op false op clusterniveau. De waarde `true` van is optimaal en stel de parameter zo nodig in op basis van de aard van gegevens en query.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
