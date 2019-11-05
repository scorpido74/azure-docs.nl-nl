---
title: 'Zelf studie: Azure Firewall implementeren en configureren in een hybride netwerk met behulp van de Azure Portal'
description: In deze zelf studie leert u hoe u Azure Firewall implementeert en configureert met behulp van Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/02/2019
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 4a4fd2f89bc662f394b59aa6295c3a909cb8552b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468461"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Zelf studie: Azure Firewall implementeren en configureren in een hybride netwerk met behulp van de Azure Portal

Als u het on-premises netwerk verbindt met een virtueel Azure-netwerk om een hybride netwerk te maken, is de mogelijkheid om de toegang tot uw Azure-netwerkresources te beheren een belangrijk onderdeel van een algemeen beveiligingsplan.

U kunt Azure Firewall gebruiken om de netwerktoegang in een hybride netwerk te beheren met behulp van de regels die toegestaan en geweigerd netwerkverkeer definiëren.

Voor deze zelfstudie maakt u drie virtuele netwerken:

- **VNet-Hub**: de firewall bevindt zich in dit virtuele netwerk.
- **VNet-spoke**: het virtuele spoke-netwerk vertegenwoordigt de workload die zich bevindt in Azure.
- **VNet-Onprem**: het on-premises virtuele netwerk vertegenwoordigt een on-premises netwerk. In een daad werkelijke implementatie kan het worden verbonden door een VPN-of ExpressRoute-verbinding. Om het eenvoudig te houden wordt in deze zelfstudie een VPN-gatewayverbinding gebruikt en wordt een on-premises netwerk vertegenwoordigd door een virtueel Azure-netwerk.

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

Als u in plaats daarvan Azure PowerShell wilt gebruiken om deze procedure te volt ooien, raadpleegt u [Azure firewall implementeren en configureren in een hybride netwerk met behulp van Azure PowerShell](tutorial-hybrid-ps.md).

## <a name="prerequisites"></a>Vereisten

Er zijn drie belangrijke vereisten voor de correcte werking van dit scenario:

- Een door de gebruiker gedefinieerde route (UDR) in het spoke-subnet die verwijst naar het IP-adres van Azure Firewall als de standaardgateway. Doorgifte van BGP-route moet zijn **Uitgeschakeld** voor deze routetabel.
- Een door de gebruiker gedefinieerde route (UDR) in het subnet van de hubgateway moet verwijzen naar het IP-adres van de firewall als de volgende hop naar de spoke-netwerken.

   Er is geen door de gebruiker gedefinieerde route (UDR) nodig in het Azure Firewall-subnet, omdat het de routes overneemt van BGP.
- Zorg dat u **AllowGatewayTransit** instelt voor de peering van VNet-Hub naar VNet-Spoke en **UseRemoteGateways** voor de peering van VNet-Spoke naar VNet-Hub.

