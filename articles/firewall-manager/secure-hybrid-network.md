---
title: 'Zelf studie: uw hub Virtual Network beveiligen met behulp van Azure Firewall Manager preview'
description: In deze zelf studie leert u hoe u uw virtuele netwerk kunt beveiligen met Azure Firewall Manager met behulp van de Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: fa3c83f82e1942ab76ffe039847d642448434d87
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444984"
---
# <a name="tutorial-secure-your-hub-virtual-network-using-azure-firewall-manager-preview"></a>Zelf studie: uw hub Virtual Network beveiligen met behulp van Azure Firewall Manager preview 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Als u het on-premises netwerk verbindt met een virtueel Azure-netwerk om een hybride netwerk te maken, is de mogelijkheid om de toegang tot uw Azure-netwerkresources te beheren een belangrijk onderdeel van een algemeen beveiligingsplan.

Met Azure Firewall Manager preview kunt u een virtueel netwerk hub maken om uw hybride netwerk verkeer te beveiligen dat is bestemd voor privé-IP-adressen, Azure PaaS en Internet. U kunt Azure Firewall Manager gebruiken voor het beheren van netwerk toegang in een hybride netwerk met behulp van beleids regels waarmee het toegestane en geweigerde netwerk verkeer wordt gedefinieerd.

Firewall beheer biedt ook ondersteuning voor een beveiligde virtuele hub-architectuur. Zie [Wat zijn de opties voor de Azure firewall Manager-architectuur?](vhubs-and-vnets.md) voor een vergelijking van de beveiligde virtuele hub-en hub-architectuur typen van het virtuele netwerk.

Voor deze zelfstudie maakt u drie virtuele netwerken:

- **VNet-Hub**: de firewall bevindt zich in dit virtuele netwerk.
- **VNet-spoke**: het virtuele spoke-netwerk vertegenwoordigt de workload die zich bevindt in Azure.
- **VNet-Onprem**: het on-premises virtuele netwerk vertegenwoordigt een on-premises netwerk. In een daad werkelijke implementatie kan het worden verbonden door een VPN-of ExpressRoute-verbinding. Om het eenvoudig te houden wordt in deze zelfstudie een VPN-gatewayverbinding gebruikt en wordt een on-premises netwerk vertegenwoordigd door een virtueel Azure-netwerk.

