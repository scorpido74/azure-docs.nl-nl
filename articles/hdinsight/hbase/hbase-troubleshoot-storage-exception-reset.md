---
title: Opslaguitzondering na het resetten van de verbinding in Azure HDInsight
description: Opslaguitzondering na het resetten van de verbinding in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887220"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Scenario: Opslaguitzondering na het opnieuw instellen van de verbinding in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Kan geen nieuwe Apache HBase-tabel maken.

## <a name="cause"></a>Oorzaak

Tijdens een tabeltruncation proces, was er een opslag verbinding probleem. De tabelvermelding is verwijderd in de tabel metagegevens van HBase. Op één na is op één blobbestand na verwijderd.

Hoewel er geen map `/hbase/data/default/ThatTable` blob genaamd zittend in de opslag. Het WASB-stuurprogramma vond het bestaan van het bovenstaande blobbestand `/hbase/data/default/ThatTable` en zou het niet toestaan om een blob te maken die wordt aangeroepen omdat het ervan uitging dat de bovenliggende mappen bestonden, waardoor het maken van een tabel mislukt.

## <a name="resolution"></a>Oplossing

1. Start vanaf Apache Ambari UI de actieve HMaster opnieuw. Hierdoor wordt een van de twee stand-by HMaster de actieve en zal de nieuwe actieve HMaster de metadata tabelinfo herladen. Zo ziet u `already-deleted` de tabel niet in HMaster UI.

1. U het zwevende blobbestand vinden in de `hdfs dfs -ls /xxxxxx/yyyyy`gebruikersinterfacetools zoals Cloud Explorer of de opdracht uitvoeren zoals. Voer `hdfs dfs -rmr /xxxxx/yyyy` uit om die blob te verwijderen. Bijvoorbeeld `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Nu u nieuwe tabel met dezelfde naam maken in HBase.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
