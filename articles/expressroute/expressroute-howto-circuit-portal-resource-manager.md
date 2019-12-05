---
title: Zelf studie-een circuit maken en wijzigen met ExpressRoute
description: In deze zelf studie leert u hoe u een ExpressRoute-circuit maakt, inricht, controleert, bijwerkt en verwijdert.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 686ac8013879eff8adc4476d56119bbb4a169900
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74813143"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Zelf studie: een ExpressRoute-circuit maken en wijzigen

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sjabloon](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-circuit-classic.md)
>

Dit artikel helpt u bij het maken van een ExpressRoute-circuit met behulp van de Azure-portal en het Azure Resource Manager-implementatiemodel. U kunt ook de status controleren, bijwerken, verwijderen of de inrichting van een circuit ongedaan maken.

## <a name="before-you-begin"></a>Voordat u begint

* Controleer de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.
* Zorg ervoor dat u toegang tot hebt de [Azure-portal](https://portal.azure.com).
* Zorg ervoor dat u gemachtigd bent om te maken van nieuwe netwerkresources. Neem contact op met uw accountbeheerder als u nog niet de juiste machtigingen hebt.
* U kunt [een video bekijken](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) voordat u begint om beter te begrijpen van de stappen.

## <a name="create"></a>Maken en inrichten van een ExpressRoute-circuit

### <a name="1-sign-in-to-the-azure-portal"></a>1. Meld u aan bij de Azure Portal

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

### <a name="2-create-a-new-expressroute-circuit"></a>2. een nieuw ExpressRoute-circuit maken

> [!IMPORTANT]
> Uw ExpressRoute-circuit wordt in rekening gebracht vanaf het moment dat een servicesleutel wordt uitgegeven. Zorg ervoor dat u deze bewerking uitvoeren wanneer de connectiviteitsprovider gereed is voor de inrichting van het circuit.

U kunt een ExpressRoute-circuit maken door de optie om een nieuwe resource te maken. 

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**. Selecteer **netwerk** > **ExpressRoute**, zoals wordt weer gegeven in de volgende afbeelding:

   ![Een ExpressRoute-circuit maken](./media/expressroute-howto-circuit-portal-resource-manager/create-an-expressroute-circuit.png)

2. Nadat u op **ExpressRoute**, ziet u de **maken ExpressRoute-circuit** pagina. Wanneer u de waarden op deze pagina hebt ingevuld, zorg ervoor dat u de juiste SKU-laag (Standard of Premium) en de gegevens voor het meten van facturering van maandabonnementen (onbeperkt of Metered) opgeeft.

   ![De SKU-laag en voor gegevensmeters configureren](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **Laag** bepaalt of een ExpressRoute-standard- of een premium-invoegtoepassing voor ExpressRoute is ingeschakeld. U kunt opgeven **Standard** om op te halen van de standaard-SKU of **Premium** voor de premium-invoegtoepassing.
   * **Gegevensmeters** bepaalt het gegevenstype van de facturering. U kunt opgeven **Metered** voor een abonnement naar gebruik en **onbeperkt** voor een abonnement voor onbeperkt gebruik. Houd er rekening mee dat u het facturerings type kunt wijzigen van **gemeten** naar **onbeperkt**.

     > [!IMPORTANT]
     > U kunt het type niet wijzigen van **Unlimited** in **Data limiet**.

   * **Peeringlocatie** is de fysieke locatie waar u peering met Microsoft.

     > [!IMPORTANT]
     > De Peeringlocatie geeft aan dat de [fysieke locatie](expressroute-locations.md) waar u peering met Microsoft. Dit is **niet** gekoppeld aan "Locatie" eigenschap, die verwijst naar de geografische locatie waar de Resourceprovider van de Azure-netwerk zich bevindt. Hoewel ze niet zijn gerelateerd, is het raadzaam een Netwerkresourceprovider geografisch dicht bij de locatie-Peering van het circuit kiezen.

### <a name="3-view-the-circuits-and-properties"></a>3. de circuits en eigenschappen weer geven

**Alle circuits weergeven**

U vindt alle circuits die u hebt gemaakt met het selecteren van **alle resources** in het menu aan de linkerkant.

![Weergave-circuits](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**De eigenschappen weergeven**

U kunt de eigenschappen van het circuit weergeven door deze te selecteren. Op de **overzicht** pagina voor uw circuit de servicesleutel wordt weergegeven in het veld van de service-sleutel. U moet de servicesleutel kopiëren voor uw circuit en geven deze omlaag naar de service-provider om het inrichtingsproces te voltooien. De circuitservicesleutel is specifiek voor uw circuit.

![Eigenschappen weergeven](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Stuur de service sleutel naar uw connectiviteits provider voor het inrichten

Op deze pagina **providerstatus** bevat informatie over de huidige status van inrichting aan de service-provider. **Status van circuit** biedt u de status aan de kant van Microsoft. Zie voor meer informatie over statussen inrichting circuit, de [werkstromen](expressroute-workflows.md#expressroute-circuit-provisioning-states) artikel.

Wanneer u een nieuwe ExpressRoute-circuit maakt, wordt het circuit is in de volgende status:

De status van provider: niet ingericht<BR>
Circuit status: ingeschakeld

![Inrichtingsproces initiëren](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Het circuit gewijzigd in de volgende status hebben de connectiviteitsprovider Bezig inschakelen voor u is:

De status van provider: inrichten<BR>
Circuit status: ingeschakeld

Voordat u kunt een ExpressRoute-circuit gebruiken, moet deze de status van de volgende zijn:

De status van provider: ingericht<BR>
Circuit status: ingeschakeld

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Controleer de status en de status van de circuit sleutel regel matig

Hier vindt u de eigenschappen van het circuit waarin u geïnteresseerd bent door deze te selecteren. Controleer de **providerstatus** en zorg ervoor dat het is verplaatst naar **ingerichte** voordat u doorgaat.

![Status van het circuit en -provider](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. uw routerings configuratie maken

Voor stapsgewijze instructies raadpleegt u de [ExpressRoute-circuit routeringsconfiguratie](expressroute-howto-routing-portal-resource-manager.md) artikel maken en aanpassen-circuitpeerings.

> [!IMPORTANT]
> Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services op laag 2-connectiviteit aanbieden. Als u een serviceprovider die beheerde laag-3-services (meestal een IP VPN, zoals MPLS), uw connectiviteitsprovider configureert en beheert routering voor u.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. een virtueel netwerk koppelen aan een ExpressRoute-circuit

Vervolgens moet u een virtueel netwerk koppelen aan uw ExpressRoute-circuit. Gebruik de [virtuele netwerken koppelen aan ExpressRoute-circuits](expressroute-howto-linkvnet-arm.md) artikel als u met het Resource Manager-implementatiemodel werkt.

## <a name="status"></a>Ophalen van de status van een ExpressRoute-circuit

U kunt de status van een circuit weergeven door te selecteren en weergeven van de pagina overzicht.

## <a name="modify"></a>Een ExpressRoute-circuit wijzigen

U kunt bepaalde eigenschappen van een ExpressRoute-circuit wijzigen zonder gevolgen voor connectiviteit. U kunt het factureringsmodel van bandbreedte, SKU, wijzigen en klassieke bewerkingen toestaan op de **configuratie** pagina. Zie voor informatie over limieten en beperkingen, de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md).

U kunt de volgende taken zonder uitvaltijd uitvoeren:

* In- of uitschakelen van een ExpressRoute Premium-invoegtoepassing voor uw ExpressRoute-circuit.
* Vergroot de bandbreedte van uw ExpressRoute-circuit, mits er capaciteit beschikbaar is op de poort.

  > [!IMPORTANT]
  > Het downgraden van de bandbreedte van een circuit wordt niet ondersteund.

* Wijzigen van de softwarelicentiecontrole plan is uit de *naar gebruik* naar *onbeperkte gegevens*.

  > [!IMPORTANT]
  > Wijzigen van de softwarelicentiecontrole plan van onbeperkte gegevens in naar gebruik wordt niet ondersteund.

* U kunt inschakelen en uitschakelen *klassieke bewerkingen toestaan*.
  > [!IMPORTANT]
  > U moet de ExpressRoute-circuit opnieuw te maken als er onvoldoende capaciteit op de bestaande poort. U kunt het circuit niet bijwerken als er geen extra capaciteit beschikbaar is op die locatie.
  >
  > Hoewel u de bandbreedte naadloos upgraden, kunt u de bandbreedte van een ExpressRoute-circuit zonder onderbreking niet reduceren. Het downgraden van bandbreedte, moet u de inrichting van het ExpressRoute-circuit ongedaan maken en vervolgens opnieuw inrichten van een nieuwe ExpressRoute-circuit.
  >
  > Uitschakelen van de Premium-Add-on-bewerking kan mislukken als u resources die groter zijn dan wat is toegestaan voor de standard-circuit.

Als u wilt een ExpressRoute-circuit wijzigen, klikt u op **configuratie**.

![Circuit wijzigen](./media/expressroute-howto-circuit-portal-resource-manager/modify-circuit-configuration.png)

## <a name="delete"></a>Opheffen van inrichting en het verwijderen van een ExpressRoute-circuit

U kunt uw ExpressRoute-circuit verwijderen door het selecteren van de **verwijderen** pictogram. Let op de volgende informatie:

* U moet alle virtuele netwerken loskoppelen van het ExpressRoute-circuit. Als deze bewerking is mislukt, controleert u of alle virtuele netwerken zijn gekoppeld aan het circuit.
* Als het ExpressRoute-circuit serviceprovider-Inrichtingsstatus **Provisioning** of **ingerichte** moet u werken met uw serviceprovider inrichting ongedaan maken van het circuit aan hun kant. We blijven reserveer resources en factureren u totdat de service-provider is voltooid ongedaan maken van inrichting van het circuit en ons wordt geïnformeerd.
* Als de service-provider de inrichting van het circuit is beëindigd (de serviceprovider Inrichtingsstatus is ingesteld op **niet ingericht**), kunt u het circuit verwijderen. Hiermee wordt ook de facturering voor het circuit gestopt.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw circuit hebt gemaakt, kunt u doorgaan met de volgende stappen uitvoeren:

* [Routering voor uw ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-portal-resource-manager.md)
* [Uw virtueel netwerk koppelen aan uw ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
