---
title: 'Zelfstudie: Azure Firewall implementeren en configureren in een hybride netwerk met behulp van de Azure-portal'
description: In deze zelfstudie leert u hoe u Azure Firewall implementeert en configureert met Azure-portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 208a7a677bdf0b76ffed83e679c6f1ff3041d50d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239690"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Zelfstudie: Azure Firewall implementeren en configureren in een hybride netwerk met behulp van de Azure-portal

Als u het on-premises netwerk verbindt met een virtueel Azure-netwerk om een hybride netwerk te maken, is de mogelijkheid om de toegang tot uw Azure-netwerkresources te beheren een belangrijk onderdeel van een algemeen beveiligingsplan.

U kunt Azure Firewall gebruiken om de netwerktoegang in een hybride netwerk te beheren met behulp van de regels die toegestaan en geweigerd netwerkverkeer definiëren.

Voor deze zelfstudie maakt u drie virtuele netwerken:

- **VNet-Hub**: de firewall bevindt zich in dit virtuele netwerk.
- **VNet-spoke**: het virtuele spoke-netwerk vertegenwoordigt de workload die zich bevindt in Azure.
- **VNet-Onprem**: het on-premises virtuele netwerk vertegenwoordigt een on-premises netwerk. In een daadwerkelijke implementatie kan het worden verbonden via een VPN- of ExpressRoute-verbinding. Om het eenvoudig te houden wordt in deze zelfstudie een VPN-gatewayverbinding gebruikt en wordt een on-premises netwerk vertegenwoordigd door een virtueel Azure-netwerk.

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

Zie [Azure Firewall implementeren en configureren in een hybride netwerk met Azure PowerShell](tutorial-hybrid-ps.md)als u Azure PowerShell wilt gebruiken om deze procedure te voltooien.

## <a name="prerequisites"></a>Vereisten

Een hybride netwerk maakt gebruik van het hub-and-spoke architectuurmodel om verkeer tussen Azure VNets en on-premise netwerken te routeren. De hub-and-spoke architectuur heeft de volgende eisen:

- Stel **AllowGatewayTransit** in wanneer u VNet-Hub aanVNet-Spoke kijkt. In een hub-and-spoke netwerkarchitectuur, een gateway transit kan de spaak virtuele netwerken om de VPN-gateway te delen in de hub, in plaats van het implementeren van VPN-gateways in elk spaak virtueel netwerk. 

   Bovendien worden routes naar de virtuele netwerken met gateway-verbinding of on-premises netwerken automatisch doorgegeven aan de routeringstabellen voor de peered virtuele netwerken met behulp van de gatewaytransit. Zie [VPN-gatewaytransit configureren voor virtueel netwerkpeeren voor](../vpn-gateway/vpn-gateway-peering-gateway-transit.md)meer informatie.

- Stel **UseRemoteGateways** in wanneer u vnet-spoke met VNet-Hub gebruikt. Als **UseRemoteGateways** is ingesteld en **AllowGatewayTransit** op remote peering ook is ingesteld, maakt het gesproken virtuele netwerk gebruik van gateways van het externe virtuele netwerk voor doorvoer.
- Als u het gesproken subnetverkeer door de hubfirewall wilt leiden, hebt u een UDR (User Defined route) nodig die naar de firewall verwijst met de optie voor het uitzetten van de **virtuele netwerkgatewayroute.** De optie **Voor het uitzetten van** de route voor routes voorkomt routedistributie naar de spaaksubnetten. Dit voorkomt dat geleerde routes in strijd zijn met uw UDR.
- Configureer een UDR op het subnet hubgateway dat naar het IP-adres van de firewall verwijst als de volgende hop naar de gesproken netwerken. Er is geen door de gebruiker gedefinieerde route (UDR) nodig in het Azure Firewall-subnet, omdat het de routes overneemt van BGP.

