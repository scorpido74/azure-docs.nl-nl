---
title: Fout geweigerd met Apache Hive-tabel in Azure HDInsight
description: Fout geweigerd bij het maken van een Apache Hive-tabel in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8ebad9300c126193ddb96d5f07057b9a825dbfcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895144"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Scenario: Fout geweigerd bij het maken van een Apache Hive-tabel in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactive query-componenten in Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

U ziet de volgende fout wanneer u een tabel probeert te maken:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

U ziet een soortgelijk foutbericht als u de volgende opdracht HDFS-opslag uitvoert:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Oorzaak

De mogelijkheid om een tabel in Apache Hive te maken, wordt bepaald door de machtigingen die worden toegepast op het opslagaccount van het cluster. Als de machtigingen voor het clusteropslagaccount onjuist zijn, u geen tabellen maken. Dit betekent dat u het juiste Ranger-beleid voor het maken van tabels hebben en nog steeds fouten 'Geweigerd einerde' zien.

## <a name="resolution"></a>Oplossing

Dit wordt veroorzaakt door een gebrek aan voldoende machtigingen voor de gebruikte opslagcontainer. De gebruiker die de Hive-tabel maakt, moet machtigingen voor de container lezen, schrijven en uitvoeren. Zie Aanbevolen procedures [voor Hive Authorization Using Apache Ranger in HDP 2.2 voor](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/)meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
