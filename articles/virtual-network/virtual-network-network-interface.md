---
title: Een Azure-netwerkinterface maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Meer informatie over wat een netwerkinterface is en hoe u deze maken, instellingen wijzigen en verwijderen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/22/2020
ms.author: kumud
ms.openlocfilehash: 11e6285ef70ffde5344add951801997f8541eaad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244938"
---
# <a name="create-change-or-delete-a-network-interface"></a>Een netwerkinterface maken, wijzigen of verwijderen

Meer informatie over het maken, wijzigen van instellingen voor en verwijderen van een netwerkinterface. Met een netwerkinterface kan een Azure Virtual Machine communiceren met internet-, Azure- en on-premises bronnen. Wanneer u een virtuele machine maakt met de Azure-portal, maakt de portal één netwerkinterface met standaardinstellingen voor u. U er in plaats daarvan voor kiezen om netwerkinterfaces te maken met aangepaste instellingen en een of meer netwerkinterfaces toe te voegen aan een virtuele machine wanneer u deze maakt. U ook de standaardinstellingen van de netwerkinterface voor een bestaande netwerkinterface wijzigen. In dit artikel wordt uitgelegd hoe u een netwerkinterface maakt met aangepaste instellingen, bestaande instellingen wijzigt, zoals toewijzing van netwerkfilters (netwerkbeveiligingsgroep), subnettoewijzing, DNS-serverinstellingen en IP-forwarding en een netwerkinterface verwijdert.

Zie [IP-adressen beheren](virtual-network-network-interface-addresses.md)als u IP-adressen voor een netwerkinterface wilt toevoegen, wijzigen of verwijderen. Zie [Netwerkinterfaces](virtual-network-network-interface-vm.md)toevoegen of verwijderen als u netwerkinterfaces aan virtuele machines wilt toevoegen of verwijderen.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende taken uit voordat u stappen in een gedeelte van dit artikel voltooit:

