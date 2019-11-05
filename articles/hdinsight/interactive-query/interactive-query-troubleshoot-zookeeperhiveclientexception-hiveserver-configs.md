---
title: Apache Hive Zeppelin-interpreter fout-Azure HDInsight
description: De Apache Zeppelin Hive JDBC-interpreter verwijst naar de verkeerde URL in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: dd5a45cb885d800d9922e7c8967f20b63b92be60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494167"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Scenario: Apache Hive Zeppelin-interpreter bevat een Zookeeper-fout in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactieve query onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Op een Apache Hive LLAP-cluster geeft de Zeppelin-interpreter het volgende fout bericht weer wanneer wordt geprobeerd een query uit te voeren:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Oorzaak

De Zeppelin Hive JDBC-interpreter verwijst naar de verkeerde URL.

## <a name="resolution"></a>Resolutie

1. Navigeer naar het samen vatting van Hive-onderdelen en kopieer de ' hive JDBC-URL ' naar het klem bord.

1. Ga naar `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. De JDBC-instellingen bewerken: werk de Hive. URL-waarde bij naar de component JDBC-URL die u in stap 1 hebt gekopieerd

1. Sla het bestand op en voer de query opnieuw uit

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Zie [Een ondersteuningsaanvraag maken voor Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) voor meer informatie. Toegang tot abonnementsbeheer en factuurbeheer is in uw Microsoft Azure-abonnement inbegrepen, en technische ondersteuning wordt verstrekt via een van de [Azure-ondersteuningsplannen](https://azure.microsoft.com/support/plans/).
