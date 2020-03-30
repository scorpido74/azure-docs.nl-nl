---
title: Een Site-to-Site (S2S) VPN configureren voor gebruik met Azure Files | Microsoft Documenten
description: Een Site-to-Site (S2S) VPN configureren voor gebruik met Azure Files
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ae3d38d92990d7a1af4146c25b017286ebd29352
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061040"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Een Site-to-Site VPN configureren voor gebruik met Azure Files
U een Site-to-Site (S2S) VPN-verbinding gebruiken om uw Azure-bestandsshares via SMB te monteren vanuit uw on-premises netwerk, zonder poort 445 te openen. U een Site-to-Site VPN instellen met [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md), een Azure-bron die VPN-services aanbiedt, en wordt geïmplementeerd in een brongroep naast opslagaccounts of andere Azure-bronnen.

![Een topologiegrafiek die de topologie van een Azure VPN-gateway illustreert die een Azure-bestandsshare verbindt met een on-premises site met behulp van een S2S VPN](media/storage-files-configure-s2s-vpn/s2s-topology.png)

We raden u ten zeerste aan [het netwerkoverzicht azure files](storage-files-networking-overview.md) te lezen voordat u verdergaat met dit artikel voor een volledige bespreking van de netwerkopties die beschikbaar zijn voor Azure Files.

In het artikel worden de stappen beschreven om een Site-to-Site VPN te configureren om Azure-bestandsshares direct on-premises te monteren. Als u synchronisatieverkeer wilt routeren voor Azure File Sync via een site-naar-site VPN, raadpleegt u [de proxy- en firewall-instellingen voor Azure File Sync configureren.](storage-sync-files-firewall-and-proxy.md)

## <a name="prerequisites"></a>Vereisten
- Een Azure-bestandsshare dat u on-premises wilt monteren. Azure-bestandsshares worden geïmplementeerd in opslagaccounts, die beheerconstructies zijn die een gedeelde opslaggroep vertegenwoordigen waarin u meerdere bestandsshares implementeren, evenals andere opslagbronnen, zoals blobcontainers of wachtrijen. Meer informatie over het implementeren van Azure-bestandsshares en opslagaccounts in [Een Azure-bestandsshare maken.](storage-how-to-create-file-share.md)

- Een privéeindpunt voor het opslagaccount met het Azure-bestandsaandeel dat u on-premises wilt monteren. Zie [Eindpunten voor Azure Files-netwerk configureren](storage-files-networking-endpoints.md?tabs=azure-portal)voor meer informatie over het maken van een privéeindpunt. 

