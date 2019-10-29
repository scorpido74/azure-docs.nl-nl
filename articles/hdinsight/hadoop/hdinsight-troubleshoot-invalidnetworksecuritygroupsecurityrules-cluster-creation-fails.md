---
title: InvalidNetworkSecurityGroupSecurityRules-fout-Azure HDInsight
description: Het maken van het cluster mislukt met de error code InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 749cfbb3aeb0d82a81a8383919b9a6568419e967
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044755"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scenario: InvalidNetworkSecurityGroupSecurityRules-het maken van een cluster mislukt in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

U ontvangt fout code `InvalidNetworkSecurityGroupSecurityRules` met een beschrijving die vergelijkbaar is met ' de beveiligings regels in de netwerk beveiligings groep die is geconfigureerd met het subnet, staat geen vereiste binnenkomende en/of uitgaande connectiviteit toe. '

## <a name="cause"></a>Oorzaak

Er is waarschijnlijk een probleem met de regels voor binnenkomende [netwerk beveiligings groepen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) die zijn geconfigureerd voor uw cluster.

## <a name="resolution"></a>Resolutie

Ga naar de Azure Portal en Identificeer de NSG die is gekoppeld aan het subnet waar het cluster wordt geïmplementeerd. Controleer in de sectie **binnenkomende beveiligings regels** of de regels binnenkomende toegang tot poort 443 toestaan voor de IP-adressen die [hier](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)worden vermeld.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
