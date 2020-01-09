---
title: Veelgestelde vragen over Azure Firewall
description: Veelgestelde vragen over Azure Firewall. Een beheerde, Cloud service voor netwerk beveiliging die uw Azure Virtual Network-Resources beveiligt.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ac8ec9a86c388f41493d60d79a21db28fbf2aa95
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646208"
---
# <a name="azure-firewall-faq"></a>Veelgestelde vragen over Azure Firewall

## <a name="what-is-azure-firewall"></a>Wat is Azure Firewall?

Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Het is een volledig stateful firewall-as-a-service met ingebouwde hoge Beschik baarheid en een onbeperkte Cloud schaalbaarheid. U kunt beleid voor toepassings- en netwerkconnectiviteit centraal maken, afdwingen en registreren voor abonnementen en virtuele netwerken.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Welke mogelijkheden worden er in Azure Firewall ondersteund?

* Stateful firewall als een service
* Ingebouwde hoge beschikbaarheid met onbeperkte cloudschaalbaarheid
* Filteren op FQDN
* FQDN-tags
* Regels voor het filteren van netwerkverkeer
* Ondersteuning voor uitgaande SNAT
* Ondersteuning voor inkomende DNAT
* Toepassingen en beleids regels voor netwerk verbindingen centraal maken, afdwingen en registreren in azure-abonnementen en VNETs
* Volledig geïntegreerd met Azure Monitor voor registratie en analyses

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Wat is het typische implementatie model voor Azure Firewall?

U kunt Azure Firewall op elk virtueel netwerk implementeren, maar klanten implementeren dit doorgaans op een centraal virtueel netwerk en andere virtuele netwerken op de peer in een hub-en-spoke-model. U kunt vervolgens de standaard route van de gekoppelde virtuele netwerken instellen om naar dit virtuele netwerk met centrale firewall te verwijzen. Wereld wijde VNet-peering wordt ondersteund, maar dit wordt niet aanbevolen vanwege mogelijke problemen met de prestaties en latentie tussen regio's. Voor de beste prestaties moet u één firewall per regio implementeren.

Het voor deel van dit model is de mogelijkheid om centraal controle te uitoefenen op meerdere spoke-VNETs in verschillende abonnementen. Er zijn ook kosten besparingen, omdat u niet afzonderlijk een firewall in elk VNet hoeft te implementeren. De kosten besparingen moeten worden gemeten tegenover de kosten voor het koppelen van de peering op basis van de verkeers patronen van de klant.

## <a name="how-can-i-install-the-azure-firewall"></a>Hoe kan ik de Azure Firewall installeren?

U kunt Azure Firewall instellen met behulp van de Azure Portal, Power shell, REST API of door sjablonen te gebruiken. Zie [zelf studie: Azure firewall implementeren en configureren met behulp van de Azure Portal](tutorial-firewall-deploy-portal.md) voor stapsgewijze instructies.

## <a name="what-are-some-azure-firewall-concepts"></a>Wat zijn enkele Azure Firewall concepten?

Azure Firewall ondersteunt regels en regel verzamelingen. Een regel verzameling is een set regels die dezelfde volg orde en prioriteit hebben. Regel verzamelingen worden uitgevoerd op volg orde van prioriteit. Netwerk regel verzamelingen hebben een hogere prioriteit dan toepassings regel verzamelingen en alle regels worden beëindigd.

Er zijn drie typen regel verzamelingen:

* *Toepassings regels*: Configureer FQDN-namen (Fully Qualified Domain names) die toegankelijk zijn vanuit een subnet.
* *Netwerk regels*: Configureer regels die bron adressen, protocollen, doel poorten en doel adressen bevatten.
* *NAT-regels*: DNAT-regels configureren om binnenkomende verbindingen toe te staan.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Ondersteunt Azure Firewall het filteren van inkomend verkeer?

Azure Firewall ondersteunt filters voor inkomend en uitgaand verkeer. Inkomende beveiliging is voor niet-HTTP/S-protocollen. Bijvoorbeeld RDP-, SSH-en FTP-protocollen.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Welke logboek registratie en analyse services worden ondersteund door de Azure Firewall?

Azure Firewall is geïntegreerd met Azure Monitor voor het weer geven en analyseren van Firewall Logboeken. Logboeken kunnen worden verzonden naar Log Analytics, Azure Storage of Event Hubs. Ze kunnen worden geanalyseerd in Log Analytics of door verschillende hulpprogram ma's zoals Excel en Power BI. Zie voor meer informatie [zelf studie: Azure firewall logboeken bewaken](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Hoe werkt Azure Firewall anders dan bestaande services zoals Nva's in de Marketplace?

Azure Firewall is een eenvoudige firewall service die bepaalde klant scenario's kan oplossen. Er wordt naar verwachting een combi natie van Nva's en Azure Firewall van derden. Een goede samen werking is een kern prioriteit.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Wat is het verschil tussen Application Gateway WAF en Azure Firewall?

