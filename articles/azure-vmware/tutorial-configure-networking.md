---
title: 'Zelf studie: netwerken configureren voor uw VMWare privécloud in azure'
description: Meer informatie over het maken en configureren van het netwerk dat nodig is voor het implementeren van uw privécloud in azure
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 41043b98a6e270d8d9f4373de7876b3fcae86747
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837614"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Zelf studie: netwerken configureren voor uw VMWare Private Cloud in azure

Een Azure VMware-oplossing (AVS) privécloud vereist een virtueel netwerk. Omdat AVS geen ondersteuning biedt voor uw on-premises vCenter tijdens de preview, zijn er aanvullende stappen nodig voor de integratie met uw on-premises omgeving. Het instellen van een ExpressRoute-circuit en een Virtual Network gateway zijn ook vereist en worden in deze zelf studie opgelost.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk maken
> * Een Virtual Network-gateway maken
> * Uw ExpressRoute-circuit verbinden met de gateway
> * De Url's voor vCenter en NSX Manager zoeken

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Ga naar de resource groep die u hebt gemaakt in de [vorige zelf studie](tutorial-create-private-cloud.md)en selecteer **+ toevoegen** om een nieuwe resource te definiëren.

Typ **Virtual Network**in het tekstvak **Marketplace zoeken** . Zoek de Virtual Network resource en selecteer deze.

Selecteer op de pagina Virtual Network **maken** om uw virtuele netwerk in te stellen voor uw privécloud.

Voer op de pagina **Virtual Network maken** de relevante gegevens voor het virtuele netwerk in, een beschrijving van de eigenschappen wordt weer gegeven in de volgende tabel:

> [!IMPORTANT]
> U moet een adres ruimte gebruiken die **niet** overlapt met de adres ruimte die u hebt gebruikt bij het maken van de privécloud in de voor gaande zelf studie.

Op het tabblad **basis beginselen** voert u een naam in voor het virtuele netwerk en selecteert u de gewenste regio en selecteert u **volgende: IP-adressen**

Voer op het tabblad **IP-adressen** onder **IPv4-adres ruimte**de adres ruimte in die u hebt gemaakt in de vorige zelf studie.

Selecteer **+ subnet toevoegen**en geef op de pagina **subnet toevoegen** het subnet een naam en het juiste adres bereik. Als u klaar bent, selecteert u **Toevoegen**.

Selecteer **controleren + maken**

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="een virtueel netwerk maken" border="true":::

Controleer de gegevens en selecteer **maken**. Zodra de implementatie is voltooid, ziet u het virtuele netwerk in de resource groep.

## <a name="create-a-virtual-network-gateway"></a>Een Virtual Network-gateway maken

U hebt een virtueel netwerk gemaakt in de vorige sectie. u maakt nu een Virtual Network gateway.

Selecteer in de resource groep **+ toevoegen** om een nieuwe resource toe te voegen.

Typ in het tekstvak **Marketplace** -tekst, **virtuele netwerk gateway**. Zoek de Virtual Network resource en selecteer deze.

Selecteer op de pagina **Virtual Network gateway** de optie **maken**.

Op het tabblad basis principes van de pagina **virtuele netwerk gateway maken** geeft u waarden op voor de velden. beschrijvingen van de velden worden weer gegeven in de volgende tabel:

| Veld | Waarde |
| --- | --- |
| **Abonnement** | Deze waarde is al ingevuld met het abonnement waartoe de resource groep behoort. |
| **Resourcegroep** | Deze waarde is al ingevuld voor de huidige resource groep. Dit moet de resource groep zijn die u in een vorige test hebt gemaakt. |
| **Naam** | Voer een unieke naam in voor de gateway van het virtuele netwerk. |
| **Regio** | Selecteer de geografische locatie van de gateway van het virtuele netwerk. |
| **Gateway type** | Selecteer **ExpressRoute**. |
| **VPN-type** | Selecteer **Op route gebaseerd**. |
| **SKU** | De standaard waarde behouden: **standaard**. |
| **Virtueel netwerk** | Selecteer het virtuele netwerk dat u eerder hebt gemaakt. Als u het virtuele netwerk niet ziet, zorg er dan voor dat de regio van de gateway overeenkomt met de regio van het virtuele netwerk. |
| **Adres bereik gateway-subnet** | Deze waarde wordt ingevuld wanneer u het virtuele netwerk selecteert. Wijzig de standaard waarde niet. |
| **Openbaar IP-adres** | Selecteer **Nieuw maken**. |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="een gateway maken" border="true":::

Selecteer **controleren + maken**, op de volgende pagina Controleer of de details juist zijn en selecteer **maken** om de implementatie van uw virtuele netwerk gateway te starten. Zodra de implementatie is voltooid, gaat u naar de volgende sectie in deze zelf studie om uw ExpressRoute-verbinding te verbinden met het virtuele netwerk met uw privécloud.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>ExpressRoute verbinden met de Virtual Network-gateway

In deze sectie wordt uitgelegd hoe u een verbinding kunt toevoegen tussen de privécloud van uw AVS en de virtuele netwerk gateway die u hebt gemaakt.

Ga naar de privécloud die u hebt gemaakt in de vorige zelf studie en selecteer **connectiviteit** onder **beheren**. Selecteer het tabblad **ExpressRoute** .

Kopieer de autorisatie sleutel. Als er geen autorisatie sleutel is, moet u er een maken om **een autorisatie sleutel** te selecteren en op te vragen.

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="een autorisatie sleutel aanvragen" border="true":::

Ga naar de Virtual Network-gateway die u hebt gemaakt in de vorige stap en selecteer onder **instellingen**de optie **verbindingen**. Selecteer op de pagina **verbindingen** **+ toevoegen**.

Geef op de pagina **verbinding toevoegen** waarden op voor de velden. Beschrijvingen van de velden worden weer gegeven in de volgende tabel:

| Veld | Waarde |
| --- | --- |
| **Naam**  | Geef een naam voor de verbinding op.  |
| **Verbindings type**  | Selecteer **ExpressRoute**.  |
| **Autorisatie inwisselen**  | Zorg ervoor dat dit selectie vakje is ingeschakeld.  |
| **Gateway van virtueel netwerk** | De virtuele netwerk gateway die u eerder hebt gemaakt  |
| **Autorisatie sleutel**  | Kopieer en plak de autorisatie sleutel op het tabblad ExpressRoute voor de resource groep. |
| **URI van peer circuit**  | Kopieer en plak de ExpressRoute-ID op het tabblad ExpressRoute voor uw resource groep.  |

Selecteer **OK**. Hiermee maakt u de verbinding tussen het ExpressRoute-circuit en het virtuele netwerk.

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="een verbinding toevoegen" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>De Url's voor vCenter en NSX Manager zoeken

Als u zich wilt aanmelden bij vVenter en NSX Manager, hebt u de url's nodig voor de vCenter-webclient en de NSX-T-beheer site. De url's vinden:

Ga naar de privécloud van uw AVS, onder **beheren**, selecteer **identiteit**, hier vindt u de informatie die nodig is.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="de vCenter-url's zoeken" border="true":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een virtueel netwerk maken
> * Een Virtual Network-gateway maken
> * Uw ExpressRoute-circuit verbinden met de gateway
> * De Url's voor vCenter en NSX Manager zoeken

Ga verder met de volgende zelf studie om te leren hoe u een Jump Box maakt die wordt gebruikt om verbinding te maken met uw omgeving, zodat u uw privécloud lokaal kunt beheren.

> [!div class="nextstepaction"]
> [Toegang tot de Privécloud](tutorial-access-private-cloud.md)
