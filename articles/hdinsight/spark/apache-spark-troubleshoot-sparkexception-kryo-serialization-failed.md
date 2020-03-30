---
title: Problemen met JDBC/ODBC & Apache Thrift framework - Azure HDInsight
description: Kan geen grote gegevenssets downloaden met JDBC/ODBC en Apache Thrift-softwareframework in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894260"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Niet in staat om grote datasets te downloaden met JDBC/ODBC en Apache Thrift software framework in HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-componenten in Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Wanneer u grote gegevenssets probeert te downloaden met JDBC/ODBC en het Apache Thrift-softwareframework in Azure HDInsight, ontvangt u een foutmelding die als volgt is:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Oorzaak

Deze uitzondering wordt veroorzaakt door het serialisatieproces dat meer bufferruimte probeert te gebruiken dan is toegestaan. In Spark 2.0.0 `org.apache.spark.serializer.KryoSerializer` wordt de klasse gebruikt voor het serialiseren van objecten wanneer gegevens worden geopend via het Apache Thrift-softwareframework. Een andere klasse wordt gebruikt voor gegevens die via het netwerk worden verzonden of in geserialiseerde vorm in de cache worden opgeslagen.

## <a name="resolution"></a>Oplossing

Verhoog `Kryoserializer` de bufferwaarde. Voeg een `spark.kryoserializer.buffer.max` sleutel met `2048` de naam toe `Custom spark2-thrift-sparkconf`en stel deze in op spark2 config onder .

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