Zie het gedeelte [Routes maken](#create-the-routes) in deze zelfstudie voor informatie over hoe deze routes worden gemaakt.

>[!NOTE]
>Azure Firewall moet een directe internetverbinding hebben. Als uw AzureFirewallSubnet via BGP een standaardroute naar uw on-premises netwerk leert, moet u dit overschrijven met een 0.0.0.0/0 UDR met de **NextHopType-waarde** ingesteld als **internet** om directe internetverbinding te behouden.
>
>Azure Firewall kan worden geconfigureerd ter ondersteuning van gedwongen tunneling. Zie [Azure Firewall forced tunneling](forced-tunneling.md)voor meer informatie.

>[!NOTE]
>Verkeer tussen rechtstreeks gepeerde VNets wordt rechtstreeks gerouteerd, zelfs als de UDR naar Azure Firewall als standaardgateway wijst. Als u in dit scenario subnet-naar-subnet-verkeer wilt verzenden, moet een UDR het voorvoegsel van het doelsubnetwerk expliciet op beide subnetten bevatten.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-the-firewall-hub-virtual-network"></a>Het virtuele hub-netwerk voor de firewall maken

Maak eerst de resourcegroep voor de resources in deze zelfstudie:

1. Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .
2. Selecteer op de startpagina van de Azure-portal de optie **Resourcegroepen** > **Toevoegen**.
3. Typ **FW-Hybrid-Test**voor **de naam van de resourcegroep**.
4. Bij **Abonnement** selecteert u uw abonnement.
5. Selecteer **Voor Regio** **Oost-VS**. Alle resources die u later maakt, moeten zich op dezelfde locatie bevinden.
6. Selecteer **Controleren + maken**.
7. Selecteer **Maken**.

Maak nu het VNet:

> [!NOTE]
> De grootte van het subnet AzureFirewallSubnet is /26. Zie [Veelgestelde vragen](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)over azure firewall voor meer informatie over de subnetgrootte.

1. Selecteer op de startpagina van de Azure-portal de optie **Een bron maken**.
2. Selecteer **Onder Netwerken**de optie Virtueel **netwerk**.
4. Typ **VNet-hub**voor **Naam**.
5. Typ **voor Adresruimte** **10.5.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Selecteer **FW-Hybrid-Test**voor **resourcegroep**.
8. Selecteer **Oost-VS**voor **Locatie**.
9. Onder **Subnet** typt u bij **Naam** de naam **AzureFirewallSubnet**. De firewall zal zich in dit subnet bevinden, en de subnetnaam **moet** AzureFirewallSubnet zijn.
10. Voor **adresbereik**typt u **10.5.0.0/26**. 
11. Accepteer de andere standaardinstellingen en selecteer **Vervolgens Maken**.

## <a name="create-the-spoke-virtual-network"></a>Het virtuele spoke-netwerk maken

1. Selecteer op de startpagina van de Azure-portal de optie **Een bron maken**.
2. Selecteer **Onder Netwerken**de optie Virtueel **netwerk**.
4. Typ **VNet-Spoke**voor **Naam**.
5. Typ **voor Adresruimte** **10.6.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Selecteer **FW-Hybrid-Test**voor **resourcegroep**.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Onder **Subnet** typt u **SN-Workload** bij **Naam**.
10. Voor **adresbereik**typt u **10.6.0.0/24**.
11. Accepteer de andere standaardinstellingen en selecteer **Vervolgens Maken**.

## <a name="create-the-on-premises-virtual-network"></a>Het on-premises virtuele netwerk maken

1. Selecteer op de startpagina van de Azure-portal de optie **Een bron maken**.
2. Selecteer **Onder Netwerken**de optie Virtueel **netwerk**.
4. Typ **VNet-OnPrem**voor **naam**.
5. Bij **Adresruimte** typt u **192.168.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Selecteer **FW-Hybrid-Test**voor **resourcegroep**.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Onder **Subnet**, voor **naamtype** **SN-Corp**.
10. Bij **Adresbereik** typt u **192.168.1.0/24**.
11. Accepteer de andere standaardinstellingen en selecteer **Vervolgens Maken**.

Maak nu een tweede subnet voor de gateway.

1. Selecteer **Subnetten**op de pagina **VNet-Onprem** .
2. Selecteer **+Subnet**.
3. Typ **GatewaySubnet**voor **Naam**.
4. Voor **adresbereik (CIDR-blok)** type **192.168.2.0/24**.
5. Selecteer **OK**.

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Dit is het openbare IP-adres dat wordt gebruikt voor de on-premises gateway.

1. Selecteer op de startpagina van de Azure-portal de optie **Een bron maken**.
2. Typ in het tekstvak zoeken **het openbare IP-adres** en druk op **Enter**.
3. Selecteer **Openbaar IP-adres** en selecteer **Vervolgens Maken**.
4. Typ **VNet-Onprem-GW-pip**voor de naam.
5. Typ **FW-Hybrid-Test**voor de resourcegroep .
6. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
7. Accepteer de andere standaardinstellingen en selecteer **Vervolgens Maken**.

## <a name="configure-and-deploy-the-firewall"></a>De firewall configureren en implementeren

Implementeer de firewall nu in het virtuele firewall-netwerk.

1. Selecteer op de startpagina van de Azure-portal de optie **Een bron maken**.
2. Selecteer **netwerken**in de linkerkolom en selecteer **Vervolgens Firewall**.
4. Gebruik op de pagina **Firewall maken** de volgende tabel om de firewall te configureren:

   |Instelling  |Waarde  |
   |---------|---------|
   |Abonnement     |\<uw abonnement\>|
   |Resourcegroep     |**FW-Hybrid-Test** |
   |Name     |**AzFW01 AzFW01**|
   |Locatie     |Selecteer dezelfde locatie die u eerder hebt gebruikt|
   |Een virtueel netwerk kiezen     |**Bestaande gebruiken**:<br> **VNet-hub**|
   |Openbaar IP-adres     |Nieuw maken: <br>**Noem** - **fw-pip**. |

5. Selecteer **Controleren + maken**.
6. Bekijk het overzicht en selecteer **Maken** om de firewall te maken.

   Dit duurt een paar minuten om te implementeren.
7. Nadat de implementatie is voltooid, gaat u naar de brongroep **FW-Hybrid-Test** en selecteert u de **AzFW01-firewall.**
8. Noteer het privé-IP-adres. U zult het later gebruiken wanneer u de standaardroute maakt.

### <a name="configure-network-rules"></a>Netwerkregels configureren

Voeg eerst een netwerkregel toe om webverkeer toe te staan.

1. Selecteer **regels**op de pagina **AzFW01** .
2. Selecteer het tabblad **Netwerkregelverzameling.**
3. Selecteer **Netwerkregelverzameling toevoegen**.
4. Typ **RCNet01**voor **naam**.
5. Voor **prioriteit**, type **100**.
6. Bij **Actie** selecteert u **Toestaan**.
6. Typ **AllowWeb**onder **Regels**voor **Naam**.
7. Bij **Protocol** selecteert u **TCP**.
8. Selecteer **IP-adres**voor **Brontype**.
9. Voor **bron**, type **192.168.1.0/24**.
10. Voor **bestemmingsadres**, type **10.6.0.0/16**
11. Voor **bestemmingspoorten**typt u **80**.

Voeg nu een regel toe om RDP-verkeer toe te staan.

Typ in de rij met de tweede regel de volgende gegevens:

1. **Naam**, typ **AllowRDP**.
2. Bij **Protocol** selecteert u **TCP**.
3. Selecteer **IP-adres**voor **Brontype**.
4. Voor **bron**, type **192.168.1.0/24**.
5. Voor **bestemmingsadres**, type **10.6.0.0/16**
6. Voor **bestemmingshavens**typt u **3389**.
7. Selecteer **Toevoegen**.

## <a name="create-and-connect-the-vpn-gateways"></a>De VPN-gateways maken en verbinden

Het virtuele hub-netwerk en het on-premises virtuele netwerk zijn verbonden via VPN-gateways.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Een VPN-gateway maken voor het virtuele hub-netwerk

Maak nu een VPN-gateway voor het virtuele hub-netwerk. Voor netwerk-naar-netwerk-configuraties is een RouteBased VpnType vereist. Het maken van een VPN-gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde VPN-gateway-SKU.

1. Selecteer op de startpagina van de Azure-portal de optie **Een bron maken**.
2. Typ virtuele **netwerkgateway** in het tekstvak zoeken en druk op **Enter**.
3. Selecteer **Virtuele netwerkgateway**en selecteer **Maken**.
4. Typ **VOOR Naam** **GW-hub**.
5. Selecteer **bij Regio**dezelfde regio die u eerder hebt gebruikt.
6. Selecteer **VPN**voor **gatewaytype**.
7. Selecteer **routegebaseerd voor VPN-type**. **Route-based**
8. Selecteer **Basic**voor **SKU**.
9. Selecteer **VNet-hub**voor **virtueel netwerk**.
10. Selecteer VNet-hub-GW-pip **voor**de naam voor **Openbaar IP-adres**en typ **VNet-hub-GW-pip.**
11. Accepteer de resterende standaardinstellingen en selecteer **Controleren + maken**.
12. Controleer de configuratie en selecteer **Vervolgens Maken**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Een VPN-gateway maken voor het on-premises virtuele netwerk

Maak nu de VPN-gateway voor het on-premises virtuele netwerk. Voor netwerk-naar-netwerk-configuraties is een RouteBased VpnType vereist. Het maken van een VPN-gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde VPN-gateway-SKU.

1. Selecteer op de startpagina van de Azure-portal de optie **Een bron maken**.
2. Typ virtuele **netwerkgateway** in het tekstvak zoeken en druk op **Enter**.
3. Selecteer **Virtuele netwerkgateway**en selecteer **Maken**.
4. Voor **naam**, typ **GW-Onprem**.
5. Selecteer **bij Regio**dezelfde regio die u eerder hebt gebruikt.
6. Selecteer **VPN**voor **gatewaytype**.
7. Selecteer **routegebaseerd voor VPN-type**. **Route-based**
8. Selecteer **Basic**voor **SKU**.
9. Selecteer **VNet-Onprem**voor **virtueel netwerk**.
10. Selecteer VNet-Onprem-GW-pip **voor**de naam voor **Openbaar IP-adres**en typ **VNet-Onprem-GW-pip.**
11. Accepteer de resterende standaardinstellingen en selecteer **Controleren + maken**.
12. Controleer de configuratie en selecteer **Vervolgens Maken**.

### <a name="create-the-vpn-connections"></a>De VPN-verbindingen maken

Nu u de VPN-verbindingen tussen de hub en on-premises gateways maken.

In deze stap maakt u de verbinding van het virtuele hub-netwerk naar het on-premises virtuele netwerk. Hier ziet u een gedeelde sleutel waarnaar wordt verwezen in de voorbeelden. U kunt uw eigen waarden voor de gedeelde sleutel gebruiken. Het belangrijkste is dat de gedeelde sleutel voor beide verbindingen moet overeenkomen. Het kan even duren voordat de verbinding is gemaakt.

1. Open de **brongroep FW-Hybrid-Test** en selecteer de **GW-hubgateway.**
2. Selecteer **Verbindingen** in de linkerkolom.
3. Selecteer **Toevoegen**.
4. De verbindingsnaam, type **Hub-to-Onprem**.
5. Selecteer **VNet-naar-VNet** voor **verbindingstype**.
6. Selecteer **GW-Onprem**voor de **tweede virtuele netwerkgateway**.
7. Typ **AzureA1b2C3** **voor gedeelde sleutel (PSK)**.
8. Selecteer **OK**.

Maak de verbinding van het on-premises virtuele netwerk naar het virtuele hub-netwerk. Deze stap is vergelijkbaar met de vorige, behalve dat u de verbinding maakt vanuit VNet-Onprem naar VNet-hub. Zorg dat de gedeelde sleutels overeenkomen. De verbinding wordt na enkele minuten tot stand gebracht.

1. Open de **brongroep FW-Hybrid-Test** en selecteer de **GW-Onprem-gateway.**
2. Selecteer **Verbindingen** in de linkerkolom.
3. Selecteer **Toevoegen**.
4. De verbindingsnaam, typ **Onprem-to-Hub**.
5. Selecteer **VNet-naar-VNet** voor **verbindingstype**.
6. Selecteer **GW-hub**voor de **tweede virtuele netwerkgateway**.
7. Typ **AzureA1b2C3** **voor gedeelde sleutel (PSK)**.
8. Selecteer **OK**.


#### <a name="verify-the-connection"></a>De verbinding controleren

Na ongeveer vijf minuten of zo, moet de status van beide verbindingen worden **aangesloten**.

![Gatewayverbindingen](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>De hub- en virtuele spoke-netwerken koppelen

Koppel nu de virtuele hub- en spoke-netwerken.

1. Open de **brongroep FW-Hybrid-Test** en selecteer het virtuele **VNet-hub-netwerk.**
2. Selecteer **Peerings**in de linkerkolom .
3. Selecteer **Toevoegen**.
4. Typ **HubtoSpoke**voor **Naam**.
5. Selecteer **VNet-spaaks** voor het **virtuele netwerk**
6. Typ **SpoketoHub**voor de naam van het peering van VNetSpoke naar VNet-hub.
7. Selecteer **Gateway-doorvoer toestaan**.
8. Selecteer **OK**.

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>Aanvullende instellingen configureren voor het spoketohub-peering

U moet het **doorgestuurde verkeer toestaan** op de SpoketoHub-peering inschakelen.

1. Open de **brongroep FW-Hybrid-Test** en selecteer het virtuele **VNet-Spoke-netwerk.**
2. Selecteer **Peerings**in de linkerkolom .
3. Selecteer de **SpoketoHub-peering.**
4. Selecteer Ingeschakeld **onder Doorgestuurd verkeer toestaan van VNet-hub naar VNet-Spoke** **.**
5. Selecteer **Opslaan**.

## <a name="create-the-routes"></a>De routes maken

Maak nu een paar routes:

- Een route van het subnet van de hub-gateway naar het spoke-subnet via het IP-adres van de firewall
- Een standaardroute van het spoke-subnet via het IP-adres van de firewall

1. Selecteer op de startpagina van de Azure-portal de optie **Een bron maken**.
2. Typ in het tekstvak zoeken **routetabel** en druk op **Enter**.
3. Selecteer **Routetabel**.
4. Selecteer **Maken**.
5. Typ **UDR-Hub-Spoke**voor de naam.
6. Selecteer de **FW-Hybrid-Test** voor de resourcegroep.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Selecteer **Maken**.
10. Nadat de routetabel is gemaakt, selecteert u deze om de routetabelpagina te openen.
11. Selecteer **Routes** in de linkerkolom.
12. Selecteer **Toevoegen**.
13. Typ **ToSpoke**voor de routenaam .
14. Typ **10.6.0.0/16**voor het adresvoorvoegsel .
15. Selecteer **Virtueel toestel**voor het volgende hoptype .
16. Typ voor het volgende hopadres het privé-IP-adres van de firewall dat u eerder hebt opgemerkt.
17. Selecteer **OK**.

Koppel de route nu aan het subnet.

1. Selecteer **subnetten**op de pagina **UDR-Hub-Spoke - Routes** .
2. Selecteer **Associate**.
3. Selecteer **Een virtueel netwerk kiezen**.
4. Selecteer **VNet-hub**.
5. Selecteer **GatewaySubnet**.
6. Selecteer **OK**.

Maak nu de standaardroute van het gesproken subnet.

1. Selecteer op de startpagina van de Azure-portal de optie **Een bron maken**.
2. Typ in het tekstvak zoeken **routetabel** en druk op **Enter**.
3. Selecteer **Routetabel**.
5. Selecteer **Maken**.
6. Typ **UDR-DG**voor de naam .
7. Selecteer de **FW-Hybrid-Test** voor de resourcegroep.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
4. Selecteer **Uitgeschakeld**voor **het uitroepen van virtuele netwerkgatewayroute**.
1. Selecteer **Maken**.
2. Nadat de routetabel is gemaakt, selecteert u deze om de routetabelpagina te openen.
3. Selecteer **Routes** in de linkerkolom.
4. Selecteer **Toevoegen**.
5. Typ **ToHub**voor de routenaam .
6. Typ **0.0.0.0/0**voor het adresvoorvoegsel .
7. Selecteer **Virtueel toestel**voor het volgende hoptype .
8. Typ voor het volgende hopadres het privé-IP-adres van de firewall dat u eerder hebt opgemerkt.
9. Selecteer **OK**.

Koppel de route nu aan het subnet.

1. Selecteer **subnetten**op de pagina **UDR-DG - Routes** .
2. Selecteer **Associate**.
3. Selecteer **Een virtueel netwerk kiezen**.
4. Selecteer **VNet-spaakjes**.
5. Selecteer **SN-workload**.
6. Selecteer **OK**.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak nu de spoke-workloadmachines en on-premises virtuele machines en plaats ze in de toepasselijke subnetten.

### <a name="create-the-workload-virtual-machine"></a>De virtuele workloadmachine maken

Maak een virtuele machine in het gesproken virtuele netwerk, met IIS, zonder openbaar IP-adres.

1. Selecteer op de startpagina van de Azure-portal de optie **Een bron maken**.
2. Selecteer Windows **Server 2016 Datacenter**onder **Populair**.
3. Voer deze waarden in voor de virtuele machine:
    - **Resourcegroep** - Selecteer **FW-Hybrid-Test**.
    - **Naam virtuele machine**: *VM-Spoke-01*.
    - **Regio** - Dezelfde regio die u eerder hebt gebruikt.
    - **Gebruikersnaam**: *azureuser*.
    - **Wachtwoord**: *Azure123456!*
4. Selecteer **Volgende: schijven**.
5. Accepteer de standaardinstellingen en selecteer **Volgende: Netwerken**.
6. Selecteer **VNet-Spoke** voor het virtuele netwerk en het subnet is **SN-Workload.**
7. Selecteer **Geen voor** **openbaar IP**.
8. Selecteer **geselecteerde poorten toestaan**voor openbare **binnenkomende poorten**en selecteer vervolgens HTTP **(80)** en **RDP (3389)**
9. Selecteer **Volgende:Beheer**.
10. Selecteer **Boot diagnostics** **Uit .**
11. Selecteer **Controleren+Maken,** controleer de instellingen op de overzichtspagina en selecteer **Vervolgens Maken**.

### <a name="install-iis"></a>IIS installeren

1. Open de Cloud Shell vanuit de Azure-portal en controleer of deze is ingesteld op **PowerShell.**
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

Dit is een virtuele machine die u gebruikt om verbinding te maken met Extern bureaublad met het openbare IP-adres. Vanaf daar maakt u vervolgens verbinding met de on-premises server via de firewall.

1. Selecteer op de startpagina van de Azure-portal de optie **Een bron maken**.
2. Selecteer Windows **Server 2016 Datacenter**onder **Populair**.
3. Voer deze waarden in voor de virtuele machine:
    - **Resourcegroep** - Selecteer bestaande en selecteer **vervolgens FW-Hybrid-Test**.
    - **Virtuele machine naam** - *VM-Onprem*.
    - **Regio** - Dezelfde regio die u eerder hebt gebruikt.
    - **Gebruikersnaam**: *azureuser*.
    - **Wachtwoord**: *Azure123456!*.
4. Selecteer **Volgende: schijven**.
5. Accepteer de standaardinstellingen en selecteer **Volgende:Netwerken**.
6. Selecteer **VNet-Onprem** voor virtueel netwerk en het subnet is **SN-Corp**.
7. Selecteer RdP (3389) **voor** **openbare binnenkomende poorten**en selecteer vervolgens **RDP (3389)**
8. Selecteer **Volgende:Beheer**.
9. Selecteer **Boot diagnostics** **Uit .**
10. Selecteer **Controleren+Maken,** controleer de instellingen op de overzichtspagina en selecteer **Vervolgens Maken**.

## <a name="test-the-firewall"></a>De firewall testen

1. Let eerst op het privé-IP-adres voor **virtuele VM-01-machine.**

2. Maak vanuit de Azure-portal verbinding met de virtuele machine **VM-OnPrem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Open een webbrowser op **VM-OnPrem** en blader naar http://\<privé-IP-adres VM-spoke-01\>.

   U ziet de **VM-spoke-01** ![webpagina: VM-Spoke-01 webpagina](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. Open vanaf de **virtuele MACHINE VM-Onprem** een extern bureaublad tot **VM-spaak-01** op het privé-IP-adres.

   Uw verbinding moet slagen en u moet zich kunnen aanmelden.

Dus nu heb je geverifieerd dat de firewall regels werken:

<!---- You can ping the server on the spoke VNet.--->
- U kunt de bladeren op de webserver in het virtuele spoke-netwerk.
- U kunt verbinding maken met de server in het virtuele spoke-netwerk met behulp van RDP.

Wijzig vervolgens de verzameling netwerkregels van de firewall in **Weigeren** om te controleren of de regels werken zoals verwacht.

1. Selecteer de **AzFW01-firewall.**
2. Selecteer **Regels**.
3. Selecteer het tabblad **Netwerkregelverzameling** en selecteer de **verzameling RCNet01-regels.**
4. Selecteer **Weigeren**voor **Actie**.
5. Selecteer **Opslaan**.

Sluit eventuele externe bureaubladen voordat u de gewijzigde regels test. Voer nu de tests opnieuw uit. Ze moeten deze keer allemaal mislukken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de firewall-resources voor de volgende zelfstudie bewaren. Als u ze niet meer nodig hebt, verwijdert u de resourcegroep **FW-Hybrid-Test** om alle firewall-gerelateerde resources te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Als volgende kunt u de Azure Firewall-logboeken bewaken.

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall-logboeken bewaken](./tutorial-diagnostics.md)
