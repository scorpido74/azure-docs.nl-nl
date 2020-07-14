---
title: Azure Virtual WAN gebruiken om een punt-naar-site-verbinding te maken met Azure | Microsoft Docs
description: In deze zelfstudie leert u hoe u Azure Virtual WAN kunt gebruiken om een punt-naar-site-VPN-verbinding te maken met Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 06/29/2020
ms.author: alzam
ms.openlocfilehash: 9c93ad0357011008c45b2898260a655509b02dc2
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560734"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Zelfstudie: Een VPN-verbinding voor gebruikers maken met Azure Virtual WAN

In deze zelfstudie leert u hoe u Virtual WAN kunt gebruiken om verbinding te maken met uw resources in Azure via een VPN-verbinding met IPsec/IKE (IKEv2)- of OpenVPN. Voor dit soort verbinding moet een client op de clientcomputer worden geconfigureerd. Zie voor meer informatie over Virtual WAN het [Overzicht van Virtual WAN](virtual-wan-about.md)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een WAN maken
> * Een P2S-configuratie maken
> * Een hub maken
> * DNS-servers opgeven
> * Een VPN-clientprofiel downloaden
> * Uw virtuele WAN weergeven

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Voordat u begint

Controleer voordat u met de configuratie begint of u aan de volgende criteria hebt voldaan:

* U hebt een virtueel netwerk waarmee u verbinding wilt maken. Controleer of geen van de subnetten van uw on-premises netwerken overlapt met de virtuele netwerken waarmee u verbinding wilt maken. Zie de [snelstart](../virtual-network/quick-create-portal.md) als u een virtueel netwerk in de Azure-portal wilt maken.

* Uw virtuele netwerk heeft geen virtuele netwerkgateways. Als uw virtuele netwerk een gateway heeft (VPN of ExpressRoute), moet u alle gateways verwijderen. Voor deze configuratie moeten virtuele netwerken in plaats daarvan zijn verbonden met de Virtual WAN-hubgateway.

* Zorg dat u een IP-adresbereik krijgt voor uw hubregio. De hub is een virtueel netwerk dat wordt gemaakt en gebruikt door Virtual WAN. Het adresbereik dat u voor de hub opgeeft mag niet overlappen met een van de bestaande virtuele netwerken waarmee u verbinding wilt maken. Dit bereik mag ook niet overlappen met de adresbereiken waarmee u on-premises verbinding wilt maken. Als u de IP-adresbereiken in uw on-premises netwerkconfiguratie niet kent, moet u contact opnemen met iemand die deze gegevens kan verstrekken.

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Een virtueel WAN maken

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

