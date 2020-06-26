---
title: 'Zelfstudie: Azure Firewall implementeren en configureren in een hybride netwerk met behulp van de Azure-portal'
description: In deze zelfstudie leert u hoe u Azure Firewall kunt implementeren en configureren via de Azure-portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 5ba9bb723ab7b052440eea2ac509692200b80f6e
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84750704"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Zelfstudie: Azure Firewall implementeren en configureren in een hybride netwerk met behulp van de Azure-portal

Als u het on-premises netwerk verbindt met een virtueel Azure-netwerk om een hybride netwerk te maken, is de mogelijkheid om de toegang tot uw Azure-netwerkresources te beheren een belangrijk onderdeel van een algemeen beveiligingsplan.

U kunt Azure Firewall gebruiken om de netwerktoegang in een hybride netwerk te beheren met behulp van de regels die toegestaan en geweigerd netwerkverkeer definiëren.

Voor deze zelfstudie maakt u drie virtuele netwerken:

- **VNet-Hub**: de firewall bevindt zich in dit virtuele netwerk.
- **VNet-spoke**: het virtuele spoke-netwerk vertegenwoordigt de workload die zich bevindt in Azure.
- **VNet-Onprem**: het on-premises virtuele netwerk vertegenwoordigt een on-premises netwerk. In een daadwerkelijke implementatie kan het zijn verbonden via een VPN of een ExpressRoute-verbinding. Om het eenvoudig te houden wordt in deze zelfstudie een VPN-gatewayverbinding gebruikt en wordt een on-premises netwerk vertegenwoordigd door een virtueel Azure-netwerk.

