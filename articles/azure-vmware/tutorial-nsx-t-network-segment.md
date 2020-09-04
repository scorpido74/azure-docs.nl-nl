---
title: 'Zelfstudie: Een NSX-T-netwerksegment maken in Azure VMware Solution'
description: In deze zelfstudie hebt u de NSX-T-netwerksegmenten gemaakt die worden gebruikt voor VM's in vCenter
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: cee65211cbef25ec029c68888bc8e6059f7c7896
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750458"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>Zelfstudie: Een NSX-T-netwerksegment maken in Azure VMware Solution

Netwerksegmenten die zijn gemaakt in NSX-T Manager worden gebruikt als netwerken voor virtuele machines (VM's) in vCenter. De VM's die in vCenter zijn gemaakt, worden geplaatst op de netwerksegmenten die zijn gemaakt in NSX-T en zijn zichtbaar in vCenter.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Navigeren in NSX-T Manager om netwerksegmenten toe te voegen
> * Een nieuw netwerksegment toevoegen
> * Het nieuwe netwerksegment observeren in vCenter

## <a name="prerequisites"></a>Vereisten

U hebt een privécloud van Azure VMware Solution met toegang tot de vCenter- en NSX-T Manager-beheerinterfaces nodig om deze zelfstudie te voltooien. Raadpleeg [Zelfstudie: Netwerken configureren voor uw VMware-privécloud in Azure](tutorial-configure-networking.md).

## <a name="provision-a-network-segment-in-nsx-t"></a>Een netwerksegment inrichten in NSX-T

1. Selecteer in vCenter **SDDC-Datacenter > Networks** voor uw privécloud. Zoals u kunt zien, zijn er nog geen netwerken.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="Selecteer in vCenter SDDC-Datacenter > Networks voor uw privécloud. Zoals u kunt zien, zijn er nog geen netwerken.":::

1. Selecteer in NSX-T Manager **Networking** voor uw privécloud.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="Selecteer in NSX-T Manager Networking voor uw privécloud.":::

1. Selecteer **Segments**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="Selecteer op de pagina Network Overview de optie Segments.":::

1. Selecteer op de overzichtspagina NSX-T Segments de optie**ADD SEGMENT**. Er worden als onderdeel van het inrichten van de privécloud drie segmenten gemaakt die kunnen worden gebruikt voor VM's.  U moet een nieuw netwerksegment voor dit doel toevoegen.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="Selecteer op de overzichtspagina van NSX-T Segments de optie ADD SEGMENT.":::

1. Geef een naam voor het segment op, kies de vooraf geconfigureerde Tier1-gateway (TNTxx-T1) als **Connected Gateway**, laat **Type** ingesteld op Flexible, kies de vooraf geconfigureerde overlay **Transport Zone** (TNTxx-OVERLAY-TZ) en selecteer vervolgens Set Subnets. Alle andere standaardinstellingen in deze sectie en de **POORTEN** en **SEGMENTPROFIELEN** blijven ongewijzigd.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="Stel de segmentnaam, de verbonden gateway, het type en de transportzone in en selecteer vervolgens Set Subnets.":::

1. Stel het IP-adres van de gateway in voor het nieuwe segment en selecteer vervolgens **TOEVOEGEN**. Het IP-adres dat u gebruikt, moet zich op een niet-overlappend RFC1918-adresblok bevinden, zodat u verbinding kunt maken met de VM's op het nieuwe segment.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="Stel het IP-adres van de gateway in voor het nieuwe segment en selecteer ADD.":::

1. Pas het nieuwe netwerksegment toe door **TOEPASSEN** te selecteren en de configuratie op te slaan met **OPSLAAN**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="Pas het nieuwe netwerksegment toe op de NSX-T-configuratie met APPLY.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="Sla het nieuwe netwerksegment op in de NSX-T-configuratie met SAVE.":::

1. Het nieuwe netwerksegment is nu gemaakt. U kunt de optie om door te gaan met het configureren van het segment afwijzen door **NO** te selecteren.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="U kunt weigeren om nieuwe netwerksegment verder te configureren door NO te selecteren.":::

1. Controleer of het nieuwe netwerksegment aanwezig is in NSX-T door **Netwerken > Segmenten** te selecteren en te controleren of het nieuwe segment wordt weergegeven in de lijst (in dit geval ls01).

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Controleer of het nieuwe netwerksegment aanwezig is in NSX-T.":::

1. Controleer of het nieuwe netwerksegment aanwezig is in vCenter door **Netwerken > SDDC-Data Center** te selecteren en te controleren of het nieuwe segment wordt weergegeven in de lijst (in dit geval ls01).

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Controleer of het nieuwe netwerksegment aanwezig is in vCenter.":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de NSX-T-netwerksegmenten gemaakt die worden gebruikt voor VM's in vCenter. U kunt nu [Zelfstudie: Een inhoudsbibliotheek maken voor het implementeren van VM's in Azure VMware Solution](tutorial-deploy-vm-content-library.md) gebruiken om een inhoudsbibliotheek in vCenter te maken en een VM in te richten op het netwerk dat u in deze zelfstudie hebt gemaakt.

<!-- LINKS - external-->

<!-- LINKS - internal -->