Zie het gedeelte [Routes maken](#create-the-routes) in deze zelfstudie voor informatie over hoe deze routes worden gemaakt.

>[!NOTE]
>Azure Firewall moet een rechtstreekse Internet verbinding hebben. Als uw AzureFirewallSubnet een standaard route naar uw on-premises netwerk via BGP leert, moet u dit overschrijven met een 0.0.0.0/0-UDR met de **NextHopType** -waarde ingesteld als **Internet** om directe Internet connectiviteit te onderhouden.
>
>Azure Firewall biedt momenteel geen ondersteuning voor geforceerde tunneling. Als voor uw configuratie geforceerde tunneling naar een on-premises netwerk is vereist en u de doel-IP-voor voegsels voor uw Internet doelen kunt bepalen, kunt u deze bereiken met het on-premises netwerk als de volgende hop configureren via een door de gebruiker gedefinieerde route op de AzureFirewallSubnet. U kunt ook BGP gebruiken om deze routes te definiëren.

>[!NOTE]
>Verkeer tussen rechtstreeks gepeerde VNets wordt rechtstreeks gerouteerd, zelfs als de UDR naar Azure Firewall als standaardgateway wijst. Als u in dit scenario subnet-naar-subnet-verkeer wilt verzenden, moet een UDR het voorvoegsel van het doelsubnetwerk expliciet op beide subnetten bevatten.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-the-firewall-hub-virtual-network"></a>Het virtuele hub-netwerk voor de firewall maken

Maak eerst de resourcegroep voor de resources in deze zelfstudie:

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Op de start pagina van Azure Portal selecteert u **resource groepen** > **toevoegen**.
3. Typ **FW-Hybrid-test**voor de naam van de **resource groep**.
4. Bij **Abonnement** selecteert u uw abonnement.
5. Selecteer voor **regio** **VS Oost**. Alle resources die u later maakt, moeten zich op dezelfde locatie bevallen.
6. Selecteer **Controleren + maken**.
7. Selecteer **Maken**.

Maak nu het VNet:

> [!NOTE]
> De grootte van het AzureFirewallSubnet-subnet is/26. Zie [Azure firewall FAQ (Engelstalig](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)) voor meer informatie over de grootte van het subnet.

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
2. Onder **netwerken**, selecteert u **virtueel netwerk**.
4. Typ **VNet-hub**bij **naam**.
5. Typ **10.5.0.0/16**bij **adres ruimte**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Selecteer voor **resource groep**de optie **FW-Hybrid-test**.
8. Selecteer voor **locatie**de optie **VS Oost**.
9. Onder **Subnet** typt u bij **Naam** de naam **AzureFirewallSubnet**. De firewall zal zich in dit subnet bevinden, en de subnetnaam **moet** AzureFirewallSubnet zijn.
10. Typ **10.5.0.0/26**voor het **adres bereik**. 
11. Accepteer de andere standaard instellingen en selecteer vervolgens **maken**.

## <a name="create-the-spoke-virtual-network"></a>Het virtuele spoke-netwerk maken

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
2. Onder **netwerken**, selecteert u **virtueel netwerk**.
4. Typ **VNet-spoke**voor **naam**.
5. Typ **10.6.0.0/16**bij **adres ruimte**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Selecteer voor **resource groep**de optie **FW-Hybrid-test**.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Onder **Subnet** typt u **SN-Workload** bij **Naam**.
10. Typ **10.6.0.0/24**voor het **adres bereik**.
11. Accepteer de andere standaard instellingen en selecteer vervolgens **maken**.

## <a name="create-the-on-premises-virtual-network"></a>Het on-premises virtuele netwerk maken

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
2. Onder **netwerken**, selecteert u **virtueel netwerk**.
4. Typ **VNet-premises**bij **naam**.
5. Bij **Adresruimte** typt u **192.168.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Selecteer voor **resource groep**de optie **FW-Hybrid-test**.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Onder **subnet**, **naam** type **sn-Corp**.
10. Bij **Adresbereik** typt u **192.168.1.0/24**.
11. Accepteer de andere standaard instellingen en selecteer vervolgens **maken**.

Maak nu een tweede subnet voor de gateway.

1. Selecteer **subnetten**op de pagina **VNet-premises** .
2. Selecteer **+ subnet**.
3. Typ **GatewaySubnet**voor **naam**.
4. Typ **192.168.2.0/24**voor het **adres bereik (CIDR-blok)** .
5. Selecteer **OK**.

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Dit is het open bare IP-adres dat wordt gebruikt voor de on-premises gateway.

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
2. Typ **openbaar IP-adres** in het tekstvak zoeken en druk op **Enter**.
3. Selecteer **openbaar IP-adres** en selecteer vervolgens **maken**.
4. Typ **VNet-premises-gw-PIP**voor de naam.
5. Typ **FW-Hybrid-test**voor de resource groep.
6. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
7. Accepteer de andere standaard waarden en selecteer vervolgens **maken**.

## <a name="configure-and-deploy-the-firewall"></a>De firewall configureren en implementeren

Implementeer de firewall nu in het virtuele netwerk van de firewall hub.

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
2. Selecteer in de linkerkolom **netwerken**en selecteer vervolgens **firewall**.
4. Gebruik op de pagina **Firewall maken** de volgende tabel om de firewall te configureren:

   |Instelling  |Waarde  |
   |---------|---------|
   |Abonnement     |\<uw abonnement\>|
   |Resourcegroep     |**FW-Hybrid-test** |
   |Naam     |**AzFW01**|
   |Locatie     |Selecteer dezelfde locatie die u eerder hebt gebruikt|
   |Een virtueel netwerk kiezen     |**Bestaande gebruiken**:<br> **VNet-hub**|
   |Openbaar IP-adres     |Nieuwe maken: <br>**Name** - **FW-PIP**. |

5. Selecteer **Controleren + maken**.
6. Bekijk de samen vatting en selecteer vervolgens **maken** om de firewall te maken.

   Dit duurt enkele minuten om te implementeren.
7. Nadat de implementatie is voltooid, gaat u naar de resource groep **FW-Hybrid-test** en selecteert u de **AzFW01** -firewall.
8. Noteer het privé-IP-adres. U zult het later gebruiken wanneer u de standaardroute maakt.

### <a name="configure-network-rules"></a>Netwerkregels configureren

Voeg eerst een netwerk regel toe om webverkeer toe te staan.

1. Selecteer op de pagina **AzFW01** de optie **regels**.
2. Selecteer het tabblad verzameling van de **netwerk regel** .
3. Selecteer **verzameling netwerk regel toevoegen**.
4. Typ **RCNet01**voor **naam**.
5. Typ **100**bij **prioriteit**.
6. Bij **Actie** selecteert u **Toestaan**.
6. Typ **AllowWeb**onder **regels**voor **naam**.
7. Bij **Protocol** selecteert u **TCP**.
8. Voor **bron adressen**typt u **192.168.1.0/24**.
9. Typ **10.6.0.0/16** bij doel adres.
10. Typ **80**bij **doel poorten**.

Voeg nu een regel toe om RDP-verkeer toe te staan.

Typ op de tweede regel rij de volgende informatie:

1. **Naam**, typ **AllowRDP**.
2. Bij **Protocol** selecteert u **TCP**.
3. Voor **bron adressen**typt u **192.168.1.0/24**.
4. Typ **10.6.0.0/16** bij doel adres.
5. Typ **3389**bij **doel poorten**.
6. Selecteer **Toevoegen**.

## <a name="create-and-connect-the-vpn-gateways"></a>De VPN-gateways maken en verbinden

Het virtuele hub-netwerk en het on-premises virtuele netwerk zijn verbonden via VPN-gateways.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Een VPN-gateway maken voor het virtuele hub-netwerk

Maak nu een VPN-gateway voor het virtuele hub-netwerk. Voor netwerk-naar-netwerk-configuraties is een RouteBased VpnType vereist. Het maken van een VPN-gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde VPN-gateway-SKU.

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
2. Typ in het zoekvak de tekst **virtuele netwerk gateway** en druk op **Enter**.
3. Selecteer **virtuele netwerk gateway**en selecteer **maken**.
4. Typ **gw-hub**bij **naam**.
5. Selecteer voor **regio**dezelfde regio die u eerder hebt gebruikt.
6. Selecteer voor **Gateway type** **VPN**.
7. Selecteer voor **VPN-type** **op route gebaseerd**.
8. Voor **SKU**selecteert u **basis**.
9. Selecteer **VNet-hub voor het** **virtuele netwerk**.
10. Voor **openbaar IP-adres**selecteert u **nieuwe maken**en typt u **VNet-hub-gw-PIP** voor de naam.
11. Accepteer de resterende standaard waarden en selecteer vervolgens **controleren + maken**.
12. Controleer de configuratie en selecteer vervolgens **maken**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Een VPN-gateway maken voor het on-premises virtuele netwerk

Maak nu de VPN-gateway voor het on-premises virtuele netwerk. Voor netwerk-naar-netwerk-configuraties is een RouteBased VpnType vereist. Het maken van een VPN-gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde VPN-gateway-SKU.

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
2. Typ in het zoekvak de tekst **virtuele netwerk gateway** en druk op **Enter**.
3. Selecteer **virtuele netwerk gateway**en selecteer **maken**.
4. Typ **gw-premises**bij **naam**.
5. Selecteer voor **regio**dezelfde regio die u eerder hebt gebruikt.
6. Selecteer voor **Gateway type** **VPN**.
7. Selecteer voor **VPN-type** **op route gebaseerd**.
8. Voor **SKU**selecteert u **basis**.
9. Selecteer **VNet-premises voor het** **virtuele netwerk**.
10. Voor **openbaar IP-adres**selecteert u **nieuwe maken**en typt u **VNet-premises-gw-PIP** voor de naam.
11. Accepteer de resterende standaard waarden en selecteer vervolgens **controleren + maken**.
12. Controleer de configuratie en selecteer vervolgens **maken**.

### <a name="create-the-vpn-connections"></a>De VPN-verbindingen maken

Nu kunt u de VPN-verbindingen tussen de hub en de on-premises gateways maken.

In deze stap maakt u de verbinding van het virtuele hub-netwerk naar het on-premises virtuele netwerk. Hier ziet u een gedeelde sleutel waarnaar wordt verwezen in de voorbeelden. U kunt uw eigen waarden voor de gedeelde sleutel gebruiken. Het belangrijkste is dat de gedeelde sleutel voor beide verbindingen moet overeenkomen. Het kan even duren voordat de verbinding is gemaakt.

1. Open de resource groep **FW-Hybrid-test** en selecteer de **gw-hub** -gateway.
2. Selecteer **verbindingen** in de linkerkolom.
3. Selecteer **Toevoegen**.
4. De naam van de verbinding, type **hub-naar-premises**.
5. Selecteer **vnet-naar-VNet** voor het **verbindings type**.
6. Voor de **tweede virtuele netwerk gateway**selecteert u **gw-premises**.
7. Typ **AzureA1b2C3**voor **gedeelde sleutel (PSK)** .
8. Selecteer **OK**.

Maak de verbinding van het on-premises virtuele netwerk naar het virtuele hub-netwerk. Deze stap is vergelijkbaar met de vorige, behalve dat u de verbinding maakt vanuit VNet-Onprem naar VNet-hub. Zorg dat de gedeelde sleutels overeenkomen. De verbinding wordt na enkele minuten tot stand gebracht.

1. Open de resource groep **FW-Hybrid-test** en selecteer de **premises-** gateway.
2. Selecteer **verbindingen** in de linkerkolom.
3. Selecteer **Toevoegen**.
4. Typ **premises-to-hub in**de naam van de verbinding.
5. Selecteer **vnet-naar-VNet** voor het **verbindings type**.
6. Voor de **tweede virtuele netwerk gateway**selecteert u **gw-hub**.
7. Typ **AzureA1b2C3**voor **gedeelde sleutel (PSK)** .
8. Selecteer **OK**.


#### <a name="verify-the-connection"></a>De verbinding controleren

Na ongeveer vijf minuten moet de status van beide verbindingen worden **verbonden**.

![Gateway verbindingen](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>De hub- en virtuele spoke-netwerken koppelen

Koppel nu de virtuele hub- en spoke-netwerken.

1. Open de resource groep **FW-Hybrid-test** en selecteer het virtuele **VNet-hub** -netwerk.
2. Selecteer **peerings**in de linkerkolom.
3. Selecteer **Toevoegen**.
4. Typ **HubtoSpoke**voor **naam**.
5. Voor het **virtuele netwerk**selecteert u **VNet-spoke**
6. Typ **SpoketoHub**voor de naam van de peering van VNetSpoke naar VNet-hub.
7. Selecteer **Gateway doorvoer toestaan**.
8. Selecteer **OK**.

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>Aanvullende instellingen configureren voor de SpoketoHub-peering

U moet het **toestaan van doorgestuurd verkeer** op de SpoketoHub-peering inschakelen.

1. Open de resource groep **FW-Hybrid-test** en selecteer het virtuele **VNet-spoke** -netwerk.
2. Selecteer **peerings**in de linkerkolom.
3. Selecteer de **SpoketoHub** -peering.
4. Selecteer onder **doorgestuurd verkeer van VNet-hub naar VNet-spoke toestaan**de optie **ingeschakeld**.
5. Selecteer **Opslaan**.

## <a name="create-the-routes"></a>De routes maken

Maak nu een paar routes:

- Een route van het subnet van de hub-gateway naar het spoke-subnet via het IP-adres van de firewall
- Een standaardroute van het spoke-subnet via het IP-adres van de firewall

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
2. Typ in het tekstvak Zoeken de tekst **route tabel** en druk op **Enter**.
3. Selecteer **route tabel**.
4. Selecteer **Maken**.
5. Typ **UDR-hub-spoke**voor de naam.
6. Selecteer de **FW-Hybrid-test** voor de resource groep.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Selecteer **Maken**.
10. Nadat de route tabel is gemaakt, selecteert u deze om de pagina route tabel te openen.
11. Selecteer **routes** in de linkerkolom.
12. Selecteer **Toevoegen**.
13. Typ **ToSpoke**voor de route naam.
14. Typ **10.6.0.0/16**voor het adres voorvoegsel.
15. Selecteer **virtueel apparaat**bij type volgende hop.
16. Voor het adres van de volgende hop typt u het privé-IP-adres van de firewall dat u eerder hebt genoteerd.
17. Selecteer **OK**.

Koppel nu de route aan het subnet.

1. Selecteer **subnetten**op de pagina **UDR-hub-spoke-routes** .
2. Selecteer **koppelen**.
3. Selecteer **een virtueel netwerk kiezen**.
4. Selecteer **VNet-hub**.
5. Selecteer **GatewaySubnet**.
6. Selecteer **OK**.

Maak nu de standaard route van het spoke-subnet.

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
2. Typ in het tekstvak Zoeken de tekst **route tabel** en druk op **Enter**.
3. Selecteer **route tabel**.
5. Selecteer **Maken**.
6. Typ **UDR-DG**voor de naam.
7. Selecteer de **FW-Hybrid-test** voor de resource groep.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
4. Selecteer **uitgeschakeld**voor **route doorgifte van virtuele netwerk gateways**.
1. Selecteer **Maken**.
2. Nadat de route tabel is gemaakt, selecteert u deze om de pagina route tabel te openen.
3. Selecteer **routes** in de linkerkolom.
4. Selecteer **Toevoegen**.
5. Typ **ToHub**voor de route naam.
6. Typ voor het adres voorvoegsel **0.0.0.0/0**.
7. Selecteer **virtueel apparaat**bij type volgende hop.
8. Voor het adres van de volgende hop typt u het privé-IP-adres van de firewall dat u eerder hebt genoteerd.
9. Selecteer **OK**.

Koppel nu de route aan het subnet.

1. Op de pagina **UDR-DG-routes** selecteert u **subnetten**.
2. Selecteer **koppelen**.
3. Selecteer **een virtueel netwerk kiezen**.
4. Selecteer **VNet-spoke**.
5. Selecteer **sn-workload**.
6. Selecteer **OK**.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak nu de spoke-workloadmachines en on-premises virtuele machines en plaats ze in de toepasselijke subnetten.

### <a name="create-the-workload-virtual-machine"></a>De virtuele workloadmachine maken

Maak een virtuele machine in het spoke-virtuele netwerk, waarop IIS wordt uitgevoerd, zonder openbaar IP-adres.

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
2. Onder **populair**selecteert u **Windows Server 2016 Data Center**.
3. Voer deze waarden in voor de virtuele machine:
    - **Resource groep** : Selecteer **FW-Hybrid-test**.
    - **Naam van de virtuele machine**: *VM-spoke-01*.
    - **Regio** -dezelfde regio die u eerder hebt gebruikt.
    - **Gebruikers naam**: *azureuser*.
    - **Wacht woord**: *Azure123456!*
4. Selecteer **Volgende: schijven**.
5. Accepteer de standaard instellingen en selecteer **volgende: netwerken**.
6. Selecteer **VNet-spoke** voor het virtuele netwerk en het subnet is **sn-workload**.
7. Selecteer voor **openbaar IP-adres** **geen**.
8. Selecteer voor **open bare binnenkomende poorten**de optie **geselecteerde poorten toestaan**en selecteer vervolgens **http (80)** en **RDP (3389)**
9. Selecteer **volgende: beheer**.
10. Selecteer **uit**voor **Diagnostische gegevens over opstarten**.
11. Selecteer **controleren + maken**, Controleer de instellingen op de pagina samen vatting en selecteer vervolgens **maken**.

### <a name="install-iis"></a>IIS installeren

1. Open in de Azure Portal de Cloud Shell en zorg ervoor dat deze is ingesteld op **Power shell**.
2. Voer de volgende opdracht uit om IIS op de virtuele machine te installeren en wijzig indien nodig de locatie:

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

Dit is een virtuele machine die u gebruikt om verbinding te maken met behulp van Extern bureaublad naar het open bare IP-adres. Vanaf daar maakt u vervolgens verbinding met de on-premises server via de firewall.

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
2. Onder **populair**selecteert u **Windows Server 2016 Data Center**.
3. Voer deze waarden in voor de virtuele machine:
    - **Resource groep** : Selecteer bestaande en selecteer vervolgens **FW-Hybrid-test**.
    - **Naam van de virtuele machine** - *VM-premises*.
    - **Regio** -dezelfde regio die u eerder hebt gebruikt.
    - **Gebruikers naam**: *azureuser*.
    - **Wacht woord**: *Azure123456!* .
4. Selecteer **Volgende: schijven**.
5. Accepteer de standaard instellingen en selecteer **volgende: netwerken**.
6. Selecteer **VNet-premises** voor het virtuele netwerk en het subnet is **sn-Corp**.
7. Selecteer voor **open bare binnenkomende poorten**de optie **geselecteerde poorten toestaan**en selecteer vervolgens **RDP (3389)**
8. Selecteer **volgende: beheer**.
9. Selecteer **uit**voor **Diagnostische gegevens over opstarten**.
10. Selecteer **controleren + maken**, Controleer de instellingen op de pagina samen vatting en selecteer vervolgens **maken**.

## <a name="test-the-firewall"></a>De firewall testen

1. Noteer eerst het privé-IP-adres voor virtuele machine van **VM-spoke-01** .

2. Maak vanuit de Azure-portal verbinding met de virtuele machine **VM-OnPrem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Open een webbrowser op **VM-OnPrem** en blader naar http://\<privé-IP-adres VM-spoke-01\>.

   U ziet de webpagina **VM-spoke-01** : ![VM-spoke-01-webpagina](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. Open vanuit de virtuele machine **VM-premises** een extern bureau blad naar **VM-spoke-01** op het privé-IP-adres.

   De verbinding moet slagen en u moet zich kunnen aanmelden.

Nu hebt u gecontroleerd of de firewall regels werken:

<!---- You can ping the server on the spoke VNet.--->
- U kunt de bladeren op de webserver in het virtuele spoke-netwerk.
- U kunt verbinding maken met de server in het virtuele spoke-netwerk met behulp van RDP.

Wijzig vervolgens de verzameling netwerkregels van de firewall in **Weigeren** om te controleren of de regels werken zoals verwacht.

1. Selecteer de **AzFW01** -firewall.
2. Selecteer **regels**.
3. Selecteer het tabblad **netwerk regel verzameling** en selecteer de regel verzameling **RCNet01** .
4. Selecteer voor **actie** **weigeren**.
5. Selecteer **Opslaan**.

Sluit eventuele externe bureaubladen voordat u de gewijzigde regels test. Voer nu de tests opnieuw uit. Ze moeten deze keer allemaal mislukken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de firewall-resources voor de volgende zelfstudie bewaren. Als u ze niet meer nodig hebt, verwijdert u de resourcegroep **FW-Hybrid-Test** om alle firewall-gerelateerde resources te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Als volgende kunt u de Azure Firewall-logboeken bewaken.

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall-logboeken bewaken](./tutorial-diagnostics.md)
