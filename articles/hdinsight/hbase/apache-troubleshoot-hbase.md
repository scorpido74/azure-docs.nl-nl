---
title: Problemen met HBase oplossen met behulp van Azure HDInsight
description: Krijg antwoorden op veelgestelde vragen over het werken met HBase en Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 595acdc46aca76a86038acebdb9a7581c51e3688
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69573954"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Problemen met Apache HBase oplossen met Azure HDInsight

Meer informatie over de belangrijkste problemen en hun oplossingen bij het werken met Apache HBase-nettoladingen in Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Hoe kan ik hbck-opdracht rapporten met meerdere niet-toegewezen regio's uitvoeren?

Een veelvoorkomend fout bericht dat kan worden weer gegeven wanneer u de `hbase hbck` opdracht uitvoert, is dat meerdere regio's niet-toegewezen of gaten in de keten van regio's zijn.

In de HBase Master gebruikers interface ziet u het aantal regio's dat niet in balans is over alle regio servers. Vervolgens kunt u de opdracht `hbase hbck` uitvoeren om gaten in de regio keten weer te geven.

Openingen kunnen worden veroorzaakt door de offline regio's, daarom moet u de toewijzingen eerst herstellen. 

Voer de volgende stappen uit om de niet-toegewezen regio's terug te brengen naar een normale status:

1. Meld u aan bij het HDInsight HBase-cluster met behulp van SSH.
2. Als u verbinding wilt maken met de Apache ZooKeeper shell `hbase zkcli` , voert u de opdracht uit.
3. Voer de `rmr /hbase/regions-in-transition` opdracht of de `rmr /hbase-unsecure/regions-in-transition` opdracht uit.
4. Als u de `hbase zkcli` shell wilt afsluiten, gebruikt `exit` u de opdracht.
5. Open de Apache Ambari-gebruikers interface en start de Active HBase Master-service opnieuw.
6. Voer de `hbase hbck` opdracht opnieuw uit (zonder opties). Controleer de uitvoer van deze opdracht om te controleren of alle regio's worden toegewezen.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Hoe kan ik time-outproblemen oplossen bij het gebruik van hbck-opdrachten voor regio toewijzingen?

### <a name="issue"></a>Probleem

Een mogelijke oorzaak van problemen met de time-out `hbck` wanneer u de opdracht gebruikt, kan het zijn dat verschillende regio's in de status ' in transitie ' lang zijn. U kunt deze regio's als offline weer geven in de gebruikers interface van HBase Master. Omdat er een groot aantal regio's probeert te overstappen, HBase Master mogelijk time-out en kan deze regio's niet weer online zetten.

### <a name="resolution-steps"></a>Oplossingen

1. Meld u aan bij het HDInsight HBase-cluster met behulp van SSH.
2. Als u verbinding wilt maken met de Apache ZooKeeper shell `hbase zkcli` , voert u de opdracht uit.
3. Voer de `rmr /hbase/regions-in-transition` of de `rmr /hbase-unsecure/regions-in-transition` opdracht uit.
4. Als u de `hbase zkcli` shell wilt afsluiten, `exit` gebruikt u de opdracht.
5. Start de Active HBase Master-service opnieuw in de gebruikers interface van Ambari.
6. Voer de `hbase hbck -fixAssignments` opdracht opnieuw uit.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de ondersteunings [abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
