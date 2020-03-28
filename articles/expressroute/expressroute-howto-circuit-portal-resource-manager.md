---
title: Zelfstudie - Een circuit maken en wijzigen met ExpressRoute
description: Leer in deze zelfstudie hoe u een ExpressRoute-circuit maakt, indient, verifieert, bijwerkt, verwijdert en deprovisioneert.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 686ac8013879eff8adc4476d56119bbb4a169900
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74813143"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Zelfstudie: Een ExpressRoute-circuit maken en wijzigen

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Azure-CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sjabloon](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-circuit-classic.md)
>

Met dit artikel u een ExpressRoute-circuit maken met behulp van de Azure-portal en het Azure Resource Manager-implementatiemodel. U ook de status controleren, een circuit bijwerken, verwijderen of deprovisionen.

## <a name="before-you-begin"></a>Voordat u begint

* Bekijk de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u met de configuratie begint.
* Zorg ervoor dat u toegang hebt tot de [Azure-portal.](https://portal.azure.com)
* Zorg ervoor dat u machtigingen hebt om nieuwe netwerkbronnen te maken. Neem contact op met uw accountbeheerder als u niet over de juiste machtigingen beschikt.
* U [een video bekijken](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) voordat u begint om de stappen beter te begrijpen.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Een ExpressRoute-circuit maken en inrichten

### <a name="1-sign-in-to-the-azure-portal"></a>1. Meld u aan bij de Azure-portal

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Maak een nieuw ExpressRoute circuit

> [!IMPORTANT]
> Uw ExpressRoute-circuit wordt gefactureerd vanaf het moment dat een servicesleutel wordt uitgegeven. Zorg ervoor dat u deze bewerking uitvoert wanneer de connectiviteitsprovider klaar is om het circuit in te richten.

U een ExpressRoute-circuit maken door de optie te selecteren om een nieuwe bron te maken. 

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**. Selecteer > **Netwerkexpressroute,** zoals weergegeven in de volgende afbeelding: **Networking**

   ![Een ExpressRoute-circuit maken](./media/expressroute-howto-circuit-portal-resource-manager/create-an-expressroute-circuit.png)

2. Nadat u op **ExpressRoute**hebt geklikt, ziet u de **circuitpagina ExpressRoute maken.** Wanneer u de waarden op deze pagina invult, moet u de juiste SKU-laag (Standaard of Premium) en het factureringsmodel voor gegevensmeting (Onbeperkt of gemeten) opgeven.

   ![De SKU-laag en gegevensmeting configureren](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **Tier** bepaalt of een ExpressRoute-standaard of een ExpressRoute premium add-on is ingeschakeld. U **Standaard** opgeven om de standaard SKU of **Premium** voor de premium-add-on te krijgen.
   * **Gegevensmeting** bepaalt het factureringstype. U **Gemeten** opgeven voor een data-abonnement met datamet **ingemetert** en Onbeperkt voor een onbeperkt data-abonnement. Houd er rekening mee dat u het factureringstype wijzigen van **Gemeten** naar **Onbeperkt**.

     > [!IMPORTANT]
     > U het type niet wijzigen van **Onbeperkt** naar **Gemeten.**

   * **Peering-locatie** is de fysieke locatie waar u peering met Microsoft.

     > [!IMPORTANT]
     > De peeringlocatie geeft de [fysieke locatie](expressroute-locations.md) aan waar u peert met Microsoft. Dit is **niet** gekoppeld aan de eigenschap Locatie, die verwijst naar de geografie waar de Azure Network Resource Provider zich bevindt. Hoewel ze niet gerelateerd zijn, is het een goede gewoonte om een Network Resource Provider te kiezen die geografisch dicht bij de peeringlocatie van het circuit ligt.

### <a name="3-view-the-circuits-and-properties"></a>3. Bekijk de circuits en eigenschappen

**Bekijk alle circuits**

U alle circuits bekijken die u hebt gemaakt door **Alle bronnen** in het linkermenu te selecteren.

![Circuits bekijken](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Bekijk de eigenschappen**

U de eigenschappen van het circuit bekijken door het te selecteren. Op de **overzichtspagina** voor uw circuit wordt de servicesleutel weergegeven in het veld servicesleutel. U moet de servicesleutel voor uw circuit kopiëren en doorgeven aan de serviceprovider om het inrichtingsproces te voltooien. De circuitservicesleutel is specifiek voor uw circuit.

![Eigenschappen weergeven](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Stuur de servicesleutel naar uw connectiviteitsprovider voor inlevering

Op deze pagina geeft **de status van provider** informatie over de huidige stand van de inrichting aan de kant van de serviceprovider. **Circuitstatus** geeft de status aan de Microsoft-kant. Zie het artikel [Werkstromen](expressroute-workflows.md#expressroute-circuit-provisioning-states) voor meer informatie over de inrichten van circuits.

Wanneer u een nieuw ExpressRoute-circuit maakt, bevindt het circuit zich in de volgende staat:

Providerstatus: niet ingericht<BR>
Circuitstatus: ingeschakeld

![Inrichten starten](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Het circuit verandert in de volgende status wanneer de connectiviteitsprovider het voor u inschakelt:

Providerstatus: Inrichting<BR>
Circuitstatus: ingeschakeld

Om een ExpressRoute-circuit te kunnen gebruiken, moet het in de volgende staat zijn:

Providerstatus: ingericht<BR>
Circuitstatus: ingeschakeld

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Controleer periodiek de status en de status van de circuitsleutel

U de eigenschappen van het circuit waarin u geïnteresseerd bent bekijken door het te selecteren. Controleer de **status van provider** en zorg ervoor dat deze is verplaatst naar **Ingericht** voordat u verdergaat.

![Status van circuit en provider](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Uw routeringsconfiguratie maken

Raadpleeg voor stapsgewijze instructies het artikel over de [routeringsconfiguratie van het ExpressRoute-circuit](expressroute-howto-routing-portal-resource-manager.md) om circuitpeeringen te maken en te wijzigen.

> [!IMPORTANT]
> Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die layer 2-connectiviteitsservices aanbieden. Als u een serviceprovider gebruikt die beheerde laag 3-services aanbiedt (meestal een IP VPN, zoals MPLS), configureert en beheert uw connectiviteitsprovider routering voor u.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Koppel een virtueel netwerk aan een ExpressRoute-circuit

Koppel vervolgens een virtueel netwerk aan uw ExpressRoute-circuit. Gebruik het artikel [Virtuele netwerken koppelen aan expressroute-circuits](expressroute-howto-linkvnet-arm.md) wanneer u met het implementatiemodel Resource Manager werkt.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>De status van een ExpressRoute-circuit verkrijgen

U de status van een circuit bekijken door deze te selecteren en de pagina Overzicht te bekijken.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Een ExpressRoute-circuit wijzigen

U bepaalde eigenschappen van een ExpressRoute-circuit wijzigen zonder dat dit gevolgen heeft voor de connectiviteit. U de bandbreedte, SKU, factureringsmodel wijzigen en klassieke bewerkingen toestaan op de **pagina Configuratie.** Zie de [veelgestelde vragen over ExpressRoute voor](expressroute-faqs.md)informatie over limieten en beperkingen.

U de volgende taken uitvoeren zonder downtime:

* Een ExpressRoute Premium-add-on voor uw ExpressRoute-circuit in- of uitschakelen.
* Verhoog de bandbreedte van uw ExpressRoute-circuit, mits er capaciteit beschikbaar is op de poort.

  > [!IMPORTANT]
  > Het verlagen van de bandbreedte van een circuit wordt niet ondersteund.

* Wijzig het meetplan van *gemeten gegevens* in *onbeperkte gegevens.*

  > [!IMPORTANT]
  > Het wijzigen van het meetplan van Unlimited Data naar Gemeten Data wordt niet ondersteund.

* U *Klassieke bewerkingen*toestaan en uitschakelen.
  > [!IMPORTANT]
  > Het kan zijn dat u het ExpressRoute-circuit opnieuw moet maken als er onvoldoende capaciteit is op de bestaande poort. U het circuit niet upgraden als er op die locatie geen extra capaciteit beschikbaar is.
  >
  > Hoewel u de bandbreedte naadloos upgraden, u de bandbreedte van een ExpressRoute-circuit niet zonder onderbreking verminderen. Voor het verlagen van de bandbreedte moet u het ExpressRoute-circuit deprovisioneren en vervolgens een nieuw ExpressRoute-circuit opnieuw inrichten.
  >
  > Het uitschakelen van de Premium-invoegtoepassing kan mislukken als u resources gebruikt die groter zijn dan wat is toegestaan voor het standaardcircuit.

Als u een ExpressRoute-circuit wilt wijzigen, klikt u op **Configuratie**.

![Circuit wijzigen](./media/expressroute-howto-circuit-portal-resource-manager/modify-circuit-configuration.png)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning and deleting an ExpressRoute circuit (Inrichting van een ExpressRoute-circuit ongedaan maken en het circuit verwijderen)

U uw ExpressRoute-circuit verwijderen door het **pictogram Verwijderen te** selecteren. Let op de volgende informatie:

* U moet alle virtuele netwerken loskoppelen van het ExpressRoute-circuit. Als deze bewerking mislukt, controleert u of virtuele netwerken zijn gekoppeld aan het circuit.
* Als de ExpressRoute-dienstverlener provisioning state is **Provisioning** or **Provisioned,** moet u samenwerken met uw serviceprovider om het circuit aan hun kant te deprovisioneren. We blijven resources reserveren en u factureren totdat de serviceprovider het circuit heeft voltooid en ons hiervan op de hoogte stelt.
* Als de serviceprovider het circuit heeft gedeprovisioneerd (de status van de serviceprovider is ingesteld op **Niet-ingericht),** u het circuit verwijderen. Hiermee wordt ook de facturering voor het circuit gestopt.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw circuit hebt gemaakt, gaat u verder met de volgende stappen:

* [Routering voor uw ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-portal-resource-manager.md)
* [Koppel uw virtuele netwerk aan uw ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
