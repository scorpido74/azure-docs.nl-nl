---
title: Zelfstudie - On-premises omgevingen peeren met een privécloud
description: Meer informatie over hoe u ExpressRoute Global Reach-peering maakt met een privécloud in een Azure VMware Solution.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 07b7e1c2636f3754eda56af574586a1027403d3e
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576724"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Zelfstudie: On-premises omgevingen peeren met een privécloud

ExpressRoute Global Reach verbindt uw on-premises omgeving verbindt met uw Azure VMware Solution-privécloud. De ExpressRoute-Global Reach-verbinding wordt tot stand gebracht tussen de ExpressRoute-circuit in de privécloud en een bestaande ExpressRoute-verbinding met uw on-premises omgevingen. 

Het ExpressRoute-circuit dat u gebruikt wanneer u [een netwerkverbinding tussen Azure en een privécloud configureert](tutorial-configure-networking.md), vereist dat u autorisatiesleutels maakt en gebruikt wanneer u peert met ExpressRoute-gateways of met andere ExpressRoute-circuits met behulp van Global Reach. U hebt al een autorisatiesleutel uit het ExpressRoute-circuit gebruikt en in deze zelfstudie gaat u een tweede maken om te peeren met uw on-premises ExpressRoute-circuit.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een tweede autorisatiesleutel maken voor _circuit 2_, het ExpressRoute-circuit van de privécloud
> * Gebruik de [Azure Portal](#azure-portal-method) of de [Azure CLI gebruiken in een Cloud Shell-methode](#azure-cli-in-a-cloud-shell-method) in het abonnement van _circuit 1_ om peering van on-premises naar een privécloud van ExpressRoute Global Reach in te schakelen


## <a name="before-you-begin"></a>Voordat u begint

Raadpleeg de documentatie over het [inschakelen van connectiviteit in verschillende Azure-abonnementen](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions) voordat u de connectiviteit tussen twee ExpressRoute-circuits inschakelt met behulp van ExpressRoute Global Reach.  


## <a name="prerequisites"></a>Vereisten

1. Vastgelegde connectiviteit naar en van een Azure VMware Solution-privécloud met een ExpressRoute-circuit die is gekoppeld aan een ExpressRoute-gateway in een virtueel Azure-netwerk (VNet). Dit is _circuit 2_ vanuit procedures voor peering.  
1. Een afzonderlijk, functionerend ExpressRoute-circuit dat wordt gebruikt om on-premises omgevingen te verbinden met Azure. Dit is _circuit 1_ vanuit het perspectief van de procedures voor peering.
1. Een/29 niet-overlappend [blok met netwerkadressen](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) voor de ExpressRoute Global Reach-peering.

> [!TIP]
> In de context van deze vereisten is uw on-premises ExpressRoute-circuit _circuit 1_ en het ExpressRoute-circuit van de privécloud in een ander abonnement _circuit 2_. 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>Een ExpressRoute-autorisatiesleutel maken in de privécloud

1. Selecteer in de privécloud **Overzicht**onder Beheren, selecteer **Connectiviteit > ExpressRoute > Een machtigingssleutel aanvragen**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Selecteer Connectiviteit > ExpressRoute > Een machtigingssleutel aanvragen om een nieuwe aanvraag te starten.":::

2. Voer de naam in voor de autorisatiesleutel en selecteer **Maken**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Selecteer Connectiviteit > ExpressRoute > Een machtigingssleutel aanvragen om een nieuwe aanvraag te starten.":::

   Zodra de nieuwe sleutel is gemaakt, wordt deze weergegeven in de lijst met autorisatiesleutels voor de privécloud. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Selecteer Connectiviteit > ExpressRoute > Een machtigingssleutel aanvragen om een nieuwe aanvraag te starten.":::

3. Noteer de autorisatiesleutel en de ExpressRoute-id, evenals het /29-adresblok. U hebt deze nodig in de volgende stap om de peering te voltooien. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Privécloud peeren met on-premises met behulp van autorisatiesleutel

U beschikt nu over een autorisatiesleutel voor het ExpressRoute-circuit van de privécloud en kunt het circuit dus peeren met uw on-premises ExpressRoute-circuit.  De peering wordt uitgevoerd vanuit het perspectief van een on-premises ExpressRoute-circuit in de [Azure Portal](#azure-portal-method) of met de [Azure CLI in een Cloud Shell](#azure-cli-in-a-cloud-shell-method). Bij beide methoden gebruikt u de resource-id en autorisatiesleutel van het ExpressRoute-circuit van uw privécloud die u in de vorige stappen hebt gemaakt om de peering af te ronden.

### <a name="azure-portal-method"></a>Azure Portal-methode

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met hetzelfde abonnement als dat van het on-premises ExpressRoute-circuit.

1. Selecteer in de privécloud **Overzicht**onder Beheren, selecteer **Connectiviteit > ExpressRoute > Global Reach > Toevoegen**.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Selecteer Connectiviteit > ExpressRoute > Een machtigingssleutel aanvragen om een nieuwe aanvraag te starten.":::

1. U kunt een on-premises cloudverbinding maken door een van de volgende dingen te doen:

   - Selecteer het ExpressRoute-circuit uit de lijst.
   - Als u een circuit-id hebt, kopieert en plakt u die.

1. Selecteer **Verbinding maken**. De nieuwe verbinding wordt weergegeven in de lijst on-premises cloudverbindingen.  

>[!TIP]
>U kunt een verbinding verwijderen of verbreken uit de lijst door **Meer** te selecteren.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Selecteer Connectiviteit > ExpressRoute > Een machtigingssleutel aanvragen om een nieuwe aanvraag te starten.":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Azure CLI in een Cloud Shell-methode

De [CLI-opdrachten](../expressroute/expressroute-howto-set-global-reach-cli.md) zijn uitgebreid met specifieke details en voorbeelden om u te helpen bij de configuratie van ExpressRoute Global Reach-peering tussen on-premises omgevingen en een privécloud van Azure VMware Solution.  

> [!TIP]  
> Om de uitvoer van de Azure CLI-opdrachten overzichtelijk te houden, kunnen deze instructies een [`–query`-argument gebruiken om een JMESPath-query uit te voeren, zodat alleen de vereiste resultaten worden weergegeven](https://docs.microsoft.com/cli/azure/query-azure-cli).


1. Meld u aan bij de Azure-portal met hetzelfde abonnement als dat van het on-premises ExpressRoute-circuit en open een Cloud Shell. Laat de shell op Bash staan.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Selecteer Connectiviteit > ExpressRoute > Een machtigingssleutel aanvragen om een nieuwe aanvraag te starten.":::.
 
2. Voer op de opdrachtregel de Azure CLI-opdracht in om de peering te maken. Gebruik hierbij uw specifieke gegevens en resource-id, autorisatiesleutel en /29 CIDR-netwerkblok. 

   Hier volgt een voorbeeld van de opdracht die u gaat gebruiken en de uitvoer die aangeeft dat de peering is gelukt. De voorbeeldopdracht is gebaseerd op de opdracht die wordt gebruikt in [stap 3 van Connectiviteit inschakelen tussen ExpressRoute-circuits in verschillende Azure-abonnementen](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Selecteer Connectiviteit > ExpressRoute > Een machtigingssleutel aanvragen om een nieuwe aanvraag te starten.":::
 
   U moet nu via de ExpressRoute-Global Reach-peering vanaf on-premises omgevingen verbinding kunnen maken met uw privécloud.

> [!TIP]
> U kunt de peering die u zojuist hebt gemaakt, verwijderen met behulp van de instructies in [Connectiviteit tussen uw on-premises netwerken uitschakelen](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks).


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een tweede verificatiesleutel maakt voor het ExpressRoute-circuit in uw privécloud en hoe u de ExpressRoute Global Reach-peering van on-premises naar privécloud inschakelt. 

Ga verder met de volgende zelfstudie voor meer informatie over hoe u VMware HCX-oplossing implementeert en configureert voor uw Azure VMware Solution-privécloud.

> [!div class="nextstepaction"]
> [VMware HCX implementeren en configureren](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
