---
title: 'Klassieke virtuele netwerken verbinden met Azure Resource Manager VNets: PowerShell'
description: Maak een VPN-verbinding tussen klassieke VNets en Resource Manager VNets met VPN Gateway en PowerShell.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 1dc0eec6178420976181b05a059e9f8b4859ec2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152003"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Virtuele netwerken van verschillende implementatiemodellen verbinden met PowerShell

Met dit artikel u klassieke VNets verbinden met VNets van Resource Manager, zodat de resources in de afzonderlijke implementatiemodellen met elkaar kunnen communiceren. De stappen in dit artikel gebruiken PowerShell, maar u deze configuratie ook maken met behulp van de Azure-portal door het artikel in deze lijst te selecteren.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Powershell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Het aansluiten van een klassieke VNet met een Resource Manager VNet is vergelijkbaar met het aansluiten van een VNet op een on-premises locatie. Voor beide connectiviteitstypen wordt een VPN-gateway gebruikt om een beveiligde tunnel met IPsec/IKE te bieden. U een verbinding maken tussen VNets die zich in verschillende abonnementen en in verschillende regio's bevinden. U ook VNets verbinden die al verbindingen hebben met on-premises netwerken, zolang de gateway waarmee ze zijn geconfigureerd dynamisch of routegebaseerd is. Zie voor meer informatie over verbindingen tussen VNets de [Veelgestelde vragen over VNet-naar-VNet](#faq) aan het einde van dit artikel. 

Als u nog geen virtuele netwerkgateway hebt en er geen wilt maken, u in plaats daarvan overwegen uw VNets aan te sluiten via VNet Peering. Bij VNet-peering wordt geen VPN-gateway gebruikt. Zie het artikel [VNet-peering](../virtual-network/virtual-network-peering-overview.md) voor meer informatie.

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

De volgende stappen leiden u door de instellingen die nodig zijn om een dynamische of routegebaseerde gateway voor elke VNet te configureren en een VPN-verbinding tussen de gateways te maken. Deze configuratie biedt geen ondersteuning voor statische of op beleid gebaseerde gateways.

### <a name="prerequisites"></a><a name="pre"></a>Vereisten

* Beide VNets zijn al gemaakt. Zie [Een resourcegroep en een virtueel netwerk](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network)maken als u een virtueel netwerk voor resourcebeheer wilt maken. Zie [Een klassiek VNet maken](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic)als u een klassiek virtueel netwerk wilt maken.
* De adresbereiken voor de VNets overlappen elkaar niet of overlappen elkaar niet met een van de bereiken voor andere verbindingen waarmee de gateways kunnen worden verbonden.
* U hebt de nieuwste PowerShell-cmdlets geïnstalleerd. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor meer informatie. Zorg ervoor dat u zowel de cmdlets Service Management (SM) als de Resource Manager (RM) installeert. 

### <a name="example-settings"></a><a name="exampleref"></a>Voorbeeldinstellingen

U kunt deze waarden gebruiken om een testomgeving te maken of ze raadplegen om meer inzicht te krijgen in de voorbeelden in dit artikel.

**Klassieke VNet-instellingen**

VNet-naam = ClassicVNet <br>
Locatie = West US <br>
Ruimten voor virtueel netwerkadres = 10.0.0.0/24 <br>
Subnet-1 = 10,0,0,0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Lokale netwerknaam = RMVNetLocal <br>
GatewayType = DynamicRouting

**VNet-instellingen voor Resource Manager**

