---
title: Azure Virtual WAN gebruiken om een punt-naar-site-verbinding te maken met Azure | Microsoft Docs
description: In deze zelfstudie leert u hoe u Azure Virtual WAN kunt gebruiken om een punt-naar-site-VPN-verbinding te maken met Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: alzam
ms.openlocfilehash: 11007bc39cb1112799c89afaf0ca670aa6760de6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482136"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Zelfstudie: Een VPN-verbinding voor gebruikers maken met Azure Virtual WAN

In deze zelfstudie leert u hoe u Virtual WAN kunt gebruiken om verbinding te maken met uw resources in Azure via een VPN-verbinding met IPsec/IKE (IKEv2)- of OpenVPN. Voor dit soort verbinding moet een client op de clientcomputer worden geconfigureerd. Zie het [Virtual WAN-overzicht voor](virtual-wan-about.md) meer informatie over Virtual WAN

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een WAN maken
> * Een hub maken
> * Een P2S-configuratie maken
> * Een VPN-clientprofiel downloaden
> * P2S-configuratie toepassen op een hub
> * Een VNet verbinden met een hub
> * Een configuratie voor een VPN-client downloaden en toepassen
> * Uw virtuele WAN weergeven

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Voordat u begint

Controleer voordat u met de configuratie begint of u aan de volgende criteria hebt voldaan:

* U hebt een virtueel netwerk waarmee u verbinding wilt maken. Controleer of geen van de subnetten van uw on-premises netwerken overlapt met de virtuele netwerken waarmee u verbinding wilt maken. Zie snel [start](../virtual-network/quick-create-portal.md)als u een virtueel netwerk wilt maken in de Azure-portal.

* Uw virtuele netwerk heeft geen virtuele netwerkgateways. Als uw virtuele netwerk een gateway heeft (VPN of ExpressRoute), moet u alle gateways verwijderen. Deze configuratie vereist dat virtuele netwerken in plaats daarvan zijn verbonden met de Virtual WAN-hubgateway.

* Zorg dat u een IP-adresbereik krijgt voor uw hubregio. De hub is een virtueel netwerk dat wordt gemaakt en gebruikt door Virtual WAN. Het adresbereik dat u voor de hub opgeeft, kan niet overlappen met een van uw bestaande virtuele netwerken waarmee u verbinding maakt. Dit bereik mag ook niet overlappen met de adresbereiken waarmee u on-premises verbinding wilt maken. Als u niet bekend bent met de IP-adresbereiken in uw on-premises netwerkconfiguratie, coördineert u met iemand die deze gegevens voor u kan verstrekken.

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan.

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Een virtueel WAN maken

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