- Als u nog geen Azure-account hebt, meldt u zich aan voor een [gratis proefaccount.](https://azure.microsoft.com/free)
- Als u de https://portal.azure.comportal gebruikt, opent u en logt u in met uw Azure-account.
- Als u PowerShell-opdrachten gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell vanaf uw computer uit te voeren. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelfstudie is de Azure PowerShell-module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u CLI-opdrachten (Azure Command-line interface) gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Voor deze zelfstudie is de Azure CLI-versie 2.0.28 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal uitvoert, `az login` moet u ook worden uitgevoerd om een verbinding met Azure te maken.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure, moet worden toegewezen aan de [rol van netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [Machtigingen](#permissions).

## <a name="create-a-network-interface"></a>Een netwerkinterface maken

Wanneer u een virtuele machine maakt met de Azure-portal, maakt de portal een netwerkinterface met standaardinstellingen voor u. Als u liever al uw netwerkinterface-instellingen opgeeft, u een netwerkinterface maken met aangepaste instellingen en de netwerkinterface aan een virtuele machine koppelen bij het maken van de virtuele machine (met PowerShell of azure CLI). U ook een netwerkinterface maken en deze toevoegen aan een bestaande virtuele machine (met PowerShell of de Azure CLI). Zie [Netwerkinterfaces](virtual-network-network-interface-vm.md)toevoegen of verwijderen voor meer informatie over het maken van een virtuele machine met een bestaande netwerkinterface of het toevoegen aan of verwijderen van netwerkinterfaces van bestaande virtuele machines. Voordat u een netwerkinterface maakt, moet u een bestaand [virtueel netwerk](manage-virtual-network.md) op dezelfde locatie hebben en een abonnement waarin u een netwerkinterface maakt.

1. Typ *netwerkinterfaces*in het vak met de tekst *Zoekbronnen* boven aan de Azure-portal. Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer **+ Toevoegen** onder **Netwerkinterfaces**.
3. Voer waarden in of selecteer waarden voor de volgende instellingen en selecteer **Vervolgens Maken:**

    |Instelling|Vereist?|Details|
    |---|---|---|
    |Name|Ja|De naam moet uniek zijn binnen de resourcegroep die u selecteert. Na verloop van tijd hebt u waarschijnlijk verschillende netwerkinterfaces in uw Azure-abonnement. Zie Conventies benoemen voor suggesties bij het maken van een [naamgevingsconventie](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)om het beheren van verschillende netwerkinterfaces eenvoudiger te maken. De naam kan niet worden gewijzigd nadat de netwerkinterface is gemaakt.|
    |Virtueel netwerk|Ja|Selecteer het virtuele netwerk voor de netwerkinterface. U een netwerkinterface alleen toewijzen aan een virtueel netwerk dat bestaat in hetzelfde abonnement en dezelfde locatie als de netwerkinterface. Zodra een netwerkinterface is gemaakt, u het virtuele netwerk waaraan het is toegewezen, niet meer wijzigen. De virtuele machine waaraan u de netwerkinterface toevoegt, moet ook op dezelfde locatie en een abonnement als de netwerkinterface bestaan.|
    |Subnet|Ja|Selecteer een subnet in het virtuele netwerk dat u hebt geselecteerd. U het subnet wijzigen waaraan de netwerkinterface is toegewezen nadat deze is gemaakt.|
    |Toewijzing van privé-IP-adres|Ja| In deze instelling kiest u de toewijzingsmethode voor het IPv4-adres. Kies uit de volgende toewijzingsmethoden: **Dynamisch:** Wanneer u deze optie selecteert, wijst Azure automatisch het volgende beschikbare adres toe uit de adresruimte van het subnet dat u hebt geselecteerd. **Statisch:** Wanneer u deze optie selecteert, moet u handmatig een beschikbaar IP-adres toewijzen vanuit de adresruimte van het subnet dat u hebt geselecteerd. Statische en dynamische adressen worden pas gewijzigd als u ze wijzigt of de netwerkinterface is verwijderd. U de toewijzingsmethode wijzigen nadat de netwerkinterface is gemaakt. De Azure DHCP-server wijst dit adres toe aan de netwerkinterface binnen het besturingssysteem van de virtuele machine.|
    |Netwerkbeveiligingsgroep|Nee| Laat ingesteld op **Geen,** selecteer een bestaande [netwerkbeveiligingsgroep](security-overview.md)of [maak een netwerkbeveiligingsgroep](tutorial-filter-network-traffic.md). Met netwerkbeveiligingsgroepen u netwerkverkeer filteren in en uit een netwerkinterface. U nul of één netwerkbeveiligingsgroep toepassen op een netwerkinterface. Nul of één netwerkbeveiligingsgroep kan ook worden toegepast op het subnet waaraan de netwerkinterface is toegewezen. Wanneer een netwerkbeveiligingsgroep wordt toegepast op een netwerkinterface en het subnet waaraan de netwerkinterface is toegewezen, treden soms onverwachte resultaten op. Zie [Netwerkbeveiligingsgroepen oplossen](diagnose-network-traffic-filter-problem.md)als u netwerkbeveiligingsgroepen wilt oplossen die zijn toegepast op netwerkinterfaces en subnetten.|
    |Abonnement|Ja|Selecteer een van uw [Azure-abonnementen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). De virtuele machine waarop u een netwerkinterface koppelt en het virtuele netwerk waarmee u verbinding maakt, moet in hetzelfde abonnement bestaan.|
    |Privé-IP-adres (IPv6)|Nee| Als u dit selectievakje inschakelt, wordt een IPv6-adres toegewezen aan de netwerkinterface, naast het IPv4-adres dat is toegewezen aan de netwerkinterface. Zie het iPv6-gedeelte van dit artikel voor belangrijke informatie over het gebruik van IPv6 met netwerkinterfaces. U geen toewijzingsmethode selecteren voor het IPv6-adres. Als u ervoor kiest een IPv6-adres toe te wijzen, wordt het toegewezen aan de dynamische methode.
    |IPv6-naam (wordt alleen weergegeven wanneer het selectievakje **Privé-IP-adres (IPv6)** is ingeschakeld) |Ja, als het selectievakje **Privé-IP-adres (IPv6)** is ingeschakeld.| Deze naam wordt toegewezen aan een secundaire IP-configuratie voor de netwerkinterface. Zie [Netwerkinterface-instellingen weergeven](#view-network-interface-settings)voor meer informatie over IP-configuraties.|
    |Resourcegroep|Ja|Selecteer een bestaande [resourcegroep](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) of maak er een. Er kan een netwerkinterface bestaan in dezelfde of andere brongroep dan de virtuele machine waaraan u deze koppelt of het virtuele netwerk waarmee u deze aansluit.|
    |Locatie|Ja|De virtuele machine waarop u een netwerkinterface koppelt en het virtuele netwerk waarmee u verbinding maakt, moet op dezelfde [locatie](https://azure.microsoft.com/regions)bestaan , ook wel een regio genoemd.|

De portal biedt niet de optie om een openbaar IP-adres toe te wijzen aan de netwerkinterface wanneer u het maakt, hoewel de portal wel een openbaar IP-adres maakt en het aan een netwerkinterface toewijst wanneer u een virtuele machine maakt met behulp van de portal. Zie [IP-adressen beheren](virtual-network-network-interface-addresses.md)voor meer informatie over het toevoegen van een openbaar IP-adres aan de netwerkinterface. Als u een netwerkinterface met een openbaar IP-adres wilt maken, moet u de CLI of PowerShell gebruiken om de netwerkinterface te maken.

De portal biedt niet de optie om de netwerkinterface toe te wijzen aan toepassingsbeveiligingsgroepen bij het maken van een netwerkinterface, maar azure CLI en PowerShell wel. U een bestaande netwerkinterface toewijzen aan een toepassingsbeveiligingsgroep via de portal, zolang de netwerkinterface is gekoppeld aan een virtuele machine. Zie [Toevoegen aan of verwijderen uit toepassingsbeveiligingsgroepen](#add-to-or-remove-from-application-security-groups)voor meer informatie over het toewijzen van een netwerkinterface aan een toepassingsbeveiligingsgroep.

>[!Note]
> Azure wijst een MAC-adres alleen toe aan de netwerkinterface nadat de netwerkinterface is gekoppeld aan een virtuele machine en de virtuele machine de eerste keer is gestart. U het MAC-adres dat Azure aan de netwerkinterface toewijst, niet opgeven. Het MAC-adres blijft toegewezen aan de netwerkinterface totdat de netwerkinterface wordt verwijderd of het privé-IP-adres dat is toegewezen aan de primaire IP-configuratie van de primaire netwerkinterface wordt gewijzigd. Zie [IP-adressen beheren](virtual-network-network-interface-addresses.md) voor meer informatie over IP-adressen en IP-configuraties

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Netwerkinterface-instellingen weergeven

U de meeste instellingen voor een netwerkinterface weergeven en wijzigen nadat deze is gemaakt. De portal geeft niet het DNS-achtervoegsel of het lidmaatschap van de toepassingsbeveiligingsgroep voor de netwerkinterface weer. U de PowerShell- of Azure [CLI-opdrachten](#view-settings-commands) gebruiken om het DNS-achtervoegsel en het lidmaatschap van de toepassingsbeveiligingsgroep weer te geven.

1. Typ *netwerkinterfaces*in het vak met de tekst *Zoekbronnen* boven aan de Azure-portal. Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer de netwerkinterface die u wilt weergeven of wijzig de instellingen in de lijst.
3. De volgende items worden weergegeven voor de netwerkinterface die u hebt geselecteerd:
   - **Overzicht:** Biedt informatie over de netwerkinterface, zoals de IP-adressen die eraan zijn toegewezen, het virtuele netwerk/subnet waaraan de netwerkinterface is toegewezen en de virtuele machine waaraan de netwerkinterface is gekoppeld (als deze aan een is gekoppeld). De volgende afbeelding toont de overzichtsinstellingen voor een ![netwerkinterface met de naam **mywebserver256**: Overzicht van de netwerkinterface](./media/virtual-network-network-interface/nic-overview.png)

     U een netwerkinterface verplaatsen naar een andere brongroep of -abonnement door naast de **brongroep** of **abonnementsnaam**te selecteren (**wijzigen).** Als u de netwerkinterface verplaatst, moet u alle bronnen met betrekking tot de netwerkinterface ermee verplaatsen. Als de netwerkinterface bijvoorbeeld is gekoppeld aan een virtuele machine, moet u ook de virtuele machine en andere bronnen met betrekking tot virtuele machines verplaatsen. Zie Bron [verplaatsen naar een nieuwe resourcegroep of -abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal)als u een netwerkinterface wilt verplaatsen. In het artikel worden vereisten weergegeven en hoe resources kunnen worden verplaatst met de Azure-portal, PowerShell en Azure CLI.
   - **IP-configuraties:** Openbare en private IPv4- en IPv6-adressen die zijn toegewezen aan IP-configuraties worden hier weergegeven. Als een IPv6-adres is toegewezen aan een IP-configuratie, wordt het adres niet weergegeven. Zie [IP-adressen configureren voor een Azure-netwerkinterface voor](virtual-network-network-interface-addresses.md)meer informatie over IP-configuraties en het toevoegen en verwijderen van IP-adressen. IP-forwarding en subnettoewijzing zijn ook geconfigureerd in deze sectie. Zie [IP-doorstuur en](#enable-or-disable-ip-forwarding) [Subnettoewijzing wijzigen](#change-subnet-assignment)voor meer informatie over deze instellingen.
   - **DNS-servers:** U opgeven welke DNS-server een netwerkinterface is toegewezen door de Azure DHCP-servers. De netwerkinterface kan de instelling overnemen van het virtuele netwerk waaraan de netwerkinterface is toegewezen, of een aangepaste instelling hebben die de instelling overschrijft voor het virtuele netwerk waaraan het is toegewezen. Zie [DNS-servers wijzigen](#change-dns-servers)om te wijzigen wat wordt weergegeven.
   - **Netwerkbeveiligingsgroep (NSG):** Hiermee wordt weergegeven welke NSG is gekoppeld aan de netwerkinterface (indien aanwezig). Een NSG bevat inkomende en uitgaande regels om netwerkverkeer voor de netwerkinterface te filteren. Als een NSG is gekoppeld aan de netwerkinterface, wordt de naam van de bijbehorende NSG weergegeven. Zie [Een netwerkbeveiligingsgroep koppelen of](#associate-or-dissociate-a-network-security-group)scheiden als u wilt wijzigen wat wordt weergegeven.
   - **Eigenschappen:** Hiermee worden belangrijke instellingen weergegeven over de netwerkinterface, inclusief het MAC-adres (leeg als de netwerkinterface niet is gekoppeld aan een virtuele machine) en het abonnement waarin het bestaat.
   - **Effectieve beveiligingsregels:**  Beveiligingsregels worden weergegeven als de netwerkinterface is gekoppeld aan een draaiende virtuele machine en een NSG is gekoppeld aan de netwerkinterface, het subnet waaraan het is toegewezen of beide. Zie [Effectieve beveiligingsregels weergeven](#view-effective-security-rules)voor meer informatie over wat er wordt weergegeven. Zie [Netwerkbeveiligingsgroepen](security-overview.md)voor meer informatie over NSGs.
   - **Effectieve routes:** Routes worden weergegeven als de netwerkinterface is gekoppeld aan een draaiende virtuele machine. De routes zijn een combinatie van de Azure-standaardroutes, eventuele door de gebruiker gedefinieerde routes en alle BGP-routes die mogelijk bestaan voor het subnet waaraan de netwerkinterface is toegewezen. Zie [Effectieve routes weergeven](#view-effective-routes)voor meer informatie over wat er wordt weergegeven. Zie [Routeringsoverzicht](virtual-networks-udr-overview.md)voor meer informatie over Azure-standaardroutes en door de gebruiker gedefinieerde routes.
Algemene Azure Resource Manager-instellingen: zie [Activiteitslogboek](../azure-monitor/platform/platform-logs-overview.md), [Toegangsbeheer (IAM),](../role-based-access-control/overview.md) [Tags,](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [Sloten](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)en [Automatiseringsscript](../azure-resource-manager/templates/export-template-portal.md)voor meer informatie over algemene Azure Resource Manager-instellingen .

<a name="view-settings-commands"></a>**Opdrachten**

Als een IPv6-adres is toegewezen aan een netwerkinterface, retourneert de PowerShell-uitvoer het feit dat het adres is toegewezen, maar wordt het toegewezen adres niet geretourneerd. Op dezelfde manier geeft de CLI het feit dat het adres is toegewezen, maar retourneert *null* in de uitvoer voor het adres.

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az-netwerknic lijst](/cli/azure/network/nic) om netwerkinterfaces in het abonnement te bekijken; [az-netwerk nic show](/cli/azure/network/nic) om instellingen voor een netwerkinterface te bekijken|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) om netwerkinterfaces te bekijken in het abonnement of instellingen voor een netwerkinterface weer te geven|

## <a name="change-dns-servers"></a>DNS-servers wijzigen

De DNS-server wordt door de Azure DHCP-server toegewezen aan de netwerkinterface binnen het besturingssysteem voor virtuele machines. De toegewezen DNS-server is wat de DNS-serverinstelling ook is voor een netwerkinterface. Zie [Naamresolutie voor virtuele machines voor](virtual-networks-name-resolution-for-vms-and-role-instances.md)meer informatie over instellingen voor naamomzetting voor een netwerkinterface. De netwerkinterface kan de instellingen van het virtuele netwerk overnemen of zijn eigen unieke instellingen gebruiken die de instelling voor het virtuele netwerk overschrijven.

1. Typ *netwerkinterfaces*in het vak met de tekst *Zoekbronnen* boven aan de Azure-portal. Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer de netwerkinterface waarvoor u een DNS-server wilt wijzigen in de lijst.
3. Selecteer **DNS-servers** onder **INSTELLINGEN**.
4. Selecteer een van beide:
   - **Overnemen van virtueel netwerk:** kies deze optie om de DNS-serverinstelling over te nemen die is gedefinieerd voor het virtuele netwerk waaraan de netwerkinterface is toegewezen. Op virtueel netwerkniveau wordt een aangepaste DNS-server of de door Azure geleverde DNS-server gedefinieerd. De door Azure geleverde DNS-server kan hostnamen oplossen voor resources die aan hetzelfde virtuele netwerk zijn toegewezen. FQDN moet worden gebruikt om op te lossen voor resources die zijn toegewezen aan verschillende virtuele netwerken.
   - **Aangepast:** U uw eigen DNS-server configureren om namen op meerdere virtuele netwerken op te lossen. Voer het IP-adres in van de server die u als DNS-server wilt gebruiken. Het op opgegeven DNS-serveradres wordt alleen toegewezen aan deze netwerkinterface en overschrijft elke DNS-instelling voor het virtuele netwerk waaraan de netwerkinterface is toegewezen.
     >[!Note]
     >Als de VM een NIC gebruikt die deel uitmaakt van een beschikbaarheidsset, worden alle DNS-servers die zijn opgegeven voor elk van de VM's van alle NIC's die deel uitmaken van de beschikbaarheidsset, overgenomen.
5. Selecteer **Opslaan**.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az netwerk nic update](/cli/azure/network/nic)|
|PowerShell|[Set-azNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>IP-forwarding in- of uitschakelen

Met IP forwarding kan de virtuele machine een netwerkinterface is aangesloten op:
- Ontvang netwerkverkeer dat niet is bestemd voor een van de IP-adressen die zijn toegewezen aan een van de IP-configuraties die aan de netwerkinterface zijn toegewezen.
- Netwerkverkeer verzenden met een ander bron-IP-adres dan het IP-adres van een netwerkinterface.

De instelling moet zijn ingeschakeld voor elke netwerkinterface die is gekoppeld aan de virtuele machine die verkeer ontvangt dat de virtuele machine nodig heeft om vooruit te gaan. Een virtuele machine kan verkeer doorsturen, ongeacht of er meerdere netwerkinterfaces of één netwerkinterface aan zijn gekoppeld. Hoewel IP-forwarding een Azure-instelling is, moet de virtuele machine ook een toepassing uitvoeren die in staat is om het verkeer door te sturen, zoals firewall, WAN-optimalisatie en taakverdelingstoepassingen. Wanneer een virtuele machine netwerktoepassingen uitvoert, wordt de virtuele machine vaak een virtueel netwerktoestel genoemd. U een lijst met gereed om virtuele netwerkapparaten te implementeren in de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)bekijken. IP-forwarding wordt meestal gebruikt met door de gebruiker gedefinieerde routes. Zie Door [de gebruiker gedefinieerde routes](virtual-networks-udr-overview.md)voor meer informatie over door de gebruiker gedefinieerde routes.

1. Typ *netwerkinterfaces*in het vak met de tekst *Zoekbronnen* boven aan de Azure-portal. Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer de netwerkinterface waarvoor u IP-forwarding wilt inschakelen of uitschakelen.
3. Selecteer **IP-configuraties** in de sectie **INSTELLINGEN.**
4. Selecteer **Ingeschakeld** of **uitgeschakeld** (standaardinstelling) om de instelling te wijzigen.
5. Selecteer **Opslaan**.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az netwerk nic update](/cli/azure/network/nic)|
|PowerShell|[Set-azNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Subnettoewijzing wijzigen

U het subnet wijzigen, maar niet het virtuele netwerk waaraan een netwerkinterface is toegewezen.

1. Typ *netwerkinterfaces*in het vak met de tekst *Zoekbronnen* boven aan de Azure-portal. Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer de netwerkinterface waarvoor u subnettoewijzing wilt wijzigen.
3. Selecteer **IP-configuraties** onder **INSTELLINGEN**. Als er privé-IP-adressen voor alle vermelde IP-configuraties **(Static)** naast zich hebben staan, moet u de IP-adrestoewijzingsmethode wijzigen in dynamisch door de volgende stappen uit te voeren. Alle privé-IP-adressen moeten worden toegewezen met de dynamische toewijzingsmethode om de subnettoewijzing voor de netwerkinterface te wijzigen. Als de adressen zijn toegewezen aan de dynamische methode, gaat u verder met stap vijf. Als iPv4-adressen zijn toegewezen aan de statische toewijzingsmethode, voert u de volgende stappen uit om de toewijzingsmethode te wijzigen in dynamisch:
   - Selecteer de IP-configuratie waarvoor u de iPv4-adrestoewijzingsmethode wilt wijzigen in de lijst met IP-configuraties.
   - Selecteer **Dynamisch** voor de **methode** toewijzing van privé-IP-adres. U geen IPv6-adres toewijzen aan de statische toewijzingsmethode.
   - Selecteer **Opslaan**.
4. Selecteer het subnet waarnaar u de netwerkinterface wilt verplaatsen vanuit de vervolgkeuzelijst **Subnet.**
5. Selecteer **Opslaan**. Nieuwe dynamische adressen worden toegewezen vanuit het subnetadresbereik voor het nieuwe subnet. Nadat u de netwerkinterface aan een nieuw subnet hebt toegewezen, u desgevraagd een statisch IPv4-adres toewijzen uit het nieuwe subnetadresbereik. Zie [IP-adressen](virtual-network-network-interface-addresses.md)beheren voor meer informatie over het toevoegen, wijzigen en verwijderen van IP-adressen voor een netwerkinterface.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az netwerk nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-azNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Toevoegen aan of verwijderen uit toepassingsbeveiligingsgroepen

U alleen een netwerkinterface toevoegen aan of een netwerkinterface verwijderen uit een beveiligingsgroep voor toepassingen met behulp van de portal als de netwerkinterface is gekoppeld aan een virtuele machine. U PowerShell of de Azure CLI gebruiken om een netwerkinterface toe te voegen aan of een netwerkinterface uit een toepassingsbeveiligingsgroep te verwijderen, ongeacht of de netwerkinterface is gekoppeld aan een virtuele machine of niet. Meer informatie over [toepassingsbeveiligingsgroepen](security-overview.md#application-security-groups) en het [maken van een toepassingsbeveiligingsgroep](manage-network-security-group.md).

1. Begin in het vak *Bronnen, services en documenten zoeken* boven aan de portal met het typen van de naam van een virtuele machine met een netwerkinterface die u wilt toevoegen aan of verwijderen uit een toepassingsbeveiligingsgroep. Wanneer de naam van uw vm wordt weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer onder **INSTELLINGEN** de optie **Netwerken**.  Selecteer **Toepassingsbeveiligingsgroepen** en **configureer de toepassingsbeveiligingsgroepen**en kies de toepassingsbeveiligingsgroepen waaraan u de netwerkinterface wilt toevoegen of schakel de toepassingsbeveiligingsgroepen uit waaruit u de netwerkinterface wilt verwijderen uit en selecteer **Opslaan**. Alleen netwerkinterfaces die in hetzelfde virtuele netwerk aanwezig zijn, kunnen aan dezelfde toepassingsbeveiligingsgroep worden toegevoegd. De toepassingsbeveiligingsgroep moet op dezelfde locatie als de netwerkinterface bestaan.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az netwerk nic update](/cli/azure/network/nic)|
|PowerShell|[Set-azNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Een netwerkbeveiligingsgroep koppelen of scheiden

1. Voer in het zoekvak boven aan de portal *netwerkinterfaces* in het zoekvak in. Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer de netwerkinterface in de lijst waaraan u een netwerkbeveiligingsgroep wilt koppelen of van een netwerkbeveiligingsgroep scheiden.
3. Selecteer **netwerkbeveiligingsgroep** onder **INSTELLINGEN**.
4. Selecteer **Bewerken**.
5. Selecteer **netwerkbeveiligingsgroep** en selecteer vervolgens de netwerkbeveiligingsgroep die u aan de netwerkinterface wilt koppelen of selecteer **Geen**om een netwerkbeveiligingsgroep te scheiden.
6. Selecteer **Opslaan**.

**Opdrachten**

- Azure CLI: [az-netwerk nic-update](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Een netwerkinterface verwijderen

U een netwerkinterface verwijderen zolang deze niet aan een virtuele machine is gekoppeld. Als een netwerkinterface is gekoppeld aan een virtuele machine, moet u eerst de virtuele machine in de gestopte (deallocated) status plaatsen en vervolgens de netwerkinterface loskoppelen van de virtuele machine. Als u een netwerkinterface wilt loskoppelen van een virtuele machine, voert u de stappen in [Een netwerkinterface loskoppelen van een virtuele machine.](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm) U een netwerkinterface echter niet loskoppelen van een virtuele machine als dit de enige netwerkinterface is die aan de virtuele machine is gekoppeld. Een virtuele machine moet altijd ten minste één netwerkinterface hebben. Als u een virtuele machine verwijdert, worden alle netwerkinterfaces die eraan zijn gekoppeld, maar worden de netwerkinterfaces niet verwijderd.

1. Typ *netwerkinterfaces*in het vak met de tekst *Zoekbronnen* boven aan de Azure-portal. Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer de netwerkinterface in de lijst die u wilt verwijderen.
3. Selecteer Onder **Overzicht** **Verwijderen selecteren**.
4. Selecteer **Ja** om de verwijdering van de netwerkinterface te bevestigen.

Wanneer u een netwerkinterface verwijdert, worden alle MAC- of IP-adressen die eraan zijn toegewezen, vrijgegeven.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az-netwerk nic verwijderen](/cli/azure/network/nic)|
|PowerShell|[Infrastructuur en ogen verwijderen](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Verbindingsproblemen oplossen

Als u niet communiceren van of naar een virtuele machine, kunnen beveiligingsregels voor netwerkbeveiligingsgroepen of -routes die van kracht zijn voor een netwerkinterface, het probleem veroorzaken. U hebt de volgende opties om het probleem op te lossen:

### <a name="view-effective-security-rules"></a>Effectieve beveiligingsregels weergeven

De effectieve beveiligingsregels voor elke netwerkinterface die aan een virtuele machine is gekoppeld, zijn een combinatie van de regels die u hebt gemaakt in een netwerkbeveiligingsgroep en [standaardbeveiligingsregels.](security-overview.md#default-security-rules) Als u de effectieve beveiligingsregels voor een netwerkinterface begrijpt, u bepalen waarom u niet van of naar een virtuele machine communiceren. U de effectieve regels bekijken voor elke netwerkinterface die is gekoppeld aan een draaiende virtuele machine.

1. Voer in het zoekvak boven aan de portal de naam in van een virtuele machine waarvoor u effectieve beveiligingsregels wilt weergeven. Als u de naam van een virtuele machine niet kent, voert u *virtuele machines* in het zoekvak in. Wanneer **virtuele machines** in de zoekresultaten worden weergegeven, selecteert u deze en selecteert u een virtuele machine in de lijst.
2. Selecteer **Netwerken** onder **INSTELLINGEN**.
3. Selecteer de naam van een netwerkinterface.
4. Selecteer **Effectieve beveiligingsregels** onder **ONDERSTEUNING + PROBLEEMOPLOSSING**.
5. Bekijk de lijst met effectieve beveiligingsregels om te bepalen of de juiste regels bestaan voor uw vereiste inkomende en uitgaande communicatie. Meer informatie over wat u ziet in de lijst in [het overzicht van de netwerkbeveiligingsgroep](security-overview.md).

Met de functie IP-stroomcontrole van Azure Network Watcher u ook bepalen of beveiligingsregels de communicatie tussen een virtuele machine en een eindpunt verhinderen. Zie [IP-stroom verifiëren](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie.

**Opdrachten**

- Azure CLI: [az-netwerk nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-azeffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Bekijk effectieve routes

De effectieve routes voor de netwerkinterfaces die aan een virtuele machine zijn gekoppeld, zijn een combinatie van standaardroutes, alle routes die u hebt gemaakt en alle routes die via BGP via een Azure-virtuele netwerkgateway worden gepropageerd vanaf on-premises netwerken. Als u de effectieve routes voor een netwerkinterface begrijpt, u bepalen waarom u niet communiceren van of naar een virtuele machine. U de effectieve routes bekijken voor elke netwerkinterface die is gekoppeld aan een draaiende virtuele machine.

1. Voer in het zoekvak boven aan de portal de naam in van een virtuele machine waarvoor u effectieve beveiligingsregels wilt weergeven. Als u de naam van een virtuele machine niet kent, voert u *virtuele machines* in het zoekvak in. Wanneer **virtuele machines** in de zoekresultaten worden weergegeven, selecteert u deze en selecteert u een virtuele machine in de lijst.
2. Selecteer **Netwerken** onder **INSTELLINGEN**.
3. Selecteer de naam van een netwerkinterface.
4. Selecteer **Effectieve routes** onder ONDERSTEUNING + **PROBLEEMOPLOSSING**.
5. Bekijk de lijst met effectieve routes om te bepalen of de juiste routes bestaan voor uw vereiste inkomende en uitgaande communicatie. Meer informatie over wat u ziet in de lijst in [het routeringsoverzicht](virtual-networks-udr-overview.md).

De volgende hopfunctie van Azure Network Watcher kan u ook helpen bepalen of routes de communicatie tussen een virtuele machine en een eindpunt verhinderen. Zie [Volgende hop](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie.

**Opdrachten**

- Azure CLI: [az-netwerk nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-azeffectiveroutetable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren op netwerkinterfaces, moet uw account worden toegewezen aan de [rol van netwerkinzender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rol waaraan de juiste machtigingen zijn toegewezen die in de volgende tabel worden vermeld:

| Actie                                                                     | Name                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Netwerkinterface downloaden                                     |
| Microsoft.Network/networkInterfaces/write                                  | Netwerkinterface maken of bijwerken                        |
| Microsoft.Network/networkInterfaces/join/action                            | Een netwerkinterface koppelen aan een virtuele machine           |
| Microsoft.Network/networkInterfaces/delete                                 | Netwerkinterface verwijderen                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Word lid van een bron om een netwerk interface via een servi ...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Effectieve routetabel voor netwerkinterface downloaden               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Effectieve beveiligingsgroepen voor netwerkinterface downloaden           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Load balancers voor netwerkinterface downloaden                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Servicekoppeling ophalen                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Een servicekoppeling maken of bijwerken                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Servicekoppeling verwijderen                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Servicekoppeling valideren                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | IP-configuratie van netwerkinterface downloaden                    |

## <a name="next-steps"></a>Volgende stappen

- Een VM maken met meerdere NIC's met de [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Eén NIC-vm maken met meerdere IPv4-adressen met de [Azure CLI](virtual-network-multiple-ip-addresses-cli.md) of [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Eén NIC-VM maken met een privé-IPv6-adres (achter een Azure Load Balancer) met de sjabloon [Azure CLI,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)of [Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Een netwerkinterface maken met [PowerShell-](powershell-samples.md) of [Azure CLI-voorbeeldscripts](cli-samples.md) of azure [resource manager-sjabloon gebruiken](template-samples.md)
- [Azure-beleid](policy-samples.md) maken en toepassen voor virtuele netwerken