VNet-naam = RMVNet <br>
Resourcegroep = RG1 <br>
IP-adresruimten voor virtuele netwerken = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Locatie = Oost-VS <br>
Gateway openbare IP-naam = gwpip <br>
Lokale netwerkgateway = ClassicVNetLocal <br>
Naam virtuele netwerkgateway = RMGateway <br>
Gateway IP-adresseringsconfiguratie = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>Sectie 1 - De klassieke VNet configureren
### <a name="1-download-your-network-configuration-file"></a>1. Uw netwerkconfiguratiebestand downloaden
1. Meld u aan bij uw Azure-account in de PowerShell-console met verhoogde rechten. Met de volgende cmdlet wordt u gevraagd om de inloggegevens voor uw Azure-account. Na het aanmelden, worden de instellingen van uw account gedownload zodat ze beschikbaar zijn voor Azure PowerShell. De klassieke Azure PowerShell-cmdlets (Classic Service Management) worden in deze sectie gebruikt.

   ```azurepowershell
   Add-AzureAccount
   ```

   Ontvang uw Azure-abonnement.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Als u meerdere abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Exporteer uw Azure-netwerkconfiguratiebestand door de volgende opdracht uit te voeren. U de locatie van het bestand wijzigen om indien nodig naar een andere locatie te exporteren.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Open het XML-bestand dat u hebt gedownload om het te bewerken. Zie het [netwerkconfiguratieschema](https://msdn.microsoft.com/library/jj157100.aspx)voor een voorbeeld van het netwerkconfiguratiebestand .

### <a name="2-verify-the-gateway-subnet"></a>2. Controleer het gateway-subnet
Voeg in het element **VirtualNetworkSites** een gateway-subnet toe aan uw VNet als er nog geen is gemaakt. Wanneer u met het netwerkconfiguratiebestand werkt, moet het gatewaysubnet "GatewaySubnet" worden genoemd of kan Azure het niet herkennen en gebruiken als subnet van de gateway.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Voorbeeld:**

    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>

### <a name="3-add-the-local-network-site"></a>3. De lokale netwerksite toevoegen
De lokale netwerksite die u toevoegt, vertegenwoordigt de RM VNet waarmee u verbinding wilt maken. Voeg een **element LocalNetworkSites** toe aan het bestand als er nog geen bestaat. Op dit punt in de configuratie kan de VPNGatewayAddress elk geldig openbaar IP-adres zijn omdat we de gateway voor de Resource Manager VNet nog niet hebben gemaakt. Zodra we de gateway hebben gemaakt, vervangen we dit IP-adres van tijdelijke aanduidingen door het juiste openbare IP-adres dat is toegewezen aan de RM-gateway.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Het VNet koppelen aan de lokale netwerksite
In deze sectie geven we de lokale netwerksite op waarmee u het VNet wilt verbinden. In dit geval is het de Resource Manager VNet waarnaar u eerder hebt verwezen. Zorg ervoor dat de namen overeenkomen. Met deze stap wordt geen gateway gemaakt. Hiermee wordt het lokale netwerk opgegeven waarmee de gateway verbinding maakt.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Het bestand opslaan en uploaden
Sla het bestand op en importeer het vervolgens naar Azure door de volgende opdracht uit te voeren. Zorg ervoor dat u het bestandspad wijzigt indien nodig voor uw omgeving.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

U ziet een vergelijkbaar resultaat waaruit blijkt dat de import is geslaagd.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. De gateway maken

Raadpleeg dit voorbeeld voordat u dit voorbeeld uitvoert, naar het netwerkconfiguratiebestand dat u hebt gedownload voor de exacte namen die Azure verwacht te zien. Het netwerkconfiguratiebestand bevat de waarden voor uw klassieke virtuele netwerken. Soms worden de namen voor klassieke VNets gewijzigd in het netwerkconfiguratiebestand bij het maken van klassieke VNet-instellingen in de Azure-portal vanwege de verschillen in de implementatiemodellen. Als u bijvoorbeeld de Azure-portal hebt gebruikt om een klassieke VNet met de naam 'Classic VNet' te maken en deze hebt gemaakt in een brongroep met de naam 'ClassicRG', wordt de naam die in het netwerkconfiguratiebestand is opgenomen, geconverteerd naar 'Group ClassicRG Classic VNet'. Wanneer u de naam opgeeft van een VNet dat spaties bevat, gebruikt u aanhalingstekens rond de waarde.


Gebruik het volgende voorbeeld om een dynamische routeringsgateway te maken:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

U de status van de gateway controleren met de **cmdlet Get-AzureVNetGateway.**

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>Sectie 2 - De RM VNet-gateway configureren



De voorwaarden gaan ervan uit dat u al een RM VNet hebt gemaakt. In deze stap maakt u een VPN-gateway voor de RM VNet. Start deze stappen pas nadat u het openbare IP-adres voor de klassieke VNet-gateway hebt opgehaald. 

1. Meld u aan bij uw Azure-account in de PowerShell-console. Met de volgende cmdlet wordt u gevraagd om de inloggegevens voor uw Azure-account. Na het aanmelden worden uw accountinstellingen gedownload, zodat ze beschikbaar zijn voor Azure PowerShell. U optioneel de functie Uitproberen gebruiken om Azure Cloud Shell in de browser te starten.

   Als u Azure Cloud Shell gebruikt, slaat u de volgende cmdlet over:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Voer de volgende cmdlet uit om te controleren of u het juiste abonnement gebruikt:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Als u meer dan één abonnement hebt, geeft u het abonnement op dat u wilt gebruiken.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Maak een lokale netwerkgateway. In een virtueel netwerk verwijst de lokale netwerkgateway doorgaans naar uw on-premises locatie. In dit geval verwijst de lokale netwerkgateway naar uw Klassieke VNet. Geef het een naam waarmee Azure ernaar kan verwijzen en geef ook het adresruimtevoorvoegsel op. Azure gebruikt het IP-adresvoorvoegsel dat u opgeeft om te bepalen welk verkeer naar uw on-premises locatie moet worden verzonden. Als u de informatie hier later moet aanpassen voordat u uw gateway maakt, u de waarden wijzigen en het voorbeeld opnieuw uitvoeren.
   
   **-Naam** is de naam die u wilt toewijzen om naar de lokale netwerkgateway te verwijzen.<br>
   **-AddressPrefix** is de adresruimte voor uw klassieke VNet.<br>
   **-GatewayIpAddress** is het openbare IP-adres van de klassieke VNet gateway. Zorg ervoor dat u de volgende voorbeeldtekst "n.n.n.n" wijzigt om het juiste IP-adres weer te geven.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Vraag een openbaar IP-adres aan om te worden toegewezen aan de virtuele netwerkgateway voor de Resource Manager VNet. U kunt het IP-adres dat u wilt gebruiken niet zelf opgeven. Het IP-adres wordt dynamisch toegewezen aan de virtuele netwerkgateway. Dit betekent echter niet dat het IP-adres verandert. De enige keer dat het IP-adres van de virtuele netwerkgateway wordt gewijzigd, is wanneer de gateway wordt verwijderd en opnieuw wordt gemaakt. Het verandert niet in het formaat, het resetten of andere interne onderhouds-/upgrades van de gateway.

   In deze stap stellen we ook een variabele in die in een latere stap wordt gebruikt.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Controleer of uw virtuele netwerk een gateway-subnet heeft. Als er geen gatewaysubnet bestaat, voegt u er een toe. Controleer of het subnet gateway *GatewaySubnet*heet.
5. Haal het subnet op dat voor de gateway wordt gebruikt door de volgende opdracht uit te voeren. In deze stap stellen we ook een variabele in die in de volgende stap moet worden gebruikt.
   
   **-Naam** is de naam van uw Resource Manager VNet.<br>
   **-ResourceGroupName** is de resourcegroep waarmee de VNet is gekoppeld. Het gatewaysubnet moet al bestaan voor deze VNet en moet GatewaySubnet de naam *GatewaySubnet* hebben om goed te werken.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Maak de configuratie van de gateway-IP-adressering. De gatewayconfiguratie bepaalt welk subnet en openbaar IP-adres moeten worden gebruikt. Gebruik het volgende voorbeeld om de gatewayconfiguratie te maken.

   In deze stap moeten de parameters **-SubnetId** en **-PublicIpAddressId** worden doorgegeven aan de eigenschap id van respectievelijk het subnet en IP-adresobjecten. Je geen simpel touwtje gebruiken. Deze variabelen zijn ingesteld in de stap om een openbaar IP aan te vragen en de stap om het subnet op te halen.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Maak de virtuele netwerkgateway Resource Manager door de volgende opdracht uit te voeren. De `-VpnType` moet *RouteBased*zijn. Het kan 45 minuten of langer duren voordat de gateway is gemaakt.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Kopieer het openbare IP-adres zodra de VPN-gateway is gemaakt. U gebruikt het wanneer u de lokale netwerkinstellingen voor uw Klassieke VNet configureert. U de volgende cmdlet gebruiken om het openbare IP-adres op te halen. Het openbare IP-adres wordt in de retour vermeld als *IpAddress*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>Sectie 3 - De klassieke vnet-lokale site-instellingen wijzigen

In deze sectie werk je met het klassieke VNet. U vervangt het IP-adres van tijdelijke aanduidingen dat u hebt gebruikt bij het opgeven van de lokale site-instellingen die worden gebruikt om verbinding te maken met de VNet-gateway voor Resource Manager. Omdat u met het klassieke VNet werkt, gebruikt u PowerShell die lokaal op uw computer is geïnstalleerd, niet de Azure Cloud Shell TryIt.

1. Exporteer het netwerkconfiguratiebestand.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Wijzig met behulp van een teksteditor de waarde voor VPNGatewayAddress. Vervang het IP-adres van de tijdelijke aanduiding door het openbare IP-adres van de Gateway Resource Manager en sla de wijzigingen op.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importeer het gewijzigde netwerkconfiguratiebestand in Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>Sectie 4 - Een verbinding tussen de gateways maken
Voor het maken van een verbinding tussen de gateways is PowerShell vereist. Mogelijk moet u uw Azure-account toevoegen om de klassieke versie van de PowerShell-cmdlets te gebruiken. Gebruik hiervoor **Add-AzureAccount**.

1. Stel in de PowerShell-console de gedeelde sleutel in. Raadpleeg voor het uitvoeren van de cmdlets het netwerkconfiguratiebestand dat u hebt gedownload voor de exacte namen die Azure verwacht te zien. Wanneer u de naam opgeeft van een VNet dat spaties bevat, gebruikt u enkele aanhalingstekens rond de waarde.<br><br>In het volgende voorbeeld is **-VNetName** de naam van de klassieke VNet en **-LocalNetworkSiteName** is de naam die u hebt opgegeven voor de lokale netwerksite. De **-SharedKey** is een waarde die u genereert en opgeeft. In het voorbeeld gebruikten we 'abc123', maar je iets complexer genereren en gebruiken. Het belangrijkste is dat de waarde die u hier opgeeft dezelfde waarde moet hebben die u in de volgende stap opgeeft wanneer u uw verbinding maakt. De terugkeer moet **status weergeven: Geslaagd**.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Maak de VPN-verbinding door de volgende opdrachten uit te voeren:
   
   Stel de variabelen in.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Maak de verbinding. Merk op dat het **-ConnectionType** IPsec is, niet Vnet2Vnet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>Sectie 5 - Uw verbindingen verifiëren

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>De verbinding van uw klassieke VNet met uw Resource Manager VNet verifiëren

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>De verbinding van uw Resource Manager VNet met uw klassieke VNet verifiëren

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Veelgestelde vragen over VNet-naar-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
