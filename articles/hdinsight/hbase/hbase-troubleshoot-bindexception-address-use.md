---
title: BindException - Adres dat al in Azure HDInsight wordt gebruikt
description: BindException - Adres dat al in Azure HDInsight wordt gebruikt
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887339"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Scenario: BindException - Adres dat al in Azure HDInsight wordt gebruikt

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De herstartbewerking op een Apache HBase-regioserver is niet voltooid. In `region-server.log` de `/var/log/hbase` in-directory op de werknemersknooppunten waar het starten van de regioserver mislukt, ziet u mogelijk een soortgelijk foutbericht als volgt:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Oorzaak

Apache HBase-regioservers opnieuw starten tijdens zware werkbelasting. Hieronder vindt u wat er achter de schermen gebeurt wanneer een gebruiker de herstartbewerking op HBase-regioserver's van Apache Ambari UI initieert:

1. De Ambari-agent stuurt een stopverzoek naar de regioserver.

1. De Ambari-agent wacht 30 seconden tot de regioserver op een elegante manier wordt afgesloten

1. Als uw toepassing verbinding blijft maken met de regioserver, wordt de server niet onmiddellijk afgesloten. De time-out van 30 seconden verloopt voordat de afsluiting plaatsvindt.

1. Na 30 seconden stuurt de Ambari-agent`kill -9`een opdracht force-kill ( ) naar de regioserver.

1. Als gevolg van deze abrupte shutdown, hoewel de regio server proces wordt gedood, `AddressBindException`de poort in verband met het proces kan niet worden vrijgegeven, wat uiteindelijk leidt tot .

## <a name="resolution"></a>Oplossing

Verlaag de belasting op de HBase-regioservers voordat u een herstart start. Ook is het een goed idee om eerst alle tafels door te spoelen. Zie [HDInsight HBase: Hoe u de herstarttijd van het Apache HBase-cluster verbeteren door tabellen door te spoelen voor](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)een referentie over het doorspoelen van tabellen.

U ook regioservers op de werknemersknooppunten handmatig opnieuw starten met behulp van volgende opdrachten:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
