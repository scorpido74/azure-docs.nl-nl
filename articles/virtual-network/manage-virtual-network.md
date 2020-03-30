---
title: Een virtueel Azure-netwerk maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Meer informatie over het maken, wijzigen of verwijderen van een virtueel netwerk in Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 70523dc12f3f20362fcf4a2c3cb456a182038e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280233"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Een virtueel netwerk maken, wijzigen of verwijderen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Meer informatie over het maken en verwijderen van een virtueel netwerk en het wijzigen van instellingen, zoals DNS-servers en IP-adresruimten, voor een bestaand virtueel netwerk. Als u nieuw bent in virtuele netwerken, u er meer over te weten komen in het overzicht van het [virtuele netwerk](virtual-networks-overview.md) of door een zelfstudie in te [vullen.](quick-create-portal.md) Een virtueel netwerk bevat subnetten. Zie [Subnetten](virtual-network-manage-subnet.md)beheren voor meer informatie over het maken, wijzigen en verwijderen van subnetten.

## <a name="before-you-begin"></a>Voordat u begint

Voer de volgende taken uit voordat u stappen in een gedeelte van dit artikel voltooit:

- Als u nog geen Azure-account hebt, meldt u zich aan voor een [gratis proefaccount.](https://azure.microsoft.com/free)
- Als u de https://portal.azure.comportal gebruikt, opent u en logt u in met uw Azure-account.
- Als u PowerShell-opdrachten gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell vanaf uw computer uit te voeren. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelfstudie is de Azure PowerShell-module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u CLI-opdrachten (Azure Command-line interface) gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Deze zelfstudie vereist de Azure CLI-versie 2.0.31 of hoger. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal uitvoert, `az login` moet u ook worden uitgevoerd om een verbinding met Azure te maken.
- Het account waarmee u zich aanmeldt of verbinding maakt met Azure, moet worden toegewezen aan de [rol van netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [Machtigingen](#permissions).

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer **+ Maak een virtueel** > **Networking** > **netwerknetwerk voor**bronnen .
2. Voer waarden in of selecteer waarden voor de volgende instellingen en selecteer **Vervolgens Maken:**
   - **Naam:** De naam moet uniek zijn in de [resourcegroep](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) waarin u selecteert om het virtuele netwerk te maken. U de naam niet wijzigen nadat het virtuele netwerk is gemaakt. U in de loop van de tijd meerdere virtuele netwerken maken. Zie [Conventies benoemen voor](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)naamgevingssuggesties. Het volgen van een naamgevingsconventie kan het eenvoudiger maken om meerdere virtuele netwerken te beheren.
   - **Adresruimte**: de adresruimte voor een virtueel netwerk bestaat uit een of meer niet-overlappende adresbereiken die zijn opgegeven in CIDR-notatie. Het adresbereik dat u definieert, kan openbaar of privé zijn (RFC 1918). Of u nu het adresbereik definieert als openbaar of privé, het adresbereik is alleen bereikbaar vanuit het virtuele netwerk, vanuit onderling verbonden virtuele netwerken en van alle on-premises netwerken die u hebt aangesloten op het virtuele netwerk. U de volgende adresbereiken niet toevoegen:
     - 224.0.0.0/4 (Multicast)
     - 255.255.255.255/32 (Uitzending)
     - 127.0.0.0/8 (Loopback)
     - 169.254.0.0/16 (Link-lokaal)
     - 168.63.129.16/32 (interne [statusssdetectie](../load-balancer/load-balancer-custom-probe-overview.md#probesource)van DNS, DHCP en Azure Load Balancer)

     Hoewel u slechts één adresbereik definiëren wanneer u het virtuele netwerk in de portal maakt, u meer adresbereiken toevoegen aan de adresruimte nadat het virtuele netwerk is gemaakt. Zie [Een adresbereik toevoegen of verwijderen](#add-or-remove-an-address-range)voor meer informatie over het toevoegen van een adresbereik aan een bestaand virtueel netwerk.

     >[!WARNING]
     >Als een virtueel netwerk adresbereiken heeft die overlappen met een ander virtueel netwerk of on-premises netwerk, kunnen de twee netwerken niet worden verbonden. Voordat u een adresbereik definieert, moet u overwegen of u het virtuele netwerk in de toekomst wilt verbinden met andere virtuele netwerken of on-premises netwerken.
     >
     >

     - **Subnetnaam**: De subnetnaam moet uniek zijn binnen het virtuele netwerk. U de subnetnaam niet wijzigen nadat het subnet is gemaakt. De portal vereist dat u één subnet definieert wanneer u een virtueel netwerk maakt, ook al hoeft een virtueel netwerk geen subnetten te hebben. In de portal u slechts één subnet definiëren wanneer u een virtueel netwerk maakt. U later meer subnetten aan het virtuele netwerk toevoegen, nadat het virtuele netwerk is gemaakt. Zie [Subnetten beheren](virtual-network-manage-subnet.md)als u een subnet aan een virtueel netwerk wilt toevoegen. U een virtueel netwerk maken met meerdere subnetten met Azure CLI of PowerShell.

       >[!TIP]
       >Soms maken beheerders verschillende subnetten om de routering van verkeer tussen de subnetten te filteren of te beheren. Voordat u subnetten definieert, moet u overwegen hoe u verkeer tussen uw subnetten wilt filteren en routeren. Zie [Netwerkbeveiligingsgroepen](security-overview.md)voor meer informatie over het filteren van verkeer tussen subnetten. Azure leidt automatisch verkeer tussen subnetten, maar u azure-standaardroutes overschrijven. Zie [Routeringsoverzicht](virtual-networks-udr-overview.md)voor meer informatie over azures standaard subnetverkeer.
       >

     - **Subnetadresbereik**: het bereik moet zich binnen de adresruimte bevinden die u voor het virtuele netwerk hebt ingevoerd. Het kleinste bereik dat u opgeven is /29, dat acht IP-adressen voor het subnet biedt. Azure behoudt het eerste en laatste adres in elk subnet voor protocolconformiteit. Drie extra adressen zijn gereserveerd voor Azure-servicegebruik. Als gevolg hiervan heeft een virtueel netwerk met een subnetadresbereik van /29 slechts drie bruikbare IP-adressen. Als u van plan bent een virtueel netwerk aan te sluiten op een VPN-gateway, moet u een gateway-subnet maken. Meer informatie over [specifieke adresbereikoverwegingen voor gateway-subnetten](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). U het adresbereik wijzigen nadat het subnet is gemaakt, onder specifieke voorwaarden. Zie [Subnetten beheren](virtual-network-manage-subnet.md)voor meer informatie over het wijzigen van een subnetadresbereik.
     - **Abonnement**: Selecteer een [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). U niet hetzelfde virtuele netwerk gebruiken in meer dan één Azure-abonnement. U echter een virtueel netwerk in één abonnement verbinden met virtuele netwerken in andere abonnementen met [virtuele netwerkpeering.](virtual-network-peering-overview.md) Elke Azure-bron die u met het virtuele netwerk verbindt, moet zich in hetzelfde abonnement bevinden als het virtuele netwerk.
     - **Resourcegroep:** selecteer een bestaande [resourcegroep](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) of maak een nieuwe groep. Een Azure-bron die u met het virtuele netwerk verbindt, kan zich in dezelfde brongroep bevinden als het virtuele netwerk of in een andere brongroep.
     - **Locatie**: Selecteer een [Azure-locatie,](https://azure.microsoft.com/regions/)ook wel regio genoemd. Een virtueel netwerk kan zich op slechts één Azure-locatie bevinden. U echter een virtueel netwerk op één locatie verbinden met een virtueel netwerk op een andere locatie met behulp van een VPN-gateway. Elke Azure-bron die u met het virtuele netwerk verbindt, moet zich op dezelfde locatie bevinden als het virtuele netwerk.

**Opdrachten**

- Azure CLI: [vnet van AZ-netwerk maken](/cli/azure/network/vnet)
- PowerShell: [Nieuw-azvirtualnetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Virtuele netwerken en instellingen weergeven

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer in de lijst met virtuele netwerken het virtuele netwerk waarvoor u de instellingen wilt weergeven.
3. De volgende instellingen worden weergegeven voor het virtuele netwerk dat u hebt geselecteerd:
   - **Overzicht**: Geeft informatie over het virtuele netwerk, inclusief adresruimte en DNS-servers. De volgende screenshot toont de overzichtsinstellingen voor een virtueel netwerk met de naam **MyVNet:**

     ![Overzicht van de netwerkinterface](./media/manage-virtual-network/vnet-overview.png)

     U een virtueel netwerk verplaatsen naar een ander abonnement of resourcegroep door **Wijzigen** te selecteren naast **Resourcegroep** of **Abonnementsnaam**. Zie Resources verplaatsen naar een [andere resourcegroep of -abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het verplaatsen van een virtueel netwerk. In het artikel worden vereisten weergegeven en hoe resources kunnen worden verplaatst met behulp van de Azure-portal, PowerShell en Azure CLI. Alle bronnen die zijn verbonden met het virtuele netwerk moeten worden verplaatst met het virtuele netwerk.
   - **Adresruimte:** de adresruimten die aan het virtuele netwerk zijn toegewezen, worden weergegeven. Voer de stappen in [Een adresbereik toevoegen of verwijderen](#add-or-remove-an-address-range)uit voor meer informatie over het toevoegen en verwijderen van een adresbereik aan de adresruimte.
   - **Verbonden apparaten:** alle bronnen die zijn verbonden met het virtuele netwerk worden weergegeven. In de vorige schermafbeelding zijn drie netwerkinterfaces en één load balancer verbonden met het virtuele netwerk. Alle nieuwe bronnen die u maakt en verbinding maakt met het virtuele netwerk worden weergegeven. Als u een bron verwijdert die is verbonden met het virtuele netwerk, wordt deze niet meer weergegeven in de lijst.
   - **Subnetten**: Er wordt een lijst met subnetten weergegeven die binnen het virtuele netwerk bestaan. Zie [Subnetten beheren](virtual-network-manage-subnet.md)voor meer informatie over het toevoegen en verwijderen van een subnet.
   - **DNS-servers**: U opgeven of de interne DNS-server van Azure of een aangepaste DNS-server naamomzetting biedt voor apparaten die zijn verbonden met het virtuele netwerk. Wanneer u een virtueel netwerk maakt met behulp van de Azure-portal, worden de DNS-servers van Azure standaard gebruikt voor naamomzetting binnen een virtueel netwerk. Voer de stappen in [DNS-servers](#change-dns-servers) wijzigen in dit artikel uit om de DNS-servers te wijzigen.
   - **Peerings**: Als er bestaande peerings in het abonnement zijn, worden ze hier vermeld. U instellingen voor bestaande peerings bekijken of peerings maken, wijzigen of verwijderen. Zie [Virtueel netwerkpeeren](virtual-network-peering-overview.md)voor meer informatie over peerings.
   - **Eigenschappen:** geeft instellingen weer over het virtuele netwerk, inclusief de bron-id van het virtuele netwerk en het Azure-abonnement waarin het zich bevindt.
   - **Diagram:** Het diagram biedt een visuele weergave van alle apparaten die zijn verbonden met het virtuele netwerk. Het diagram heeft een aantal belangrijke informatie over de apparaten. Als u een apparaat in deze weergave wilt beheren, selecteert u in het diagram het apparaat.
   - **Veelvoorkomende Azure-instellingen:** Zie de volgende informatie voor meer informatie over algemene Azure-instellingen:
     - [Activiteitenlogboek](../azure-monitor/platform/platform-logs-overview.md)
     - [Toegangsbeheer (IAM)](../role-based-access-control/overview.md)
     - [Tags](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Sloten](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Automation-script](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Opdrachten**

- Azure CLI: vnet show [az-netwerk](/cli/azure/network/vnet)
- PowerShell: [Get-azVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Een adresbereik toevoegen of verwijderen

U adresbereiken voor een virtueel netwerk toevoegen en verwijderen. Een adresbereik moet worden opgegeven in CIDR-notatie en kan niet overlappen met andere adresbereiken binnen hetzelfde virtuele netwerk. De adresbereiken die u definieert, kunnen openbaar of privé zijn (RFC 1918). Of u nu het adresbereik definieert als openbaar of privé, het adresbereik is alleen bereikbaar vanuit het virtuele netwerk, vanuit onderling verbonden virtuele netwerken en van alle on-premises netwerken die u hebt aangesloten op het virtuele netwerk. 

U het adresbereik voor een virtueel netwerk verlagen zolang het nog steeds de bereiken van de bijbehorende subnetten bevat. Daarnaast u het adresbereik uitbreiden, bijvoorbeeld door een /16 naar /8 te wijzigen. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

U de volgende adresbereiken niet toevoegen:

- 224.0.0.0/4 (Multicast)
- 255.255.255.255/32 (Uitzending)
- 127.0.0.0/8 (Loopback)
- 169.254.0.0/16 (Link-lokaal)
- 168.63.129.16/32 (interne [statusssdetectie](../load-balancer/load-balancer-custom-probe-overview.md#probesource)van DNS, DHCP en Azure Load Balancer)

Een adresbereik toevoegen of verwijderen:

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer in de lijst met virtuele netwerken het virtuele netwerk waarvoor u een adresbereik wilt toevoegen of verwijderen.
3. Selecteer **Adresruimte**onder **INSTELLINGEN**.
4. Voltooi een van de volgende opties:
    - **Een adresbereik toevoegen:** voer het nieuwe adresbereik in. Het adresbereik kan niet overlappen met een bestaand adresbereik dat is gedefinieerd voor het virtuele netwerk.
    - **Een adresbereik verwijderen:** Rechts van het adresbereik dat u wilt verwijderen, selecteert u **...**, selecteert u **Verwijderen**. Als er een subnet bestaat in het adresbereik, u het adresbereik niet verwijderen. Als u een adresbereik wilt verwijderen, moet u eerst alle subnetten (en alle bronnen in de subnetten) verwijderen die in het adresbereik bestaan.
5. Selecteer **Opslaan**.

**Opdrachten**

- Azure CLI: [vnet-update az-netwerk](/cli/azure/network/vnet)
- PowerShell: [Set-azVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>DNS-servers wijzigen

Alle VM's die zijn verbonden met het virtuele netwerk registreren met de DNS-servers die u opgeeft voor het virtuele netwerk. Ze gebruiken ook de opgegeven DNS-server voor naamomzetting. Elke netwerkinterface (NIC) in een VM kan zijn eigen DNS-serverinstellingen hebben. Als een NIC zijn eigen DNS-serverinstellingen heeft, overschrijven ze de DNS-serverinstellingen voor het virtuele netwerk. Zie [Netwerkinterfacetaken en -instellingen](virtual-network-network-interface.md#change-dns-servers)voor meer informatie over NIC DNS-instellingen. Zie [Naamoplossing voor VM's en rolinstanties voor](virtual-networks-name-resolution-for-vms-and-role-instances.md)meer informatie over naamomzetting voor VM's en rolexemplaren. Een DNS-server toevoegen, wijzigen of verwijderen:

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer in de lijst met virtuele netwerken het virtuele netwerk waarvoor u DNS-servers wilt wijzigen.
3. Selecteer **DNS-servers**onder **INSTELLINGEN**.
4. Selecteer één van de volgende opties:
   - **Standaard (Azure-mits)**: Alle bronnamen en privé-IP-adressen worden automatisch geregistreerd op de Azure DNS-servers. U namen oplossen tussen bronnen die zijn verbonden met hetzelfde virtuele netwerk. U deze optie niet gebruiken om namen op te lossen in virtuele netwerken. Als u namen in virtuele netwerken wilt oplossen, moet u een aangepaste DNS-server gebruiken.
   - **Aangepast:** u een of meer servers toevoegen, tot de Azure-limiet voor een virtueel netwerk. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic)voor meer informatie over DNS-serverlimieten. U hebt de volgende opties:
   - **Een adres toevoegen:** voegt de server toe aan de lijst met DNS-servers voor virtuele netwerken. Met deze optie registreert u ook de DNS-server met Azure. Als u al een DNS-server met Azure hebt geregistreerd, u die DNS-server in de lijst selecteren.
   - **Een adres verwijderen:** Selecteer naast de server die u wilt verwijderen **...**, en **verwijder**. Als u de server verwijdert, wordt de server alleen uit deze lijst met virtuele netwerken verwijderd. De DNS-server blijft geregistreerd in Azure voor het gebruik van uw andere virtuele netwerken.
   - **DNS-serveradressen opnieuw ordenen:** het is belangrijk om te controleren of u uw DNS-servers in de juiste volgorde voor uw omgeving vermeldt. DNS-serverlijsten worden gebruikt in de volgorde waarin ze zijn opgegeven. Ze werken niet als een round-robin setup. Als de eerste DNS-server in de lijst kan worden bereikt, gebruikt de client die DNS-server, ongeacht of de DNS-server naar behoren functioneert. Verwijder alle DNS-servers die worden vermeld en voeg ze vervolgens terug in de gewenste volgorde.
   - **Een adres wijzigen:** markeer de DNS-server in de lijst en voer het nieuwe adres in.
5. Selecteer **Opslaan**.
6. Start de VM's die zijn verbonden met het virtuele netwerk opnieuw, zodat ze de nieuwe DNS-serverinstellingen toegewezen krijgen. VM's blijven hun huidige DNS-instellingen gebruiken totdat ze opnieuw worden opgestart.

**Opdrachten**

- Azure CLI: [vnet-update az-netwerk](/cli/azure/network/vnet)
- PowerShell: [Set-azVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Een virtueel netwerk verwijderen

U een virtueel netwerk alleen verwijderen als er geen bronnen op zijn verbonden. Als er bronnen zijn verbonden met een subnet binnen het virtuele netwerk, moet u eerst de bronnen verwijderen die zijn verbonden met alle subnetten binnen het virtuele netwerk. De stappen die u neemt om een resource te verwijderen, zijn afhankelijk van de resource. Lees de documentatie voor elk brontype dat u wilt verwijderen voor het verwijderen van resources die zijn verbonden met subnetten. Ga als lid van het netwerk:

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer in de lijst met virtuele netwerken het virtuele netwerk dat u wilt verwijderen.
3. Controleer of er geen apparaten zijn verbonden met het virtuele netwerk door **Verbonden apparaten**te selecteren onder **INSTELLINGEN**. Als er verbonden apparaten zijn, moet u deze verwijderen voordat u het virtuele netwerk verwijderen. Als er geen verbonden apparaten zijn, selecteert u **Overzicht**.
4. Selecteer **Verwijderen**.
5. Als u de verwijdering van het virtuele netwerk wilt bevestigen, selecteert u **Ja**.

**Opdrachten**

- Azure CLI: [vnet verwijderen azure-netwerkvnet](/cli/azure/network/vnet)
- PowerShell: [Remove-azVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Machtigingen

Als u taken in virtuele netwerken wilt uitvoeren, moet uw account worden toegewezen aan de [rol van netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rol waaraan de juiste acties in de volgende tabel zijn toegewezen:

| Actie                                  |   Name                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/lezen   |   Een virtueel netwerk lezen              |
|Microsoft.Network/virtualNetworks/write  |   Een virtueel netwerk maken of bijwerken  |
|Microsoft.Network/virtualNetworks/delete |   Een virtueel netwerk verwijderen            |

## <a name="next-steps"></a>Volgende stappen

- Een virtueel netwerk maken met [PowerShell-](powershell-samples.md) of [Azure CLI-voorbeeldscripts](cli-samples.md) of Azure [Resource Manager-sjablonen gebruiken](template-samples.md)
- [Azure-beleid](policy-samples.md) maken en toepassen voor virtuele netwerken