![Firewall in een hybride netwerk](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De variabelen declareren
> * Het virtuele hub-netwerk voor de firewall maken
> * Het virtuele spoke-netwerk maken
> * Het on-premises virtuele netwerk maken
> * De firewall configureren en implementeren
> * De VPN-gateways maken en verbinden
> * De hub- en virtuele spoke-netwerken koppelen
> * De routes maken
> * De virtuele machines maken
> * De firewall testen

Als u in plaats daarvan Azure PowerShell wilt gebruiken om deze procedure te voltooien, raadpleegt u [Azure Firewall implementeren en configureren in een hybride netwerk met Azure PowerShell](tutorial-hybrid-ps.md).

## <a name="prerequisites"></a>Vereisten

Een hybride netwerk gebruikt het model van hub-en-spoke om verkeer tussen Azure VNets en on-premises netwerken te routeren. De hub-en-spoke-architectuur heeft de volgende vereisten:

- Stel **AllowGatewayTransit** in bij peering tussen VNet-hub en VNet-spoke. In een hub en spoke-netwerkarchitectuur zorgt een gatewayovergang dat virtuele spoke-netwerken de VPN-gateway in de hub kunnen delen, in plaats van in elk virtueel spoke-netwerk VPN-gateways te implementeren. 

   Routes naar de via de gateway verbonden virtuele netwerken of on-premises netwerken worden bovendien automatisch doorgegeven aan de routeringstabellen voor de als peer ingestelde virtuele netwerken met behulp van de gatewayovergang. Zie [VPN-gatewayoverdracht kunt configureren voor peering voor virtuele netwerken](../vpn-gateway/vpn-gateway-peering-gateway-transit.md) voor meer informatie.

- Stel **UseRemoteGateways** bij peering tussen VNet-spoke en VNet-hub. Als **UseRemoteGateways** is ingesteld en **AllowGatewayTransit** ook is ingesteld voor externe peering, gebruikt het virtuele spoke-netwerk gateways van het externe virtuele netwerk voor overdracht.
- Om het verkeer van het spoke-subnet te routeren via de hub-firewall, kunt u een door de gebruiker gedefinieerde route (UDR) gebruiken die naar de firewall wijst en waarvoor de optie **Doorgifte van route van virtuele netwerkgateway** is uitgeschakeld. De uitgeschakelde optie **Doorgifte van route van virtuele netwerkgateway** voorkomt routedistributie naar de spoke-subnetten. Hierdoor veroorzaken geleerde routes geen conflicten met uw UDR. Als u **routepropagatie van de virtuele netwerkgateway** ingeschakeld wilt houden, moet u ervoor zorgen dat specifieke routes naar de firewall worden gedefinieerd om de routes te overschrijven die on-premises zijn gedefinieerd vanuit BGP.
- Configureer een UDR in het subnet van de hubgateway die verwijst naar het IP-adres van de firewall als de volgende hop naar de spoke-netwerken. Er is geen door de gebruiker gedefinieerde route (UDR) nodig in het Azure Firewall-subnet, omdat het de routes overneemt van BGP.

Zie het gedeelte [Routes maken](#create-the-routes) in deze zelfstudie voor informatie over hoe deze routes worden gemaakt.

>[!NOTE]
>Azure Firewall moet een directe verbinding met internet hebben. Als uw AzureFirewallSubnet een standaardroute naar uw on-premises netwerk via BGP leert, moet u deze overschrijven met een UDR van 0.0.0.0/0 met de waarde **NextHopType** ingesteld op **Internet** om directe verbinding met internet te houden.
>
>Azure Firewall kan worden geconfigureerd voor ondersteuning van geforceerde tunneling. Zie [Geforceerde tunneling van Azure Firewall](forced-tunneling.md) voor meer informatie.

>[!NOTE]
>Verkeer tussen rechtstreeks gepeerde VNets wordt rechtstreeks gerouteerd, zelfs als de UDR naar Azure Firewall als standaardgateway wijst. Als u in dit scenario subnet-naar-subnet-verkeer wilt verzenden, moet een UDR het voorvoegsel van het doelsubnetwerk expliciet op beide subnetten bevatten.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-the-firewall-hub-virtual-network"></a>Het virtuele hub-netwerk voor de firewall maken

Maak eerst de resourcegroep voor de resources in deze zelfstudie:

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer op de startpagina van de Azure-portal **Resourcegroepen** > **Toevoegen**.
3. Geef voor **Resourcegroep** de naam **FW-Hybrid-Test** op.
4. Bij **Abonnement** selecteert u uw abonnement.
5. Selecteer bij **Regio** **VS - oost**. Alle resources die u later maakt, moeten zich op dezelfde locatie bevinden.
6. Selecteer **Controleren + maken**.
7. Selecteer **Maken**.

Maak nu het VNet:

> [!NOTE]
> De grootte van het subnet AzureFirewallSubnet is /26. Zie [Veelgestelde vragen over Azure Firewall](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size) voor meer informatie over de grootte van het subnet.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Selecteer **Virtueel netwerk** onder **Netwerken**.
4. Geef bij **Naam** **VNet-hub** op.
5. Bij **Adresruimte** typt u **10.5.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Geef voor **Resourcegroep** de naam **FW-Hybrid-Test** op.
8. Selecteer bij **Locatie** **VS - oost**.
9. Onder **Subnet** typt u bij **Naam** de naam **AzureFirewallSubnet**. De firewall zal zich in dit subnet bevinden, en de subnetnaam **moet** AzureFirewallSubnet zijn.
10. Bij **Adresbereik** typt u **10.5.0.0/26**. 
11. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**.

## <a name="create-the-spoke-virtual-network"></a>Het virtuele spoke-netwerk maken

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Selecteer **Virtueel netwerk** onder **Netwerken**.
4. Bij **Naam** typt u **VNet-Spoke**.
5. Bij **Adresruimte** typt u **10.6.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Geef voor **Resourcegroep** de naam **FW-Hybrid-Test** op.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Onder **Subnet** typt u **SN-Workload** bij **Naam**.
10. Bij **Adresbereik** typt u **10.6.0.0/24**.
11. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**.

## <a name="create-the-on-premises-virtual-network"></a>Het on-premises virtuele netwerk maken

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Selecteer **Virtueel netwerk** onder **Netwerken**.
4. Bij **Naam** typt u **VNet-OnPrem**.
5. Bij **Adresruimte** typt u **192.168.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Geef voor **Resourcegroep** de naam **FW-Hybrid-Test** op.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Onder **Subnet** typt u **SN-Corp** bij **Naam**.
10. Bij **Adresbereik** typt u **192.168.1.0/24**.
11. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**.

Maak nu een tweede subnet voor de gateway.

1. Selecteer op de pagina **VNet-Onprem** **Subnetten**.
2. Selecteer **+Subnet**.
3. Typ **GatewaySubnet** bij **Naam**.
4. Typ **192.168.2.0/24** bij **Adresbereik (CIDR-blok)** .
5. Selecteer **OK**.

## <a name="configure-and-deploy-the-firewall"></a>De firewall configureren en implementeren

Implementeer de firewall nu in het virtuele hub-netwerk voor de firewall.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Selecteer in de linkerkolom **Netwerken**en selecteer vervolgens **Firewall**.
4. Gebruik op de pagina **Firewall maken** de volgende tabel om de firewall te configureren:

   |Instelling  |Waarde  |
   |---------|---------|
   |Abonnement     |\<your subscription\>|
   |Resourcegroep     |**FW-Hybrid-Test** |
   |Naam     |**AzFW01**|
   |Locatie     |Selecteer dezelfde locatie die u eerder hebt gebruikt|
   |Een virtueel netwerk kiezen     |**Bestaande gebruiken**:<br> **VNet-hub**|
   |Openbaar IP-adres     |Nieuw netwerk maken: <br>**Naam** - **fw-pip**. |

5. Selecteer **Controleren + maken**.
6. Controleer de samenvatting en selecteer vervolgens **Maken** om de firewall te maken.

   Het implementeren duurt een paar minuten.
7. Als de implementatie is voltooid, gaat u naar de resourcegroep **FW-Hybrid-Test** en selecteert u de firewall **AzFW01**.
8. Noteer het privé-IP-adres. U zult het later gebruiken wanneer u de standaardroute maakt.

### <a name="configure-network-rules"></a>Netwerkregels configureren

Voeg eerst een netwerkregel toe om webverkeer toe te staan.

1. Selecteer **Regels** op de pagina **AzFW01**.
2. Selecteer het tabblad **Verzameling met netwerkregels**.
3. Selecteer **Verzameling met netwerkregels toevoegen**.
4. Bij **Naam** typt u **RCNet01**.
5. Bij **Prioriteit** typt u **100**.
6. Bij **Actie** selecteert u **Toestaan**.
6. Onder **Regels** typt u bij **Naam** de naam **AllowWeb**.
7. Bij **Protocol** selecteert u **TCP**.
8. Selecteer **IP-adres** bij **Brontype**.
9. Typ bij **Bron** **192.168.1.0/24**.
10. Typ bij **Doeladres** **10.6.0.0/16**
11. Typ bij **Doelpoorten** **80**.

Voeg nu een regel toe om RDP-verkeer toe te staan.

Typ op de rij van de tweede regel de volgende informatie:

1. Typ bij **Naam** **AllowRDP**.
2. Bij **Protocol** selecteert u **TCP**.
3. Selecteer **IP-adres** bij **Brontype**.
4. Typ bij **Bron** **192.168.1.0/24**.
5. Typ bij **Doeladres** **10.6.0.0/16**
6. Typ bij **Doelpoorten** **3389**.
7. Selecteer **Toevoegen**.

## <a name="create-and-connect-the-vpn-gateways"></a>De VPN-gateways maken en verbinden

Het virtuele hub-netwerk en het on-premises virtuele netwerk zijn verbonden via VPN-gateways.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Een VPN-gateway maken voor het virtuele hub-netwerk

Maak nu een VPN-gateway voor het virtuele hub-netwerk. Voor netwerk-naar-netwerk-configuraties is een RouteBased VpnType vereist. Het maken van een VPN-gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde VPN-gateway-SKU.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Typ in het zoekvak **virtuele netwerkgateway** en druk op **Enter**.
3. Selecteer **Virtuele netwerkgateway** en vervolgens **Maken**.
4. Bij **Naam** typt u **GW-hub**.
5. Bij **Regio** selecteert u dezelfde regio die u eerder hebt gebruikt.
6. Bij **Gatewaytype** selecteert u **VPN**.
7. Bij **VPN-type** selecteert u **Op route gebaseerd**.
8. Bij **SKU** selecteert u **Basis**.
9. Bij **Virtueel netwerk** selecteert u **VNet-hub**.
10. Bij **Openbaar IP-adres** selecteert u **Nieuwe maken** en typt u **VNet-hub-GW-pip** als de naam.
11. Accepteer voor de overige instellingen de standaardwaarden en selecteer **Beoordelen en maken**.
12. Controleer de configuratie en selecteer vervolgens **Maken**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Een VPN-gateway maken voor het on-premises virtuele netwerk

Maak nu de VPN-gateway voor het on-premises virtuele netwerk. Voor netwerk-naar-netwerk-configuraties is een RouteBased VpnType vereist. Het maken van een VPN-gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde VPN-gateway-SKU.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Typ in het zoekvak **virtuele netwerkgateway** en druk op **Enter**.
3. Selecteer **Virtuele netwerkgateway** en vervolgens **Maken**.
4. Bij **Naam** typt u **GW-Onprem**.
5. Bij **Regio** selecteert u dezelfde regio die u eerder hebt gebruikt.
6. Bij **Gatewaytype** selecteert u **VPN**.
7. Bij **VPN-type** selecteert u **Op route gebaseerd**.
8. Bij **SKU** selecteert u **Basis**.
9. Bij **Virtueel netwerk** selecteert u **VNet-Onprem**.
10. Bij **Openbaar IP-adres** selecteert u **Nieuwe maken** en typt u **VNet-Onprem-GW-pip** als de naam.
11. Accepteer voor de overige instellingen de standaardwaarden en selecteer **Beoordelen en maken**.
12. Controleer de configuratie en selecteer vervolgens **Maken**.

### <a name="create-the-vpn-connections"></a>De VPN-verbindingen maken

U kunt nu de VPN-verbindingen maken tussen de hub-gateway en de on-premises gateway.

In deze stap maakt u de verbinding van het virtuele hub-netwerk naar het on-premises virtuele netwerk. Hier ziet u een gedeelde sleutel waarnaar wordt verwezen in de voorbeelden. U kunt uw eigen waarden voor de gedeelde sleutel gebruiken. Het belangrijkste is dat de gedeelde sleutel voor beide verbindingen moet overeenkomen. Het kan even duren voordat de verbinding is gemaakt.

1. Open de resourcegroep **FW-Hybrid-Test** en selecteer de gateway **GW-hub**.
2. Selecteer **Verbindingen** in de linkerkolom.
3. Selecteer **Toevoegen**.
4. Typ **Hub-to-Onprem** als de naam van de verbinding.
5. Bij **Verbindingstype** selecteert u **VNet-naar-VNet**.
6. Bij **Tweede virtuele netwerkgateway** selecteert u **GW-Onprem**.
7. Bij **Gedeeld sleutel (PSK)** typt u **AzureA1b2C3**.
8. Selecteer **OK**.

Maak de verbinding van het on-premises virtuele netwerk naar het virtuele hub-netwerk. Deze stap is vergelijkbaar met de vorige, behalve dat u de verbinding maakt vanuit VNet-Onprem naar VNet-hub. Zorg dat de gedeelde sleutels overeenkomen. De verbinding wordt na enkele minuten tot stand gebracht.

1. Open de resourcegroep **FW-Hybrid-Test** en selecteer de gateway **GW-Onprem**.
2. Selecteer **Verbindingen** in de linkerkolom.
3. Selecteer **Toevoegen**.
4. Typ **Onprem-to-Hub** als de naam van de verbinding.
5. Bij **Verbindingstype** selecteert u **VNet-naar-VNet**.
6. Bij **Tweede virtuele netwerkgateway** selecteert u **GW-hub**.
7. Bij **Gedeeld sleutel (PSK)** typt u **AzureA1b2C3**.
8. Selecteer **OK**.


#### <a name="verify-the-connection"></a>De verbinding controleren

Na ongeveer vijf minuten moeten beide verbindingen de status **Verbonden** hebben.

![Gatewayverbindingen](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>De hub- en virtuele spoke-netwerken koppelen

Koppel nu de virtuele hub- en spoke-netwerken.

1. Open de resourcegroep **FW-Hybrid-Test** en selecteer het virtuele netwerk **VNet-hub**.
2. Selecteer in de linkerkolom **Peerings**.
3. Selecteer **Toevoegen**.
4. Bij **Naam** typt u **HubtoSpoke**.
5. Bij **Virtueel netwerk** selecteert u **VNet-spoke**.
6. Voor de naam van de peering van VNetSpoke naar VNet-hub typt u **SpoketoHub**.
7. Selecteer **Gatewayoverdracht toestaan**.
8. Selecteer **OK**.

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>Aanvullende instellingen configureren voor de SpoketoHub-peering

U moet **Doorgestuurd verkeer toestaan** inschakelen voor de SpoketoHub-peering.

1. Open de resourcegroep **FW-Hybrid-Test** en selecteer het virtuele netwerk **VNet-Spoke**.
2. Selecteer in de linkerkolom **Peerings**.
3. Selecteer de peering **SpoketoHub**.
4. Selecteer **Ingeschakeld** onder **Doorgestuurd verkeer van VNet-hub naar VNet-spoke toestaan**.
5. Selecteer **Opslaan**.

## <a name="create-the-routes"></a>De routes maken

Maak nu een paar routes:

- Een route van het subnet van de hub-gateway naar het spoke-subnet via het IP-adres van de firewall
- Een standaardroute van het spoke-subnet via het IP-adres van de firewall

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Typ in het zoekvak **routeringstabel** en druk op **Enter**.
3. Selecteer **Routeringstabel**.
4. Selecteer **Maken**.
5. Voor de naam typt u **UDR-Hub-Spoke**.
6. Selecteer **FW-Hybrid-Test** als de resourcegroep.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Selecteer **Maken**.
10. Nadat de routeringstabel is gemaakt, selecteert u deze om de pagina Routeringstabel te openen.
11. Selecteer **Routes** in de linkerkolom.
12. Selecteer **Toevoegen**.
13. Voor de routenaam typt u **ToSpoke**.
14. Voor het adresvoorvoegsel typt u **10.6.0.0/16**.
15. Voor het volgende hoptype selecteert u **Virtueel apparaat**.
16. Voor het adres van de volgende hop typt u het privé-IP-adres voor de firewall dat u eerder hebt genoteerd.
17. Selecteer **OK**.

Koppel nu de route aan het subnet.

1. Selecteer op de pagina **UDR-Hub-Spoke - Routes** **Subnetten**.
2. Selecteer **Koppelen**.
3. Selecteer **Een virtueel netwerk kiezen**.
4. Selecteer **VNet-hub**.
5. Selecteer **GatewaySubnet**.
6. Selecteer **OK**.

Maak nu de standaardroute vanaf het spoke-subnet.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Typ in het zoekvak **routeringstabel** en druk op **Enter**.
3. Selecteer **Routeringstabel**.
5. Selecteer **Maken**.
6. Voor de naam typt u **UDR-DG**.
7. Selecteer **FW-Hybrid-Test** als de resourcegroep.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
4. Selecteer **Uitgeschakeld** voor **Doorgifte van route van virtuele netwerkgateway**.
1. Selecteer **Maken**.
2. Nadat de routeringstabel is gemaakt, selecteert u deze om de pagina Routeringstabel te openen.
3. Selecteer **Routes** in de linkerkolom.
4. Selecteer **Toevoegen**.
5. Voor de routenaam typt u **ToHub**.
6. Voor het adresvoorvoegsel typt u **0.0.0.0/0**.
7. Voor het volgende hoptype selecteert u **Virtueel apparaat**.
8. Voor het adres van de volgende hop typt u het privé-IP-adres voor de firewall dat u eerder hebt genoteerd.
9. Selecteer **OK**.

Koppel nu de route aan het subnet.

1. Selecteer op de pagina **UDR-DG - Routes** **Subnetten**.
2. Selecteer **Koppelen**.
3. Selecteer **Een virtueel netwerk kiezen**.
4. Selecteer **VNet-spoke**.
5. Selecteer **SN-Workload**.
6. Selecteer **OK**.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak nu de spoke-workloadmachines en on-premises virtuele machines en plaats ze in de toepasselijke subnetten.

### <a name="create-the-workload-virtual-machine"></a>De virtuele workloadmachine maken

Maak in het virtuele spoke-netwerk een virtuele machine waarop IIS wordt uitgevoerd, zonder openbaar IP-adres.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Selecteer **Windows Server 2016 Datacenter** onder **Populair**.
3. Voer deze waarden in voor de virtuele machine:
    - **Resourcegroep**: selecteer **FW-Hybrid-Test**.
    - **Naam van virtuele machine**: *VM-Spoke-01*.
    - **Regio**: dezelfde regio die u eerder hebt gebruikt.
    - **Gebruikersnaam**: *azureuser*.
    - **Wachtwoord**: *Azure123456!*
4. Selecteer **Volgende: schijven**.
5. Accepteer de standaardwaarden en selecteer **Volgende: Netwerken**.
6. Selecteer **VNet-Spoke** voor het virtuele netwerk en het subnet is **SN-Workload**.
7. Selecteer **Geen** voor **Openbaar IP**.
8. Selecteer voor **Openbare binnenkomende poorten** **Geselecteerde poorten toestaan** en selecteer vervolgens **HTTP (80)** en **RDP (3389)** .
9. Selecteer **Volgende: Beheer**.
10. Selecteer **Uit** voor **Diagnostische gegevens over opstarten**.
11. Selecteer **Beoordelen en maken**, controleer de instellingen op de overzichtspagina en selecteer ten slotte **Maken**.

### <a name="install-iis"></a>IIS installeren

1. Open Cloud Shell via de Azure-portal en controleer of deze is ingesteld op **PowerShell**.
2. Voer de volgende opdracht uit om IIS op de virtuele machine te installeren en indien nodig de locatie te wijzigen:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>De on-premises virtuele machine maken

Dit is een virtuele machine waarmee u verbinding kunt maken met het openbare IP-adres via Extern bureaublad. Vanaf daar maakt u vervolgens verbinding met de on-premises server via de firewall.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Selecteer **Windows Server 2016 Datacenter** onder **Populair**.
3. Voer deze waarden in voor de virtuele machine:
    - **Resourcegroep**: selecteer Bestaande gebruiken en selecteer vervolgens **FW-Hybrid-Test**.
    - **Naam virtuele machine** - *VM-Onprem*.
    - **Regio**: dezelfde regio die u eerder hebt gebruikt.
    - **Gebruikersnaam**: *azureuser*.
    - **Wachtwoord**: *Azure123456!* .
4. Selecteer **Volgende: schijven**.
5. Accepteer de standaardwaarden en selecteer **Volgende: Netwerken**.
6. Selecteer **VNet-Onprem** voor het virtuele netwerk en het subnet is **SN-Corp**.
7. Selecteer voor **Openbare binnenkomende poorten** **Geselecteerde poorten toestaan** en selecteer vervolgens **RDP (3389)** .
8. Selecteer **Volgende: Beheer**.
9. Selecteer **Uit** voor **Diagnostische gegevens over opstarten**.
10. Selecteer **Beoordelen en maken**, controleer de instellingen op de overzichtspagina en selecteer ten slotte **Maken**.

## <a name="test-the-firewall"></a>De firewall testen

1. Noteer eerst het privé-IP-adres van de virtuele machine **VM-spoke-01**.

2. Maak vanuit de Azure-portal verbinding met de virtuele machine **VM-OnPrem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Open een webbrowser op **VM-OnPrem** en ga naar http://\<VM-spoke-01 private IP\>.

   U ziet de webpagina **VM-spoke-01**: ![Webpagina VM-Spoke-01](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. Maak vanaf de virtuele machine **VM-Onprem** via Extern bureaublad verbinding met **VM-spoke-01** op het privé-IP-adres.

   Deze verbinding moet worden gemaakt en u moet zich kunnen aanmelden.

Nu u hebt geverifieerd dat de firewallregels werken:

<!---- You can ping the server on the spoke VNet.--->
- U kunt de bladeren op de webserver in het virtuele spoke-netwerk.
- U kunt verbinding maken met de server in het virtuele spoke-netwerk met behulp van RDP.

Wijzig vervolgens de verzameling netwerkregels van de firewall in **Weigeren** om te controleren of de regels werken zoals verwacht.

1. Selecteer de firewall **AzFW01**.
2. Selecteer **Regels**.
3. Selecteer het tabblad **Verzameling met netwerkregels** en selecteer de verzameling regels **RCNet01**.
4. Bij **Actie** selecteert u **Weigeren**.
5. Selecteer **Opslaan**.

Sluit eventuele externe bureaubladen voordat u de gewijzigde regels test. Voer nu de tests opnieuw uit. Ze moeten deze keer allemaal mislukken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de firewall-resources voor de volgende zelfstudie bewaren. Als u ze niet meer nodig hebt, verwijdert u de resourcegroep **FW-Hybrid-Test** om alle firewall-gerelateerde resources te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Als volgende kunt u de Azure Firewall-logboeken bewaken.

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall-logboeken bewaken](./tutorial-diagnostics.md)
