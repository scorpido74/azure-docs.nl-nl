---
title: 'Zelfstudie: Een NSX-T-netwerksegment maken in Azure VMware Solution'
description: Meer informatie over het maken van de NSX-T-netwerksegmenten die worden gebruikt voor VM's in vCenter
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: f88f186d2af10bcc114d64920a3ac489ef7be54f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367666"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>Zelfstudie: Een NSX-T-netwerksegment maken in Azure VMware Solution

De virtuele machines (VM's) die in vCenter zijn gemaakt, worden geplaatst op de netwerksegmenten die in NSX-T zijn gemaakt. Deze zijn zichtbaar in vCenter.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Navigeren in NSX-T Manager om netwerksegmenten toe te voegen
> * Een nieuw netwerksegment toevoegen
> * Het nieuwe netwerksegment observeren in vCenter

## <a name="prerequisites"></a>Vereisten

U hebt een privécloud van Azure VMware Solution nodig met toegang tot de vCenter- en NSX-T Manager-beheerinterfaces. Zie de zelfstudie [Netwerken configureren](tutorial-configure-networking.md) voor meer informatie.

## <a name="provision-a-network-segment-in-nsx-t"></a>Een netwerksegment inrichten in NSX-T

1. Selecteer in vCenter **SDDC-Datacenter > Networks** voor uw privécloud. Zoals u kunt zien, zijn er nog geen netwerken.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="Selecteer in vCenter SDDC-Datacenter > Networks voor uw privécloud. Zoals u kunt zien, zijn er nog geen netwerken.":::

1. Selecteer in NSX-T Manager **Networking** voor uw privécloud.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="Selecteer in vCenter SDDC-Datacenter > Networks voor uw privécloud. Zoals u kunt zien, zijn er nog geen netwerken.":::

1. Selecteer **Segments** .

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="Selecteer in vCenter SDDC-Datacenter > Networks voor uw privécloud. Zoals u kunt zien, zijn er nog geen netwerken.":::

1. Selecteer op de overzichtspagina NSX-T Segments de optie **ADD SEGMENT** . Er worden als onderdeel van het inrichten van de privécloud drie segmenten gemaakt die kunnen worden gebruikt voor VM's.  U moet een nieuw netwerksegment voor dit doel toevoegen.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="Selecteer in vCenter SDDC-Datacenter > Networks voor uw privécloud. Zoals u kunt zien, zijn er nog geen netwerken.":::

1. Geef een naam voor het segment op, kies de vooraf geconfigureerde Tier1-gateway (TNTxx-T1) als **Connected Gateway** , laat **Type** ingesteld op Flexible, kies de vooraf geconfigureerde overlay **Transport Zone** (TNTxx-OVERLAY-TZ) en selecteer vervolgens Set Subnets. Alle andere standaardinstellingen in deze sectie en de **POORTEN** en **SEGMENTPROFIELEN** blijven ongewijzigd.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="Selecteer in vCenter SDDC-Datacenter > Networks voor uw privécloud. Zoals u kunt zien, zijn er nog geen netwerken.":::

1. Stel het IP-adres van de gateway in voor het nieuwe segment en selecteer vervolgens **TOEVOEGEN** . Het IP-adres dat u gebruikt, moet zich op een niet-overlappend RFC1918-adresblok bevinden, zodat u verbinding kunt maken met de VM's op het nieuwe segment.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="Selecteer in vCenter SDDC-Datacenter > Networks voor uw privécloud. Zoals u kunt zien, zijn er nog geen netwerken.":::

1. Pas het nieuwe netwerksegment toe door **TOEPASSEN** te selecteren en de configuratie op te slaan met **OPSLAAN** .

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="Selecteer in vCenter SDDC-Datacenter > Networks voor uw privécloud. Zoals u kunt zien, zijn er nog geen netwerken.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="Selecteer in vCenter SDDC-Datacenter > Networks voor uw privécloud. Zoals u kunt zien, zijn er nog geen netwerken.":::

1. Het nieuwe netwerksegment is nu gemaakt. U kunt de optie om door te gaan met het configureren van het segment afwijzen door **NO** te selecteren.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Selecteer in vCenter SDDC-Datacenter > Networks voor uw privécloud. Zoals u kunt zien, zijn er nog geen netwerken.":::

1. Controleer of het nieuwe netwerksegment aanwezig is in NSX-T door **Netwerken > Segmenten** te selecteren en te controleren of het nieuwe segment wordt weergegeven in de lijst (in dit geval ls01).

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Selecteer in vCenter SDDC-Datacenter > Networks voor uw privécloud. Zoals u kunt zien, zijn er nog geen netwerken.":::

1. Controleer of het nieuwe netwerksegment aanwezig is in vCenter door **Netwerken > SDDC-Data Center** te selecteren en te controleren of het nieuwe segment wordt weergegeven in de lijst (in dit geval ls01).

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Selecteer in vCenter SDDC-Datacenter > Networks voor uw privécloud. Zoals u kunt zien, zijn er nog geen netwerken.":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de NSX-T-netwerksegmenten gemaakt die worden gebruikt voor VM's in vCenter. U kunt nu [een inhoudsbibliotheek maken voor het implementeren van VM's in Azure VMware Solution](deploy-vm-content-library.md). U kunt ook een VM inrichten op het netwerk dat u in deze zelfstudie hebt gemaakt.

Indien u dit niet wilt doen, gaat u door met de volgende zelfstudie voor meer informatie over het maken van ExpressRoute Global Reach-peering met een privécloud in een Azure VMware Solution.

> [!div class="nextstepaction"]
> [On-premises omgevingen peeren met een privécloud](tutorial-expressroute-global-reach-private-cloud.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
