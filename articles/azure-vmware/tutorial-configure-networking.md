---
title: 'Zelfstudie: netwerken configureren voor uw VMware-privécloud in Azure'
description: Meer informatie over het maken en configureren van de netwerken die nodig zijn voor het implementeren van uw privécloud in Azure
ms.topic: tutorial
ms.date: 07/22/2020
ms.openlocfilehash: aa4247f60c3e1ec54bfcde336d1ae8c8f70ff7a8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079432"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Zelfstudie: Netwerken configureren voor uw VMware-privécloud in Azure

Voor een AVS-privécloud (Azure VMware Solution) is een virtueel Azure-netwerk vereist. Omdat AVS geen ondersteuning biedt voor uw on-premises vCenter tijdens de preview, zijn er aanvullende stappen nodig voor de integratie met uw on-premises omgeving. Het instellen van een ExpressRoute-circuit en een gateway voor een virtueel netwerk zijn ook vereist en worden in deze zelfstudie behandeld.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk maken
> * De gateway van een virtueel netwerk maken
> * Uw ExpressRoute-circuit met de gateway verbinden
> * De URL's voor vCenter en NSX Manager zoeken

## <a name="prerequisites"></a>Vereisten 
Voordat u een virtueel netwerk kunt maken, moet u ervoor zorgen dat u een [AVS-privécloud](tutorial-create-private-cloud.md) hebt gemaakt. 

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Ga naar de resourcegroep die u hebt gemaakt in de [zelfstudie over het maken van een privécloud](tutorial-create-private-cloud.md) en selecteer **+Toevoegen** om een nieuwe resource te definiëren. 

1. In het tekstvak **Marketplace doorzoeken** typt u **virtueel netwerk**. Zoek de resource voor het virtuele netwerk en selecteer deze.

1. Selecteer op de pagina **Virtueel netwerk** **Maken** om een virtueel netwerk voor uw privécloud in te stellen.

1. Voer op de pagina **Virtueel netwerk maken** de gegevens voor uw virtuele netwerk in.

1. Voer op het tabblad **Basisinformatie** een naam in voor het virtuele netwerk, selecteer de gewenste regio en selecteer **Volgende: IP-adressen**.

1. Voer op het tabblad **IP-adressen**, onder **IPv4-adresruimte**, de adresruimte in die u in de vorige zelfstudie hebt gemaakt.

   > [!IMPORTANT]
   > U moet een adresruimte gebruiken die **niet** overlapt met de adresruimte die u hebt gebruikt bij het maken van de privécloud in de voorgaande zelfstudie.

1. Selecteer **+ Subnet toevoegen** en geef op de pagina **Subnet toevoegen** het subnet een naam en het juiste adresbereik. Als u klaar bent, selecteert u **Toevoegen**.

1. Selecteer **Controleren + maken**.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Een virtueel netwerk maken" border="true":::

1. Controleer de gegevens en selecteer **Maken**. Zodra de implementatie is voltooid, ziet u het virtuele netwerk in de resourcegroep.

## <a name="create-a-virtual-network-gateway"></a>De gateway van een virtueel netwerk maken

Nu u in de vorige sectie een virtueel netwerk hebt gemaakt, gaat u een gateway voor een virtueel netwerk maken.

1. Selecteer in de resourcegroep **+ Toevoegen** om een nieuwe resource toe te voegen.

1. Typ in het tekstvak **Marketplace doorzoeken** **gateway voor virtueel netwerk**. Zoek de resource voor het virtuele netwerk en selecteer deze.

1. Selecteer op de pagina **Gateway voor virtueel netwerk** de optie **Maken**.

