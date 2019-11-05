---
title: Fout met geweigerde machtiging bij Apache Hive tabel in azure HDInsight
description: Fout met geweigerde machtiging bij het maken van een Apache Hive tabel in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: d9901132af992ea95a60773f404b1351386cfbcb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494200"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Scenario: fout bericht over geweigerde machtiging bij het maken van een Apache Hive tabel in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactieve query onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De volgende fout wordt weer geven bij het maken van een tabel:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Er wordt een soortgelijk fout bericht weer gegeven als u de volgende HDFS-opslag opdracht uitvoert:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Oorzaak

De mogelijkheid om een tabel in Apache Hive te maken, wordt bepaald door de machtigingen die worden toegepast op het opslag account van het cluster. Als de machtigingen voor het cluster opslag account onjuist zijn, kunt u geen tabellen maken. Dit betekent dat u het juiste beleid voor Zwerver kunt hebben voor het maken van tabellen en nog steeds de fout berichten ' permission denied ' ziet.

## <a name="resolution"></a>Resolutie

Dit wordt veroorzaakt door het ontbreken van voldoende machtigingen voor de gebruikte opslag container. De gebruiker die de Hive-tabel maakt, moet lees-, schrijf-en uitvoer machtigingen voor de container hebben. Zie [Aanbevolen procedures voor Hive-autorisatie met behulp van Apache zwerver in HDP 2,2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Zie [Een ondersteuningsaanvraag maken voor Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) voor meer informatie. Toegang tot abonnementsbeheer en factuurbeheer is in uw Microsoft Azure-abonnement inbegrepen, en technische ondersteuning wordt verstrekt via een van de [Azure-ondersteuningsplannen](https://azure.microsoft.com/support/plans/).