De Web Application firewall (WAF) is een functie van Application Gateway die gecentraliseerde inkomende beveiliging biedt voor uw webtoepassingen van veelvoorkomende aanvallen en beveiligings problemen. Azure Firewall biedt inkomende beveiliging voor niet-HTTP/S-protocollen (bijvoorbeeld RDP, SSH, FTP), uitgaande beveiliging op netwerk niveau voor alle poorten en protocollen en beveiliging op toepassings niveau voor uitgaande HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Wat is het verschil tussen netwerk beveiligings groepen (Nsg's) en Azure Firewall?

De Azure Firewall-service is een aanvulling op de functionaliteit van de netwerk beveiligings groep. Samen bieden ze een betere "ingrijpende" netwerk beveiliging. Netwerk beveiligings groepen bieden gedistribueerde filters voor het filteren van netwerk lagen om het verkeer te beperken tot bronnen in virtuele netwerken in elk abonnement. Azure Firewall is een volledig stateful, gecentraliseerd netwerk firewall as-a-service, die beveiliging op netwerk-en toepassings niveau biedt voor verschillende abonnementen en virtuele netwerken.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Worden netwerk beveiligings groepen (Nsg's) ondersteund op het subnet van de Azure Firewall?

Azure Firewall is een beheerde service met meerdere beveiligings lagen, waaronder platform beveiliging met NIC-niveau Nsg's (niet zichtbaar).  Nsg's op subnetniveau zijn niet vereist op het Azure Firewall subnet en zijn uitgeschakeld om te voor komen dat de service wordt onderbroken.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hoe kan ik Azure Firewall met mijn service-eind punten instellen?

Voor beveiligde toegang tot PaaS Services wordt service-eind punten aanbevolen. U kunt ervoor kiezen om service-eind punten in te scha kelen in het subnet Azure Firewall en ze uit te scha kelen in de verbonden spoke-netwerken. Op deze manier profiteert u van beide functies: service Endpoint Security en centrale logboek registratie voor al het verkeer.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Wat is de prijs voor Azure Firewall?

Zie [Azure firewall prijzen](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Hoe kan ik Azure Firewall stoppen en starten?

U kunt Azure PowerShell *toewijzings* -en *toewijzings* methoden gebruiken.

Bijvoorbeeld:

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> U moet een firewall en een openbaar IP-adres toewijzen aan de oorspronkelijke resource groep en dit abonnement.

## <a name="what-are-the-known-service-limits"></a>Wat zijn de bekende service limieten?

Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)voor Azure Firewall service limieten.

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Kan Azure Firewall in een hub virtueel netwerk netwerk verkeer door sturen en filteren tussen twee spoke-virtuele netwerken?

Ja, u kunt Azure Firewall in een hub-virtueel netwerk gebruiken om verkeer tussen twee spoke-netwerken te routeren en te filteren. Subnetten in elk van de spoke virtuele netwerken moeten UDR de Azure Firewall als standaard gateway voor dit scenario goed kunnen gebruiken.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Kan Azure Firewall netwerk verkeer tussen subnetten in hetzelfde virtuele netwerk of gekoppelde virtuele netwerken door sturen en filteren?

Ja. Het configureren van de Udr's voor het omleiden van verkeer tussen subnetten in hetzelfde VNET vereist echter extra aandacht. Wanneer het VNET-adres bereik als een doel voorvoegsel voor de UDR wordt gebruikt, stuurt dit ook alle verkeer van de ene machine naar een andere machine in hetzelfde subnet via de Azure Firewall-instantie. Als u dit wilt voor komen, moet u een route voor het subnet in de UDR toevoegen met het volgende hop-type **VNET**. Het beheer van deze routes kan omslachtig zijn en gevoelig voor fouten. De aanbevolen methode voor het segmenteren van interne netwerken is het gebruik van netwerk beveiligings groepen. hiervoor is geen Udr's vereist.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Azure Firewall uitgaande SNAT tussen particuliere netwerken?

Azure Firewall geen SNAT wanneer het doel-IP-adres een privé-IP-bereik is per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Als uw organisatie gebruikmaakt van een openbaar IP-adres bereik voor particuliere netwerken, Azure Firewall SNATs het verkeer naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Wordt geforceerde tunneling/keten van een virtueel netwerk apparaat ondersteund?

Geforceerde tunneling wordt momenteel niet ondersteund. Azure Firewall moet een rechtstreekse Internet verbinding hebben. Als uw AzureFirewallSubnet een standaard route naar uw on-premises netwerk via BGP leert, moet u dit overschrijven met een 0.0.0.0/0-UDR met de **NextHopType** -waarde ingesteld als **Internet** om directe Internet connectiviteit te onderhouden.

Als voor uw configuratie geforceerde tunneling naar een on-premises netwerk is vereist en u de doel-IP-voor voegsels voor uw Internet doelen kunt bepalen, kunt u deze bereiken met het on-premises netwerk als de volgende hop configureren via een door de gebruiker gedefinieerde route op de AzureFirewallSubnet. U kunt ook BGP gebruiken om deze routes te definiëren.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Zijn er beperkingen voor de resource groep van de firewall?

Ja. De firewall, het subnet, het VNet en het open bare IP-adres moeten zich in dezelfde resource groep bevinden.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Moet ik ook een bijbehorende netwerk regel configureren om dat verkeer toe te staan bij het configureren van DNAT voor binnenkomend netwerk verkeer?

Nee. NAT-regels voegen impliciet een bijbehorende netwerk regel toe om het vertaalde verkeer toe te staan. U kunt dit gedrag overschrijven door expliciet een verzameling netwerkregels toe te voegen met regels voor weigeren die overeenkomen met het omgezette verkeer. Zie [Verwerkingslogica voor Azure Firewall-regels](rule-processing.md) voor meer informatie over de verwerkingslogica voor Azure Firewall-regels.

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Hoe werken joker tekens in een toepassings regel doel-FQDN?

Als u * **. contoso.com**configureert, is *anyvalue*. contoso.com, maar niet contoso.com (het domein Apex) toegestaan. Als u het domein Apex wilt toestaan, moet u het expliciet configureren als een doel-FQDN.

## <a name="what-does-provisioning-state-failed-mean"></a>Wat betekent de *inrichtings status: mislukt* ?

Wanneer een configuratie wijziging wordt toegepast, probeert Azure Firewall alle onderliggende back-end-exemplaren bij te werken. In zeldzame gevallen kan een van deze backend-exemplaren niet worden bijgewerkt met de nieuwe configuratie en wordt het update proces gestopt met een mislukte inrichtings status. Uw Azure Firewall is nog steeds operationeel, maar de toegepaste configuratie kan een inconsistente status hebben, waarbij sommige instanties de vorige configuratie hebben, waarbij anderen de bijgewerkte regelset hebben. Als dit het geval is, kunt u proberen om de configuratie nog een keer bij te werken totdat de bewerking is geslaagd en uw firewall een *geslaagde* inrichtings status heeft.

### <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Hoe verwerkt Azure Firewall gepland onderhoud en niet-geplande fouten?
Azure Firewall bestaat uit verschillende back-end-knoop punten in een actief/actief-configuratie.  Voor elk gepland onderhoud hebben we verbindings verwerkings logica voor het op de juiste wijze bijwerken van knoop punten.  Updates worden gepland tijdens niet-kantoor uren voor elk van de Azure-regio's om het risico van onderbrekingen verder te beperken.  Voor ongeplande problemen instantiëren we een nieuw knoop punt om het knoop punt dat is mislukt te vervangen.  De verbinding met het nieuwe knoop punt wordt doorgaans binnen tien seconden na het tijdstip van de fout hersteld.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Is er een maximum aantal tekens voor een firewall naam?

Ja. Er is een limiet van 50 tekens voor de naam van een firewall.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Waarom is de subnetruimte van een/26 Azure Firewall vereist?

Azure Firewall moet meer exemplaren van virtuele machines inrichten terwijl deze worden geschaald. Een/26 adres ruimte zorgt ervoor dat er voldoende IP-adressen beschikbaar zijn voor de firewall om de schaal te verg Roten.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>Moet de grootte van het subnet van de firewall veranderen wanneer de service wordt geschaald?

Nee. Azure Firewall hebt geen subnet dat groter is dan/26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Hoe kan ik de door Voer van de firewall verhogen?

De aanvankelijke doorvoer capaciteit van Azure Firewall is 2,5-3 Gbps. Op dit moment is uitschalen alleen gebaseerd op het CPU-gebruik. In sommige gevallen kan een firewall met netwerk regels alleen worden geschaald om de door voer te verhogen omdat de netwerk regels het CPU-gebruik niet aanzienlijk beïnvloeden. Als u een hogere door Voer voor uw firewall nodig hebt, neemt u contact op met de ondersteuning om de oorspronkelijke doorvoer capaciteit van uw firewall te verhogen.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Hoe lang duurt het om Azure Firewall uit te schalen?

Het duurt momenteel vijf tot zeven minuten voordat Azure Firewall uitschalen. Als u bursts hebt die een sneller automatisch schalen vereisen, neemt u contact op met de ondersteuning om de oorspronkelijke doorvoer capaciteit van uw firewall te verg Roten.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Staat Azure Firewall standaard toegang tot Active Directory toe?

Nee. Azure Firewall blokkeert standaard Active Directory toegang. Configureer de AzureActiveDirectory-service label om toegang toe te staan. Zie [Azure Firewall-service Tags](service-tags.md)voor meer informatie.
