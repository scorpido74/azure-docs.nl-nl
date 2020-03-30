---
title: Apache HBase REST reageert niet op aanvragen in Azure HDInsight
description: Probleem met Apache HBase REST oplossen en reageert niet op aanvragen in Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 49b547829a369ea6df35e2f1c2f7d60458e41040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887169"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Scenario: Apache HBase REST reageert niet op aanvragen in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De Apache HBase REST-service reageert niet op aanvragen in Azure HDInsight.

## <a name="cause"></a>Oorzaak

De mogelijke oorzaak hier zou kunnen zijn Apache HBase REST service lekt sockets, die vooral gebruikelijk is wanneer de dienst is uitgevoerd voor een lange tijd (bijvoorbeeld, maanden). Van de SDK van de client ziet u mogelijk een foutbericht dat vergelijkbaar is met:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Oplossing

Start HBase REST opnieuw op met de onderstaande opdracht na SSHing naar de host. U ook scriptacties gebruiken om deze service opnieuw te starten op alle werknemersknooppunten:

```bash
sudo service hdinsight-hbrest restart
```

Met deze opdracht stopt HBase Region Server op dezelfde host. U HBase Region Server handmatig starten via Ambari of ambari automatisch de functionaliteit laten herstellen van de Functionaliteit hbase-regioserver automatisch herstellen.

Als het probleem nog steeds bestaat, u het volgende mitigatiescript installeren als een CRON-taak die elke 5 minuten wordt uitgevoerd op elk werknemersknooppunt. Dit mitigatiescript pingt de REST-service en start deze opnieuw op voor het geval de REST-service niet reageert.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
