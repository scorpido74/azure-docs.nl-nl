---
title: 'Azure AD-verificatie configureren voor de VPN-verbinding van de gebruiker: Virtual WAN'
description: Meer informatie over het configureren van Azure Active Directory-verificatie voor gebruikers-VPN.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/17/2020
ms.author: alzam
ms.openlocfilehash: dd80724d62c71fdec81965fb4aa6a07a6233a288
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84753950"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>Azure Active Directory-verificatie voor gebruikers-VPN configureren

In dit artikel wordt beschreven hoe u Azure AD-verificatie configureert voor gebruikers-VPN in een virtueel WAN om verbinding te maken met uw resources in azure via een OpenVPN-VPN-verbinding. Azure Active Directory-verificatie is alleen beschikbaar voor gateways met behulp van OpenVPN-protocol en clients met Windows.

Voor dit soort verbinding moet een client op de clientcomputer worden geconfigureerd. Zie voor meer informatie over Virtual WAN het [Overzicht van Virtual WAN](virtual-wan-about.md).

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een WAN maken
> * Een hub maken
> * Een P2S-configuratie maken
> * Een VPN-client profiel downloaden
> * P2S-configuratie toepassen op een hub
> * Een VNet verbinden met een hub
> * Een configuratie voor een VPN-client downloaden en toepassen
> * Uw virtuele WAN weergeven

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Voordat u begint

Controleer voordat u met de configuratie begint of u aan de volgende criteria hebt voldaan:

* U hebt een virtueel netwerk waarmee u verbinding wilt maken. Controleer of geen van de subnetten van uw on-premises netwerken overlapt met de virtuele netwerken waarmee u verbinding wilt maken. Als u een virtueel netwerk in de Azure Portal wilt maken, raadpleegt u [Quick](../virtual-network/quick-create-portal.md)start.

* Het virtuele netwerk heeft geen virtuele netwerk gateways. Als uw virtuele netwerk een gateway heeft (VPN of ExpressRoute), moet u alle gateways verwijderen. Voor deze configuratie moeten virtuele netwerken in plaats daarvan zijn verbonden met de virtuele WAN hub-gateway.

* Zorg dat u een IP-adresbereik krijgt voor uw hubregio. De hub is een virtueel netwerk dat wordt gemaakt en gebruikt door Virtual WAN. Het adres bereik dat u voor de hub opgeeft, mag niet overlappen met een van de bestaande virtuele netwerken waarmee u verbinding maakt. Dit bereik mag ook niet overlappen met de adresbereiken waarmee u on-premises verbinding wilt maken. Als u niet bekend bent met de IP-adresbereiken die zich in uw on-premises netwerk configuratie bevinden, coördineert u met iemand die deze gegevens voor u kan opgeven.

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Een virtueel WAN maken

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