![Hybride netwerk](media/tutorial-hybrid-portal/hybrid-network-firewall.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een firewall beleid maken
> * De virtuele netwerken maken
> * De firewall configureren en implementeren
> * De VPN-gateways maken en verbinden
> * De hub- en virtuele spoke-netwerken koppelen
> * De routes maken
> * De virtuele machines maken
> * De firewall testen


## <a name="prerequisites"></a>Vereisten

Een hybride netwerk gebruikt het hub-en-spoke-architectuur model om verkeer tussen Azure VNets en on-premises netwerken te routeren. De hub-en-spoke-architectuur heeft de volgende vereisten:

- Stel **AllowGatewayTransit** in als peering Vnet-hub naar Vnet-spoke. In een hub-en-spoke-netwerk architectuur kunnen met een gateway-Transit de virtuele spoke-netwerken de VPN-gateway in de hub delen, in plaats van dat VPN-gateways in elk spoke-virtueel netwerk worden geïmplementeerd. 

   Daarnaast worden routes naar de met gateways verbonden virtuele netwerken of on-premises netwerken automatisch door gegeven aan de routerings tabellen voor de gekoppelde virtuele netwerken met behulp van de gateway-door voer. Zie [VPN-gateway doorvoer configureren voor peering van virtuele netwerken](../vpn-gateway/vpn-gateway-peering-gateway-transit.md)voor meer informatie.

- Stel **UseRemoteGateways** in wanneer u met Vnet-spoke naar Vnet-hub. Als **UseRemoteGateways** is ingesteld en **AllowGatewayTransit** op externe peering is ingesteld, gebruikt het spoke-virtuele netwerk gateways van het externe virtuele netwerk voor door voer.
- Om het spoke-subnet verkeer via de hub-firewall te routeren, hebt u een door de gebruiker gedefinieerde route (UDR) nodig die naar de firewall wijst met de instelling voor het **door sturen van de virtuele netwerk gateway** uitgeschakeld. Deze optie voor komt dat route verdeling naar de spoke-subnetten wordt gedistribueerd. Dit voor komt dat geleerde routes conflicteren met uw UDR.
- Configureer een UDR op het hub gateway-subnet dat verwijst naar het IP-adres van de firewall als de volgende hop naar de spoke-netwerken. Er is geen door de gebruiker gedefinieerde route (UDR) nodig in het Azure Firewall-subnet, omdat het de routes overneemt van BGP.

Zie het gedeelte [Routes maken](#create-the-routes) in deze zelfstudie voor informatie over hoe deze routes worden gemaakt.

>[!NOTE]
>Azure Firewall moet een rechtstreekse Internet verbinding hebben. Als uw AzureFirewallSubnet een standaard route naar uw on-premises netwerk via BGP leert, moet u dit overschrijven met een 0.0.0.0/0-UDR met de **NextHopType** -waarde ingesteld als **Internet** om directe Internet connectiviteit te onderhouden.
>
>Azure Firewall kunnen worden geconfigureerd voor de ondersteuning van geforceerde tunneling.

<!---For more information, see [Azure Firewall forced tunneling](../firewall/forced-tunneling.md)--->

>[!NOTE]
>Verkeer tussen rechtstreeks gepeerde VNets wordt rechtstreeks gerouteerd, zelfs als de UDR naar Azure Firewall als standaardgateway wijst. Als u in dit scenario subnet-naar-subnet-verkeer wilt verzenden, moet een UDR het voorvoegsel van het doelsubnetwerk expliciet op beide subnetten bevatten.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-firewall-policy"></a>Een firewall beleid maken

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Typ in de Azure Portal zoek balk **firewall beheer** en druk op **Enter**.
3. Selecteer op de pagina Azure Firewall Manager de optie **Azure firewall-beleid weer geven**.

   ![Firewall beleid](media/tutorial-hybrid-portal/firewall-manager-policy.png)

1. Selecteer **Azure firewall beleid maken**.
1. Selecteer uw abonnement en selecteer voor resource groep de optie **nieuwe maken** en maak een resource groep met de naam **FW-Hybrid-test**.
2. Typ **Pol-Net01**voor de beleids naam.
3. Selecteer voor regio **VS Oost**.
4. Selecteer **volgende: regels**.
5. Selecteer **een regel verzameling toevoegen**.
6. Typ **RCNet01**voor **naam**.
7. Selecteer **netwerk**bij **type regel verzameling**.
8. Typ **100**bij **prioriteit**.
9. Bij **Actie** selecteert u **Toestaan**.
10. Typ **AllowWeb**onder **regels**voor **naam**.
11. Voor **bron adressen**typt u **192.168.1.0/24**.
12. Bij **Protocol** selecteert u **TCP**.
13. Typ **80**bij **doel poorten**.
14. Selecteer **IP-adres**bij **doel type**.
15. Typ **10.6.0.0/16**bij **doel**.
16. Voer de volgende gegevens in op de rij volgende regel:
 
    Naam: Typ **AllowRDP**<br>
    IP-adres van Bron: type **192.168.1.0/24**.<br>
    Protocol, selecteer **TCP**<br>
    Doel poorten, type **3389**<br>
    Doel type, selecteer **IP-adres**<br>
    Typ **10.6.0.0/16** bij doel.

1. Selecteer **Toevoegen**.
2. Selecteer **Controleren + maken**.
3. Bekijk de details en selecteer vervolgens **maken**.

## <a name="create-the-firewall-hub-virtual-network"></a>Het virtuele hub-netwerk voor de firewall maken

> [!NOTE]
> De grootte van het AzureFirewallSubnet-subnet is/26. Zie [Azure firewall FAQ (Engelstalig](../firewall/firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)) voor meer informatie over de grootte van het subnet.

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

Nadat het virtuele netwerk is geïmplementeerd, maakt u een tweede subnet voor de gateway.

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
6. Selecteer voor **locatie**de optie **VS-Oost**.
7. Accepteer de andere standaard waarden en selecteer vervolgens **maken**.

## <a name="configure-and-deploy-the-firewall"></a>De firewall configureren en implementeren

Wanneer het beveiligings beleid is gekoppeld aan een hub, wordt dit een hub- *virtueel netwerk*genoemd.

Converteer het virtuele **VNet-hub-** netwerk naar een *hub-virtueel netwerk* en Beveilig het met Azure firewall.

1. Typ in de Azure Portal zoek balk **firewall beheer** en druk op **Enter**.
3. Selecteer op de pagina Azure Firewall beheer onder **beveiliging toevoegen aan virtuele netwerken**de optie **hub virtuele netwerken weer geven**.
4. Selecteer **virtuele netwerken converteren**.
5. Selecteer **VNet-hub** en selecteer vervolgens **volgende: Azure firewall**.
6. Selecteer **Pol-Net01**voor het **firewall beleid**.
7. Selecteer **volgende controleren + bevestigen**
8. Controleer de details en selecteer vervolgens **bevestigen**.


   Dit duurt enkele minuten om te implementeren.
7. Nadat de implementatie is voltooid, gaat u naar de resource groep **FW-Hybrid-test** en selecteert u de firewall.
9. Noteer het **privé-IP-** adres van de firewall op de pagina **overzicht** . U zult het later gebruiken wanneer u de standaardroute maakt.

## <a name="create-and-connect-the-vpn-gateways"></a>De VPN-gateways maken en verbinden

Het virtuele hub-netwerk en het on-premises virtuele netwerk zijn verbonden via VPN-gateways.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Een VPN-gateway maken voor het virtuele hub-netwerk

Maak nu een VPN-gateway voor het virtuele hub-netwerk. Voor netwerk-naar-netwerk-configuraties is een RouteBased VpnType vereist. Het maken van een VPN-gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde VPN-gateway-SKU.

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
2. Typ in het zoekvak de tekst **virtuele netwerk gateway** en druk op **Enter**.
3. Selecteer **virtuele netwerk gateway**en selecteer **maken**.
4. Typ **gw-hub**bij **naam**.
5. Selecteer voor **regio** **(VS) vs-Oost**.
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
5. Selecteer voor **regio** **(VS) vs-Oost**.
6. Selecteer voor **Gateway type** **VPN**.
7. Selecteer voor **VPN-type** **op route gebaseerd**.
8. Voor **SKU**selecteert u **basis**.
9. Selecteer **VNet-premises voor het** **virtuele netwerk**.
10. Voor **openbaar IP-adres**selecteert*u * bestaande gebruiken*en selecteert u **VNet-premises-gw-PIP** voor de naam.
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

![Gateway verbindingen](media/secure-hybrid-network/gateway-connections.png)

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
8. Voor **locatie**selecteert u **(VS) vs-Oost)** .
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
4. Onder **virtueel netwerk**selecteert u **VNet-hub**.
5. Onder **subnet**selecteert u **GatewaySubnet**.
6. Selecteer **OK**.

Maak nu de standaard route van het spoke-subnet.

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
2. Typ in het tekstvak Zoeken de tekst **route tabel** en druk op **Enter**.
3. Selecteer **route tabel**.
5. Selecteer **Maken**.
6. Typ **UDR-DG**voor de naam.
7. Selecteer de **FW-Hybrid-test** voor de resource groep.
8. Voor **locatie**selecteert u **(VS) vs-Oost)** .
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
4. Selecteer **VNet-spoke**onder **virtueel netwerk**.
5. Selecteer onder **subnet**de optie **sn-workload**.
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
    - **Regio** -  *(VS) vs-Oost)* .
    - **Gebruikers naam**: *azureuser*.
    - **Wacht woord**: Typ uw wacht woord

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
    - **Regio** -  *(VS) vs-Oost)* .
    - **Gebruikers naam**: *azureuser*.
    - **Wacht woord**: Typ uw wacht woord.

