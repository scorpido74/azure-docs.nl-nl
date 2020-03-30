---
title: 'Azure AD-verificatie configureren voor vpn-verbinding gebruiker: Virtual WAN'
description: Meer informatie over het configureren van Azure Active Directory-verificatie voor gebruikers-VPN.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: alzam
ms.openlocfilehash: 703b832d58f2374eac131cfd380ba27f2c890618
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059485"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>Azure Active Directory-verificatie configureren voor gebruikers-VPN

In dit artikel ziet u hoe u Azure AD-verificatie voor gebruikersVPN in Virtual WAN configureert om verbinding te maken met uw bronnen in Azure via een OpenVPN VPN-verbinding. Azure Active Directory-verificatie is alleen beschikbaar voor gateways met OpenVPN-protocol en clients met Windows.

Voor dit soort verbinding moet een client op de clientcomputer worden geconfigureerd. Zie voor meer informatie over Virtual WAN het [Overzicht van Virtual WAN](virtual-wan-about.md).

In dit artikel leert u het volgende:

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

* U hebt een virtueel netwerk waarmee u verbinding wilt maken. Controleer of geen van de subnetten van uw on-premises netwerken overlapt met de virtuele netwerken waarmee u verbinding wilt maken. Zie [Snelstart](../virtual-network/quick-create-portal.md)als u een virtueel netwerk wilt maken in de Azure-portal.

* Uw virtuele netwerk heeft geen virtuele netwerkgateways. Als uw virtuele netwerk een gateway heeft (VPN of ExpressRoute), moet u alle gateways verwijderen. Deze configuratie vereist dat virtuele netwerken in plaats daarvan zijn verbonden met de Virtual WAN-hubgateway.

* Zorg dat u een IP-adresbereik krijgt voor uw hubregio. De hub is een virtueel netwerk dat wordt gemaakt en gebruikt door Virtual WAN. Het adresbereik dat u voor de hub opgeeft, kan niet overlappen met een van uw bestaande virtuele netwerken waarmee u verbinding maakt. Dit bereik mag ook niet overlappen met de adresbereiken waarmee u on-premises verbinding wilt maken. Als u niet bekend bent met de IP-adresbereiken in uw on-premises netwerkconfiguratie, coördineert u met iemand die deze gegevens voor u kan verstrekken.

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan.

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Een virtueel WAN maken

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

