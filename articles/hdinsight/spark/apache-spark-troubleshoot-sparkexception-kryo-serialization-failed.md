---
title: Problemen met JDBC/ODBC & Apache Thrift Framework-Azure HDInsight
description: Kan geen grote gegevens sets downloaden met behulp van JDBC/ODBC en Apache Thrift software framework in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 7423687cbca069792a584277699c6bf8ce904d5d
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241753"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Kan geen grote gegevens sets downloaden met behulp van JDBC/ODBC en Apache Thrift software framework in HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Wanneer u probeert grote gegevens sets te downloaden met behulp van JDBC/ODBC en het Apache Thrift-software raamwerk in azure HDInsight, wordt een fout bericht als volgt weer gegeven:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Oorzaak

Deze uitzonde ring wordt veroorzaakt door het serialisatie proces en probeert meer buffer ruimte te gebruiken dan is toegestaan. In Spark 2.0.0 wordt de klasse `org.apache.spark.serializer.KryoSerializer` gebruikt voor het serialiseren van objecten wanneer gegevens worden geopend via het Apache Thrift software Framework. Een andere klasse wordt gebruikt voor gegevens die via het netwerk worden verzonden of in de cache worden opgeslagen.

## <a name="resolution"></a>Resolutie

Verhoog de `Kryoserializer` buffer waarde. Voeg een sleutel toe met de naam `spark.kryoserializer.buffer.max` en stel deze in op `2048` in spark2 config onder `Custom spark2-thrift-sparkconf`.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