4. Selecteer **Volgende: schijven**.
5. Accepteer de standaard instellingen en selecteer **volgende: netwerken**.
6. Selecteer **VNet-premises** voor het virtuele netwerk en controleer of het subnet **sn-Corp**is.
7. Selecteer voor **open bare binnenkomende poorten**de optie **geselecteerde poorten toestaan**en selecteer vervolgens **RDP (3389)**
8. Selecteer **volgende: beheer**.
9. Selecteer **uit**voor **Diagnostische gegevens over opstarten**.
10. Selecteer **controleren + maken**, Controleer de instellingen op de pagina samen vatting en selecteer vervolgens **maken**.

## <a name="test-the-firewall"></a>De firewall testen

1. Noteer eerst het privé-IP-adres voor de VM-spoke-01-virtuele machine op de overzichts pagina van de VM-spoke-01.

2. Maak vanuit de Azure-portal verbinding met de virtuele machine **VM-OnPrem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Open een webbrowser op **VM-OnPrem** en blader naar http://\<privé-IP-adres VM-spoke-01\>.

   U ziet de webpagina **VM-spoke-01** : ![VM-spoke-01-webpagina](media/secure-hybrid-network/vm-spoke-01-web.png)

4. Open vanuit de virtuele machine **VM-premises** een extern bureau blad naar **VM-spoke-01** op het privé-IP-adres.

   De verbinding moet slagen en u moet zich kunnen aanmelden.

Nu hebt u gecontroleerd of de firewall regels werken:

<!---- You can ping the server on the spoke VNet.--->
- U kunt de bladeren op de webserver in het virtuele spoke-netwerk.
- U kunt verbinding maken met de server in het virtuele spoke-netwerk met behulp van RDP.

Wijzig vervolgens de verzameling netwerkregels van de firewall in **Weigeren** om te controleren of de regels werken zoals verwacht.

1. Open de resource groep **FW-Hybrid-test** en selecteer het **Net01-** firewall beleid.
2. Onder **instellingen**selecteert u **regels**.
3. Onder **netwerk regels**selecteert u de regel verzameling **RCNet01** , selecteert u de weglatings tekens (...) en selecteert u **bewerken**.
4. Selecteer voor **regel verzamelings actie** **weigeren**.
5. Selecteer **Opslaan**.

Sluit alle bestaande externe Bureau bladen en browsers op **VM-premises** voordat u de gewijzigde regels test. Nadat de update van de regel verzameling is voltooid, voert u de tests opnieuw uit. Ze moeten deze keer allemaal mislukken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de firewall-resources voor de volgende zelfstudie bewaren. Als u ze niet meer nodig hebt, verwijdert u de resourcegroep **FW-Hybrid-Test** om alle firewall-gerelateerde resources te verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: uw virtuele WAN beveiligen met Azure Firewall Manager-preview](secure-cloud-network.md)