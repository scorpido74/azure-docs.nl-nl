---
title: 'Zelfstudie: netwerken configureren voor uw VMware-privécloud in Azure'
description: Meer informatie over het maken en configureren van de netwerken die nodig zijn voor het implementeren van uw privécloud in Azure
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 5a8086f78f465f52d0f9107932c09c4690f505e8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873850"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Zelfstudie: Netwerken configureren voor uw VMware-privécloud in Azure

Een AVS-privécloud (Azure VMware Solution) vereist een virtueel netwerk. Omdat AVS geen ondersteuning biedt voor uw on-premises vCenter tijdens de preview, zijn er aanvullende stappen nodig voor de integratie met uw on-premises omgeving. Het instellen van een ExpressRoute-circuit en een gateway voor een virtueel netwerk zijn ook vereist en worden in deze zelfstudie toegelicht.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk maken
> * Een gateway voor een virtueel netwerk maken
> * Uw ExpressRoute-circuit met de gateway verbinden
> * De URL's voor vCenter en NSX Manager zoeken

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Ga naar de resourcegroep die u hebt gemaakt in de [vorige zelfstudie](tutorial-create-private-cloud.md)en selecteer **+ Toevoegen** om een nieuwe resource te definiëren.

In het tekstvak **Marketplace doorzoeken** typt u **virtueel netwerk**. Zoek de resource voor het virtuele netwerk en selecteer deze.

Selecteer op de pagina Virtueel netwerk **Maken** om een virtueel netwerk voor uw privécloud in te stellen.

Voer op de pagina **Virtueel netwerk maken** de relevante gegevens voor het virtuele netwerk in. Een beschrijving van de eigenschappen wordt weergegeven in de volgende tabel:

> [!IMPORTANT]
> U moet een adresruimte gebruiken die **niet** overlapt met de adresruimte die u hebt gebruikt bij het maken van de privécloud in de voorgaande zelfstudie.

Voer op het tabblad **Basisinformatie** een naam in voor het virtuele netwerk, selecteer de gewenste regio en selecteer **Volgende: IP-adressen**

Voer op het tabblad **IP-adressen**, onder **IPv4-adresruimte**, de adresruimte in die u in de vorige zelfstudie hebt gemaakt.

Selecteer **+ Subnet toevoegen** en geef op de pagina **Subnet toevoegen** het subnet een naam en het juiste adresbereik. Als u klaar bent, selecteert u **Toevoegen**.

Selecteer **Beoordelen en maken**

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Een virtueel netwerk maken" border="true":::

Controleer de gegevens en selecteer **Maken**. Zodra de implementatie is voltooid, ziet u het virtuele netwerk in de resourcegroep.

## <a name="create-a-virtual-network-gateway"></a>Een gateway voor een virtueel netwerk maken

U hebt in de vorige sectie een virtueel netwerk gemaakt. U gaat nu een gateway voor een virtueel netwerk maken.

Selecteer in de resourcegroep **+ Toevoegen** om een nieuwe resource toe te voegen.

Typ in het tekstvak **Marketplace doorzoeken** **gateway voor virtueel netwerk**. Zoek de resource voor het virtuele netwerk en selecteer deze.

Selecteer op de pagina **Gateway voor virtueel netwerk** de optie **Maken**.

Geef op het tabblad Basisinformatie van de pagina **Gateway voor virtueel netwerk maken** waarden op voor de velden. Beschrijvingen van de velden worden weergegeven in de volgende tabel:

