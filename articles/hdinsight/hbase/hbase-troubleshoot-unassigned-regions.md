---
title: Problemen met regioservers in Azure HDInsight
description: Problemen met regioservers in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272758"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problemen met regioservers in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="scenario-unassigned-regions"></a>Scenario: Niet-toegewezen gebieden

### <a name="issue"></a>Probleem

Wanneer `hbase hbck` u de opdracht uitvoert, ziet u een foutbericht dat vergelijkbaar is met:

```
multiple regions being unassigned or holes in the chain of regions
```

Vanuit de Apache HBase Master UI u het aantal regio's zien dat niet in balans is tussen alle regioservers. Vervolgens u `hbase hbck` de opdracht uitvoeren om gaten in de regioketen te zien.

### <a name="cause"></a>Oorzaak

Gaten kunnen het gevolg zijn van offline regio's.

### <a name="resolution"></a>Oplossing

Repareer de opdrachten. Volg de onderstaande stappen om de niet-toegewezen gebieden weer in de normale staat te brengen:

1. Meld u aan bij het HDInsight HBase-cluster met SSH.

1. Voer `hbase zkcli` de opdracht uit om verbinding te maken met de shell ZooKeeper.

1. Uitvoeren `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` of opdracht geven.

1. Verlaat de dierenverzorgersmethet commando. `exit`

1. Open de Apache Ambari-gebruikersinterface en start de Active HBase Master-service opnieuw.

1. Voer `hbase hbck` de opdracht opnieuw uit (zonder verdere opties). Controleer de uitvoer en zorg ervoor dat alle regio's worden toegewezen.

---

## <a name="scenario-dead-region-servers"></a>Scenario: Servers van dode regio's

### <a name="issue"></a>Probleem

Regioservers starten niet.

### <a name="cause"></a>Oorzaak

Meerdere splitsenDE WAL-mappen.

1. Overzicht som met `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`huidige WAL's: .

1. Controleer `wals.out` het bestand. Als er te veel splitsing mappen (te beginnen met *-splitsing), de regio server is waarschijnlijk niet als gevolg van deze mappen.

### <a name="resolution"></a>Oplossing

1. Stop HBase van Ambari portal.

1. Uitvoeren `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` om nieuwe lijst van WALs te krijgen.

1. Verplaats de mappen *splitsen naar een `splitWAL`tijdelijke map en verwijder de mappen *-splitsing.

1. Voer `hbase zkcli` opdracht uit om verbinding te maken met de shell van zookeeper.

1. Uitvoeren `rmr /hbase-unsecure/splitWAL`.

1. Start de HBase-service opnieuw.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
