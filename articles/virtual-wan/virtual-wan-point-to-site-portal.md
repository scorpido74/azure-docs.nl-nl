---
title: Azure Virtual WAN gebruiken om een punt-naar-site-verbinding te maken met Azure | Microsoft Docs
description: In deze zelfstudie leert u hoe u Azure Virtual WAN kunt gebruiken om een punt-naar-site-VPN-verbinding te maken met Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: alzam
ms.openlocfilehash: 394ca0c244db751645eb56f50e7023fcee229876
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807456"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Zelf studie: een VPN-verbinding voor een gebruiker maken met behulp van Azure Virtual WAN

In deze zelfstudie leert u hoe u Virtual WAN kunt gebruiken om verbinding te maken met uw resources in Azure via een VPN-verbinding met IPsec/IKE (IKEv2)- of OpenVPN. Voor dit soort verbinding moet een client op de clientcomputer worden geconfigureerd. Zie voor meer informatie over Virtual WAN het [Overzicht van Virtual WAN](virtual-wan-about.md)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een WAN maken
> * Een hub maken
> * Een P2S-configuratie maken
> * Een VPN-client profiel downloaden
> * P2S-configuratie toepassen op een hub
> * Een VNet verbinden met een hub
> * Een configuratie voor een VPN-client downloaden en toepassen
> * Uw virtuele WAN weergeven
> * Resourcestatus weergeven

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Voordat u begint

Controleer voordat u met de configuratie begint of u aan de volgende criteria hebt voldaan:

* U hebt een virtueel netwerk waarmee u verbinding wilt maken. Controleer of geen van de subnetten van uw on-premises netwerken overlapt met de virtuele netwerken waarmee u verbinding wilt maken. Als u een virtueel netwerk in de Azure Portal wilt maken, raadpleegt u [Quick](../virtual-network/quick-create-portal.md)start.

* Het virtuele netwerk heeft geen virtuele netwerk gateways. Als uw virtuele netwerk een gateway heeft (VPN of ExpressRoute), moet u alle gateways verwijderen. Voor deze configuratie moeten virtuele netwerken in plaats daarvan zijn verbonden met de virtuele WAN hub-gateway.

* Zorg dat u een IP-adresbereik krijgt voor uw hubregio. De hub is een virtueel netwerk dat wordt gemaakt en gebruikt door Virtual WAN. Het adres bereik dat u voor de hub opgeeft, mag niet overlappen met een van de bestaande virtuele netwerken waarmee u verbinding maakt. Dit bereik mag ook niet overlappen met de adresbereiken waarmee u on-premises verbinding wilt maken. Als u niet bekend bent met de IP-adresbereiken die zich in uw on-premises netwerk configuratie bevinden, coördineert u met iemand die deze gegevens voor u kan opgeven.

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.

## <a name="wan"></a>Een virtueel WAN maken

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