1. Ga naar de pagina Virtual WAN. Selecteer in de portal **+Een resource maken**. Typ **Virtual WAN** in het zoekvak en selecteer **Enter**.
1. Selecteer **Virtual WAN** uit de resultaten. Selecteer op de pagina Virtual WAN **Maken** om de pagina WAN maken te openen.
1. Vul op de pagina **Wan maken**, op het tabblad **Basisprincipes**, de volgende velden in:

   ![Virtuele WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Abonnement**, selecteer het abonnement dat u wilt gebruiken.
   * **Resourcegroep**, maak een nieuwe resourcegroep of gebruik een bestaande.
   * **Locatie van de resourcegroep**, kies een resourcelocatie uit de vervolgkeuzelijst. Een WAN een globale resource en bevindt zich niet in een bepaalde regio. U moet echter een regio selecteren om de WAN-resource die u maakt eenvoudiger te kunnen beheren en vinden.
   * **Naam**, typ de naam die u voor uw WAN hebt gekozen.
   * **Type:** Standaard. Als u een Basic-WAN maakt, kunt u alleen een Basic-hub maken. Basic-hubs zijn alleen geschikt voor VPN-verbindingen tussen sites.
1. Nadat u klaar bent met het invullen van de velden, selecteert u **+Maken**.
1. Wanneer de validatie is geslaagd, selecteert u **Maken** om het virtuele WAN te maken.

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Een P2S-configuratie maken

Een P2S-configuratie definieert de parameters om verbinding te maken met externe clients.

1. Ga naar **Alle resources**.
1. Selecteer de virtuele WAN die u hebt gemaakt.
1. Selecteer **+VPN-configuratie voor gebruikers maken** aan de bovenkant van de pagina om de pagina **Nieuwe VPN-configuratie voor gebruikers maken** te openen.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/p2s1.jpg" alt-text="VPN-configuraties voor gebruikers":::

1. Vul op de pagina **Nieuwe VPN-configuratie voor gebruikers maken** de volgende velden in:

   * **Naam van configuratie**, dit is de naam waarmee u naar uw configuratie wilt verwijzen.
   * **Type tunnel**, het protocol dat voor de tunnel moet worden gebruikt.
   * **Certificaatnaam van hoofdmap**, een beschrijvende naam voor het certificaat.
   * **Openbare certificaatgegevens**, Base-64-gecodeerde X.509-certificaatgegevens.
  
1. Selecteer **Maken** om de configuratie te maken.

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Hub maken met punt-naar-site-gateway

1. Selecteer onder uw virtuele WAN de optie Hubs en selecteer **+Nieuwe hub**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub1.jpg" alt-text="Nieuwe hub":::

1. Vul de volgende velden in op de pagina virtuele hub maken.

   * **Regio**, selecteer de regio waarin u de virtuele hub wilt implementeren.
   * **Naam**, typ de naam die u voor uw virtuele hub hebt gekozen.
   * **Privé-adresruimte hub**, het adresbereik van de hub in CIDR-notatie.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub2.jpg" alt-text="Virtuele hub maken":::

1. Vul op het tabblad Punt-naar-site de volgende velden in:

   * **Schaaleenheden gateway**, deze vertegenwoordigen de totale capaciteit van de VPN-gateway van de gebruiker.
   * **Punt-naar-site-configuratie**, die u in de vorige stap hebt gemaakt.
   * **Adresgroep client**, voor de externe gebruikers.
   * **Aangepast IP-adres DNS-server**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub-with-p2s.png" alt-text="Hub met point-to-site":::

1. Selecteer **Controleren + maken**.
1. Selecteer op de pagina **Validatie geslaagd**, **Maken**.

## <a name="specify-dns-server"></a><a name="dns"></a>DNS-servers opgeven

Met de virtuele VPN-gateways voor WAN-gebruikers kunt u maximaal vijf DNS-servers opgeven. U kunt dit configureren tijdens het maken van de hub of wijzigen op een later tijdstip. Ga hiervoor naar de virtuele hub. Klik onder **Gebruikers-VPN (punt-naar-site)** op configureren en voer het/de IP-adress(sen) van de DNS-server in het/de tekstvak(ken) **Aangepaste DNS-servers** in.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="Aangepaste DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>VPN-profiel downloaden

Gebruik het VPN-profiel om uw clients te configureren.

1. Selecteer op de pagina voor uw virtuele WAN op **Configuraties gebruikers-VPN**.
2. Selecteer bovenaan de pagina **Configuratie gebruikers-VPN downloaden**. Door een WAN-configuratie te downloaden, wordt een ingebouwd gebruikersprofiel op basis van Traffic Manager gemaakt. Zie deze [Hubprofielen](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile) voor meer informatie over wereldwijde of op hub gebaseerde profielen.   Failover-scenario's worden vereenvoudigd met wereldwijde profielen.

   Als om enige reden een hub niet beschikbaar is, zal het ingebouwde verkeerbeheer dat wordt geboden door de service de verbinding naar Azure-resources voor punt-naar-site-gebruikers garanderen via een andere hub. U kunt altijd een VPN-configuratie specifiek voor een hub downloaden door naar de specifieke hub te gaan. Download onder **Gebruikers-VPN (punt-naar-site)** het **Gebruikers-VPN**-profiel van de virtuele hub.

1. Wanneer het bestand gereed is, selecteert u de koppeling om het te downloaden.
1. Gebruik het profielbestand om de VPN-clients te configureren.

### <a name="configure-user-vpn-clients"></a>VPN-clients voor gebruikers configureren

Gebruik het gedownloade profiel om de clients voor externe toegang te configureren. De procedure verschilt per besturingssysteem. Volg daarom de juiste instructies hieronder:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Download en installeer de OpenVPN-client op de officiële website.
1. Download het VPN-profiel voor de gateway. Dit kan via het tabblad VPN-configuraties voor gebruikers in de Azure-portal of via New-AzureRmVpnClientConfiguration in PowerShell.
1. Pak het profiel uit. Open het configuratiebestand vpnconfig.ovpn uit de map OpenVPN in Kladblok.
1. Vul het gedeelte P2S client certificate met de openbare P2S-clientcertificatcode in base64. In een certificaat met PEM-indeling kunt u gewoon het .cer-bestand openen en de base64-code tussen de headers van het certificaat kopiëren. Zie [Een certificaat exporteren om de gecodeerde openbare sleutel te verkrijgen](certificates-point-to-site.md) voor de te volgen stappen.
1. Vul in het gedeelte voor de persoonlijke sleutel de persoonlijke P2S-clientcertificaatsleutel in Base64 in. Zie [De persoonlijke sleutel uitpakken](howto-openvpn-clients.md#windows) voor de te volgen stappen.
1. Wijzig geen andere velden. Gebruik de ingevulde configuratie in de clientinvoer om verbinding te maken met de VPN.
1. Kopieer het bestand vpnconfig.ovpn naar C:\Program Files\OpenVPN\config folder.
1. Klik met de rechtermuisknop op het pictogram van OpenVPN in het systeemvak en selecteer **Verbinding maken**.

##### <a name="ikev2"></a>IKEv2

1. Selecteer de VPN-clientconfiguratiebestanden die overeenkomen met de architectuur van de Windows-computer. Kies voor een architectuur met 64-bits processor het installatiepakket ‘VpnClientSetupAmd64’. Kies voor een architectuur met 32-bits processor het installatiepakket ‘VpnClientSetupAmdX86’.
1. Dubbelklik op het pakket om het te installeren. Selecteer **Meer info** en vervolgens **Toch uitvoeren** als u een SmartScreen-melding ziet.
1. Navigeer op de clientcomputer naar **Netwerkinstellingen** en selecteer **VPN**. De VPN-verbinding bevat de naam van het virtuele netwerk waarmee verbinding wordt gemaakt.
1. Controleer of u een clientcertificaat op de clientcomputer hebt geïnstalleerd voordat u verbinding probeert te maken. Een clientcertificaat is vereist voor verificatie als u het systeemeigen verificatietype met Azure-certificaat gebruikt. Raadpleeg [Certificaten genereren](certificates-point-to-site.md) voor meer informatie over het genereren van certificaten. Raadpleeg [Een clientcertificaat installeren](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) voor meer informatie over het installeren van een clientcertificaat.

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Uw virtuele WAN weergeven

1. Navigeer naar uw virtuele WAN.
1. Op de pagina **Overzicht** vertegenwoordigt elk punt op de kaart een hub.
1. In de sectie **Hubs en verbindingen** vindt u informatie over de hubstatus, site, regio, VPN-verbindingsstatus en verzonden en ontvangen bytes.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Resources opschonen

U kunt de opdracht [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt. Vervangen 'myResourceGroup' door de naam van uw resourcegroep en voer de volgende PowerShell-opdracht uit:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
