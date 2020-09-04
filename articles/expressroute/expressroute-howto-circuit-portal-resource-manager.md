---
title: 'Zelfstudie: Een circuit maken en wijzigen met ExpressRoute'
description: In deze zelfstudie leert u hoe u een ExpressRoute-circuit kunt maken, inrichten, controleren, verwijderen en de inrichting ervan ongedaan kunt maken.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 686ac8013879eff8adc4476d56119bbb4a169900
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "74813143"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Zelfstudie: Een ExpressRoute-circuit maken en wijzigen

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure-CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sjabloon](expressroute-howto-circuit-resource-manager-template.md)
> * [Video: Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-circuit-classic.md)
>

Dit artikel helpt u bij het maken van een ExpressRoute-circuit met behulp van Azure Portal en het Azure Resource Manager-implementatiemodel. U kunt ook de status van een circuit controleren, een circuit bijwerken of verwijderen, of de inrichting ervan ongedaan maken.

## <a name="before-you-begin"></a>Voordat u begint

* Bekijk de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.
* Zorg ervoor dat u toegang hebt tot [Azure Portal](https://portal.azure.com).
* Zorg ervoor dat u bent gemachtigd om nieuwe netwerkresources te maken. Neem contact op met de accountbeheerder als u niet over de juiste machtigingen beschikt.
* U kunt [een video bekijken](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) voordat u begint om de stappen beter te begrijpen.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Een ExpressRoute-circuit maken en inrichten

### <a name="1-sign-in-to-the-azure-portal"></a>1. Aanmelden bij Azure Portal

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Een nieuw ExpressRoute-circuit maken

> [!IMPORTANT]
> Vanaf het moment dat een servicesleutel is uitgegeven, worden er kosten voor een ExpressRoute-circuit in rekening gebracht. Zorg ervoor dat u deze bewerking uitvoert wanneer de connectiviteitsprovider gereed is om het circuit in te richten.

U kunt een ExpressRoute-circuit maken door de optie voor het maken van een nieuwe resource te selecteren. 

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**. Selecteer **Netwerken** > **ExpressRoute**, zoals wordt weergegeven in de volgende afbeelding:

   ![Een ExpressRoute-circuit maken](./media/expressroute-howto-circuit-portal-resource-manager/create-an-expressroute-circuit.png)

2. Nadat u op **ExpressRoute** hebt geklikt, ziet u de pagina **ExpressRoute-circuit maken**. Wanneer u de waarden op deze pagina invult, moet u ervoor zorgen dat u de juiste SKU-laag (Standard of Premium) en het juiste factureringsmodel voor datameting (Onbeperkt of Naar gebruik) opgeeft.

   ![De SKU-laag en datameting configureren](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **Laag**: bepaalt of een Standard- of Premium-invoegtoepassing van ExpressRoute is ingeschakeld. U kunt **Standard** opgeven om de standaard SKU-invoegtoepassing te krijgen, of **Premium** voor de Premium-invoegtoepassing.
   * **Datameting**: bepaalt het factureringstype. U kunt **Naar gebruik** opgeven voor een data-abonnement naar gebruik, en **Onbeperkt** voor een onbeperkt data-abonnement. Houd er rekening mee dat u het factureringstype allen kunt wijzigen van **Naar gebruik** in **Onbeperkt**.

     > [!IMPORTANT]
     > U kunt het type niet wijzigen van **Onbeperkt** in **Naar gebruik**.

   * **Peeringlocatie** is de fysieke locatie vanwaaruit u koppelt met Microsoft.

     > [!IMPORTANT]
     > Peeringlocatie geeft de [fysieke locatie](expressroute-locations.md) aan vanwaaruit u koppelt met Microsoft. Dit is **niet** gekoppeld aan de eigenschap Location, die verwijst naar de geografische locatie waar de Azure-netwerkresourceprovider zich bevindt. Hoewel deze twee niet zijn gerelateerd, wordt wel aanbevolen een netwerkresourceprovider te kiezen die zich geografisch dicht bij de peeringlocatie van het circuit bevindt.

### <a name="3-view-the-circuits-and-properties"></a>3. De circuits en eigenschappen weergeven

**Alle circuits weergeven**

U kunt alle circuits bekijken die u hebt gemaakt, door in het menu aan de linkerkant **Alle resources** te selecteren.

![Circuits weergeven](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**De eigenschappen weergeven**

U kunt de eigenschappen van het circuit bekijken door het circuit te selecteren. Op de pagina **Overzicht** voor uw circuit wordt de servicesleutel weergegeven in het veld Servicesleutel. U moet de servicesleutel voor het circuit kopiëren en deze doorgeven aan de serviceprovider om het inrichtingsproces te voltooien. De servicesleutel voor het circuit is specifiek voor uw circuit.

![Eigenschappen weergeven](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. De servicesleutel voor inrichting verzenden naar de connectiviteitsprovider

Op deze pagina bevat **Providerstatus** informatie over de huidige status van de inrichting aan de zijde van de serviceprovider. **Circuitstatus** biedt de status aan de zijde van Microsoft. Zie het artikel [Werkstromen](expressroute-workflows.md#expressroute-circuit-provisioning-states) voor meer informatie over de inrichtingsstatussen van circuits.

Wanneer u een nieuw ExpressRoute-circuit maakt, heeft het circuit de volgende status:

Providerstatus: Niet ingericht<BR>
Circuitstatus: Ingeschakeld

![Inrichtingsproces initiëren](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Wanneer de connectiviteitsprovider bezig is met het inschakelen van uw circuit, verandert de status van het circuit in:

Providerstatus: Inrichten<BR>
Circuitstatus: Ingeschakeld

Als u een ExpressRoute-circuit wilt gebruiken, moet dit circuit de volgende status hebben:

Providerstatus: Ingericht<BR>
Circuitstatus: Ingeschakeld

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Controleer regelmatig de circuitstatus en de status van de circuitsleutel

U kunt de eigenschappen van het gewenste circuit bekijken door het circuit te selecteren. Controleer de **Providerstatus** en zorg ervoor dat deze is gewijzigd in **Ingericht** voordat u doorgaat.

![Circuit- en providerstatus](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. De routeringsconfiguratie maken

Raadpleeg voor stapsgewijze instructies het artikel [routeringsconfiguratie voor ExpressRoute-circuits](expressroute-howto-routing-portal-resource-manager.md) voor het maken en wijzigen van een circuitpeering.

> [!IMPORTANT]
> Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services met laag-2-connectiviteit aanbieden. Als u gebruikmaakt van een serviceprovider die beheerde laag-3-services aanbiedt (meestal een IP-adres voor VPN, zoals MPLS), wordt de routering voor u geconfigureerd en beheerd via de connectiviteitsprovider.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Een virtueel netwerk koppelen aan een ExpressRoute-circuit

Koppel vervolgens een virtueel netwerk aan een ExpressRoute-circuit. Gebruik het artikel [Virtuele netwerken koppelen aan ExpressRoute-circuits](expressroute-howto-linkvnet-arm.md) wanneer u werkt met het Resource Manager-implementatiemodel.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>De status van een ExpressRoute-circuit ophalen

U kunt de status van een circuit weergeven door het circuit te selecteren en de pagina Overzicht te bekijken.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Een ExpressRoute-circuit wijzigen

U kunt bepaalde eigenschappen van een ExpressRoute-circuit wijzigen zonder dat dit van invloed is op de connectiviteit. Op de pagina **Configuratie** kunt u bandbreedte, SKU en factureringsmodel wijzigen, en klassieke bewerkingen toestaan. Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor informatie over limieten en beperkingen.

U kunt de volgende taken uitvoeren zonder dat dit resulteert in downtime:

* Een ExpressRoute Premium-invoegtoepassing in- of uitschakelen voor uw ExpressRoute-circuit.
* De bandbreedte van uw ExpressRoute-circuit verhogen, mits er capaciteit beschikbaar is op de poort.

  > [!IMPORTANT]
  > Verminderen van de bandbreedte van een circuit wordt niet ondersteund.

* Het verbruiksabonnement wijzigen van *Datalimiet* in *Onbeperkte data*.

  > [!IMPORTANT]
  > Wijzigen van het verbruiksabonnement van Onbeperkte data in Datalimiet wordt niet ondersteund.

* U kunt *klassieke bewerkingen toestaan* in- en uitschakelen.
  > [!IMPORTANT]
  > Mogelijk moet u het ExpressRoute-circuit opnieuw maken als er onvoldoende capaciteit is op de bestaande poort. U kunt het circuit niet upgraden als er geen extra capaciteit beschikbaar is op de betreffende locatie.
  >
  > Hoewel u de bandbreedte naadloos kunt upgraden, is het niet mogelijk om de bandbreedte van een ExpressRoute-circuit zonder onderbreking te verminderen. Voor het verminderen van de bandbreedte moet u de inrichting van het ExpressRoute-circuit ongedaan maken, en vervolgens een nieuw ExpressRoute-circuit inrichten.
  >
  > Uitschakelen van de bewerking van de Premium-invoegtoepassing kan mislukken, als u resources gebruikt die groter zijn dan is toegestaan voor het Standard-circuit.

Klik op **Configuratie** als u een ExpressRoute-circuit wilt wijzigen.

![Circuit wijzigen](./media/expressroute-howto-circuit-portal-resource-manager/modify-circuit-configuration.png)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning and deleting an ExpressRoute circuit (Inrichting van een ExpressRoute-circuit ongedaan maken en het circuit verwijderen)

U kunt uw ExpressRoute-circuit verwijderen door het pictogram **Verwijderen** te selecteren. Let op de volgende informatie:

* U moet alle virtuele netwerken loskoppelen van het ExpressRoute-circuit. Als deze bewerking mislukt, controleert u of er virtuele netwerken zijn gekoppeld aan het circuit.
* Als de serviceprovider van het circuit de inrichtingsstatus **Inrichten** of **Ingericht** heeft, moet u contact opnemen met de serviceprovider om de inrichting van het circuit aan hun zijde ongedaan te maken. We blijven resources reserveren en kosten in rekening brengen, totdat de serviceprovider de inrichting van het circuit helemaal ongedaan heeft gemaakt en ons op de hoogte heeft gesteld.
* Als de serviceprovider dit heeft gedaan (de inrichtingsstatus bij de serviceprovider is ingesteld op **Niet ingericht**), kunt u het circuit verwijderen. Hiermee wordt ook de facturering voor het circuit gestopt.

## <a name="next-steps"></a>Volgende stappen

Nadat u het circuit hebt gemaakt, gaat u door met de volgende stappen:

* [Routering voor uw ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-portal-resource-manager.md)
* [Uw virtuele netwerk koppelen aan uw ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
