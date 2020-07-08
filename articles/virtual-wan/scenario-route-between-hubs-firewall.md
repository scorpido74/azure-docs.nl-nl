---
title: 'Scenario: Azure Firewall-Interhub-route ring'
titleSuffix: Azure Virtual WAN
description: Scenario's voor route ring-routing tussen meerdere hubs met Azure Firewall
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 1057dc28682c90374b2f8e8e0297f3d769c08bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568513"
---
# <a name="scenario-azure-firewall---interhub-preview"></a>Scenario: Azure Firewall-Interhub (preview-versie)

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In dit scenario is het doel om te routeren tussen meerdere hubs die Azure Firewall bevatten. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over route ring van virtuele hub.

## <a name="scenario-architecture"></a><a name="architecture"></a>Scenario-architectuur

In dit scenario wordt ervan uitgegaan dat de volgende configuratie:

* U hebt meerdere hubs met Azure Firewall in elke hub.
* U gebruikt een beveiligde virtuele hub.
* Het juiste beleid voor het privé verkeer (VNet), het internet en het filiaal verkeer is ingesteld.
* VNet-naar-Internet (V2I), VNet-naar-Branch (V2B), vertakking-naar-VNet (B2V), alles door lopen Azure Firewall in elke hub.

Aanvullende overwegingen:

* Voor een inter-hub-scenario met Azure Firewall is de veronderstelling dat spoke VNets niet koppelen aan afzonderlijke route tabellen. (bijvoorbeeld **VNET 1** gekoppeld aan **route tabel A**en **Vnet 2** gekoppeld aan **route tabel B**). Alle VNets worden gekoppeld aan dezelfde route tabel waarin de routes voor de Azure Firewall zich bevinden.
* Beveiligen via Azure Firewall is momenteel beperkt tot **vertakking < > VNet** en **Internet** verkeer. Vertakkings-naar-vertakkings stroom via de Azure Firewall wordt momenteel niet ondersteund.

**Afbeelding 1**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/architecture.png" alt-text="opstelling":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario werk stroom

Als u dit scenario wilt configureren, voert u de volgende stappen uit:

### <a name="step-1"></a><a name="step-1"></a>Stap 1

Ervan uitgaande dat u de verbindingen van Azure Firewall Manager al hebt beveiligd, is de eerste stap ervoor te zorgen dat alle vertakkings-en VNet-verbindingen **geen**worden door gegeven. Dit is vereist om ervoor te zorgen dat verkeer wordt ingesteld via de Azure Firewall.

1. Selecteer de hub en bewerk de tabel **geen** route.
1. Update **doorgifte** om alle vertakkingen en alle VNets te selecteren.

### <a name="step-2"></a><a name="step-2"></a>Stap 2

Stel een aangepaste route tabel per hub in.

1. Voor **hub 1**maakt u een Route tabel **RT_Hub1**.

    * Als u de Azure Firewall Manager hebt gebruikt, wordt er automatisch een route voor 0.0.0.0/0 gemaakt met de volgende hop **AZFW1** in de standaard route tabel van de hub. U kunt deze instelling wijzigen in stap 3. Voor **RT_Hub1**maakt u een vermelding voor 0.0.0.0/0 expliciet met de **AZFW1**van de volgende hop. Met deze instelling worden V2V, B2V en V2I via AZFW1 geactiveerd.
    * Omdat u de **hub 2** -vertakkingen en VNets wilt bereiken via **AZFW2** van **hub 1** (in plaats van via AZFW1), moet u nog een 2 geaggregeerde routes voor **hub 2** -vertakkingen (10.5.0.0/16->AZFW2) en VNets (10.2.0.0/16->AZFW2) toevoegen.

1. Voor **hub 2**maakt u een Route tabel **RT_Hub2**.

    * Als u de Azure Firewall Manager hebt gebruikt, wordt er automatisch een route voor 0.0.0.0/0 gemaakt met de volgende hop **AZFW2** in de standaard route tabel van de hub. U kunt deze instelling wijzigen in stap 3. Voor **RT_Hub2**maakt u een vermelding voor 0.0.0.0/0 expliciet met de **AZFW2**van de volgende hop. Met deze instelling worden V2V, B2V en V2I via **AZFW2**geactiveerd.
    * Omdat u het verkeer tussen de hub wilt beveiligen door de **AZFW2**van hub 2, hoeft u niet expliciet een stap toe te voegen die vergelijkbaar is met het tweede opsommings teken in de vorige stap van de hub 1.

### <a name="step-3"></a><a name="step-3"></a>Stap 3

Wijzig de **standaard route tabel** in elk van de hubs om een statische route voor de vertakking toe te voegen **aan VNet** (B2V) en B2I-stromen ( **Branch to Internet** ) via de Azure firewall. Vertakking naar vertakking wordt momenteel niet ondersteund via Azure Firewall.

1. Voor de **standaard route tabel van hub 1**:

    * **Vertakking naar hub 2 branches via AZFW2**: 10.5.0.0/16->AZFW2. Met deze configuratie wordt een route ingesteld voor de hub 2-firewall.
    * **Vertakking naar hub 2 VNets via AZFW2**: 10.2.0.0/16->AZFW2. Met deze configuratie wordt een route ingesteld voor de hub 2-firewall.
    * **Vertakking naar vertakking (lokaal)/vertakking naar VNet (lokaal)/vertakking naar Internet**: 0.0.0.0/0->AZFW1.

2. Voor de **hub 2-standaard route tabel**:

    * Vertakking naar vertakking (lokaal en extern)/vertakking naar VNet (lokaal en extern)/vertakking naar Internet: 0.0.0.0/0->AZFW2.

Dit heeft tot gevolg dat de routerings configuratie wordt gewijzigd, zoals in de onderstaande afbeelding wordt weer gegeven

**Afbeelding 2**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/workflow.png" alt-text="workflowconfiguraties":::

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
* Zie [virtuele hub-route ring configureren](how-to-virtual-hub-routing.md)voor meer informatie over het configureren van virtuele hub-route ring.
