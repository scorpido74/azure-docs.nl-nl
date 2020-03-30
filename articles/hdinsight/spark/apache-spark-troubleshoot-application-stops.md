---
title: Apache Spark Streaming-toepassing stopt na 24 dagen in Azure HDInsight
description: Een Apache Spark Streaming-toepassing stopt na het uitvoeren van 24 dagen en er zijn geen fouten in de logboekbestanden.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894436"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scenario: Apache Spark Streaming-toepassing stopt na 24 dagen uitvoeren in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-componenten in Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Een Apache Spark Streaming-toepassing stopt na het uitvoeren van 24 dagen en er zijn geen fouten in de logboekbestanden.

## <a name="cause"></a>Oorzaak

De `livy.server.session.timeout` waarde bepaalt hoe lang Apache Livy moet wachten tot een sessie is voltooid. Zodra de sessielengte `session.timeout` de waarde bereikt, worden de Livy-sessie en de toepassing automatisch gedood.

## <a name="resolution"></a>Oplossing

Voor langlopende taken verhoogt `livy.server.session.timeout` u de waarde van het gebruik van de Ambari-gebruikersinterface. U hebt toegang tot de Livy-configuratie vanuit `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`de Ambari-gebruikersinterface via de URL.

Vervang `<yourclustername>` de naam van uw HDInsight-cluster zoals weergegeven in de portal.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