1. Navigeer naar de pagina Virtueel WAN. Klik in de portal op **+Een resource maken**. Typ **Virtueel WAN** in het zoekvak en selecteer Enter.
2. Selecteer **Virtueel WAN** in de resultaten. Klik op de pagina Virtueel WAN op **Maken** om de pagina WAN maken te openen.
3. Vul op de pagina **WAN maken** op het tabblad **Basisbeginselen** de volgende velden in:

   ![Virtuele WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Abonnement** - selecteer het abonnement dat u wilt gebruiken.
   * **Resourcegroep** - Maak nieuwe of bestaande gebruiken.
   * **Locatie resourcegroep** - Kies een resourcelocatie in de vervolgkeuzelijst. Een WAN een globale resource en bevindt zich niet in een bepaalde regio. U moet echter een regio selecteren om de WAN-resource die u maakt eenvoudiger te kunnen beheren en vinden.
   * **Naam** - Typ de naam die u uw WAN wilt noemen.
   * **Type:** Standaard. Als u een Basic WAN maakt, u alleen een Basic-hub maken. Basishubs zijn alleen geschikt voor VPN-site-to-site-connectiviteit.
4. Nadat u klaar bent met het invullen van de velden, selecteert u **Controleren +Maken**.
5. Nadat validatie is doorgegeven, selecteert **u Maken** om het virtuele WAN te maken.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>Een lege virtuele hub maken

1. Selecteer onder uw virtuele WAN Hubs en klik op **+Nieuwe hub**.

   ![Nieuwe site](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Vul op de pagina Virtuele hub maken de volgende velden in.

   **Regio** - Selecteer het gebied waarin u de virtuele hub wilt implementeren.

   **Naam** - Voer de naam in die u uw virtuele hub wilt noemen.

   **Hub privéadresruimte** - Het adresbereik van de hub in CIDR-notatie.

   ![Nieuwe site](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Klik op **Controleren + maken**.
4. Klik op de **doorgegeven validatie** op **Maken**.

## <a name="create-a-new-p2s-configuration"></a><a name="site"></a>Een nieuwe P2S-configuratie maken

Een P2S-configuratie definieert de parameters om verbinding te maken met externe clients.

1. Selecteer onder uw virtuele WAN **de VPN-configuraties van gebruikers.**

   ![nieuwe config](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. klik op **+GebruikersVPN-config maken**.

   ![nieuwe config](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Voer de gegevens in en klik op **Maken**

   ![nieuwe config](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>Hubtoewijzing bewerken

1. Navigeer naar het **hubsblad** onder het virtuele WAN.
2. Selecteer de hub waaraan u de configuratie van de VPN-server wilt koppelen en klik op de ellips (...).

   ![Nieuwe site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Klik **op Virtuele hub bewerken**.
4. Schakel het selectievakje **Point-to-site gateway opnemen in** en kies de gewenste **gatewayschaaleenheid.**

   ![Nieuwe site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Voer de **groep Adres** in van waaruit de VPN-clients IP-adressen toegewezen krijgen.
6. Klik op **Bevestigen**.
7. Het kan tot 30 minuten duren voordat de bewerking is voltooid.

## <a name="download-vpn-profile"></a><a name="device"></a>VPN-profiel downloaden

Gebruik het VPN-profiel om uw clients te configureren.

1. Klik op de pagina voor uw virtuele WAN op **GEBRUIKERSVPN-configuraties**.
2. Klik boven aan de pagina op **VPN-config van de gebruiker downloaden.**
3. Wanneer het bestand gereed is, klikt u op de koppeling om het te downloaden.
4. Gebruik het profielbestand om de VPN-clients te configureren.

## <a name="configure-user-vpn-clients"></a>VPN-clients van gebruikers configureren

Om verbinding te maken, moet u de Azure VPN-client downloaden en het VPN-clientprofiel importeren dat in de vorige stappen is gedownload op elke computer die verbinding wil maken met het VNet.

> [!NOTE]
> Azure AD-verificatie wordt alleen&reg; ondersteund voor OpenVPN-protocolverbindingen.
>

#### <a name="to-download-the-azure-vpn-client"></a>De Azure VPN-client downloaden

Gebruik deze [koppeling](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) om de Azure VPN-client te downloaden.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Een klantprofiel importeren

1. Selecteer **importeren**op de pagina .

    ![importeren](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Blader naar het xml-bestand van het profiel en selecteer het. Als het bestand is geselecteerd, selecteert u **Openen**.

    ![importeren](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Geef de naam van het profiel op en selecteer **Opslaan**.

    ![importeren](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Selecteer **Verbinding maken** om verbinding te maken met de VPN.

    ![importeren](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Eenmaal aangesloten, wordt het pictogram groen en zegt **Verbonden**.

    ![importeren](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Een clientprofiel verwijderen

1. Selecteer de ellips (...) naast het clientprofiel dat u wilt verwijderen. Selecteer vervolgens **Verwijderen**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Selecteer **Verwijderen** om te verwijderen.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Verbindingsproblemen diagnosticeren

1. Als u verbindingsproblemen wilt diagnosticeren, u het hulpprogramma **Diagnose** gebruiken. Selecteer de ellips (...) naast de VPN-verbinding die u wilt diagnosticeren om het menu te onthullen. Selecteer vervolgens **Diagnosticeren**.

    ![Diagnosticeren](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Selecteer **Diagnose uitvoeren**op de pagina **Verbindingseigenschappen** .

    ![Diagnosticeren](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Meld u aan met uw referenties.

    ![Diagnosticeren](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Bekijk de diagnoseresultaten.

    ![Diagnosticeren](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Uw virtuele WAN weergeven

1. Navigeer naar uw virtuele WAN.
2. Op de pagina Overzicht vertegenwoordigt elk punt op de kaart een hub.
3. In de sectie Hubs en verbindingen vindt u informatie over de hubstatus, site, regio, VPN-verbindingsstatus en verzonden en ontvangen bytes.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Resources opschonen

U kunt de opdracht [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt. Vervangen 'myResourceGroup' door de naam van uw resourcegroep en voer de volgende PowerShell-opdracht uit:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