- Een netwerktoestel of -server in uw on-premises datacenter dat compatibel is met Azure VPN Gateway. Azure Files is agnost van het gekozen on-premises netwerktoestel, maar Azure VPN Gateway houdt een [lijst bij met geteste apparaten.](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) Verschillende netwerkapparaten bieden verschillende functies, prestatiekenmerken en beheerfunctionaliteiten, dus houd hier rekening mee bij het selecteren van een netwerktoestel.

    Als u geen bestaand netwerktoestel hebt, bevat Windows Server een ingebouwde serverrol, routering en rastoegang (RRAS), die kan worden gebruikt als het on-premises netwerktoestel. Zie [RAS Gateway](https://docs.microsoft.com/windows-server/remote/remote-access/ras-gateway/ras-gateway)voor meer informatie over het configureren van routering en rastoegang in Windows Server.

## <a name="add-storage-account-to-vnet"></a>Opslagaccount toevoegen aan VNet
Navigeer in de Azure-portal naar het opslagaccount met het Azure-bestandsaandeel dat u on-premises wilt monteren. Selecteer in de inhoudsopgave voor het opslagaccount de invoer **van firewalls en virtuele netwerken.** Tenzij u een virtueel netwerk aan uw opslagaccount hebt toegevoegd toen u het hebt gemaakt, moet het resulterende deelvenster de **knop Toegang toestaan via** de keuzerondje voor alle **netwerken** hebben geselecteerd.

Als u uw opslagaccount wilt toevoegen aan het gewenste virtuele netwerk, selecteert u **Geselecteerde netwerken**. Klik onder de onderverdeling **Virtuele netwerken** op + Bestaand virtueel **netwerk toevoegen** of **+Nieuw virtueel netwerk toevoegen,** afhankelijk van de gewenste status. Als u een nieuw virtueel netwerk maakt, wordt een nieuwe Azure-bron gemaakt. De nieuwe of bestaande VNet-bron hoeft zich niet in dezelfde brongroep of hetzelfde abonnement te bevinden als het opslagaccount, maar moet zich in dezelfde regio bevinden als het opslagaccount en de brongroep en het abonnement waarin u uw VNet implementeert, moet overeenkomen met het account waarop u uw VPN-gateway in te zetten. 

![Schermafbeelding van de Azure-portal met de optie om een bestaand of nieuw virtueel netwerk toe te voegen aan het opslagaccount](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Als u een bestaand virtueel netwerk toevoegt, wordt u gevraagd een of meer subnetten van dat virtuele netwerk te selecteren waaraan het opslagaccount moet worden toegevoegd. Als u een nieuw virtueel netwerk selecteert, maakt u een subnet als onderdeel van het maken van het virtuele netwerk en u later meer toevoegen via de resulterende Azure-bron voor het virtuele netwerk.

Als u nog niet eerder een opslagaccount aan uw abonnement hebt toegevoegd, moet het eindpunt van de Microsoft.Storage-service aan het virtuele netwerk worden toegevoegd. Dit kan enige tijd duren en totdat deze bewerking is voltooid, hebt u geen toegang tot de Azure-bestandsshares binnen dat opslagaccount, ook niet via de VPN-verbinding. 

## <a name="deploy-an-azure-vpn-gateway"></a>Een Azure VPN-gateway implementeren
Selecteer in de inhoudsopgave voor de Azure-portal de optie **Een nieuwe bron maken** en zoeken naar virtuele *netwerkgateway*. Uw virtuele netwerkgateway moet zich in dezelfde abonnements-, Azure-regio en resourcegroep bevinden als het virtuele netwerk dat u in de vorige stap hebt geïmplementeerd (houd er rekening mee dat de brongroep automatisch wordt geselecteerd wanneer het virtuele netwerk wordt gekozen). 

Voor het implementeren van een Azure VPN Gateway moet u de volgende velden invullen:

- **Naam:** De naam van de Azure-bron voor de VPN-gateway. Deze naam kan elke naam zijn die u nuttig vindt voor uw management.
- **Regio**: De regio waarin de VPN-gateway wordt geïmplementeerd.
- **Gatewaytype:** Met het oog op het implementeren van een Site-to-Site VPN moet u **VPN**selecteren.
- **VPN-type**: U kiezen voor *routegebaseerde** of **beleidsgebaseerde,** afhankelijk van uw VPN-apparaat. Route-gebaseerde VPN's ondersteunen IKEv2, terwijl op beleid gebaseerde VPN's alleen IKEv1 ondersteunen. Zie [Over beleidsgebaseerde en routegebaseerde VPN-gateways](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about) voor meer informatie over de twee typen VPN-gateways
- **SKU**: De SKU regelt het aantal toegestane Site-to-Site tunnels en de gewenste prestaties van de VPN. Raadpleeg de [Gateway SKU-vermelding](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) om de juiste SKU voor uw use case te selecteren. De SKU van de VPN Gateway kan later worden gewijzigd indien nodig.
- **Virtueel netwerk:** het virtuele netwerk dat u in de vorige stap hebt gemaakt.
- **Openbaar IP-adres**: het IP-adres van VPN Gateway dat wordt blootgesteld aan het internet. Waarschijnlijk moet u een nieuw IP-adres maken, maar u ook een bestaand ongebruikt IP-adres gebruiken als dat geschikt is. Als u kiest voor **Nieuw maken,** wordt een nieuwe AZURE-bron van een NIEUW IP-adres gemaakt in dezelfde brongroep als de VPN-gateway en is de naam van het **openbare IP-adres** de naam van het nieuw gemaakte IP-adres. Als u **Bestaand gebruiken**selecteert, moet u het bestaande ongebruikte IP-adres selecteren.
- **Actieve actieve modus inschakelen:** selecteer alleen **Ingeschakeld** als u een actief actieve gatewayconfiguratie maakt, anders **uitgeschakeld ingeschakeld.** Zie [Hoog beschikbare cross-premises en VNet-to-VNet-connectiviteit](../../vpn-gateway/vpn-gateway-highlyavailable.md)voor meer informatie over de actieve modus.
- **BGP ASN configureren:** selecteer alleen **Ingeschakeld** als uw configuratie specifiek deze instelling vereist. Zie [Over BGP met Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md)voor meer informatie over deze instelling.

Selecteer **Controleren + maken** om de VPN-gateway te maken. Het kan tot 45 minuten duren voordat een VPN-gateway volledig is gemaakt en geïmplementeerd.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Een lokale netwerkgateway maken voor uw on-premises gateway 
Een lokale netwerkgateway is een Azure-bron die uw on-premises netwerktoestel vertegenwoordigt. Selecteer in de inhoudsopgave voor de Azure-portal de optie **Een nieuwe bron maken** en zoeken naar lokale *netwerkgateway.* De lokale netwerkgateway is een Azure-bron die wordt geïmplementeerd naast uw opslagaccount, virtueel netwerk en VPN-gateway, maar hoeft zich niet in dezelfde brongroep of -abonnement te bevinden als het opslagaccount. 

Voor het implementeren van de lokale netwerkgatewaybron moet u de volgende velden invullen:

- **Naam:** de naam van de Azure-bron voor de lokale netwerkgateway. Deze naam kan elke naam zijn die u nuttig vindt voor uw management.
- **IP-adres**: het openbare IP-adres van uw lokale gateway on-premises.
- **Adresruimte:** de adresbereiken voor het netwerk dat deze lokale netwerkgateway vertegenwoordigt. U meerdere adresruimtebereiken toevoegen, maar zorg ervoor dat de bereiken die u hier opgeeft niet overlappen met bereiken van andere netwerken waarmee u verbinding wilt maken. 
- **BGP-instellingen configureren:** configureer alleen BGP-instellingen als deze instelling voor uw configuratie vereist is. Zie [Over BGP met Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md)voor meer informatie over deze instelling.
- **Abonnement**: Het gewenste abonnement. Dit hoeft niet overeen te komen met het abonnement dat wordt gebruikt voor de VPN Gateway of het opslagaccount.
- **Resourcegroep:** de gewenste resourcegroep. Dit hoeft niet overeen te komen met de brongroep die wordt gebruikt voor de VPN-gateway of het opslagaccount.
- **Locatie**: de Azure-regio waarin de lokale netwerkgatewaybron moet worden gemaakt. Dit moet overeenkomen met de regio die u hebt geselecteerd voor de VPN-gateway en het opslagaccount.

Selecteer **Maken** om de lokale netwerkgatewaybron te maken.  

## <a name="configure-on-premises-network-appliance"></a>On-premises netwerktoestel configureren
De specifieke stappen om uw on-premises netwerktoestel te configureren, zijn afhankelijk van het netwerktoestel dat uw organisatie heeft geselecteerd. Afhankelijk van het apparaat dat uw organisatie heeft gekozen, kan de [lijst met geteste apparaten](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) een koppeling hebben naar de instructies van uw apparaatleverancier voor het configureren met Azure VPN Gateway.

## <a name="create-the-site-to-site-connection"></a>De site-naar-site-verbinding maken
Als u de implementatie van een S2S VPN wilt voltooien, moet u een verbinding maken tussen uw on-premises netwerktoestel (vertegenwoordigd door de lokale netwerkgatewaybron) en de VPN-gateway. Ga hiervoor naar de VPN-gateway die u hierboven hebt gemaakt. Selecteer **Verbindingen**in de inhoudsopgave voor de VPN-gateway en klik op **Toevoegen**. Voor het resulterende **verbindingsvenster toevoegen** zijn de volgende velden vereist:

- **Naam:** De naam van de verbinding. Een VPN-gateway kan meerdere verbindingen hosten, dus kies een naam die nuttig is voor uw beheer en die deze specifieke verbinding onderscheidt.
- **Verbindingstype:** Aangezien dit een S2S-verbinding is, selecteert u **Site-to-site (IPSec)** in de vervolgkeuzelijst.
- **Virtuele netwerkgateway**: Dit veld is automatisch geselecteerd op de VPN-gateway waartoe u de verbinding maakt en kan niet worden gewijzigd.
- **Lokale netwerkgateway:** dit is de lokale netwerkgateway die u wilt verbinden met uw VPN-gateway. Het resulterende selectievenster moet de naam hebben van de lokale netwerkgateway die u hierboven hebt gemaakt.
- **Gedeelde sleutel (PSK):** Een mengsel van letters en cijfers, gebruikt om encryptie voor de verbinding vast te stellen. Dezelfde gedeelde sleutel moet worden gebruikt in zowel het virtuele netwerk als de lokale netwerkgateways. Als uw gateway-apparaat er geen biedt, u er hier een maken en het aan uw apparaat verstrekken.

Selecteer **OK** om de verbinding te maken. U controleren of de verbinding is gemaakt via de pagina **Verbindingen.**

## <a name="mount-azure-file-share"></a>Azure-bestandsshare monteren 
De laatste stap in het configureren van een S2S VPN is om te controleren of deze werkt voor Azure Files. U dit doen door uw Azure-bestandsshare on-premises te monteren met uw favoriete besturingssysteem. Zie de instructies te monteren door OS hier:

- [Windows](storage-how-to-use-files-windows.md)
- [Macos](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Zie ook
- [Overzicht van Azure Files-netwerken](storage-files-networking-overview.md)
- [Een Point-to-Site (P2S) VPN op Windows configureren voor gebruik met Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Een Point-to-Site (P2S) VPN op Linux configureren voor gebruik met Azure Files](storage-files-configure-p2s-vpn-linux.md)