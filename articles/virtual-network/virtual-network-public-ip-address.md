---
title: Een openbaar IP-adres in Azure maken, wijzigen of verwijderen | Microsoft Documenten
description: Meer informatie over het maken, wijzigen of verwijderen van een openbaar IP-adres.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 38ff40b537730418fe9f0f8295884dae98a2fe0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244912"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Een openbaar IP-adres maken, wijzigen of verwijderen

Meer informatie over een openbaar IP-adres en hoe u er een maken, wijzigen en verwijderen. Een openbaar IP-adres is een bron met zijn eigen configureerbare instellingen. Als u een openbaar IP-adres toestelt aan een Azure-bron die openbare IP-adressen ondersteunt, wordt het instaat:
- Binnenkomende communicatie van internet naar de bron, zoals Azure Virtual Machines (VM), Azure Application Gateways, Azure Load Balancers, Azure VPN Gateways en anderen. U nog steeds communiceren met bepaalde bronnen, zoals VM's, via internet, als een VM geen openbaar IP-adres heeft toegewezen, zolang de VM deel uitmaakt van een back-endpool van load balancer en de load balancer een openbaar IP-adres krijgt toegewezen. Zie de documentatie voor de service om te bepalen of een bron voor een specifieke Azure-service een openbaar IP-adres kan worden toegewezen of dat deze kan worden gecommuniceerd via het openbare IP-adres van een andere Azure-bron.
- Uitgaande connectiviteit met het internet met behulp van een voorspelbaar IP-adres. Een virtuele machine kan bijvoorbeeld uitgaand communiceren naar het internet zonder dat er een openbaar IP-adres aan is toegewezen, maar het adres is standaard netwerkadres dat door Azure wordt vertaald naar een onvoorspelbaar openbaar adres. Als u een openbaar IP-adres aan een resource toewijst, weet u welk IP-adres wordt gebruikt voor de uitgaande verbinding. Hoewel voorspelbaar, kan het adres veranderen, afhankelijk van de gekozen toewijzingsmethode. Zie [Een openbaar IP-adres maken](#create-a-public-ip-address)voor meer informatie . Zie Uitgaande verbindingen begrijpen voor meer informatie over uitgaande verbindingen van [Azure-bronnen.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende taken uit voordat u stappen in een gedeelte van dit artikel voltooit:

- Als u nog geen Azure-account hebt, meldt u zich aan voor een [gratis proefaccount.](https://azure.microsoft.com/free)
- Als u de https://portal.azure.comportal gebruikt, opent u en logt u in met uw Azure-account.
- Als u PowerShell-opdrachten gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell vanaf uw computer uit te voeren. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelfstudie is de Azure PowerShell-module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u CLI-opdrachten (Azure Command-line interface) gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Deze zelfstudie vereist de Azure CLI-versie 2.0.31 of hoger. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal uitvoert, `az login` moet u ook worden uitgevoerd om een verbinding met Azure te maken.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure, moet worden toegewezen aan de [rol van netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [Machtigingen](#permissions).

Openbare IP-adressen hebben een nominale vergoeding. Als u de prijzen wilt bekijken, leest u de pagina [met de prijzen van het IP-adres.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**.
2. Voer *een openbaar ip-adres in* het vak Marketplace *zoeken* in. Wanneer **openbaar IP-adres** wordt weergegeven in de zoekresultaten, selecteert u het.
3. Selecteer **Onder Openbaar IP-adres**de optie **Maken**.
4. Voer waarden in of selecteer waarden voor de volgende instellingen onder **Openbaar IP-adres maken**en selecteer **Vervolgens Maken:**

   |Instelling|Vereist?|Details|
   |---|---|---|
   |IP-versie|Ja| Selecteer IPv4 of IPv6 of Beide. Als u Beide selecteert, worden 2 openbare IP-adressen gemaakt: 1 IPv4-adres en 1 IPv6-adres. Meer informatie over [IPv6 in Azure VNETs](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Ja|Alle openbare IP-adressen die vóór de introductie van SKU's zijn **gemaakt,** zijn openbare IP-adressen van Basic SKU. U de SKU niet wijzigen nadat het openbare IP-adres is gemaakt. Een standalone virtuele machine, virtuele machines binnen een beschikbaarheidsset of virtuele machineschaalsets kunnen Basis- of StandaardSKU's gebruiken. Het mengen van SKU's tussen virtuele machines binnen beschikbaarheidssets of schaalsets of standalone VM's is niet toegestaan. **Basis** SKU: Als u een openbaar IP-adres maakt in een regio die beschikbaarheidszones ondersteunt, wordt de instelling **Beschikbaarheidszone** standaard ingesteld op *Geen.* Basisip-IP's bieden geen ondersteuning voor beschikbaarheidszones. **Standaard** SKU: Een standaard SKU public IP kan worden gekoppeld aan een virtuele machine of een load balancer front-end. Als u een openbaar IP-adres maakt in een regio die beschikbaarheidszones ondersteunt, wordt de instelling **Beschikbaarheidszone** standaard ingesteld op *Zone-redundant.* Zie de instelling **Beschikbaarheidszone** voor meer informatie over beschikbaarheidszones. De standaard SKU is vereist als u het adres koppelt aan een standaardloadbalancer. Zie [Azure load balancer standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over standaard load balancers. Als u een openbaar IP-adres van een standaard-SKU toewijst aan een netwerkinterface van een virtuele machine, moet u het bedoelde verkeer expliciet toestaan met een [netwerkbeveiligingsgroep](security-overview.md#network-security-groups). Communicatie met de resource mislukt totdat u een netwerkbeveiligingsgroep maakt en koppelt en het gewenste verkeer expliciet toestaat.|
   |Name|Ja|De naam moet uniek zijn binnen de resourcegroep die u selecteert.|
   |Toewijzing ip-adres|Ja|**Dynamisch:** Dynamische adressen worden pas toegewezen nadat een openbaar IP-adres is gekoppeld aan een Azure-bron en de bron voor de eerste keer is gestart. Dynamische adressen kunnen worden gewijzigd als ze zijn toegewezen aan een resource, zoals een virtuele machine, en de virtuele machine wordt gestopt (deallocated) en vervolgens opnieuw worden gestart. Het adres blijft hetzelfde als een virtuele machine opnieuw wordt opgestart of gestopt (maar niet wordt toegewezen). Dynamische adressen worden vrijgegeven wanneer een openbare IP-adresbron wordt gescheiden van een bron waaraan is gekoppeld. **Statisch:** Statische adressen worden toegewezen wanneer een openbaar IP-adres wordt gemaakt. Statische adressen worden pas vrijgegeven als een openbare IP-adresbron is verwijderd. Als het adres niet is gekoppeld aan een resource, u de toewijzingsmethode wijzigen nadat het adres is gemaakt. Als het adres is gekoppeld aan een resource, u de toewijzingsmethode mogelijk niet wijzigen. Als u *IPv6* selecteert voor de **IP-versie,** moet de toewijzingsmethode *Dynamisch* zijn voor Basic SKU.  Standaard SKU-adressen zijn *statisch* voor zowel IPv4 als IPv6. |
   |Niet-actieve time-out (minuten)|Nee|Hoeveel minuten om een TCP- of HTTP-verbinding open te houden zonder erop te vertrouwen dat clients keep-alive berichten verzenden. Als u IPv6 voor **IP-versie**selecteert, kan deze waarde niet worden gewijzigd. |
   |DNS-naamlabel|Nee|Moet uniek zijn binnen de Azure-locatie waarin u de naam maakt (voor alle abonnementen en alle klanten). Azure registreert automatisch de naam en het IP-adres in zijn DNS, zodat u verbinding maken met een bron met de naam. Azure voegt een standaardsubnet toe, zoals *location.cloudapp.azure.com* (waar locatie de locatie is die u selecteert) aan de naam die u opgeeft, om de volledig gekwalificeerde DNS-naam te maken. Als u ervoor kiest om beide adresversies te maken, wordt dezelfde DNS-naam toegewezen aan zowel de IPv4- als de IPv6-adressen. De standaard DNS van Azure bevat zowel IPv4 A- als IPv6 AAAA-naamrecords en reageert met beide records wanneer de DNS-naam wordt opgezocht. De client kiest met welk adres (IPv4 of IPv6) hij wil communiceren. In plaats van of naast het gebruik van het DNS-naamlabel met het standaardachtervoegsel, kunt u de Azure DNS-service gebruiken om een DNS-naam met een aangepast achtervoegsel te configureren dat wordt omgezet naar het openbare IP-adres. Zie [Azure DNS gebruiken met een openbaar IP-adres van Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) voor meer informatie.|
   |Naam (alleen zichtbaar als u IP-versie van **beide**selecteert)|Ja, als u IP-versie van **beide** selecteert|De naam moet anders zijn dan de naam die u invoert voor de **voornaam** in deze lijst. Als u ervoor kiest om zowel een IPv4- als een IPv6-adres te maken, maakt de portal twee afzonderlijke openbare IP-adresbronnen, één met elke IP-adresversie die eraan is toegewezen.|
   |TOEWIJZING IP-adres (alleen zichtbaar als u IP-versie van **beide**selecteert)|Ja, als u IP-versie van **beide** selecteert|Dezelfde beperkingen als IP-adrestoewijzing hierboven|
   |Abonnement|Ja|Moet bestaan in hetzelfde [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) als de bron waaraan u de openbare IP's koppelt.|
   |Resourcegroep|Ja|Kan in dezelfde of andere [resourcegroep](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) bestaan als de resource waaraan u de ip-adressen van het openbaar koppelt.|
   |Locatie|Ja|Moet bestaan op dezelfde [locatie](https://azure.microsoft.com/regions), ook wel regio genoemd, als de bron waaraan u de openbare IP's koppelt.|
   |Beschikbaarheidszone| Nee | Deze instelling wordt alleen weergegeven als u een ondersteunde locatie selecteert. Zie [Overzicht beschikbaarheidszones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor een lijst met ondersteunde locaties. Als u de **BasissKU** hebt geselecteerd, wordt *Geen* automatisch voor u geselecteerd. Als u een specifieke zone wilt garanderen, u een specifieke zone selecteren. Beide keuzes zijn niet zone-overbodig. Als u **Standard** de standaardSKU: Zone-redundant hebt geselecteerd, wordt deze automatisch voor u geselecteerd en wordt uw gegevenspad bestand tegen zonefouten. Als u liever een specifieke zone garandeert, die niet bestand is tegen zonefalen, u een specifieke zone selecteren.

**Opdrachten**

Hoewel de portal de mogelijkheid biedt om twee openbare IP-adresbronnen te maken (een IPv4 en een IPv6), maken de volgende CLI- en PowerShell-opdrachten de ene bron met een adres voor de ene IP-versie of het andere. Als u twee openbare IP-adresbronnen wilt, één voor elke IP-versie, moet u de opdracht twee keer uitvoeren, waarbij verschillende namen en IP-versies voor de openbare IP-adresbronnen worden opgegeven.

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Instellingen weergeven, wijzigen voor of een openbaar IP-adres verwijderen

1. Typ in het vak met de tekst *Zoekbronnen* boven aan de *Azure-portal een openbaar ip-adres*. Wanneer **openbare IP-adressen** in de zoekresultaten worden weergegeven, selecteert u deze.
2. Selecteer de naam van het openbare IP-adres dat u wilt weergeven, instellingen wijzigen of verwijderen uit de lijst.
3. Voltooi een van de volgende opties, afhankelijk van of u het openbare IP-adres wilt bekijken, verwijderen of wijzigen.
   - **Weergave:** In de sectie **Overzicht** worden de belangrijkste instellingen voor het openbare IP-adres weergegeven, zoals de netwerkinterface waaraan het is gekoppeld (als het adres is gekoppeld aan een netwerkinterface). De portal geeft de versie van het adres (IPv4 of IPv6) niet weer. Als u de versie-informatie wilt weergeven, gebruikt u de opdracht PowerShell of CLI om het openbare IP-adres weer te geven. Als de IP-adresversie IPv6 is, wordt het toegewezen adres niet weergegeven door de portal, PowerShell of de CLI.
   - **Verwijderen:** als u het openbare IP-adres wilt verwijderen, selecteert u **Verwijderen** in de sectie **Overzicht.** Als het adres momenteel is gekoppeld aan een IP-configuratie, kan het niet worden verwijderd. Als het adres momenteel is gekoppeld aan een configuratie, selecteert u **Dissociaat** om het adres los te maken van de IP-configuratie.
   - **Wijzigen:** selecteer **Configuratie**. Wijzig de instellingen met behulp van de informatie in stap 4 van [Een openbaar IP-adres maken](#create-a-public-ip-address). Als u de toewijzing voor een IPv4-adres wilt wijzigen van statisch naar dynamisch, moet u eerst het openbare IPv4-adres distantiëren van de IP-configuratie waaraan het is gekoppeld. U vervolgens de toewijzingsmethode wijzigen in dynamisch en **selecteer Associate** om het IP-adres te koppelen aan dezelfde IP-configuratie, een andere configuratie, of u het gescheiden laten. Als u een openbaar IP-adres wilt scheiden, selecteert u in de sectie **Overzicht** de optie **Scheiden**.

   >[!WARNING]
   >Wanneer u de toewijzingsmethode wijzigt van statisch naar dynamisch, verliest u het IP-adres dat is toegewezen aan het openbare IP-adres. Terwijl de openbare DNS-servers van Azure een toewijzing tussen statische of dynamische adressen en een DNS-naamlabel (als u er een hebt gedefinieerd) in kaart brengen, kan een dynamisch IP-adres veranderen wanneer de virtuele machine wordt gestart nadat deze in de status gestopt (deallocated) is. Als u wilt voorkomen dat het adres wordt gewijzigd, wijst u een statisch IP-adres toe.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az-netwerk public-ip lijst](/cli/azure/network/public-ip#az-network-public-ip-list) om openbare IP-adressen, [az-netwerk public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) to show to show instellingen; [openbaar-ip-update van het AZ-netwerk](/cli/azure/network/public-ip#az-network-public-ip-update) bij te werken; [az-netwerk public-ip verwijderen](/cli/azure/network/public-ip#az-network-public-ip-delete) om te verwijderen|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om een openbaar IP-adresobject op te halen en de instellingen ervan te bekijken, [AzPublicIpAddress instellen](/powershell/module/az.network/set-azpublicipaddress) om instellingen bij te werken; [Verwijderen-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) te verwijderen|

## <a name="assign-a-public-ip-address"></a>Een openbaar IP-adres toewijzen

Meer informatie over het toewijzen van een openbaar IP-adres aan de volgende bronnen:

- Een [Windows-](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Linux-vm](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (bij het maken) of naar een [bestaande vm](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Internet-facing Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Site-to-site-verbinding met een Azure VPN-gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Virtual Machine Scale Set](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren op openbare IP-adressen, moet uw account worden toegewezen aan de [rol van netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rol waaraan de juiste acties in de volgende tabel zijn toegewezen:

| Actie                                                             | Name                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Een openbaar IP-adres lezen                                          |
| Microsoft.Network/publicIPAddresses/write                          | Een openbaar IP-adres maken of bijwerken                           |
| Microsoft.Network/publicIPAddresses/delete                         | Een openbaar IP-adres verwijderen                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Een openbaar IP-adres koppelen aan een resource                    |

## <a name="next-steps"></a>Volgende stappen

- Een openbaar IP-adres maken met [PowerShell-](powershell-samples.md) of [Azure CLI-voorbeeldscripts](cli-samples.md) of Azure [Resource Manager-sjablonen gebruiken](template-samples.md)
- [Azure-beleid](policy-samples.md) maken en toepassen voor openbare IP-adressen