1. Ga naar de virtuele WAN-pagina. Klik in de portal op **+Een resource maken**. Typ **Virtual WAN** in het zoekvak en selecteer ENTER.
2. Selecteer **virtueel WAN** in de resultaten. Klik op de pagina virtueel WAN op **maken** om de pagina WAN maken te openen.
3. Vul op de pagina **WAN maken** op het tabblad **basis beginselen** de volgende velden in:

   ![Virtual WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Abonnement** - selecteer het abonnement dat u wilt gebruiken.
   * **Resource groep** : nieuwe maken of bestaande gebruiken.
   * **Locatie van resource groep** : Kies een resource locatie in de vervolg keuzelijst. Een WAN een globale resource en bevindt zich niet in een bepaalde regio. U moet echter een regio selecteren om de WAN-resource die u maakt eenvoudiger te kunnen beheren en vinden.
   * **Naam** : Typ de naam die u voor uw WAN wilt aanroepen.
   * **Type:** Standaard. Als u een WAN Basic maakt, kunt u alleen een Basic-hub maken. Basis hubs zijn alleen geschikt voor VPN-verbindingen tussen sites.
4. Wanneer u klaar bent met het invullen van de velden, selecteert u **controleren + maken**.
5. Wanneer de validatie is geslaagd, selecteert u **maken** om het virtuele WAN te maken.

## <a name="site"></a>Een lege virtuele hub maken

1. Onder uw virtuele WAN selecteert u hubs en klikt u op **+ nieuwe hub**

   ![Nieuwe site](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. Vul op de pagina virtuele hub maken de volgende velden in.

   **Regio** : Selecteer de regio waarin u de virtuele hub wilt implementeren.

   **Naam** : Voer de naam in die u wilt aanroepen van de virtuele hub.

   **Privé-adres ruimte van hub** : het adres bereik van de hub in CIDR-notatie.

   ![Nieuwe site](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. Klik op **beoordeling + maken**
4. Klik op de pagina **door gegeven validatie** op **maken**

## <a name="site"></a>Een P2S-configuratie maken

Een P2S-configuratie definieert de parameters om verbinding te maken met externe clients.

1. Ga naar **Alle resources**.
2. Klik op de virtuele WAN die u hebt gemaakt.
3. Klik boven aan de pagina op **+ gebruikers VPN-configuratie maken** om de pagina **nieuwe gebruiker VPN-configuratie maken** te openen.

   ![Nieuwe site](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. Vul op de pagina **nieuwe gebruiker VPN-configuratie maken** de volgende velden in:

   **Naam van configuratie** - Dit is de naam waarmee u naar uw configuratie wilt verwijzen.

   **Type tunnel** -Het protocol dat voor de tunnel moet worden gebruikt.

   **Certificaatnaam van hoofdmap** - Een beschrijvende naam voor het certificaat.

   **Public Certificate data** -Base-64 Encoded X. 509-certificaat gegevens.
  
   ![Nieuwe site](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Klik op **Maken** om de configuratie te maken.

## <a name="hub"></a>Hub-toewijzing bewerken

1. Navigeer naar de Blade **hubs** onder het virtuele WAN
2. Selecteer de hub waaraan u de VPN-server configuratie wilt koppelen en klik op **...**

   ![Nieuwe site](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. Klik op **virtuele hub bewerken**.
4. Schakel het selectie vakje een **punt-naar-site-gateway insluiten** in en selecteer de gewenste **Gateway-schaal eenheid** .

   ![Nieuwe site](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Voer de **adres groep** in van waaruit de VPN-clients IP-adressen worden toegewezen.
6. Klik op **bevestigen**
7. Het kan tot 30 minuten duren voordat de bewerking is voltooid.

## <a name="device"></a>VPN-profiel downloaden

Gebruik het VPN-profiel om uw clients te configureren.

1. Klik op de pagina voor uw virtuele WAN op **VPN-configuraties voor gebruikers**.
2. Klik boven aan de pagina op **VPN-configuratie voor gebruiker downloaden**.
3. Wanneer het bestand gereed is, klikt u op de koppeling om het te downloaden.
4. Gebruik het profiel bestand voor het configureren van de VPN-clients.

### <a name="configure-user-vpn-clients"></a>VPN-clients voor gebruikers configureren
Gebruik het gedownloade profiel om de clients voor externe toegang te configureren. De procedure verschilt per besturingssysteem. Volg daarom de juiste instructies hieronder:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Download en installeer de OpenVPN-client op de officiële website.
2. Download het VPN-profiel voor de gateway. U kunt dit doen via het tabblad gebruikers VPN-configuraties in Azure Portal, of New-AzureRmVpnClientConfiguration in Power shell.
3. Pak het profiel uit. Open het configuratiebestand vpnconfig.ovpn uit de map OpenVPN in Kladblok.
4. Vul het gedeelte P2S client certificate met de openbare P2S-clientcertificatcode in base64. In een certificaat met PEM-indeling kunt u gewoon het .cer-bestand openen en de base64-code tussen de headers van het certificaat kopiëren. Hier ziet u [hoe u een certificaat exporteert om de gecodeerde open bare sleutel op te halen.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site)
5. Vul in het gedeelte voor de persoonlijke sleutel de persoonlijke P2S-clientcertificaatsleutel in Base64 in. Bekijk hier hoe u de persoonlijke sleutel kunt uitpakken.
6. Wijzig geen andere velden. Gebruik de ingevulde configuratie in de clientinvoer om verbinding te maken met de VPN.
7. Kopieer het bestand vpnconfig.ovpn naar C:\Program Files\OpenVPN\config folder.
8. Klik met de rechtermuisknop op het pictogram van OpenVPN in het systeemvak en klik op Verbinding maken.

##### <a name="ikev2"></a>IKEv2

1. Selecteer de VPN-clientconfiguratiebestanden die overeenkomen met de architectuur van de Windows-computer. Kies voor een architectuur met 64-bits processor het installatiepakket ‘VpnClientSetupAmd64’. Kies voor een architectuur met 32-bits processor het installatiepakket ‘VpnClientSetupAmdX86’.
2. Dubbelklik op het pakket om het te installeren. Klik op Meer info en vervolgens op Toch uitvoeren als u een SmartScreen-melding ziet.
3. Navigeer op de clientcomputer naar Netwerkinstellingen en klik op VPN. De VPN-verbinding bevat de naam van het virtuele netwerk waarmee verbinding wordt gemaakt.
4. Controleer of u een clientcertificaat op de clientcomputer hebt geïnstalleerd voordat u verbinding probeert te maken. Een clientcertificaat is vereist voor verificatie als u het systeemeigen verificatietype met Azure-certificaat gebruikt. Zie [certificaten genereren](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site)voor meer informatie over het genereren van certificaten. Raadpleeg Een clientcertificaat installeren voor meer informatie over het installeren van een clientcertificaat.

## <a name="viewwan"></a>Uw virtuele WAN weer geven

1. Navigeer naar uw virtuele WAN.
2. Op de pagina Overzicht vertegenwoordigt elk punt op de kaart een hub. Houd de muisaanwijzer boven een punt om de statussamenvatting voor de hub weer te geven.
3. In de sectie Hubs en verbindingen vindt u informatie over de hubstatus, site, regio, VPN-verbindingsstatus en verzonden en ontvangen bytes.

## <a name="viewhealth"></a>De status van uw resource weer geven

1. Navigeer naar uw WAN.
2. Klik op de pagina van uw WAN, in de sectie **Ondersteuning en probleemoplossing**, op **Status** en bekijk de status van uw resource.


## <a name="cleanup"></a>Resources opschonen

U kunt de opdracht [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt. Vervangen 'myResourceGroup' door de naam van uw resourcegroep en voer de volgende PowerShell-opdracht uit:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
