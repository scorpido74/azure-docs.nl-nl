---
title: Opslag uitzondering na opnieuw instellen van verbinding in azure HDInsight
description: Opslag uitzondering na opnieuw instellen van verbinding in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: 52135391024eafdfea15afd6c05a5d13bf92a2c7
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091573"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Scenario: Opslag uitzondering na opnieuw instellen van verbinding in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Kan geen nieuwe Apache HBase-tabel maken.

## <a name="cause"></a>Oorzaak

Tijdens het afkappen van een tabel is er een probleem met de opslag verbinding. De tabel vermelding is verwijderd uit de HBase-meta gegevens tabel. Er is maar één blob-bestand verwijderd.

Er is geen map-blob met `/hbase/data/default/ThatTable` de naam in de opslag. Het WASB-stuur programma heeft vastgesteld dat het bestand het bovenliggende element bevat en dat er geen BLOB `/hbase/data/default/ThatTable` zou kunnen worden gemaakt, omdat er wordt ervan uitgegaan dat er voor het hoofd mappen bestaan, waardoor de tabel niet kan worden gemaakt.

## <a name="resolution"></a>Oplossing

1. Start de actieve HMaster opnieuw vanuit Apache Ambari UI. Hiermee kan een van de twee stand-HMaster worden geactiveerd en wordt de gegevens tabel informatie opnieuw geladen met de nieuwe Active HMaster. Daarom ziet u de tabel niet `already-deleted` in de HMaster-gebruikers interface.

1. U vindt het zwevende blob-bestand in UI-hulpprogram ma's zoals Cloud Explorer of `hdfs dfs -ls /xxxxxx/yyyyy`het uitvoeren van opdrachten zoals. Voer `hdfs dfs -rmr /xxxxx/yyyy` uit om de BLOB te verwijderen. Bijvoorbeeld `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

U kunt nu een nieuwe tabel met dezelfde naam maken in HBase.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
