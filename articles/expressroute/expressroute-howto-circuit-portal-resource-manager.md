---
title: 'Zelfstudie: Een circuit maken en wijzigen met ExpressRoute'
description: In deze zelfstudie leert u hoe u een ExpressRoute-circuit kunt maken, inrichten, controleren, verwijderen en de inrichting ervan ongedaan kunt maken.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: duau
ms.openlocfilehash: e0ba14cd5db47c12435b2de35d0753b402c947ea
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566259"
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

Deze zelfstudie laat zien hoe u een ExpressRoute-circuit maakt met behulp van Azure Portal en het Azure Resource Manager-implementatiemodel. U kunt ook de status van een circuit controleren, een circuit bijwerken of verwijderen, of de inrichting ervan ongedaan maken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een ExpressRoute-circuit maken
> * Bekijk de huidige status van een circuit
> * Een circuit wijzigen
> * Een circuit uitschrijven en verwijderen

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

1. Selecteer **Een resource maken** in het menu van Azure Portal. Selecteer **Netwerken** > **ExpressRoute**, zoals wordt weergegeven in de volgende afbeelding:

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="Een ExpressRoute-circuit maken":::

2. Nadat u op **ExpressRoute** hebt geklikt, ziet u de pagina **ExpressRoute maken**. Geef de **resourcegroep**, **regio** en **naam** op voor het circuit. Klik vervolgens op **Volgende: Configuratie >** .

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Configureer de resourcegroep en regio":::

3. Wanneer u de waarden op deze pagina invult, moet u ervoor zorgen dat u de juiste SKU-laag (Lokaal, Standard of Premium) en het juiste factureringsmodel voor datameting (Onbeperkt of Naar gebruik) opgeeft.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Het circuit configureren":::
    
    * **Poorttype** bepaalt of u verbinding maakt met een serviceprovider of rechtstreeks met het wereldwijde netwerk van Microsoft op een peeringlocatie.
    * **Nieuw maken of importeren vanuit klassiek** bepaalt of er een nieuw circuit wordt gemaakt of dat u een klassiek circuit migreert naar ARM.
    * **Provider** is de internetprovider bij wie u uw service aanvraagt.
    * **Peeringlocatie** is de fysieke locatie vanwaaruit u koppelt met Microsoft.

    > [!IMPORTANT]
    > Peeringlocatie geeft de [fysieke locatie](expressroute-locations.md) aan vanwaaruit u koppelt met Microsoft. Dit is **niet** gekoppeld aan de eigenschap Location, die verwijst naar de geografische locatie waar de Azure-netwerkresourceprovider zich bevindt. Hoewel deze twee niet zijn gerelateerd, wordt wel aanbevolen een netwerkresourceprovider te kiezen die zich geografisch dicht bij de peeringlocatie van het circuit bevindt.

    * **SKU** bepaalt of een Lokale-, Standaard- of Premium-ExpressRoute invoegtoepassing is ingeschakeld. U kunt **Local** opgeven om de lokale SKU-invoegtoepassing te krijgen, **Standard** om de standaard SKU-invoegtoepassing te krijgen of **Premium** voor de Premium-invoegtoepassing. U kunt de SKU wijzigen om de Premium-invoegtoepassing in te schakelen.
    > [!IMPORTANT]
    > U kunt de SKU niet wijzigen van **Standard/Premium** in **Local**.
    
    * **Factureringsmodel** bepaalt het factureringstype. U kunt **Naar gebruik** opgeven voor een data-abonnement naar gebruik, en **Onbeperkt** voor een onbeperkt data-abonnement. Houd er rekening mee dat u het factureringstype allen kunt wijzigen van **Naar gebruik** in **Onbeperkt**.

    > [!IMPORTANT]
    > U kunt het type niet wijzigen van **Onbeperkt** in **Naar gebruik**.

    * Met **Klassieke werking toestaan** kunnen klassieke virtuele netwerken worden gekoppeld aan het circuit.

### <a name="3-view-the-circuits-and-properties"></a>3. De circuits en eigenschappen weergeven

**Alle circuits weergeven**

U kunt alle circuits bekijken die u hebt gemaakt, door in het menu aan de linkerkant **Alle services> Netwerken> ExpressRoute-circuits** te selecteren.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="Expressroute-circuitmenu":::

Alle Expressroute-circuits die in het abonnement zijn gemaakt, worden hier weergegeven.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="Expressroute-circuitlijst":::

**De eigenschappen weergeven**

