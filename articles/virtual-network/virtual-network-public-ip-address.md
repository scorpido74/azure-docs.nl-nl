---
title: Een openbaar IP-adres voor Azure maken, wijzigen of verwijderen | Microsoft Docs
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
ms.openlocfilehash: 5e71f30723826a18f10d4c8e1b9d34e356c9962b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114635"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Een openbaar IP-adres maken, wijzigen of verwijderen

Meer informatie over een openbaar IP-adres en hoe u er een kunt maken, wijzigen en verwijderen. Een openbaar IP-adres is een bron met eigen Configureer bare instellingen. U kunt als volgt een openbaar IP-adres toewijzen aan een Azure-resource die open bare IP-adressen ondersteunt:
- Inkomende communicatie van het internet naar de resource, zoals Azure Virtual Machines (VM), Azure-toepassing gateways, Azure load balancers, Azure VPN-gateways en anderen. U kunt nog steeds communiceren met sommige resources, zoals Vm's, van het Internet, als aan een virtuele machine geen openbaar IP-adres is toegewezen, mits de virtuele machine deel uitmaakt van een load balancer back-end-pool en aan de load balancer een openbaar IP-adres is toegewezen. Raadpleeg de documentatie voor de service om te bepalen of er een openbaar IP-adres kan worden toegewezen aan een resource voor een specifieke Azure-service of dat deze kan worden gecommuniceerd via het open bare IP-adres van een andere Azure-resource.
- Uitgaande verbinding met internet met behulp van een voorspelbaar IP-adres. Een virtuele machine kan bijvoorbeeld uitgaand verkeer naar Internet communiceren zonder dat hieraan een openbaar IP-adres is toegewezen, maar het adres is standaard het netwerk adres dat door Azure wordt vertaald naar een onvoorspelbaar openbaar adres. Door een openbaar IP-adres toe te wijzen aan een resource kunt u bepalen welk IP-adres wordt gebruikt voor de uitgaande verbinding. Het adres kan echter wel worden gewijzigd, afhankelijk van de gekozen toewijzings methode. Zie [een openbaar IP-adres maken](#create-a-public-ip-address)voor meer informatie. Zie [begrijpen van uitgaande verbindingen](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over uitgaande verbindingen van Azure-resources.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende taken uit voordat u de stappen in een van de secties van dit artikel uitvoert:

- Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proef account](https://azure.microsoft.com/free).
- Als u de portal gebruikt, opent u https://portal.azure.comen meldt u zich aan met uw Azure-account.
- Als u Power shell-opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/powershell)of Power shell uitvoeren vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelf studie is de Azure PowerShell module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u Azure-opdracht regel interface opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Voor deze zelf studie is de Azure CLI-versie 2.0.31 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal uitvoert, moet u ook `az login` uitvoeren om een verbinding te maken met Azure.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure met, moet worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [machtigingen](#permissions).

Open bare IP-adressen hebben een nominale belasting. Lees de pagina met [prijzen voor IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses) om de prijzen weer te geven.

## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

1. Selecteer boven aan de linkerbovenhoek van de Portal de optie **+ een resource maken**.
2. Geef het *open bare IP-adres* op in het vak *Zoeken in de Marketplace* . Wanneer het **open bare IP-adres** wordt weer gegeven in de zoek resultaten, selecteert u dit.
3. Selecteer onder **openbaar IP-adres** **maken**.
4. Typ of selecteer waarden voor de volgende instellingen onder **openbaar IP-adres maken**en selecteer vervolgens **maken**:

   |Instelling|Vereist?|Details|
   |---|---|---|
   |Naam|Ja|De naam moet uniek zijn binnen de resource groep die u selecteert.|
   |SKU|Ja|Alle open bare IP-adressen die zijn gemaakt voor de introductie van Sku's zijn de open bare IP-adressen van de **basis** -SKU. U kunt de SKU niet wijzigen nadat het open bare IP-adres is gemaakt. Een zelfstandige virtuele machine, virtuele machines binnen een beschikbaarheidsset of schaal sets voor virtuele machines kunnen gebruikmaken van basis-of standaard-Sku's. Het is niet toegestaan Sku's te mengen tussen virtuele machines binnen beschikbaarheids sets of schaal sets. **Basis** SKU: als u een openbaar IP-adres maakt in een regio die beschikbaarheids zones ondersteunt, wordt de instelling voor de **beschikbaarheids zone** standaard ingesteld op *geen* . Algemene open bare Ip's bieden geen ondersteuning voor beschikbaarheids zones. **Standaard** SKU: een openbaar IP-adres van een standaard-SKU kan worden gekoppeld aan een virtuele machine of een load balancer front-end. Als u een openbaar IP-adres maakt in een regio die beschikbaarheids zones ondersteunt, wordt de instelling voor de **beschikbaarheids zone** standaard ingesteld op *zone-redundant* . Zie de instelling **beschikbaarheids zone** voor meer informatie over beschikbaarheids zones. De standaard-SKU is vereist als u het adres koppelt aan een standaard load balancer. Zie [Azure Load Balancer Standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over standaard load balancers. Als u een openbaar IP-adres van een standaard-SKU toewijst aan een netwerkinterface van een virtuele machine, moet u het bedoelde verkeer expliciet toestaan met een [netwerkbeveiligingsgroep](security-overview.md#network-security-groups). Communicatie met de resource mislukt totdat u een netwerkbeveiligingsgroep maakt en koppelt en het gewenste verkeer expliciet toestaat.|
   |IP-versie|Ja| Selecteer IPv4 of IPv6. Hoewel open bare IPv4-adressen kunnen worden toegewezen aan verschillende Azure-resources, kan een openbaar IP-adres voor IPv6 alleen worden toegewezen aan een Internet gerichte load balancer. De load balancer kan het taak verdeling van IPv6-verkeer naar virtuele Azure-machines. Meer informatie over [het taak verdeling van IPv6-verkeer naar virtuele machines](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Als u de **standaard-SKU**hebt geselecteerd, hebt u geen optie om *IPv6*te selecteren. U kunt alleen een IPv4-adres maken wanneer u de **standaard-SKU**gebruikt.|
   |Toewijzing van IP-adres|Ja|**Dynamisch:** Dynamische adressen worden alleen toegewezen nadat een openbaar IP-adres aan een Azure-resource is gekoppeld en de resource voor de eerste keer wordt gestart. Dynamische adressen kunnen worden gewijzigd als ze zijn toegewezen aan een resource, zoals een virtuele machine, en de virtuele machine is gestopt (toewijzing ongedaan gemaakt) en vervolgens opnieuw gestart. Het adres blijft hetzelfde als een virtuele machine opnieuw wordt opgestart of gestopt (maar niet ongedaan gemaakt). Dynamische adressen worden vrijgegeven wanneer een open bare IP-adres bron wordt ontkoppeld van een bron waaraan deze is gekoppeld. **Statisch:** Statische adressen worden toegewezen wanneer een openbaar IP-adres wordt gemaakt. Statische adressen worden pas vrijgegeven nadat een open bare IP-adres resource is verwijderd. Als het adres niet aan een resource is gekoppeld, kunt u de toewijzings methode wijzigen nadat het adres is gemaakt. Als het adres aan een resource is gekoppeld, kunt u de toewijzings methode mogelijk niet wijzigen. Als u *IPv6* selecteert voor de **IP-versie**, is de toewijzings methode *dynamisch*. Als u *standaard* voor **SKU**selecteert, is de toewijzings methode *statisch*.|
   |Time-out voor inactiviteit (minuten)|Nee|Hoe lang duurt het voordat een TCP-of HTTP-verbinding is geopend, zonder dat er wordt gebruikgemaakt van clients om Keep-Alive-berichten te verzenden. Als u IPv6 selecteert voor de **IP-versie**, kan deze waarde niet worden gewijzigd. |
   |Label voor de DNS-naam|Nee|Moet uniek zijn binnen de Azure-locatie waar u de naam maakt in (voor alle abonnementen en alle klanten). Azure registreert automatisch de naam en het IP-adres in DNS zodat u verbinding kunt maken met een resource met de naam. Azure voegt een standaard-subnet, zoals *location.cloudapp.Azure.com* (waarbij locatie de locatie is die u hebt geselecteerd), toe aan de naam die u opgeeft, om de volledig gekwalificeerde DNS-naam te maken. Als u ervoor kiest beide adres versies te maken, wordt dezelfde DNS-naam toegewezen aan zowel IPv4-als IPv6-adressen. De standaard-DNS van Azure bevat IPv4 A-en IPv6 AAAA-naam records en reageert met beide records wanneer de DNS-naam wordt opgezocht. De client kiest voor welk adres (IPv4 of IPv6) moet worden gecommuniceerd. In plaats van of naast het gebruik van het DNS-naamlabel met het standaardachtervoegsel, kunt u de Azure DNS-service gebruiken om een DNS-naam met een aangepast achtervoegsel te configureren dat wordt omgezet naar het openbare IP-adres. Zie [Azure DNS gebruiken met een openbaar IP-adres van Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) voor meer informatie.|
   |Een IPv6-(of IPv4-) adres maken|Nee| Of IPv6 of IPv4 wordt weer gegeven, is afhankelijk van wat u selecteert voor de **IP-versie**. Als u bijvoorbeeld **IPv4** selecteert voor **IP-versie**, wordt **IPv6** hier weer gegeven. Als u *standaard* voor **SKU**selecteert, hebt u geen mogelijkheid om een IPv6-adres te maken.
   |Naam (alleen zichtbaar als u het selectie vakje **een IPv6 (of IPv4)-adres maken** hebt ingeschakeld)|Ja, als u het selectie vakje **een IPv6** (of IPv4) maken inschakelt.|De naam moet anders zijn dan de naam die u invoert voor de eerste **naam** in deze lijst. Als u ervoor kiest om zowel een IPv4-als een IPv6-adres te maken, worden er in de portal twee afzonderlijke open bare IP-adres bronnen gemaakt, waarvan elke toegewezen IP-adres versie hieraan is gekoppeld.|
   |Toewijzing van IP-adressen (alleen zichtbaar als u het selectie vakje **een IPv6 (of IPv4-adres maken** ) hebt ingeschakeld|Ja, als u het selectie vakje **een IPv6** (of IPv4) maken inschakelt.|Als het selectie vakje **een IPv4-adres maken**, kunt u een toewijzings methode selecteren. Als het selectie vakje **staat voor het maken van een IPv6-adres**, kunt u geen toewijzings methode selecteren, omdat deze **dynamisch**moet zijn.|
   |Abonnement|Ja|Moet zich in hetzelfde [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) bevinden als de resource waaraan u het open bare IP-adres wilt koppelen.|
   |Resourcegroep|Ja|Kan zich in dezelfde of verschillende [resource groepen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) bevinden als de resource waaraan u het open bare IP-adres wilt koppelen.|
   |Locatie|Ja|Moet zich op dezelfde [locatie](https://azure.microsoft.com/regions)bevinden, ook wel regio genoemd, als de resource waaraan u het open bare IP-adres wilt koppelen.|
   |Beschikbaarheidszone| Nee | Deze instelling wordt alleen weer gegeven als u een ondersteunde locatie selecteert. Zie [overzicht van beschikbaarheids zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor een lijst met ondersteunde locaties. Als u de **basis** -SKU hebt *geselecteerd, wordt er niet* automatisch voor u geselecteerd. Als u liever een specifieke zone wilt garanderen, kunt u een specifieke zone selecteren. Een van de keuzesen is niet zone-redundant. Als u de **Standard** -SKU hebt geselecteerd: zone-redundante wordt automatisch voor u geselecteerd en wordt het gegevenspad flexibeler voor zone storingen. Als u de voor keur geeft aan een specifieke zone, wat niet kan leiden tot een zone storing, kunt u een specifieke zone selecteren.

**Opdrachten**

Hoewel de Portal de mogelijkheid biedt om twee open bare IP-adres bronnen te maken (één IPv4 en één IPv6), maken de volgende CLI-en Power shell-opdrachten één resource met een adres voor één of meer IP-versies. Als u twee open bare IP-adres bronnen wilt, één voor elke IP-versie, moet u de opdracht twee keer uitvoeren, waarbij u verschillende namen en versies opgeeft voor de open bare IP-adres bronnen.

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Een openbaar IP-adres weer geven, wijzigen of verwijderen

1. Typ *openbaar IP-adres*in het vak met de tekst *zoeken naar resources* bovenaan het Azure Portal. Wanneer **open bare IP-adressen** worden weer gegeven in de zoek resultaten, selecteert u deze.
2. Selecteer de naam van het open bare IP-adres dat u wilt weer geven, wijzig de instellingen voor of verwijder deze in de lijst.
3. Voer een van de volgende opties uit, afhankelijk van of u het open bare IP-adres wilt weer geven, verwijderen of wijzigen.
   - **Weer gave**: in de sectie **overzicht** ziet u de belangrijkste instellingen voor het open bare IP-adres, zoals de netwerk interface waaraan deze is gekoppeld (als het adres is gekoppeld aan een netwerk interface). In de portal wordt de versie van het adres (IPv4 of IPv6) niet weer gegeven. Als u de versie-informatie wilt weer geven, gebruikt u de Power shell-of CLI-opdracht om het open bare IP-adres weer te geven. Als de IP-adres versie IPv6 is, wordt het toegewezen adres niet weer gegeven in de portal, Power shell of de CLI.
   - **Verwijderen**: als u het open bare IP-adres wilt verwijderen, selecteert u **verwijderen** in het gedeelte **overzicht** . Als het adres momenteel is gekoppeld aan een IP-configuratie, kan het niet worden verwijderd. Als het adres momenteel is gekoppeld aan een configuratie, selecteert u **loskoppelen** om het adres te ontkoppelen van de IP-configuratie.
   - **Wijzigen**: Selecteer **configuratie**. Wijzig de instellingen met behulp van de informatie in stap 4 van [een openbaar IP-adres maken](#create-a-public-ip-address). Als u de toewijzing voor een IPv4-adres van statisch naar dynamisch wilt wijzigen, moet u eerst het open bare IPv4-adres ontkoppelen van de IP-configuratie waaraan het is gekoppeld. U kunt vervolgens de toewijzings methode wijzigen in dynamisch en **koppelen** selecteren om het IP-adres te koppelen aan dezelfde IP-configuratie, een andere configuratie of u kunt de koppeling verbreken. Als u een openbaar IP-adres wilt ontkoppelen **, selecteert u**in de sectie **overzicht** de optie ontkoppelen.

   >[!WARNING]
   >Wanneer u de toewijzings methode van statisch naar dynamisch wijzigt, verliest u het IP-adres dat is toegewezen aan het open bare IP-adres. Terwijl de Azure open bare DNS-servers een toewijzing onderhouden tussen statische of dynamische adressen en een DNS-naam label (als u deze hebt gedefinieerd), kan een dynamisch IP-adres worden gewijzigd wanneer de virtuele machine wordt gestart nadat de status gestopt (toewijzing ongedaan gemaakt) is. Wijs een statisch IP-adres toe om te voor komen dat het adres wordt gewijzigd.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[AZ Network Public-IP List](/cli/azure/network/public-ip#az-network-public-ip-list) to list Public IP Addresss, [AZ Network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) to show settings; [AZ Network Public-IP update](/cli/azure/network/public-ip#az-network-public-ip-update) to update; [AZ Network Public-IP delete](/cli/azure/network/public-ip#az-network-public-ip-delete) om te verwijderen|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) voor het ophalen van een openbaar IP-adres object en het weer geven van de instellingen, [set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) om instellingen bij te werken; [Verwijder-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) om te verwijderen|

## <a name="assign-a-public-ip-address"></a>Een openbaar IP-adres toewijzen

Meer informatie over het toewijzen van een openbaar IP-adres aan de volgende bronnen:

- Een virtuele [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machine (bij het maken) of een [bestaande virtuele machine](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Internet gerichte Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Site-naar-site-verbinding met behulp van een Azure-VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Schaalset voor virtuele Azure-machines](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren op open bare IP-adressen, moet uw account worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rol waaraan de juiste acties in de volgende tabel zijn toegewezen:

| Actie                                                             | Naam                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Een openbaar IP-adres lezen                                          |
| Microsoft.Network/publicIPAddresses/write                          | Een openbaar IP-adres maken of bijwerken                           |
| Microsoft.Network/publicIPAddresses/delete                         | Een openbaar IP-adres verwijderen                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Een openbaar IP-adres aan een resource koppelen                    |

## <a name="next-steps"></a>Volgende stappen

- Een openbaar IP-adres maken met behulp van [Power shell](powershell-samples.md) of [Azure cli](cli-samples.md) -voorbeeld scripts of met behulp van Azure [Resource Manager-sjablonen](template-samples.md)
- [Azure-beleid](policy-samples.md) maken en Toep assen voor open bare IP-adressen
