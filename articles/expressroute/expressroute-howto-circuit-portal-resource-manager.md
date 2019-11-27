---
title: Zelf studie-een circuit maken en wijzigen met ExpressRoute
description: In deze zelf studie leert u hoe u een ExpressRoute-circuit maakt, inricht, controleert, bijwerkt en verwijdert.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 7327031a7cd05674e9823f21601aab34c859f540
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423563"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Zelf studie: een ExpressRoute-circuit maken en wijzigen

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sjabloon](expressroute-howto-circuit-resource-manager-template.md)
> * [Video-Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-circuit-classic.md)
>

Dit artikel helpt u bij het maken van een ExpressRoute-circuit met behulp van de Azure-portal en het Azure Resource Manager-implementatiemodel. U kunt ook de status controleren, bijwerken, verwijderen of de inrichting van een circuit ongedaan maken.

## <a name="before-you-begin"></a>Voordat u begint

* Controleer de [vereisten](expressroute-prerequisites.md) en [werk stromen](expressroute-workflows.md) voordat u begint met de configuratie.
* Zorg ervoor dat u toegang hebt tot de [Azure Portal](https://portal.azure.com).
* Zorg ervoor dat u gemachtigd bent om te maken van nieuwe netwerkresources. Neem contact op met uw accountbeheerder als u nog niet de juiste machtigingen hebt.
* U kunt [een video bekijken](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) voordat u begint om de stappen beter te begrijpen.

## <a name="create"></a>Een ExpressRoute-circuit maken en inrichten

### <a name="1-sign-in-to-the-azure-portal"></a>1. Meld u aan bij de Azure Portal

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

### <a name="2-create-a-new-expressroute-circuit"></a>2. een nieuw ExpressRoute-circuit maken

> [!IMPORTANT]
> Uw ExpressRoute-circuit wordt in rekening gebracht vanaf het moment dat een servicesleutel wordt uitgegeven. Zorg ervoor dat u deze bewerking uitvoeren wanneer de connectiviteitsprovider gereed is voor de inrichting van het circuit.

1. U kunt een ExpressRoute-circuit maken door de optie om een nieuwe resource te maken. Klik op **een resource maken** > **netwerk** > **ExpressRoute**, zoals wordt weer gegeven in de volgende afbeelding:

   ![Een ExpressRoute-circuit maken](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Nadat u op **ExpressRoute**hebt geklikt, wordt de pagina **ExpressRoute-circuit maken** weer geven. Wanneer u de waarden op deze pagina hebt ingevuld, zorg ervoor dat u de juiste SKU-laag (Standard of Premium) en de gegevens voor het meten van facturering van maandabonnementen (onbeperkt of Metered) opgeeft.

   ![De SKU-laag en voor gegevensmeters configureren](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * De **laag** bepaalt of een ExpressRoute-standaard of een ExpressRoute Premium-invoeg toepassing is ingeschakeld. U kunt **Standard** opgeven om de standaard SKU of **Premium** voor de Premium-invoeg toepassing op te halen.
   * De **gegevens meting** bepaalt het facturerings type. U **kunt voor een** onbeperkt data-abonnement een **gemeten** data-abonnement opgeven. Houd er rekening mee dat u het facturerings type kunt wijzigen van **gemeten** naar **onbeperkt**.

     > [!IMPORTANT]
     > U kunt het type niet wijzigen van **Unlimited** in **Data limiet**.

   * De **locatie van peering** is de fysieke locatie waar u met micro soft bent gepeerd.

     > [!IMPORTANT]
     > De locatie van de peering geeft de [fysieke locatie](expressroute-locations.md) aan waar u met micro soft bent gepeerd. Deze is **niet** gekoppeld aan de eigenschap Location, die verwijst naar de geografische locatie waar de Azure-netwerk resource provider zich bevindt. Hoewel ze niet zijn gerelateerd, is het raadzaam een Netwerkresourceprovider geografisch dicht bij de locatie-Peering van het circuit kiezen.

### <a name="3-view-the-circuits-and-properties"></a>3. de circuits en eigenschappen weer geven

**Alle circuits weer geven**

U kunt alle gemaakte circuits weer geven door **alle resources** in het menu aan de linkerkant te selecteren.

![Weergave-circuits](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**De eigenschappen weer geven**

U kunt de eigenschappen van het circuit weergeven door deze te selecteren. Op de **overzichts** pagina voor uw circuit wordt de service sleutel weer gegeven in het veld Service sleutel. U moet de servicesleutel kopiëren voor uw circuit en geven deze omlaag naar de service-provider om het inrichtingsproces te voltooien. De circuitservicesleutel is specifiek voor uw circuit.

![Eigenschappen weergeven](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Stuur de service sleutel naar uw connectiviteits provider voor het inrichten

Op deze pagina biedt de **provider status** informatie over de huidige status van de inrichting van de service provider. **Status van circuit** geeft de status aan de kant van micro soft. Zie het artikel [workflows](expressroute-workflows.md#expressroute-circuit-provisioning-states) voor meer informatie over de inrichtings status van circuits.

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

Hier vindt u de eigenschappen van het circuit waarin u geïnteresseerd bent door deze te selecteren. Controleer de **provider status** en zorg ervoor dat deze is verplaatst naar **ingericht** voordat u doorgaat.

![Status van het circuit en -provider](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. uw routerings configuratie maken

Raadpleeg het artikel [ExpressRoute circuit routerings configuratie](expressroute-howto-routing-portal-resource-manager.md) voor het maken en wijzigen van circuit peerings voor stapsgewijze instructies.

> [!IMPORTANT]
> Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services op laag 2-connectiviteit aanbieden. Als u een serviceprovider die beheerde laag-3-services (meestal een IP VPN, zoals MPLS), uw connectiviteitsprovider configureert en beheert routering voor u.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. een virtueel netwerk koppelen aan een ExpressRoute-circuit

Vervolgens moet u een virtueel netwerk koppelen aan uw ExpressRoute-circuit. Gebruik het artikel [koppel virtuele netwerken aan ExpressRoute-circuits](expressroute-howto-linkvnet-arm.md) wanneer u werkt met het Resource Manager-implementatie model.

## <a name="status"></a>De status van een ExpressRoute-circuit ophalen

U kunt de status van een circuit weergeven door te selecteren en weergeven van de pagina overzicht.

## <a name="modify"></a>Een ExpressRoute-circuit wijzigen

U kunt bepaalde eigenschappen van een ExpressRoute-circuit wijzigen zonder gevolgen voor connectiviteit. U kunt de band breedte, het SKU-, facturerings model wijzigen en klassieke bewerkingen op de **configuratie** pagina toestaan. Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor meer informatie over limieten en beperkingen.

U kunt de volgende taken zonder uitvaltijd uitvoeren:

* In- of uitschakelen van een ExpressRoute Premium-invoegtoepassing voor uw ExpressRoute-circuit.
* Vergroot de bandbreedte van uw ExpressRoute-circuit, mits er capaciteit beschikbaar is op de poort.

  > [!IMPORTANT]
  > Het downgraden van de bandbreedte van een circuit wordt niet ondersteund.

* Wijzig het licentie controle plan van *gegevens* met data limiet naar *onbeperkte gegevens*.

  > [!IMPORTANT]
  > Wijzigen van de softwarelicentiecontrole plan van onbeperkte gegevens in naar gebruik wordt niet ondersteund.

* U kunt *klassieke bewerkingen toestaan*in-en uitschakelen.
  > [!IMPORTANT]
  > U moet de ExpressRoute-circuit opnieuw te maken als er onvoldoende capaciteit op de bestaande poort. U kunt het circuit niet bijwerken als er geen extra capaciteit beschikbaar is op die locatie.
  >
  > Hoewel u de bandbreedte naadloos upgraden, kunt u de bandbreedte van een ExpressRoute-circuit zonder onderbreking niet reduceren. Het downgraden van bandbreedte, moet u de inrichting van het ExpressRoute-circuit ongedaan maken en vervolgens opnieuw inrichten van een nieuwe ExpressRoute-circuit.
  >
  > Uitschakelen van de Premium-Add-on-bewerking kan mislukken als u resources die groter zijn dan wat is toegestaan voor de standard-circuit.

Als u een ExpressRoute-circuit wilt wijzigen, klikt u op **configuratie**.

![Circuit wijzigen](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

## <a name="delete"></a>Inrichting en verwijdering van een ExpressRoute-circuit ongedaan maken

U kunt het ExpressRoute-circuit verwijderen door het pictogram **verwijderen** te selecteren. Let op de volgende informatie:

* U moet alle virtuele netwerken loskoppelen van het ExpressRoute-circuit. Als deze bewerking is mislukt, controleert u of alle virtuele netwerken zijn gekoppeld aan het circuit.
* Als de inrichtings status van de ExpressRoute-circuit service provider is **ingericht** of **ingericht** , moet u samen met uw service provider de inrichting van het circuit aan hun zijde opheffen. We blijven reserveer resources en factureren u totdat de service-provider is voltooid ongedaan maken van inrichting van het circuit en ons wordt geïnformeerd.
* Als de service provider het circuit heeft ontsteld (de inrichtings status van de service provider is ingesteld op **niet ingericht**), kunt u het circuit verwijderen. Hiermee wordt ook de facturering voor het circuit gestopt.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw circuit hebt gemaakt, kunt u doorgaan met de volgende stappen uitvoeren:

* [Route ring voor uw ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-portal-resource-manager.md)
* [Uw virtuele netwerk koppelen aan uw ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
