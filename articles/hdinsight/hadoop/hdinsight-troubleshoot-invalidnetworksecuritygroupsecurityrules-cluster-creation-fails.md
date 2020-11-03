---
title: InvalidNetworkSecurityGroupSecurityRules-fout-Azure HDInsight
description: Het maken van het cluster mislukt met de error code InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: b1423dc965a3169a5f615ccc371849cc177be244
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289102"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scenario: InvalidNetworkSecurityGroupSecurityRules-het maken van een cluster mislukt in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

U ontvangt fout code `InvalidNetworkSecurityGroupSecurityRules` met een beschrijving die vergelijkbaar is met de beveiligings regels in de netwerk beveiligings groep die is geconfigureerd met het subnet, de vereiste binnenkomende en/of uitgaande connectiviteit niet toestaat.

## <a name="cause"></a>Oorzaak

Er is waarschijnlijk een probleem met de regels voor binnenkomende [netwerk beveiligings groepen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) die zijn geconfigureerd voor uw cluster.

## <a name="resolution"></a>Oplossing

Ga naar de Azure Portal en Identificeer de NSG die is gekoppeld aan het subnet waar het cluster wordt geïmplementeerd. Controleer in de sectie **binnenkomende beveiligings regels** of de regels binnenkomende toegang tot poort 443 toestaan voor de IP-adressen die [hier](../control-network-traffic.md)worden vermeld.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]