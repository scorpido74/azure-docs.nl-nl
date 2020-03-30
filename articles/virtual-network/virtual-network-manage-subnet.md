---
title: Een subnet van een Extern Netwerk azure toevoegen, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Meer informatie over het toevoegen, wijzigen of verwijderen van een virtueel netwerksubnet in Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: e5b78969f6b4315bb02b3f4152c6eeab94adddb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246939"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Een subnet van een virtueel netwerk maken, wijzigen of verwijderen

Meer informatie over het toevoegen, wijzigen of verwijderen van een virtueel netwerksubnet. Alle Azure-resources die worden geïmplementeerd in een virtueel netwerk worden geïmplementeerd in een subnet binnen het virtuele netwerk. Als u nieuw bent in virtuele netwerken, u er meer over te weten komen in het [virtuele netwerkoverzicht](virtual-networks-overview.md) of door een snelle start in [te vullen.](quick-create-portal.md) Zie [Een virtueel netwerk maken, wijzigen of verwijderen](manage-virtual-network.md)voor meer informatie over het beheren van een virtueel netwerk.

## <a name="before-you-begin"></a>Voordat u begint

Als u er geen hebt, stelt u een Azure-account in met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Voer vervolgens een van deze taken uit voordat u stappen start in een gedeelte van dit artikel: 

- **Portalgebruikers:** Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw Azure-account.