1. Geef op het tabblad Basisinformatie van de pagina **Gateway voor virtueel netwerk maken** waarden op voor de velden en selecteer daarna **Controleren en maken**. 

   | Veld | Waarde |
   | --- | --- |
   | **Abonnement** | Deze waarde is al ingevuld met het abonnement waartoe de resourcegroep behoort. |
   | **Resourcegroep** | Deze waarde is al ingevuld voor de huidige resourcegroep. Dit hoort de resourcegroep te zijn die u in een vorige test hebt gemaakt. |
   | **Naam** | Voer een unieke naam in voor de gateway voor het virtuele netwerk. |
   | **Regio** | Selecteer de geografische locatie van de gateway voor het virtuele netwerk. |
   | **Gatewaytype** | Selecteer **ExpressRoute**. |
   | **SKU** | Laat de standaardwaarde staan: **standaard**. |
   | **Virtueel netwerk** | Selecteer het virtuele netwerk dat u eerder hebt gemaakt. Als u het virtuele netwerk niet ziet, zorg er dan voor dat de regio van de gateway overeenkomt met de regio van het virtuele netwerk. |
   | **Adresbereik gatewaysubnet** | Deze waarde wordt ingevuld wanneer u het virtuele netwerk selecteert. Wijzig de standaardwaarde niet. |
   | **Openbaar IP-adres** | Selecteer **Nieuw maken**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="een gateway maken" border="true":::

1. Controleer of de gegevens juist zijn en selecteer **Maken** om de implementatie van de gateway voor uw virtuele netwerk te starten. 
1. Zodra de implementatie is voltooid, gaat u naar de volgende sectie om uw ExpressRoute-verbinding te verbinden met de gateway van het virtuele netwerk met uw AVS-privécloud.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>ExpressRoute verbinden met de gateway voor het virtuele netwerk

Nu u een gateway voor het virtueel netwerk hebt geïmplementeerd, voegt u een verbinding toe tussen de gateway en uw AVS-privécloud.

1. Ga naar de privécloud die u hebt gemaakt in de vorige zelfstudie en selecteer **Connectiviteit** onder **Beheren**, selecteer het tabblad **ExpressRoute**.

1. Kopieer de autorisatiesleutel. Als er geen autorisatiesleutel is, moet u er een maken. Daarvoor selecteert u **+ Een autorisatiesleutel aanvragen**.

   :::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="een autorisatiesleutel aanvragen" border="true":::

1. Ga naar de gateway voor het virtuele netwerk die u in de vorige stap hebt gemaakt en selecteer onder **Instellingen** de optie **Verbindingen**. Selecteer op de pagina **Verbindingen** de optie **+ Toevoegen**.

1. Geef op de pagina **Verbinding toevoegen** waarden op voor de velden en selecteer **OK**. 

   | Veld | Waarde |
   | --- | --- |
   | **Naam**  | Geef een naam voor de verbinding op.  |
   | **Verbindingstype**  | Selecteer **ExpressRoute**.  |
   | **Autorisatie inwisselen**  | Zorg ervoor dat dit selectievakje is ingeschakeld.  |
   | **Gateway voor een virtueel netwerk** | De gateway voor het virtuele netwerk die u eerder hebt gemaakt.  |
   | **Autorisatiesleutel**  | Kopieer en plak de autorisatiesleutel vanuit het tabblad ExpressRoute voor de resourcegroep. |
   | **URI van peercircuit**  | Kopieer en plak de ExpressRoute-id vanuit het tabblad ExpressRoute voor uw resourcegroep.  |

   :::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="een verbinding toevoegen" border="true":::

De verbinding tussen het ExpressRoute-circuit en uw virtuele netwerk wordt gemaakt.



## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>De URL's voor vCenter en NSX Manager zoeken

Als u zich wilt aanmelden bij vCenter en NSX Manager, hebt u de URL's nodig voor de vCenter-webclient en de NSX-T-beheersite. 

Ga naar uw AVS-privécloud, selecteer onder **Beheren** de optie **identiteit**. Hier vindt u de vereiste informatie.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="de vCenter-URL's zoeken" border="true":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een virtueel netwerk maken
> * De gateway van een virtueel netwerk maken
> * Uw ExpressRoute-circuit met de gateway verbinden
> * De URL's voor vCenter en NSX Manager zoeken

Ga verder met de volgende zelfstudie om te leren hoe u een jump-box maakt die wordt gebruikt om verbinding te maken met uw omgeving, zodat u uw privécloud lokaal kunt beheren.

> [!div class="nextstepaction"]
> [Toegang tot een privécloud](tutorial-access-private-cloud.md)
