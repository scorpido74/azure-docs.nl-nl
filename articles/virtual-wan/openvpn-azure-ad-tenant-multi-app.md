---
title: 'Virtuele WAN: Azure AD-tenant voor verschillende gebruikersgroepen: Azure AD-verificatie'
description: U P2S VPN gebruiken om verbinding te maken met uw VNet met Azure AD-verificatie
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: af5ff5817ee9ae7e6d7432fe281ecb440bf25b9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060705"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Een Azure Active Directory-tenant maken voor P2S OpenVPN-protocolverbindingen

Wanneer u verbinding maakt met uw VNet, u verificatie op basis van certificaten of RADIUS-verificatie gebruiken. Wanneer u echter het Open VPN-protocol gebruikt, u ook Azure Active Directory-verificatie gebruiken. Als u wilt dat verschillende gebruikers verbinding kunnen maken met verschillende gateways, u meerdere apps in AD registreren en deze koppelen aan verschillende gateways.

Met dit artikel u een Azure AD-tenant instellen voor P2S OpenVPN-verificatie en meerdere apps maken en registreren in Azure AD om verschillende toegang voor verschillende gebruikers en groepen mogelijk te maken en te registreren.

> [!NOTE]
> Azure AD-verificatie wordt alleen&reg; ondersteund voor OpenVPN-protocolverbindingen.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. Een nieuwe P2S-configuratie maken

Een P2S-configuratie definieert de parameters om verbinding te maken met externe clients.

1. Stel de volgende variabelen in en vervang waarden indien nodig voor uw omgeving.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Voer de volgende opdrachten uit om de configuratie te maken:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Gebruik de toepassings-id van de Azure VPN-client niet in de bovenstaande opdrachten: Het geeft alle gebruikers toegang tot de gateway. Gebruik de id van de door u geregistreerde applicatie(s).

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. Hub-toewijzing bewerken

1. Navigeer naar het **hubsblad** onder het virtuele WAN.

2. Selecteer de hub waaraan u de configuratie van de VPN-server wilt koppelen en klik op de ellips (...).

    ![Nieuwe site](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Klik **op Virtuele hub bewerken**.

4. Schakel het selectievakje **Point-to-site gateway opnemen in** en kies de gewenste **gatewayschaaleenheid.**

    ![Nieuwe site](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Voer de **groep Adres** in van waaruit de VPN-clients IP-adressen toegewezen krijgen.

6. Klik op **Bevestigen**.

7. De bewerking kan dertig minuten duren.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. Download VPN-profiel

Gebruik het VPN-profiel om uw clients te configureren.

1. Klik op de pagina voor uw virtuele WAN op **GEBRUIKERSVPN-configuraties**.

2. Klik boven aan de pagina op **VPN-config van de gebruiker downloaden.**

3. Wanneer het bestand gereed is, klikt u op de koppeling om het te downloaden.

4. Gebruik het profielbestand om de VPN-clients te configureren.

5. Haal het gedownloade zip-bestand eruit.

6. Blader naar de map "AzureVPN".

7. Noteer de locatie van het bestand "azurevpnconfig.xml". De azurevpnconfig.xml bevat de instelling voor de VPN-verbinding en kan rechtstreeks worden geïmporteerd in de Azure VPN Client-toepassing. U dit bestand ook distribueren naar alle gebruikers die verbinding moeten maken via e-mail of andere middelen. De gebruiker heeft geldige Azure AD-referenties nodig om verbinding te maken.

## <a name="9-configure-user-vpn-clients"></a>9. Vpn-clients van gebruikers configureren

Om verbinding te maken, moet u de Azure VPN-client downloaden en het VPN-clientprofiel importeren dat in de vorige stappen is gedownload op elke computer die verbinding wil maken met het VNet.

> [!NOTE]
> Azure AD-verificatie wordt alleen&reg; ondersteund voor OpenVPN-protocolverbindingen.
>

#### <a name="to-download-the-azure-vpn-client"></a>De Azure VPN-client downloaden

Gebruik deze [koppeling](https://go.microsoft.com/fwlink/?linkid=2117554) om de Azure VPN-client te downloaden.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Een klantprofiel importeren

1. Selecteer **importeren**op de pagina .

    ![importeren](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Blader naar het xml-bestand van het profiel en selecteer het. Als het bestand is geselecteerd, selecteert u **Openen**.

    ![importeren](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Geef de naam van het profiel op en selecteer **Opslaan**.

    ![importeren](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Selecteer **Verbinding maken** om verbinding te maken met de VPN.

    ![importeren](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Eenmaal aangesloten, wordt het pictogram groen en zegt **Verbonden**.

    ![importeren](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Een clientprofiel verwijderen

1. Selecteer de ellips (...) naast het clientprofiel dat u wilt verwijderen. Selecteer vervolgens **Verwijderen**.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Selecteer **Verwijderen** om te verwijderen.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Verbindingsproblemen diagnosticeren

1. Als u verbindingsproblemen wilt diagnosticeren, u het hulpprogramma **Diagnose** gebruiken. Selecteer de ellips (...) naast de VPN-verbinding die u wilt diagnosticeren om het menu te onthullen. Selecteer vervolgens **Diagnosticeren**.

    ![Diagnosticeren](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. Selecteer **Diagnose uitvoeren**op de pagina **Verbindingseigenschappen** .

    ![Diagnosticeren](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Meld u aan met uw referenties.

    ![Diagnosticeren](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Bekijk de diagnoseresultaten.

    ![Diagnosticeren](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. Bekijk uw virtuele WAN

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
