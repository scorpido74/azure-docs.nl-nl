---
title: Apache HBase-REST reageert niet op aanvragen in azure HDInsight
description: Probleem oplossen met Apache HBase REST reageert niet op aanvragen in azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 5b1291e6996cb5db366128f829ca2cdf3cad0a9d
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735301"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Scenario: Apache HBase-REST reageert niet op aanvragen in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De Apache HBase REST-service reageert niet op aanvragen in azure HDInsight.

## <a name="cause"></a>Oorzaak

De mogelijke oorzaak kan zijn dat Apache HBase REST service een lek vormt voor sockets. Dit is met name gebruikelijk wanneer de service gedurende een lange periode actief is (bijvoorbeeld maanden). Vanuit de client-SDK ziet u mogelijk een fout bericht van de volgende strekking:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Oplossing

Start HBase REST opnieuw op met behulp van de onderstaande opdracht na ssh naar de host. U kunt ook script acties gebruiken om deze service opnieuw te starten op alle worker-knoop punten:

```bash
sudo service hdinsight-hbrest restart
```

Met deze opdracht wordt de HBase-regio server op dezelfde host gestopt. U kunt de HBase-regio server hand matig starten via Ambari, of de functionaliteit van Ambari automatisch opnieuw starten HBase regio server automatisch herstellen.

Als het probleem zich blijft voordoen, kunt u het volgende beperkende script installeren als een CRON-taak die elke vijf minuten op elk worker-knoop punt wordt uitgevoerd. Dit beperkings script pingt de REST-service en start deze opnieuw op als de REST-service niet reageert.

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

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