1. Navigeer naar de pagina Virtueel WAN. Klik in de portal op **+Een resource maken**. Typ **Virtueel WAN** in het zoekvak en selecteer Enter.
2. Selecteer **Virtueel WAN** in de resultaten. Klik op de pagina Virtueel WAN op **Maken** om de pagina WAN maken te openen.
3. Vul op de pagina **WAN maken** op het tabblad **Basisbeginselen** de volgende velden in:

   ![Virtuele WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Abonnement** - selecteer het abonnement dat u wilt gebruiken.
   * **Resourcegroep** - Maak nieuwe of bestaande gebruiken.
   * **Locatie resourcegroep** - Kies een resourcelocatie in de vervolgkeuzelijst. Een WAN een globale resource en bevindt zich niet in een bepaalde regio. U moet echter een regio selecteren om de WAN-resource die u maakt eenvoudiger te kunnen beheren en vinden.
   * **Naam** - Typ de naam die u uw WAN wilt noemen.
   * **Type:** Standaard. Als u een Basic WAN maakt, u alleen een Basic-hub maken. Basishubs zijn alleen geschikt voor VPN-site-to-site-connectiviteit.
4. Nadat u klaar bent met het invullen van de velden, selecteert u **Controleren +Maken**.
5. Zodra de validatie is doorgegeven, selecteert **u Maken** om het virtuele WAN te maken.

## <a name="create-an-empty-virtual-hub"></a><a name="hub"></a>Een lege virtuele hub maken

1. Selecteer hubs onder uw virtuele WAN en klik op **+Nieuwe hub**

   ![Nieuwe site](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. Vul op de pagina Virtuele hub maken de volgende velden in.

   **Regio** - Selecteer het gebied waarin u de virtuele hub wilt implementeren.

   **Naam** - Voer de naam in die u uw virtuele hub wilt noemen.

   **Hub privéadresruimte** - Het adresbereik van de hub in CIDR-notatie.

   ![Nieuwe site](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. Klik **op Controleren + maken**
4. Klik op de **doorgegeven validatie** op **Maken**

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Een P2S-configuratie maken

Een P2S-configuratie definieert de parameters om verbinding te maken met externe clients.

1. Ga naar **Alle resources**.
2. Klik op de virtuele WAN die u hebt gemaakt.
3. Klik boven aan de pagina **op +Gebruikers-VPN-config maken** om de pagina **Nieuwe GEBRUIKERS-VPN-configuratie maken** te openen.

   ![Nieuwe site](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. Vul op de pagina **Nieuwe VPN-configuratie van gebruikers** de volgende velden in:

   **Naam van configuratie** - Dit is de naam waarmee u naar uw configuratie wilt verwijzen.

   **Type tunnel** -Het protocol dat voor de tunnel moet worden gebruikt.

   **Certificaatnaam van hoofdmap** - Een beschrijvende naam voor het certificaat.

   **Gegevens over openbare certificaten** - Basis-64 gecodeerde X.509-certificaatgegevens.
  
5. Klik op **Maken** om de configuratie te maken.

## <a name="edit-hub-assignment"></a><a name="edit"></a>Hubtoewijzing bewerken

1. Navigeren naar het **hubsblad** onder het virtuele WAN
2. Selecteer de hub waaraan u de configuratie van de VPN-server wilt koppelen en klik op **...**

   ![Nieuwe site](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. Klik **op Virtuele hub bewerken**.
4. Schakel het selectievakje **Point-to-site gateway opnemen in** en kies de gewenste **gatewayschaaleenheid.**

   ![Nieuwe site](media/virtual-wan-point-to-site-portal/p2s2.jpg)

De onderstaande tabel toont de details over de beschikbare **schaaleenheden**

| **Schaaleenheid** | **Doorvoer** | **P2S-verbindingen** |
| --- | --- | --- |
| 1| 500 Mbps | 500 |
| 2| 1 Gbps | 500 |
| 3| 1,5 Gbps | 500 |
| 4| 2 Gbps | 1000 |
| 5| 2,5 Gbps | 1000 |
| 6| 3 Gbps | 1000 |
| 7| 3,5 Gbps | 5000 |
| 8| 4 Gbps | 5000 |
| 9| 4,5 Gbps | 5000 |
| 10| 5 Gbps | 5000 |
| 11| 5,5 Gbps | 5000 |
| 12| 6 Gbps | 5000 |
| 13| 6,5 Gbps | 10.000 |
| 14| 7 Gbps | 10.000 |
| 15| 7,5 Gbps | 10.000 |
| 16| 8 Gbps | 10.000 |
| 17| 8,5 Gbps | 10.000 |
| 18| 9 Gbps | 10.000 |
| 19| 9,5 Gbps | 10.000 |
| 20| 10 Gbps | 10.000 |

5. Voer de **groep Adres** in van waaruit de VPN-clients IP-adressen toegewezen krijgen.
6. Klik **op Bevestigen**
7. Het kan tot 30 minuten duren voordat de bewerking is voltooid.

## <a name="download-vpn-profile"></a><a name="download"></a>VPN-profiel downloaden

Gebruik het VPN-profiel om uw clients te configureren.

1. Klik op de pagina voor uw virtuele WAN op **GEBRUIKERSVPN-configuraties**.
2. Klik boven aan de pagina op **VPN-config van de gebruiker downloaden.**
3. Wanneer het bestand gereed is, klikt u op de koppeling om het te downloaden.
4. Gebruik het profielbestand om de VPN-clients te configureren.

### <a name="configure-user-vpn-clients"></a>VPN-clients van gebruikers configureren
Gebruik het gedownloade profiel om de clients voor externe toegang te configureren. De procedure verschilt per besturingssysteem. Volg daarom de juiste instructies hieronder:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Download en installeer de OpenVPN-client op de officiële website.
2. Download het VPN-profiel voor de gateway. Dit kan worden gedaan via het tabblad GebruikersVPN-configuraties in Azure-portal of New-AzureRmVpnClientConfiguration in PowerShell.
3. Pak het profiel uit. Open het configuratiebestand vpnconfig.ovpn uit de map OpenVPN in Kladblok.
4. Vul het gedeelte P2S client certificate met de openbare P2S-clientcertificatcode in base64. In een certificaat met PEM-indeling kunt u gewoon het .cer-bestand openen en de base64-code tussen de headers van het certificaat kopiëren. Zie Een [certificaat exporteren om de gecodeerde openbare sleutel te krijgen](certificates-point-to-site.md) voor stappen.
5. Vul in het gedeelte voor de persoonlijke sleutel de persoonlijke P2S-clientcertificaatsleutel in Base64 in. Zie Voor stappen [Hoe u de privésleutel extraheren.](howto-openvpn-clients.md#windows)
6. Wijzig geen andere velden. Gebruik de ingevulde configuratie in de clientinvoer om verbinding te maken met de VPN.
7. Kopieer het bestand vpnconfig.ovpn naar C:\Program Files\OpenVPN\config folder.
8. Klik met de rechtermuisknop op het pictogram van OpenVPN in het systeemvak en klik op Verbinding maken.

##### <a name="ikev2"></a>IKEv2

1. Selecteer de VPN-clientconfiguratiebestanden die overeenkomen met de architectuur van de Windows-computer. Kies voor een architectuur met 64-bits processor het installatiepakket ‘VpnClientSetupAmd64’. Kies voor een architectuur met 32-bits processor het installatiepakket ‘VpnClientSetupAmdX86’.
2. Dubbelklik op het pakket om het te installeren. Klik op Meer info en vervolgens op Toch uitvoeren als u een SmartScreen-melding ziet.
3. Navigeer op de clientcomputer naar Netwerkinstellingen en klik op VPN. De VPN-verbinding bevat de naam van het virtuele netwerk waarmee verbinding wordt gemaakt.
4. Controleer of u een clientcertificaat op de clientcomputer hebt geïnstalleerd voordat u verbinding probeert te maken. Een clientcertificaat is vereist voor verificatie als u het systeemeigen verificatietype met Azure-certificaat gebruikt. Zie Certificaten genereren voor meer informatie over het genereren van [certificaten.](certificates-point-to-site.md) Zie [Een clientcertificaat installeren](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)voor informatie over het installeren van een clientcertificaat.

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Uw virtuele WAN weergeven

1. Navigeer naar uw virtuele WAN.
2. Op de pagina Overzicht vertegenwoordigt elk punt op de kaart een hub.
3. In de sectie Hubs en verbindingen vindt u informatie over de hubstatus, site, regio, VPN-verbindingsstatus en verzonden en ontvangen bytes.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Resources opschonen

U kunt de opdracht [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt. Vervangen 'myResourceGroup' door de naam van uw resourcegroep en voer de volgende PowerShell-opdracht uit:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