1. Ga naar de virtuele WAN-pagina. Klik in de portal op **+Een resource maken**. Typ **Virtual WAN** in het zoekvak en selecteer ENTER.
2. Selecteer **virtueel WAN** in de resultaten. Klik op de pagina virtueel WAN op **maken** om de pagina WAN maken te openen.
3. Vul op de pagina **WAN maken** op het tabblad **basis beginselen** de volgende velden in:

   ![Virtual WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Abonnement** - selecteer het abonnement dat u wilt gebruiken.
   * **Resource groep** : nieuwe maken of bestaande gebruiken.
   * **Locatie van resource groep** : Kies een resource locatie in de vervolg keuzelijst. Een WAN een globale resource en bevindt zich niet in een bepaalde regio. U moet echter een regio selecteren om de WAN-resource die u maakt eenvoudiger te kunnen beheren en vinden.
   * **Naam** : Typ de naam die u voor uw WAN wilt aanroepen.
   * **Type:** Standaard. Als u een WAN Basic maakt, kunt u alleen een Basic-hub maken. Basis hubs zijn alleen geschikt voor VPN-verbindingen tussen sites.
4. Wanneer u klaar bent met het invullen van de velden, selecteert u **controleren + maken**.
5. Nadat de validatie is geslaagd, selecteert u **maken** om het virtuele WAN te maken.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>Een lege virtuele hub maken

1. Onder uw virtuele WAN selecteert u hubs en klikt u op **+ nieuwe hub**.

   ![Nieuwe site](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Vul op de pagina virtuele hub maken de volgende velden in.

   **Regio** : Selecteer de regio waarin u de virtuele hub wilt implementeren.

   **Naam** : Voer de naam in die u wilt aanroepen van de virtuele hub.

   **Privé-adres ruimte van hub** : het adres bereik van de hub in CIDR-notatie.

   ![Nieuwe site](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Klik op **Controleren + maken**.
4. Klik op de pagina **door gegeven validatie** op **maken**.

## <a name="create-a-new-p2s-configuration"></a><a name="site"></a>Een nieuwe P2S-configuratie maken

Een P2S-configuratie definieert de parameters om verbinding te maken met externe clients.

1. Onder uw virtuele WAN selecteert u **VPN-configuraties voor gebruikers**.

   ![nieuwe configuratie](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. Klik op **+ VPN-configuratie voor gebruiker maken**.

   ![nieuwe configuratie](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Voer de gegevens in en klik op **maken**

   ![nieuwe configuratie](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>Hubtoewijzing bewerken

1. Navigeer naar de Blade **hubs** onder het virtuele WAN.
2. Selecteer de hub waaraan u de VPN-server configuratie wilt koppelen en klik op het weglatings teken (...).

   ![Nieuwe site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Klik op **virtuele hub bewerken**.
4. Schakel het selectie vakje een **punt-naar-site-gateway insluiten** in en selecteer de gewenste **Gateway-schaal eenheid** .

   ![Nieuwe site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Voer de **adres groep** in van waaruit de VPN-clients IP-adressen worden toegewezen.
6. Klik op **Bevestigen**.
7. Het kan tot 30 minuten duren voordat de bewerking is voltooid.

## <a name="download-vpn-profile"></a><a name="device"></a>VPN-profiel downloaden

Gebruik het VPN-profiel om uw clients te configureren.

1. Klik op de pagina voor uw virtuele WAN op **VPN-configuraties voor gebruikers**.
2. Klik boven aan de pagina op **VPN-configuratie voor gebruiker downloaden**.
3. Wanneer het bestand gereed is, klikt u op de koppeling om het te downloaden.
4. Gebruik het profiel bestand voor het configureren van de VPN-clients.

## <a name="configure-user-vpn-clients"></a>VPN-clients voor gebruikers configureren

Als u verbinding wilt maken, moet u de Azure VPN-client downloaden en het VPN-client profiel importeren dat in de vorige stappen is gedownload op elke computer die verbinding wil maken met het VNet.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN- &reg; Protocol verbindingen.
>

#### <a name="to-download-the-azure-vpn-client"></a>De Azure VPN-client downloaden

Gebruik deze [koppeling](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) om de Azure VPN-client te downloaden.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Een client profiel importeren

1. Selecteer op de pagina **importeren**.

    ![importeren](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Blader naar het profiel XML-bestand en selecteer het. Selecteer **openen**terwijl het bestand is geselecteerd.

    ![importeren](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Geef de naam van het profiel op en selecteer **Opslaan**.

    ![importeren](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Selecteer **verbinding maken** om verbinding te maken met het VPN.

    ![importeren](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Zodra de verbinding is gemaakt, wordt het pictogram groen en vervolgens **verbonden**.

    ![importeren](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Een client profiel verwijderen

1. Selecteer het beletsel teken (...) naast het client profiel dat u wilt verwijderen. Selecteer vervolgens **verwijderen**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Selecteer **verwijderen** om te verwijderen.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Problemen met de verbinding vaststellen

1. Als u verbindings problemen wilt vaststellen, kunt u het hulp programma voor **diagnose** gebruiken. Selecteer het beletsel teken (...) naast de VPN-verbinding die u wilt diagnosticeren om het menu weer te geven. Selecteer vervolgens **diagnose**.

    ![vaststellen](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Selecteer op de pagina **Eigenschappen van verbinding** de optie **diagnose uitvoeren**.

    ![vaststellen](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Meld u aan met uw referenties.

    ![vaststellen](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Bekijk de resultaten van de diagnose.

    ![vaststellen](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

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
