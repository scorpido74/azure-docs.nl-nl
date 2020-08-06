---
title: Azure Virtual WAN bewaken met Azure Monitor Insights
description: Meer informatie over het bewaken van virtuele WAN met Azure Monitor Insights
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: cherylmc
ms.openlocfilehash: e3316b4a2255652972a0b9deef813f894f993589
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836068"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>Azure Monitor Insights voor virtuele WAN (preview-versie)

[Azure monitor Insights](../azure-monitor/insights/network-insights-overview.md) voor virtuele WAN geeft gebruikers en Opera tors de mogelijkheid om de status en status van het virtuele WAN weer te geven, zoals wordt weer gegeven via een topologie toewijzing met automatische detectie. De resource status en-status worden weer gegeven op de kaart om u een moment opname te geven van de algemene status van het virtuele WAN. Resource navigatie is ingeschakeld op de kaart via één klik toegang tot de resource configuratie pagina's van de virtuele WAN-Portal.

De metrische gegevens van het virtuele WAN-resource niveau worden verzameld en weer gegeven via een vooraf verpakte virtuele WAN-meet werkmap die de metrische gegevens op een virtueel WAN, een hub, een gateway en een verbindings niveau weergeeft. In dit artikel worden de stappen beschreven voor het gebruik van Azure Monitor Insights voor virtuele WAN om uw virtuele WAN-topologie en metrische gegevens op één plek weer te geven.

> [!NOTE]
> De menu optie inzichten in de virtuele WAN-Portal is in het proces van uitrollen. Terwijl het menu inzichten voor het virtuele WAN wordt geïmplementeerd, is de werkmap Virtual WAN-topologie en metrics direct toegankelijk via Azure Montor voor netwerken. Zie [Azure monitor Insights](../azure-monitor/insights/network-insights-overview.md) voor meer informatie. 
>

## <a name="before-you-begin"></a>Voordat u begint

In de stappen in dit artikel wordt ervan uitgegaan dat u al een virtueel WAN hebt geïmplementeerd met een of meer hubs. Als u een nieuwe virtuele WAN en een nieuwe hub wilt maken, gebruikt u de stappen in de volgende artikelen:

* [Een virtueel WAN maken](virtual-wan-site-to-site-portal.md#openvwan)
* [Een hub maken](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>VWAN-topologie weer geven

In de **Azure Portal->virtuele WAN**, in het menu **monitor** aan de linkerkant, selecteert u **inzichten (preview)**. Hiermee wordt de **Insights-weer gave**geopend, waarin de virtuele WAN-afhankelijkheids kaart en metrische gegevens van het hoge niveau worden weer gegeven.

**Afbeelding 1: monitor-Insights-menu**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="afbeelding" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

In de weer gave **inzichten** kunt u de automatisch gedetecteerde virtuele WAN-bronnen zoals hubs, gateways, firewalls, verbindingen en spoke VNets, nva's van derden en vertakkingen in een end-to-end virtuele WAN weer geven, zoals wordt weer gegeven in **afbeelding 2**.

De **resource status** en- **status** zijn kleur codes en overlappen op de resource pictogrammen in de kaart. Virtuele WAN-gegevens over hoog niveau, zoals de capaciteit van de hub en het gateway gebruik, worden aan de rechter kant weer gegeven via een mini werkmap.

**Afbeelding 2: Insights-weer gave**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="afbeelding" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Afhankelijkheids weergave

De **afhankelijkheids** weergave voor virtuele WANs helpt de onderling verbonden weer gave van alle virtuele WAN-bronnen breed te visualiseren in een hub-en-spoke-architectuur.

**Afbeelding 3: VWAN-afhankelijkheids weergave**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Afhankelijkheids toewijzing" lightbox="./media/azure-monitor-insights/dependency-map.png":::

In de afhankelijkheids weergave kaart worden de volgende resources weer gegeven als verbonden grafiek:

* Virtuele WAN-hubs in de verschillende Azure-regio's.
* Spoke VNets die rechtstreeks zijn verbonden met de hub.
* VPN-en ExpressRoute-filialen en P2S-gebruikers die zijn verbonden met elke hub via hun respectievelijke ExpressRoute-, S2S-en P2S-verbindingen en virtuele netwerk gateways.
* Azure-firewalls (inclusief firewall-proxy's van derden) geïmplementeerd in een hub (beveiligde hub).
* NVA (virtuele netwerk apparaten) die zijn geïmplementeerd in een spoke VNets.

De afhankelijkheids toewijzing bevat ook indirect verbonden VNets (VNet dat is gekoppeld aan een virtuele WAN Spaak VNets).

De afhankelijkheids kaart maakt eenvoudig navigatie naar de configuratie-instellingen van elke bron mogelijk. U kunt bijvoorbeeld de muis aanwijzer over de resource van de hub bewegen om de basis configuratie van de resource, zoals een hub-regio en een hub-voor voegsel, weer te geven. Klik met de rechter muisknop om de Azure Portal-pagina van de hub-resource te openen.

**Afbeelding 4: navigeren naar resource-specifieke informatie**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="resource gegevens":::

De zoek-en filter balk in de afhankelijkheids weergave bieden een probleemloze manier om de grafiek te doorzoeken. Verschillende filters bieden hulp om uw zoek opdracht te beperken tot een specifiek pad en een specifieke status.

**Afbeelding 5: zoeken en filteren**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="Zoek-en filter balk" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Gedetailleerde metrische gegevens

U kunt **gedetailleerde metrische gegevens weer geven** selecteren om toegang te krijgen tot de pagina gedetailleerde **metrische gegevens** . De pagina metrische gegevens is een dash board dat vooraf is geconfigureerd met afzonderlijke tabbladen, met nuttige inzichten in de capaciteit, prestaties en het gebruik van virtuele WAN-bronnen op het virtuele WAN-niveau, het niveau van de hub en afzonderlijke verbindingen.

**Afbeelding 6: gedetailleerd dash board voor metrische gegevens**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="gedetailleerde metrische gegevens" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Volgende stappen

* Zie [metrische gegevens in azure monitor](../azure-monitor/platform/data-platform-metrics.md)voor meer informatie over metrische gegevens in azure monitor.
* Zie [virtuele WAN-logboeken en metrische](logs-metrics.md)gegevens voor een volledige beschrijving van alle virtuele WAN-metrische gegevens.