U kunt de eigenschappen van het circuit bekijken door het circuit te selecteren. Op de pagina **Overzicht** voor uw circuit wordt de servicesleutel weergegeven in het veld Servicesleutel. Raadpleeg de servicesleutel voor uw circuit en geef deze door aan de serviceprovider om het inrichtingsproces te voltooien. De servicesleutel is specifiek voor uw circuit.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Eigenschappen weergeven":::

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. De servicesleutel voor inrichting verzenden naar de connectiviteitsprovider

Op deze pagina bevat **Providerstatus** informatie over de huidige status van de inrichting aan de zijde van de serviceprovider. **Circuitstatus** biedt de status aan de zijde van Microsoft. Zie het artikel [Werkstromen](expressroute-workflows.md#expressroute-circuit-provisioning-states) voor meer informatie over de inrichtingsstatussen van circuits.

Wanneer u een nieuw ExpressRoute-circuit maakt, heeft het circuit de volgende status:

Providerstatus: **Niet ingericht**<BR>
Circuitstatus: **Ingeschakeld**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Inrichtingsproces initiëren":::

Wanneer de connectiviteitsprovider bezig is met het inschakelen van uw circuit, verandert de status van het circuit in:

Providerstatus: **Inrichten**<BR>
Circuitstatus: **Ingeschakeld**

Als u een ExpressRoute-circuit wilt gebruiken, moet dit circuit de volgende status hebben:

Providerstatus: **Ingericht**<BR>
Circuitstatus: **Ingeschakeld**

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Controleer regelmatig de circuitstatus en de status van de circuitsleutel

U kunt de eigenschappen van het gewenste circuit bekijken door het circuit te selecteren. Controleer de **Providerstatus** en zorg ervoor dat deze is gewijzigd in **Ingericht** voordat u doorgaat.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Circuit- en providerstatus":::

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

> [!IMPORTANT]
  > Wijzigen van de SKU van **Standard/Premium** naar **Local** wordt niet ondersteund.

* De bandbreedte van uw ExpressRoute-circuit verhogen, mits er capaciteit beschikbaar is op de poort.

  > [!IMPORTANT]
  > Verminderen van de bandbreedte van een circuit wordt niet ondersteund.

* Het verbruiksabonnement wijzigen van *Datalimiet* in *Onbeperkte data*.

  > [!IMPORTANT]
  > Wijzigen van het verbruiksabonnement van **Onbeperkte data** in **Datalimiet** wordt niet ondersteund.

* U kunt *klassieke bewerkingen toestaan* in- en uitschakelen.
  > [!IMPORTANT]
  > Mogelijk moet u het ExpressRoute-circuit opnieuw maken als er onvoldoende capaciteit is op de bestaande poort. U kunt het circuit niet upgraden als er geen extra capaciteit beschikbaar is op de betreffende locatie.
  >
  > Hoewel u de bandbreedte naadloos kunt upgraden, is het niet mogelijk om de bandbreedte van een ExpressRoute-circuit zonder onderbreking te verminderen. Voor het verminderen van de bandbreedte moet u de inrichting van het ExpressRoute-circuit ongedaan maken, en vervolgens een nieuw ExpressRoute-circuit inrichten.
  >
  > Uitschakelen van de bewerking van de Premium-invoegtoepassing kan mislukken, als u resources gebruikt die groter zijn dan is toegestaan voor het Standard-circuit.

Klik op **Configuratie** als u een ExpressRoute-circuit wilt wijzigen.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Circuit wijzigen":::

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning and deleting an ExpressRoute circuit (Inrichting van een ExpressRoute-circuit ongedaan maken en het circuit verwijderen)

Als de serviceprovider van het circuit de inrichtingsstatus **Inrichten** of **Ingericht** heeft, moet u contact opnemen met de serviceprovider om de inrichting van het circuit aan hun zijde ongedaan te maken. We blijven resources reserveren en kosten in rekening brengen, totdat de serviceprovider de inrichting van het circuit helemaal ongedaan heeft gemaakt en ons op de hoogte heeft gesteld.

> [!NOTE]
>* U moet *alle virtuele netwerken* loskoppelen van het ExpressRoute-circuit voordat u de inrichting opheft. Als deze bewerking mislukt, controleert u of er virtuele netwerken zijn gekoppeld aan het circuit.
>* Als de serviceprovider dit heeft gedaan (de inrichtingsstatus bij de serviceprovider is ingesteld op **Niet ingericht**), kunt u het circuit verwijderen. Hiermee wordt ook de facturering voor het circuit gestopt.

U kunt uw ExpressRoute-circuit verwijderen door het pictogram **Verwijderen** te selecteren. 

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Circuit verwijderen":::

## <a name="next-steps"></a>Volgende stappen

Nadat u het circuit hebt gemaakt, gaat u door met de volgende stappen:

* [Routering voor uw ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-portal-resource-manager.md)
* [Uw virtuele netwerk koppelen aan uw ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
