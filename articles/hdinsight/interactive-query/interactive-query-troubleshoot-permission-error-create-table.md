---
title: Fout met geweigerde machtiging bij Apache Hive tabel in azure HDInsight
description: Fout met geweigerde machtiging bij het maken van een Apache Hive tabel in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: b6e71f5f2c389926a2e6267fadeedebdce1c51d9
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288892"
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

## <a name="resolution"></a>Oplossing

Dit wordt veroorzaakt door het ontbreken van voldoende machtigingen voor de gebruikte opslag container. De gebruiker die de Hive-tabel maakt, moet lees-, schrijf-en uitvoer machtigingen voor de container hebben. Zie [Aanbevolen procedures voor Hive-autorisatie met behulp van Apache zwerver in HDP 2,2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]