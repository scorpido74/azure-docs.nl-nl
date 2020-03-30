---
title: Kan zich niet aanmelden bij Azure HDInsight-cluster
description: Problemen oplossen waarom u zich niet kan aanmelden bij het Apache Hadoop-cluster in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 2099d1b7583017733498946a5866ab37de43ba9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894056"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Scenario: Kan zich niet aanmelden bij Azure HDInsight-cluster

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Kan niet worden aangemeld bij het Azure HDInsight-cluster.

## <a name="cause"></a>Oorzaak

Redenen kunnen variëren. Houd er rekening mee dat wanneer u zich aanmeldt bij de cluster- of app-dashboards, uw 'clusterlogin' of HTTP-referenties gebruikt. Gebruik bij een verbinding op afstand uw referenties voor Secure Shell (SSH) of Extern bureaublad.

## <a name="resolution"></a>Oplossing

Probeer een of meer van de volgende stappen om veelvoorkomende problemen op te lossen.

* Open het clusterdashboard op een nieuw browsertabblad in de privacymodus.

* Als u uw SSH-referenties niet terugroepen, u [de referenties opnieuw instellen binnen de Ambari-gebruikersinterface.](../hdinsight-administer-use-portal-linux.md#change-passwords)

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