| Veld | Waarde |
| --- | --- |
| **Abonnement** | Deze waarde is al ingevuld met het abonnement waartoe de resourcegroep behoort. |
| **Resourcegroep** | Deze waarde is al ingevuld voor de huidige resourcegroep. Dit hoort de resourcegroep te zijn die u in een vorige test hebt gemaakt. |
| **Naam** | Voer een unieke naam in voor de gateway voor het virtuele netwerk. |
| **Regio** | Selecteer de geografische locatie van de gateway voor het virtuele netwerk. |
| **Gatewaytype** | Selecteer **ExpressRoute**. |
| **VPN-type** | Selecteer **Op route gebaseerd**. |
| **SKU** | Laat de standaardwaarde staan: **standaard**. |
| **Virtueel netwerk** | Selecteer het virtuele netwerk dat u eerder hebt gemaakt. Als u het virtuele netwerk niet ziet, zorg er dan voor dat de regio van de gateway overeenkomt met de regio van het virtuele netwerk. |
| **Adresbereik gatewaysubnet** | Deze waarde wordt ingevuld wanneer u het virtuele netwerk selecteert. Wijzig de standaardwaarde niet. |
| **Openbaar IP-adres** | Selecteer **Nieuw maken**. |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="een gateway maken" border="true":::

Selecteer **Beoordelen en maken**, controleer op de volgende pagina of de gegevens juist zijn en selecteer **Maken** om de implementatie van de gateway voor uw virtuele netwerk te starten. Zodra de implementatie is voltooid, gaat u naar de volgende sectie in deze zelfstudie om uw ExpressRoute-verbinding te verbinden met het virtuele netwerk met uw privécloud.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>ExpressRoute verbinden met de gateway voor het virtuele netwerk

In deze sectie wordt uitgelegd hoe u een verbinding kunt toevoegen tussen uw AVS-privécloud en de gateway voor uw virtuele netwerk die u hebt gemaakt.

Ga naar de privécloud die u hebt gemaakt in de vorige zelfstudie en selecteer **Connectiviteit** onder **Beheren**, selecteer het tabblad **ExpressRoute**.

Kopieer de autorisatiesleutel. Als er geen autorisatiesleutel is, moet u er een maken. Daarvoor selecteert u **+ Een autorisatiesleutel aanvragen**.

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="een autorisatiesleutel aanvragen" border="true":::

Ga naar de gateway voor het virtuele netwerk die u in de vorige stap hebt gemaakt en selecteer onder **Instellingen** de optie **Verbindingen**. Selecteer op de pagina **Verbindingen** de optie **+ Toevoegen**.

Geef op de pagina **Verbinding toevoegen** waarden op voor de velden. Beschrijvingen van de velden worden weergegeven in de volgende tabel:

| Veld | Waarde |
| --- | --- |
| **Naam**  | Geef een naam voor de verbinding op.  |
| **Verbindingstype**  | Selecteer **ExpressRoute**.  |
| **Autorisatie inwisselen**  | Zorg ervoor dat dit selectievakje is ingeschakeld.  |
| **Gateway voor een virtueel netwerk** | De gateway voor het virtuele netwerk die u eerder hebt gemaakt  |
| **Autorisatiesleutel**  | Kopieer en plak de autorisatiesleutel vanuit het tabblad ExpressRoute voor de resourcegroep. |
| **URI van peercircuit**  | Kopieer en plak de ExpressRoute-id vanuit het tabblad ExpressRoute voor uw resourcegroep.  |

Selecteer **OK**. Hiermee maakt u de verbinding tussen het ExpressRoute-circuit en het virtuele netwerk.

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="een verbinding toevoegen" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>De URL's voor vCenter en NSX Manager zoeken

Als u zich wilt aanmelden bij vCenter en NSX Manager, hebt u de URL's nodig voor de vCenter-webclient en de NSX-T-beheersite. De URL's vinden:

Ga naar uw AVS-privécloud, selecteer onder **Beheren** de optie **identiteit**. Hier vindt u de vereiste informatie.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="de vCenter-URL's zoeken" border="true":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een virtueel netwerk maken
> * Een gateway voor een virtueel netwerk maken
> * Uw ExpressRoute-circuit met de gateway verbinden
> * De URL's voor vCenter en NSX Manager zoeken

Ga verder met de volgende zelfstudie om te leren hoe u een jump-box maakt die wordt gebruikt om verbinding te maken met uw omgeving, zodat u uw privécloud lokaal kunt beheren.

> [!div class="nextstepaction"]
> [Toegang tot een privécloud](tutorial-access-private-cloud.md)
