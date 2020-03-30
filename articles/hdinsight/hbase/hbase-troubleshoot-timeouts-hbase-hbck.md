---
title: Time-outs bij gebruik van de opdracht 'hbase hbck' in Azure HDInsight
description: Time-out probleem met 'hbase hbck' commando bij de vaststelling van regio opdrachten
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887186"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scenario: Time-outs met 'hbase hbck'-opdracht in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Ontmoet time-outs met `hbase hbck` opdracht bij het oplossen van regiotoewijzingen.

## <a name="cause"></a>Oorzaak

Een mogelijke oorzaak voor time-outproblemen wanneer u de `hbck` opdracht gebruikt, kan zijn dat verschillende regio's zich lange tijd in de status 'in transitie' bevinden. U deze regio's als offline zien in de HBase Master UI. Omdat een groot aantal regio's probeert over te stappen, kan HBase Master een time-out geven en niet in staat zijn deze regio's weer online te brengen.

## <a name="resolution"></a>Oplossing

1. Meld u aan bij het HDInsight HBase-cluster met SSH.

1. Voer `hbase zkcli` de opdracht uit om verbinding te maken met de shell Apache ZooKeeper.

1. Uitvoeren `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` of opdracht geven.

1. Sluit `hbase zkcli` de shell `exit` af met behulp van de opdracht.

1. Start vanuit de Apache Ambari-gebruikersinterface de Active HBase Master-service opnieuw.

1. Voer de opdracht `hbase hbck -fixAssignments` uit.

1. Controleer de HBase Master UI "regio in transitie" die sectie om ervoor te zorgen dat geen regio vast komt te zitten.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

- Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

- Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