- **PowerShell-gebruikers**: Voer de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/powershell)of voer PowerShell uit vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Zoek op het tabblad Azure Cloud Shell de vervolgkeuzelijst **Voor de omgeving selecteren** en kies **PowerShell** als deze nog niet is geselecteerd.

    Als u PowerShell lokaal gebruikt, gebruikt u Azure PowerShell-moduleversie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az.Network` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Ook `Connect-AzAccount` worden uitgevoerd om een verbinding met Azure te maken.

- **CLI-gebruikers (Azure Command-line interface)** uitvoeren: voer de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of voer de CLI uit vanaf uw computer. Gebruik Azure CLI-versie 2.0.31 of hoger als u de Azure CLI lokaal uitvoert. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Ook `az login` worden uitgevoerd om een verbinding met Azure te maken.

Het account waarmee u zich aanmeldt of waarmee u verbinding maakt met Azure, moet worden toegewezen aan de [rolrol netwerkbijdrage medewerker](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [Machtigingen](#permissions).

## <a name="add-a-subnet"></a>Een subnet toevoegen

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerken te bekijken. Zoeken naar virtuele netwerken en selecteer **deze.**

2. Selecteer de naam van het virtuele netwerk waaraan u een subnet wilt toevoegen.

3. Selecteer **Subnetsubnet** > **Subnet**in **Instellingen**.

4. Voer in het dialoogvenster **Subnet toevoegen** waarden in voor de volgende instellingen:

    | Instelling | Beschrijving |
    | --- | --- |
    | **Naam** | De naam moet uniek zijn binnen het virtuele netwerk. Voor maximale compatibiliteit met andere Azure-services raden we u aan een letter te gebruiken als eerste teken van de naam. Azure Application Gateway wordt bijvoorbeeld niet geïmplementeerd in een subnet met een naam die begint met een getal. |
    | **Adresbereik** | <p>Het bereik moet uniek zijn binnen de adresruimte voor het virtuele netwerk. Het bereik kan niet overlappen met andere subnetadresbereiken binnen het virtuele netwerk. De adresruimte moet worden opgegeven met cidr-notatie (Classless Inter-Domain Routing).</p><p>In een virtueel netwerk met adresruimte *10.0.0.0/16*u bijvoorbeeld een subnetadresruimte van *10.0.0.0/22*definiëren. Het kleinste bereik dat u opgeven is */29*, dat acht IP-adressen voor het subnet biedt. Azure behoudt het eerste en laatste adres in elk subnet voor protocolconformiteit. Drie extra adressen zijn gereserveerd voor Azure-servicegebruik. Als gevolg hiervan resulteert het definiëren van een subnet met een */29-adresbereik* in drie bruikbare IP-adressen in het subnet.</p><p>Als u van plan bent een virtueel netwerk aan te sluiten op een VPN-gateway, moet u een gateway-subnet maken. Meer informatie over [specifieke adresbereikoverwegingen voor gateway-subnetten](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). U het adresbereik wijzigen nadat het subnet is toegevoegd, onder specifieke voorwaarden. Zie [Subnetinstellingen wijzigen](#change-subnet-settings)voor meer informatie over het wijzigen van een subnetadresbereik.</p> |
    | **Netwerkbeveiligingsgroep** | Als u inkomend en uitgaand netwerkverkeer voor het subnet wilt filteren, u een bestaande netwerkbeveiligingsgroep aan een subnet koppelen. De netwerkbeveiligingsgroep moet in hetzelfde abonnement en dezelfde locatie bestaan als het virtuele netwerk. Meer informatie over [netwerkbeveiligingsgroepen](security-overview.md) en [het maken van een netwerkbeveiligingsgroep](tutorial-filter-network-traffic.md). |
    | **Routetabel** | Als u de routering van netwerkverkeer naar andere netwerken wilt beheren, u optioneel een bestaande routetabel koppelen aan een subnet. De routetabel moet in hetzelfde abonnement en dezelfde locatie bestaan als het virtuele netwerk. Meer informatie over [Azure-routering](virtual-networks-udr-overview.md) en [het maken van een routetabel](tutorial-create-route-table-portal.md). |
    | **Service-eindpunten** | <p>Een subnet kan optioneel een of meer serviceeindpunten hiervoor hebben ingeschakeld. Als u een serviceeindpunt voor een service wilt inschakelen, selecteert u de service- of services waarvoor u serviceeindpunten wilt inschakelen in de lijst **Services.** Azure configureert de locatie automatisch voor een eindpunt. Azure configureert standaard de serviceeindpunten voor de regio van het virtuele netwerk. Als u regionale failoverscenario's wilt ondersteunen, configureert Azure automatisch eindpunten naar [azure-gekoppelde regio's](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) voor Azure Storage.</p><p>Als u een serviceeindpunt wilt verwijderen, schakelt u de optie de service waarvoor u het serviceeindpunt wilt verwijderen, uit. Zie Eindpunten voor virtuele [netwerkservices](virtual-network-service-endpoints-overview.md)voor meer informatie over serviceeindpunten en de services waarvoor ze kunnen worden ingeschakeld. Zodra u een serviceeindpunt voor een service inschakelt, moet u ook netwerktoegang voor het subnet inschakelen voor een resource die met de service is gemaakt. Als u bijvoorbeeld het serviceeindpunt voor **Microsoft.Storage**inschakelt, moet u ook netwerktoegang inschakelen tot alle Azure Storage-accounts waartoe u netwerktoegang wilt verlenen. Zie de documentatie voor de afzonderlijke service waarvoor u het eindpunt van de service hebt ingeschakeld om netwerktoegang tot subnetten in te schakelen waarvoor een eindpunt van de service is ingeschakeld.</p><p>Als u wilt valideren dat een serviceeindpunt is ingeschakeld voor een subnet, bekijkt u de [effectieve routes](diagnose-network-routing-problem.md) voor elke netwerkinterface in het subnet. Wanneer u een eindpunt configureert, ziet u een *standaardroute* met de adresvoorvoegsels van de service en een volgend hoptype **VirtualNetworkServiceEndpoint**. Zie Routering van virtueel netwerkverkeer voor meer informatie over [routering.](virtual-networks-udr-overview.md)</p> |
    | **Delegatie van subnet** | Een subnet kan optioneel een of meer delegaties hiervoor hebben ingeschakeld. Subnetdelegatie geeft expliciete machtigingen aan de service om servicespecifieke bronnen in het subnet te maken met behulp van een unieke id tijdens de implementatie van de service. Als u wilt delegeren voor een service, selecteert u de service aan wie u de service wilt delegeren in de lijst **Services.** |

5. Als u het subnet wilt toevoegen aan het virtuele netwerk dat u hebt geselecteerd, selecteert u **OK**.

### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Subnetinstellingen wijzigen

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerken te bekijken. Zoeken naar virtuele netwerken en selecteer **deze.**

2. Selecteer de naam van het virtuele netwerk dat het subnet bevat dat u wilt wijzigen.

3. Selecteer **Subnetten**in **Instellingen**.

4. Selecteer in de lijst met subnetten het subnet waarvoor u de instellingen wilt wijzigen.

5. Wijzig op de subnetpagina een van de volgende instellingen:

    | Instelling | Beschrijving |
    | --- | --- |
    | **Adresbereik** | Als er geen resources worden geïmplementeerd binnen het subnet, u het adresbereik wijzigen. Als er resources in het subnet aanwezig zijn, moet u de resources naar een ander subnet verplaatsen of ze eerst uit het subnet verwijderen. De stappen die u neemt om een resource te verplaatsen of te verwijderen, zijn afhankelijk van de resource. Lees de documentatie voor elk van deze brontypen voor meer informatie over het verplaatsen of verwijderen van bronnen in subnetten. Zie de beperkingen voor **adresbereik** in stap 4 van [Een subnet toevoegen](#add-a-subnet). |
    | **Gebruikers** | U de toegang tot het subnet beheren met behulp van ingebouwde rollen of uw eigen aangepaste rollen. Zie [Een roltoewijzing toevoegen](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment)voor meer informatie over het toewijzen van rollen en gebruikers aan het subnet. |
    | **Netwerkbeveiligingsgroep** en **Routetabel** | Zie stap 4 van [Een subnet toevoegen](#add-a-subnet). |
    | **Service-eindpunten** | <p>Zie serviceeindpunten in stap 4 van [Een subnet toevoegen](#add-a-subnet). Wanneer u een serviceeindpunt inschakelt voor een bestaand subnet, moet u ervoor zorgen dat er geen kritieke taken worden uitgevoerd op een resource in het subnet. Serviceeindpunten schakelen van route op elke netwerkinterface in het subnet. De serviceeindpunten gaan van het gebruik van de standaardroute met het adresvoorvoegsel *0.0.0/0 en* het volgende hoptype *internet*, naar het gebruik van een nieuwe route met de adresvoorvoegsels van de service en een volgend hoptype *VirtualNetworkServiceEndpoint*.</p><p>Tijdens de switch kunnen alle geopende TCP-verbindingen worden beëindigd. Het serviceeindpunt is pas ingeschakeld als het verkeer naar de service voor alle netwerkinterfaces is bijgewerkt met de nieuwe route. Zie Routering van virtueel netwerkverkeer voor meer informatie over [routering.](virtual-networks-udr-overview.md)</p> |
    | **Delegatie van subnet** | Zie serviceeindpunten in stap 4 van [Een subnet toevoegen](#add-a-subnet). Subnetdelegatie kan worden gewijzigd in nul of meerdere delegaties die hiervoor zijn ingeschakeld. Als een resource voor een service al is geïmplementeerd in het subnet, kan subnetdelegatie niet worden toegevoegd of verwijderd totdat alle bronnen voor de service zijn verwijderd. Als u wilt delegeren voor een andere service, selecteert u de service aan wie u de service wilt delegeren in de lijst **Services.** |

6. Selecteer **Opslaan**.

### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Een subnet verwijderen

U een subnet alleen verwijderen als er geen bronnen in het subnet staan. Als resources zich in het subnet bevinden, moet u deze bronnen verwijderen voordat u het subnet verwijderen. De stappen die u neemt om een resource te verwijderen, zijn afhankelijk van de resource. Lees de documentatie voor elk van deze brontypen voor meer informatie over het verwijderen van bronnen in subnetten.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerken te bekijken. Zoeken naar virtuele netwerken en selecteer **deze.**

2. Selecteer de naam van het virtuele netwerk dat het subnet bevat dat u wilt verwijderen.

3. Selecteer **Subnetten**in **Instellingen**.

4. Selecteer in de lijst met subnetten het subnet dat u wilt verwijderen.

5. Selecteer **Verwijderen**en selecteer **Opein in** het bevestigingsdialoogvenster.

### <a name="commands"></a>Opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [az-netwerk vnet subnet verwijderen](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Machtigingen

Als u taken op subnetten wilt uitvoeren, moet uw account worden toegewezen aan de [rol netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties in de volgende tabel zijn toegewezen:

|Actie                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Lees een virtueel netwerksubnet              |
|Microsoft.Network/virtualNetworks/subnetten/schrijven                          |   Een virtueel netwerksubnet maken of bijwerken  |
|Microsoft.Network/virtualNetworks/subnetten/verwijderen                         |   Een virtueel netwerksubnet verwijderen            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Lid worden van een virtueel netwerk                     |
|Microsoft.Network/virtualNetworks/subnetten/joinViaServiceEndpoint/actie  |   Een serviceeindpunt inschakelen voor een subnet     |
|Microsoft.Network/virtualNetworks/subnetten/virtualMachines/read           |   Download de virtuele machines in een subnet       |

## <a name="next-steps"></a>Volgende stappen

- Een virtueel netwerk en subnetten maken met [PowerShell-](powershell-samples.md) of [Azure CLI-voorbeeldscripts](cli-samples.md) of azure [resourcemanagersjablonen gebruiken](template-samples.md)
- [Azure-beleid](policy-samples.md) maken en toepassen voor virtuele netwerken
