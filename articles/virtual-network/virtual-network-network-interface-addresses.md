---
title: IP-adressen configureren voor een Azure-netwerkinterface | Microsoft Documenten
description: Meer informatie over het toevoegen, wijzigen en verwijderen van privé- en openbare IP-adressen voor een netwerkinterface.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: b5655a58c3538ac47e8649619b079dc46ee01242
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473231"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>IP-adressen voor Azure-netwerkinterfaces toevoegen, wijzigen en verwijderen

Meer informatie over het toevoegen, wijzigen en verwijderen van openbare en private IP-adressen voor een netwerkinterface. Met privé-IP-adressen die aan een netwerkinterface zijn toegewezen, kan een virtuele machine communiceren met andere bronnen in een virtueel Azure-netwerk en verbonden netwerken. Een privé-IP-adres maakt ook uitgaande communicatie naar het internet mogelijk met behulp van een onvoorspelbaar IP-adres. Een [openbaar IP-adres](virtual-network-public-ip-address.md) dat is toegewezen aan een netwerkinterface maakt binnenkomende communicatie naar een virtuele machine vanaf het internet mogelijk. Het adres maakt ook uitgaande communicatie van de virtuele machine naar het internet mogelijk met behulp van een voorspelbaar IP-adres. Zie Uitgaande [verbindingen in Azure begrijpen](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie.

Als u een netwerkinterface wilt maken, wijzigen of verwijderen, leest u het artikel [Een netwerkinterface beheren.](virtual-network-network-interface.md) Als u netwerkinterfaces wilt toevoegen aan of netwerkinterfaces van een virtuele machine wilt verwijderen, leest u het artikel [Netwerkinterfaces toevoegen of verwijderen.](virtual-network-network-interface-vm.md)

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende taken uit voordat u stappen in een gedeelte van dit artikel voltooit:

- Als u nog geen Azure-account hebt, meldt u zich aan voor een [gratis proefaccount.](https://azure.microsoft.com/free)
- Als u de https://portal.azure.comportal gebruikt, opent u en logt u in met uw Azure-account.
- Als u PowerShell-opdrachten gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell vanaf uw computer uit te voeren. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelfstudie is de Azure PowerShell-module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u CLI-opdrachten (Azure Command-line interface) gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Deze zelfstudie vereist de Azure CLI-versie 2.0.31 of hoger. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal uitvoert, `az login` moet u ook worden uitgevoerd om een verbinding met Azure te maken.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure, moet worden toegewezen aan de [rol van netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in machtigingen [voor netwerkinterface.](virtual-network-network-interface.md#permissions)

## <a name="add-ip-addresses"></a>IP-adressen toevoegen

U zoveel [mogelijk privé-](#private) en [openbare](#public) [IPv4-adressen](#ipv4) toevoegen aan een netwerkinterface, binnen de grenzen die worden vermeld in het [azure-limietenartikel.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) U een privé-IPv6-adres toevoegen aan één [secundaire IP-configuratie](#secondary) (zolang er geen bestaande secundaire IP-configuraties zijn) voor een bestaande netwerkinterface. Elke netwerkinterface kan hoogstens één IPv6-privéadres hebben. U optioneel een openbaar IPv6-adres toevoegen aan een IPv6-netwerkinterfaceconfiguratie. Zie [IPv6](#ipv6) voor meer informatie over het gebruik van IPv6-adressen.

1. Typ *netwerkinterfaces*in het vak met de tekst *Zoekbronnen* boven aan de Azure-portal. Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer de netwerkinterface waarvoor u een IPv4-adres wilt toevoegen in de lijst.
3. Selecteer **onder INSTELLINGEN** **IP-configuraties**.
4. Selecteer onder **IP-configuraties** **+ Toevoegen**.
5. Geef het volgende op en selecteer **OK:**

   |Instelling|Vereist?|Details|
   |---|---|---|
   |Name|Ja|Moet uniek zijn voor de netwerkinterface|
   |Type|Ja|Aangezien u een IP-configuratie toevoegt aan een bestaande netwerkinterface en elke netwerkinterface een [primaire](#primary) IP-configuratie moet hebben, is uw enige optie **Secundair**.|
   |Methode voor toewijzing van privé-IP-adres|Ja|[**Dynamisch:**](#dynamic)Azure wijst het volgende beschikbare adres toe voor het subnetadresbereik waarin de netwerkinterface is geïmplementeerd. [**Statisch**](#static): u wijst een ongebruikt adres toe voor het subnetadresbereik waarin de netwerkinterface is geïmplementeerd.|
   |Openbaar IP-adres|Nee|**Uitgeschakeld:** Er is momenteel geen openbare IP-adresbron gekoppeld aan de IP-configuratie. **Ingeschakeld:** Selecteer een bestaand IP-ip-adres van IPv4 of maak een nieuw IP-adres. Lees het artikel [Openbare IP-adressen](virtual-network-public-ip-address.md#create-a-public-ip-address) voor meer informatie over het maken van een openbaar IP-adres.|
6. Voeg handmatig secundaire privé-IP-adressen toe aan het besturingssysteem voor virtuele machines door de instructies in het artikel [Meerdere IP-adressen toewijzen aan besturingssystemen voor virtuele machines in te](virtual-network-multiple-ip-addresses-portal.md#os-config) vullen. Bekijk [private](#private) privé-IP-adressen voor speciale overwegingen voordat u handmatig IP-adressen toevoegt aan een besturingssysteem voor virtuele machines. Voeg geen openbare IP-adressen toe aan het besturingssysteem voor virtuele machines.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Ip-adresinstellingen wijzigen

Mogelijk moet u de toewijzingsmethode van een IPv4-adres wijzigen, het statische IPv4-adres wijzigen of het openbare IP-adres wijzigen dat is toegewezen aan een netwerkinterface. Als u het privé-IPv4-adres wijzigt van een secundaire IP-configuratie die is gekoppeld aan een secundaire netwerkinterface in een virtuele machine (meer informatie over [primaire en secundaire netwerkinterfaces),](virtual-network-network-interface-vm.md)plaatst u de virtuele machine in de gestopte status (deallocated) voordat u de volgende stappen voltooit:

1. Typ *netwerkinterfaces*in het vak met de tekst *Zoekbronnen* boven aan de Azure-portal. Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer de netwerkinterface waarvoor u ip-adresinstellingen wilt weergeven of wijzigen in de lijst.
3. Selecteer **onder INSTELLINGEN** **IP-configuraties**.
4. Selecteer de IP-configuratie die u wilt wijzigen in de lijst.
5. Wijzig de instellingen, zoals gewenst, met behulp van de informatie over de instellingen in stap 5 van [Een IP-configuratie toevoegen.](#add-ip-addresses)
6. Selecteer **Opslaan**.

>[!NOTE]
>Als de primaire netwerkinterface meerdere IP-configuraties heeft en u het privé-IP-adres van de primaire IP-configuratie wijzigt, moet u de primaire en secundaire IP-adressen handmatig opnieuw toewijzen aan de netwerkinterface binnen Windows (niet vereist voor Linux). Zie [Meerdere IP-adressen toewijzen aan virtuele machines](virtual-network-multiple-ip-addresses-portal.md#os-config)als u handmatig IP-adressen wilt toewijzen aan een netwerkinterface binnen een besturingssysteem. Voor speciale overwegingen raadpleegt u privé-IP-adressen [private](#private) voordat u handmatig IP-adressen toevoegt aan een besturingssysteem voor virtuele machines. Voeg geen openbare IP-adressen toe aan het besturingssysteem voor virtuele machines.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az netwerk nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-azNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>IP-adressen verwijderen

U [privé-](#private) en [openbare](#public) IP-adressen verwijderen uit een netwerkinterface, maar een netwerkinterface moet altijd ten minste één privé-IPv4-adres hebben toegewezen.

1. Typ *netwerkinterfaces*in het vak met de tekst *Zoekbronnen* boven aan de Azure-portal. Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer de netwerkinterface die u IP-adressen uit de lijst wilt verwijderen.
3. Selecteer **onder INSTELLINGEN** **IP-configuraties**.
4. Selecteer met de rechtermuisknop een [secundaire](#secondary) IP-configuratie (u de [primaire](#primary) configuratie niet verwijderen) die u wilt verwijderen, selecteer **Verwijderen**en selecteer **vervolgens Ja**om de verwijdering te bevestigen. Als aan de configuratie een openbare IP-adresbron is gekoppeld, wordt de bron gescheiden van de IP-configuratie, maar wordt de bron niet verwijderd.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az-netwerk nic ip-config verwijderen](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-azNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>IP-configuraties

[Privé-](#private) en (optioneel) [openbare](#public) IP-adressen worden toegewezen aan een of meer IP-configuraties die zijn toegewezen aan een netwerkinterface. Er zijn twee typen IP-configuraties:

### <a name="primary"></a>Primair

Aan elke netwerkinterface wordt één primaire IP-configuratie toegewezen. Een primaire IP-configuratie:

- Heeft een [privé-IPv4-adres](#ipv4) toegewezen. [private](#private) U geen [privé-IPv6-adres](#ipv6) toewijzen aan een primaire IP-configuratie.
- Kan ook een [openbaar](#public) IPv4-adres toegewezen. U geen openbaar IPv6-adres toewijzen aan een primaire IP-configuratie (IPv4). 

### <a name="secondary"></a>Secundair

Naast een primaire IP-configuratie kan een netwerkinterface nul of meer secundaire IP-configuraties hebben toegewezen. Een secundaire IP-configuratie:

- Er moet een privé-IPv4- of IPv6-adres aan zijn toegewezen. Als het adres IPv6 is, kan de netwerkinterface slechts één secundaire IP-configuratie hebben. Als het adres IPv4 is, kan de netwerkinterface meerdere secundaire IP-configuraties hebben toegewezen. Zie het artikel [Azure limits](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie over het aantal private en openbare IPv4-adressen dat aan een netwerkinterface kan worden toegewezen.
- Kan ook een openbaar IPv4- of IPv6-adres toegewezen hebben. Het toewijzen van meerdere IPv4-adressen aan een netwerkinterface is handig in scenario's zoals:
  - Hosting van meerdere websites of services met verschillende IP-adressen en TLS/SSL-certificaten op één server.
  - Een virtuele machine die dienst doet als een virtueel netwerktoestel, zoals een firewall of load balancer.
  - De mogelijkheid om een van de privé-IPv4-adressen voor een van de netwerkinterfaces toe te voegen aan een back-endpool van Azure Load Balancer. In het verleden kon alleen het primaire IPv4-adres voor de primaire netwerkinterface worden toegevoegd aan een back-endpool. Zie het artikel [Load balancing multiple IP-configuraties](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over het laden van meerdere IPv4-configuraties. 
  - De mogelijkheid om balans te laden op één IPv6-adres dat is toegewezen aan een netwerkinterface. Zie het artikel [IPv6-adressen laden](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over het laden van saldo naar een privé-IPv6-adres.

## <a name="address-types"></a>Adrestypen

U de volgende typen IP-adressen toewijzen aan een [IP-configuratie:](#ip-configurations)

### <a name="private"></a>Privé

Met [privé-IPv4-](#ipv4) of IPv6-adressen kan een virtuele machine communiceren met andere bronnen in een virtueel netwerk of andere verbonden netwerken. 

Standaard wijzen de Azure DHCP-servers het privé IPv4-adres voor de [primaire IP-configuratie](#primary) van de Azure-netwerkinterface toe aan de netwerkinterface binnen het besturingssysteem voor virtuele machines. Tenzij nodig, moet u nooit handmatig het IP-adres van een netwerkinterface instellen binnen het besturingssysteem van de virtuele machine.

> [!WARNING]
> Als het IPv4-adres dat is ingesteld als het primaire IP-adres van een netwerkinterface binnen het besturingssysteem van een virtuele machine, steeds anders is dan het privé-IPv4-adres dat is toegewezen aan de primaire IP-configuratie van de primaire netwerkinterface die is gekoppeld aan een virtuele machine binnen Azure, verliest u de verbinding met de virtuele machine.

Er zijn scenario's waarin het nodig is om handmatig het IP-adres van een netwerkinterface in te stellen binnen het besturingssysteem van de virtuele machine. U moet bijvoorbeeld handmatig de primaire en secundaire IP-adressen van een Windows-besturingssysteem instellen wanneer u meerdere IP-adressen toevoegt aan een virtuele Azure-machine. Voor een Virtuele Linux-machine hoeft u mogelijk alleen de secundaire IP-adressen handmatig in te stellen. Zie [IP-adressen toevoegen aan een VM-besturingssysteem](virtual-network-multiple-ip-addresses-portal.md#os-config) voor meer informatie. Als u ooit het adres wilt wijzigen dat is toegewezen aan een IP-configuratie, wordt u aangeraden:

1. Controleer of de virtuele machine een adres ontvangt van de Azure DHCP-servers. Zodra u dit hebt gedaan, wijzigt u de toewijzing van het IP-adres terug naar DHCP binnen het besturingssysteem en start u de virtuele machine opnieuw op.
2. Stop (deallocate) de virtuele machine.
3. Wijzig het IP-adres voor de IP-configuratie binnen Azure.
4. Start de virtuele machine.
5. [Configureer de](virtual-network-multiple-ip-addresses-portal.md#os-config) secundaire IP-adressen binnen het besturingssysteem (en ook het primaire IP-adres binnen Windows) handmatig om aan te sluiten bij wat u binnen Azure instelt.

Door de vorige stappen te volgen, blijft het privé-IP-adres dat is toegewezen aan de netwerkinterface binnen Azure en binnen het besturingssysteem van een virtuele machine hetzelfde. Als u wilt bijhouden voor welke virtuele machines binnen uw abonnement u handmatig IP-adressen in een besturingssysteem hebt ingesteld, u overwegen een [Azure-tag](../azure-resource-manager/management/tag-resources.md) toe te voegen aan de virtuele machines. U bijvoorbeeld 'IP-adrestoewijzing: Statisch' gebruiken. Op deze manier u eenvoudig de virtuele machines in uw abonnement vinden waarvoor u handmatig het IP-adres hebt ingesteld binnen het besturingssysteem.

Naast het inschakelen van een virtuele machine om te communiceren met andere bronnen binnen dezelfde, of aangesloten virtuele netwerken, een prive-IP-adres maakt het ook mogelijk een virtuele machine om outbound communiceren naar het internet. Uitgaande verbindingen zijn bronnetwerkadres dat door Azure wordt vertaald naar een onvoorspelbaar openbaar IP-adres. Lees het [outbound Internetverbinding](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) van Azure voor meer informatie over azure-in-ternetverbinding. U niet communiceren inbound naar het privé-IP-adres van een virtuele machine vanaf het internet. Als voor uw uitgaande verbindingen een voorspelbaar openbaar IP-adres nodig is, koppelt u een bron van een openbaar IP-adres aan een netwerkinterface.

### <a name="public"></a>Public

Openbare IP-adressen die via een openbare IP-adresbron zijn toegewezen, maken inkomende connectiviteit met een virtuele machine vanaf internet mogelijk. Uitgaande verbindingen met het internet gebruiken een voorspelbaar IP-adres. Zie [Uitgaande verbindingen in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) begrijpen voor meer informatie. U een openbaar IP-adres toewijzen aan een IP-configuratie, maar dat is niet nodig. Als u geen openbaar IP-adres aan een virtuele machine toewijst door een openbare IP-adresbron te koppelen, kan de virtuele machine nog steeds uitgaand communiceren naar het internet. In dit geval is het privé-IP-adres bronnetwerkadres dat door Azure wordt vertaald naar een onvoorspelbaar openbaar IP-adres. Zie Bron van openbare [IP-adresbronnen](virtual-network-public-ip-address.md)voor meer informatie over bronnen voor openbare IP-adres .

Er zijn grenzen aan het aantal privé- en openbare IP-adressen dat u aan een netwerkinterface toewijzen. Lees voor meer informatie het artikel [Azure limits.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

> [!NOTE]
> Azure vertaalt het privé-IP-adres van een virtuele machine naar een openbaar IP-adres. Als gevolg hiervan is het besturingssysteem van een virtuele machine niet op de hoogte van een openbaar IP-adres dat eraan is toegewezen, dus het is niet nodig om ooit handmatig een openbaar IP-adres toe te wijzen binnen het besturingssysteem.

## <a name="assignment-methods"></a>Toewijzingsmethoden

Openbare en particuliere IP-adressen worden toegewezen volgens een van de volgende toewijzingsmethoden:

### <a name="dynamic"></a>Dynamisch

Dynamische privé-IPv4- en IPv6-adressen (optioneel) worden standaard toegewezen.

- **Alleen openbaar:** Azure wijst het adres toe uit een bereik dat uniek is voor elke Azure-regio. Zie [Ip-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)van Microsoft Azure Datacenter voor meer informatie over welke bereiken aan elke regio zijn toegewezen. Het adres kan veranderen wanneer een virtuele machine wordt gestopt (deallocated), dan opnieuw gestart. U een openbaar IPv6-adres niet toewijzen aan een IP-configuratie met behulp van een van beide toewijzingsmethoden.
- **Alleen privé:** Azure reserveert de eerste vier adressen in elk subnetadresbereik en wijst de adressen niet toe. Azure wijst het eerstvolgende beschikbare adres in het adresbereik van het subnet toe aan een resource. Als het adresbereik van het subnet bijvoorbeeld 10.0.0.0/16 is en de adressen 10.0.0.0.4-10.0.0.14 al zijn toegewezen (.0-.3 zijn gereserveerd), wijst Azure 10.0.0.15 toe aan de resource. Dynamisch is de standaardmethode voor toewijzing. Nadat dynamische IP-adressen zijn toegewezen, worden ze alleen vrijgegeven als een netwerkinterface wordt verwijderd of wordt toegewezen aan een ander subnet binnen hetzelfde virtuele netwerk of als de toewijzingsmethode wordt gewijzigd in statisch en een ander IP-adres wordt opgegeven. Standaard wijst Azure het vorige dynamisch toegewezen adres toe als het statische adres wanneer u de toewijzingsmethode van dynamisch wijzigt in statisch. 

### <a name="static"></a>Statisch

U (optioneel) een openbaar of privé statisch IPv4- of IPv6-adres toewijzen aan een IP-configuratie. Zie [Openbare IP-adressen](virtual-network-public-ip-address.md)voor meer informatie over hoe Azure statische openbare IPv4-adressen toewijst.

- **Alleen openbaar:** Azure wijst het adres toe uit een bereik dat uniek is voor elke Azure-regio. U kunt de lijst met bereiken (voorvoegsels) downloaden voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064). Het adres wordt pas gewijzigd als de openbare IP-adresbron waaraan het is toegewezen, is verwijderd of de toewijzingsmethode is gewijzigd in dynamisch. Als de openbare IP-adresbron is gekoppeld aan een IP-configuratie, moet deze worden losgekoppeld van de IP-configuratie voordat de toewijzingsmethode wordt gewijzigd.
- **Alleen privé:** u selecteert en wijst een adres toe uit het adresbereik van het subnet. Het adres dat u toewijst, kan elk adres binnen het adresbereik van het subnet zijn dat niet een van de eerste vier adressen in het adresbereik van het subnet is en momenteel niet is toegewezen aan een andere resource in het subnet. Statische adressen worden alleen vrijgegeven als een netwerkinterface wordt verwijderd. Als u de toewijzingsmethode wijzigt in statisch, wijst Azure het eerder toegewezen dynamische IP-adres dynamisch toe als het statische adres, zelfs als het adres niet het volgende beschikbare adres in het adresbereik van het subnet is. Het adres verandert ook als de netwerkinterface wordt toegewezen aan een ander subnet binnen hetzelfde virtuele netwerk, maar als u de netwerkinterface wilt toewijzen aan een ander subnet, moet u eerst de toewijzingsmethode wijzigen van statisch in dynamisch. Nadat u de netwerkinterface hebt toegewezen aan een ander subnet, kunt u de toewijzingsmethode weer wijzigen in statisch en een IP-adres uit het adresbereik van het nieuwe subnet toewijzen.

## <a name="ip-address-versions"></a>VERSIES van IP-adres

U de volgende versies opgeven bij het toewijzen van adressen:

### <a name="ipv4"></a>IPv4

Elke netwerkinterface moet één [primaire](#primary) IP-configuratie hebben met een toegewezen [privé-IPv4-adres.](#ipv4) [private](#private) U een of meer [secundaire](#secondary) IP-configuraties toevoegen die elk een IPv4-privé- en (optioneel) een IPv4-openbaar IP-adres hebben. [public](#public)

### <a name="ipv6"></a>IPv6

U nul of één privé [IPv6-adres](#ipv6) toewijzen aan één secundaire IP-configuratie van een netwerkinterface. De netwerkinterface kan geen bestaande secundaire IP-configuraties hebben. Elke netwerkinterface kan hoogstens één IPv6-privéadres hebben. U optioneel een openbaar IPv6-adres toevoegen aan een IPv6-netwerkinterfaceconfiguratie.

> [!NOTE]
> Hoewel u een netwerkinterface met een IPv6-adres maken met behulp van de portal, u geen bestaande netwerkinterface toevoegen aan een nieuwe of bestaande virtuele machine met behulp van de portal. Gebruik PowerShell of de Azure CLI om een netwerkinterface met een privé-IPv6-adres te maken en voeg vervolgens de netwerkinterface toe bij het maken van een virtuele machine. U een netwerkinterface niet koppelen aan een privé-IPv6-adres dat eraan is toegewezen aan een bestaande virtuele machine. U geen privé-IPv6-adres toevoegen aan een IP-configuratie voor een netwerkinterface die met behulp van hulpprogramma's (portal, CLI of PowerShell) aan een netwerkinterface is gekoppeld.

U een openbaar IPv6-adres niet toewijzen aan een primaire of secundaire IP-configuratie.

## <a name="skus"></a>SKU's

Er wordt een openbaar IP-adres gemaakt met de basis- of standaard SKU. Zie [Openbare IP-adressen beheren](virtual-network-public-ip-address.md)voor meer informatie over SKU-verschillen.

> [!NOTE]
> Als u een openbaar IP-adres van een standaard-SKU toewijst aan een netwerkinterface van een virtuele machine, moet u het bedoelde verkeer expliciet toestaan met een [netwerkbeveiligingsgroep](security-overview.md#network-security-groups). Communicatie met de resource mislukt totdat u een netwerkbeveiligingsgroep maakt en koppelt en het gewenste verkeer expliciet toestaat.

## <a name="next-steps"></a>Volgende stappen
Als u een virtuele machine met verschillende IP-configuraties wilt maken, leest u de volgende artikelen:

|Taak|Hulpprogramma|
|---|---|
|Een virtuele machine met meerdere netwerkinterfaces maken|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Eén NIC-vm maken met meerdere IPv4-adressen|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Eén NIC-vm maken met een privé-IPv6-adres (achter een Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-sjabloon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
