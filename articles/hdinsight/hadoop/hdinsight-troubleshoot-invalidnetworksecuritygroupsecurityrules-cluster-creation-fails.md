---
title: Fout ongeldigeNetworkSecurityGroupSecurityRules - Azure HDInsight
description: Clustercreatie mislukt met de ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: a73e1e9f7a9c017dd29b627a24c25ae2e064c0a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894146"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scenario: InvalidNetworkSecurityGroupSecurityRules - clustercreatie mislukt in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

U ontvangt `InvalidNetworkSecurityGroupSecurityRules` foutcode met een beschrijving die vergelijkbaar is met 'De beveiligingsregels in de netwerkbeveiligingsgroep die is geconfigureerd met subnet, staan geen vereiste inkomende en/of uitgaande connectiviteit toe.'

## <a name="cause"></a>Oorzaak

Waarschijnlijk een probleem met de ingestelde regels voor [netwerkbeveiliging](../../virtual-network/virtual-network-vnet-plan-design-arm.md) die voor uw cluster zijn geconfigureerd.

## <a name="resolution"></a>Oplossing

Ga naar de Azure-portal en identificeer de NSG die is gekoppeld aan het subnet waar het cluster wordt geïmplementeerd. Controleer in het gedeelte **Binnenkomende beveiligingsregels** of de regels binnenkomende toegang tot poort 443 toestaan voor de [IP-adressen](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)die hier worden genoemd.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
