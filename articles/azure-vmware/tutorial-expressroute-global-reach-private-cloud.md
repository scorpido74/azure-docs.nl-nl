---
title: On-premises omgevingen peeren met een privécloud
description: In deze zelfstudie over Azure VMware Solution gaat u ExpressRoute Global Reach-peering met een privécloud in Azure VMware Solution gemaakt.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: db3f5988cb8c07d9b6e80f500ac6aff8f96dfded
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750443"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Zelfstudie: On-premises omgevingen peeren met een privécloud

ExpressRoute Global Reach verbindt uw on-premises omgeving verbindt met uw privéclouds. De ExpressRoute-Global Reach-verbinding wordt tot stand gebracht tussen een ExpressRoute-circuit in de privécloud en een bestaande ExpressRoute-verbinding met uw on-premises omgevingen.  U krijgt instructies voor het configureren van ExpressRoute Global Reach met Azure CLI en PowerShell. Daarnaast zijn de [CLI-opdrachten](../expressroute/expressroute-howto-set-global-reach-cli.md) uitgebreid met specifieke details en voorbeelden om u te helpen bij de configuratie van ExpressRoute Global Reach-peering tussen on-premises omgevingen en een privécloud van Azure VMware Solution.   

Raadpleeg de documentatie over het [inschakelen van connectiviteit in verschillende Azure-abonnementen](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions) voordat u de connectiviteit tussen twee ExpressRoute-circuits inschakelt met behulp van ExpressRoute Global Reach.  Het ExpressRoute-circuit dat u gebruikt wanneer u [een netwerkverbinding tussen Azure en een privécloud configureert](tutorial-configure-networking.md), vereist dat u autorisatiesleutels maakt en gebruikt wanneer u peert met ExpressRoute-gateways of met andere ExpressRoute-circuits met behulp van Global Reach. U hebt al een autorisatiesleutel uit het ExpressRoute-circuit gebruikt en u gaat een tweede maken om te peeren met uw on-premises ExpressRoute-circuit.

> [!TIP]
> In de context van deze instructies is uw on-premises ExpressRoute-circuit _circuit 1_ en het ExpressRoute-circuit van de privécloud in een ander abonnement _circuit 2_. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De bestaande instructies volgen voor het beheren van ExpressRoute-circuits en ExpressRoute Global Reach-peerings
> * Een autorisatiesleutel maken voor _circuit 2_, het ExpressRoute-circuit van de privécloud
> * De Azure CLI gebruiken in een Cloud Shell in het abonnement van _circuit 1_ om peering van on-premises naar een privécloud van ExpressRoute Global Reach in te schakelen

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor deze zelfstudie:
- Een privécloud met een ExpressRoute-circuit die is gekoppeld aan een ExpressRoute-gateway in een virtueel Azure-netwerk (VNet). Dit is _circuit 2_ vanuit het perspectief van procedures voor peering.
- Een afzonderlijk, functionerend ExpressRoute-circuit dat wordt gebruikt om on-premises omgevingen te verbinden met Azure. Dit is _circuit 1_ vanuit het perspectief van de procedures voor peering.
- Een/29 niet-overlappend [blok met netwerkadressen](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) voor de ExpressRoute Global Reach-peering.

## <a name="create-an-expressroute-authorization-key-in-the-azure-vmware-solution-private-cloud"></a>Een ExpressRoute-autorisatiesleutel maken in de privécloud van Azure VMware Solution

1. Selecteer in de privécloud **Overzicht**onder Beheren, selecteer **Connectiviteit > ExpressRoute > Een machtigingssleutel aanvragen**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Selecteer Connectiviteit > ExpressRoute > Een machtigingssleutel aanvragen om een nieuwe aanvraag te starten.":::

2. Voer de naam in voor de autorisatiesleutel en selecteer **Maken**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Klik op Maken om een nieuwe autorisatiesleutel te maken. ":::

   Zodra de nieuwe sleutel is gemaakt, wordt deze weergegeven in de lijst met autorisatiesleutels voor de privécloud. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Controleer of de nieuwe autorisatiesleutel wordt weergegeven in de lijst met sleutels voor de privécloud. ":::

3. Noteer de autorisatiesleutel en de ExpressRoute-id, evenals het /29-adresblok. U hebt deze nodig in de volgende stap om de peering te voltooien. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Privécloud peeren met on-premises met behulp van autorisatiesleutel

U beschikt nu over een autorisatiesleutel voor het ExpressRoute-circuit van de privécloud en kunt het circuit dus peeren met uw on-premises ExpressRoute-circuit.  De peering wordt uitgevoerd vanuit het perspectief van het on-premises ExpressRoute-circuit met behulp van de Azure CLI in een Cloud Shell en een resource-id en autorisatiesleutel van het ExpressRoute-circuit van de privécloud, die u in de vorige stappen hebt gemaakt.

> [!TIP]  
> Om de uitvoer van de Azure CLI-opdrachten overzichtelijk te houden, kunnen deze instructies een [`–query`-argument gebruiken om een JMESPath-query uit te voeren, zodat alleen de vereiste resultaten worden weergegeven](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).


1. Meld u aan bij de Azure-portal met hetzelfde abonnement als dat van het on-premises ExpressRoute-circuit en open een Cloud Shell. Laat de shell op Bash staan.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Meld u aan bij de Azure-portal en open een Cloud Shell":::.
 
2. Voer op de opdrachtregel de Azure CLI-opdracht in om de peering te maken. Gebruik hierbij uw specifieke gegevens en resource-id, autorisatiesleutel en /29 CIDR-netwerkblok. 

   Hier volgt een voorbeeld van de opdracht die u gaat gebruiken en de uitvoer die aangeeft dat de peering is gelukt. De voorbeeldopdracht is gebaseerd op de opdracht die wordt gebruikt in [stap 3 van Connectiviteit inschakelen tussen ExpressRoute-circuits in verschillende Azure-abonnementen](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="ExpressRoute Global Reach-peering opzetten met een Azure CLI-opdracht in een Cloud Shell.":::
 
   U moet nu via de ExpressRoute-Global Reach-peering vanaf on-premises omgevingen verbinding kunnen maken met uw privécloud.

> [!TIP]
> U kunt de peering die u zojuist hebt gemaakt, verwijderen met behulp van de instructies in [Connectiviteit tussen uw on-premises netwerken uitschakelen](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks).


## <a name="next-steps"></a>Volgende stappen

Als u van plan bent een Hybrid Cloud Extension (HCX) te gebruiken om VM-workloads naar uw privécloud te migreren, gebruikt u de [HCX voor Azure VMware Solution installeren](hybrid-cloud-extension-installation.md).


<!-- LINKS - external-->

<!-- LINKS - internal -->
