---
title: Apache HBase-REST reageert niet op aanvragen in azure HDInsight
description: Probleem oplossen met Apache HBase REST reageert niet op aanvragen in azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: c4a0ef82b951fa43eb4c58050d3148fd2d695026
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286980"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Scenario: Apache HBase REST reageert niet op aanvragen in azure HDInsight

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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]