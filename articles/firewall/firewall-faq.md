---
title: Veelgestelde vragen over Azure Firewall
description: Veelgestelde vragen over Azure Firewall. Een beheerde, Cloud service voor netwerk beveiliging die uw Azure Virtual Network-Resources beveiligt.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: victorh
ms.openlocfilehash: 75435155ba1dad798d301006a30a5d5b6e96226a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88611174"
---
# <a name="azure-firewall-faq"></a>Veelgestelde vragen over Azure Firewall

## <a name="what-is-azure-firewall"></a>Wat is Azure Firewall?

Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Het is een volledig stateful firewall-as-a-service met ingebouwde hoge Beschik baarheid en een onbeperkte Cloud schaalbaarheid. U kunt beleid voor toepassings- en netwerkconnectiviteit centraal maken, afdwingen en registreren voor abonnementen en virtuele netwerken.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Welke mogelijkheden worden er in Azure Firewall ondersteund?

Zie [Azure Firewall-functies](features.md) voor meer informatie over Azure Firewall-functies.

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Wat is het typische implementatie model voor Azure Firewall?

U kunt Azure Firewall op elk virtueel netwerk implementeren, maar klanten implementeren dit doorgaans op een centraal virtueel netwerk en peeren er andere virtuele netwerken op in een hub-en-spoke-model. U kunt vervolgens de standaard route van de gekoppelde virtuele netwerken instellen om naar dit virtuele netwerk met centrale firewall te verwijzen. Wereld wijde VNet-peering wordt ondersteund, maar dit wordt niet aanbevolen vanwege mogelijke problemen met de prestaties en latentie tussen regio's. Implementeer één firewall per regio voor de beste prestaties.

Het voordeel van dit model is de mogelijkheid om centraal controle te uitoefenen op meerdere spoke-VNET's in verschillende abonnementen. Er zijn ook kosten besparingen, omdat u niet afzonderlijk een firewall in elk VNet hoeft te implementeren. De kostenbesparingen moeten worden berekend ten opzichte van de bijbehorende peeringkosten op basis van de verkeerspatronen van de klant.

## <a name="how-can-i-install-the-azure-firewall"></a>Hoe kan ik de Azure Firewall installeren?

U kunt Azure Firewall instellen met behulp van de Azure Portal, Power shell, REST API of door sjablonen te gebruiken. Zie [zelf studie: Azure firewall implementeren en configureren met behulp van de Azure Portal](tutorial-firewall-deploy-portal.md) voor stapsgewijze instructies.

## <a name="what-are-some-azure-firewall-concepts"></a>Wat zijn enkele Azure Firewall concepten?

Azure Firewall ondersteunt regels en regel verzamelingen. Een regel verzameling is een set regels die dezelfde volg orde en prioriteit hebben. Regel verzamelingen worden uitgevoerd op volg orde van prioriteit. Netwerk regel verzamelingen hebben een hogere prioriteit dan toepassings regel verzamelingen en alle regels worden beëindigd.

Er zijn drie typen regel verzamelingen:

* *Toepassings regels*: Configureer FQDN-namen (Fully Qualified Domain names) die toegankelijk zijn vanuit een subnet.
* *Netwerk regels*: Configureer regels die bron adressen, protocollen, doel poorten en doel adressen bevatten.
* *NAT-regels*: DNAT-regels configureren om binnenkomende Internet verbindingen toe te staan.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Ondersteunt Azure Firewall het filteren van inkomend verkeer?

Azure Firewall ondersteunt filters voor inkomend en uitgaand verkeer. Inkomende beveiliging wordt doorgaans gebruikt voor niet-HTTP/S-protocollen. Bijvoorbeeld RDP-, SSH-en FTP-protocollen. Gebruik een Web Application Firewall zoals [Azure Web Application firewall (WAF)](../web-application-firewall/overview.md)voor de beste beveiliging via HTTP/S.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Welke logboek registratie en analyse services worden ondersteund door de Azure Firewall?

Azure Firewall is geïntegreerd met Azure Monitor voor het weer geven en analyseren van Firewall Logboeken. Logboeken kunnen worden verzonden naar Log Analytics, Azure Storage of Event Hubs. Ze kunnen worden geanalyseerd in Log Analytics of door verschillende hulpprogram ma's zoals Excel en Power BI. Zie voor meer informatie [zelf studie: Azure firewall logboeken bewaken](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Hoe werkt Azure Firewall anders dan bestaande services zoals Nva's in de Marketplace?

Azure Firewall is een eenvoudige firewall service die bepaalde klant scenario's kan oplossen. Er wordt naar verwachting een combi natie van Nva's en Azure Firewall van derden. Een goede samen werking is een kern prioriteit.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Wat is het verschil tussen Application Gateway WAF en Azure Firewall?

De Web Application firewall (WAF) is een functie van Application Gateway die gecentraliseerde inkomende beveiliging biedt voor uw webtoepassingen van veelvoorkomende aanvallen en beveiligings problemen. Azure Firewall biedt inkomende beveiliging voor niet-HTTP/S-protocollen (bijvoorbeeld RDP, SSH, FTP), uitgaande beveiliging op netwerk niveau voor alle poorten en protocollen en beveiliging op toepassings niveau voor uitgaande HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Wat is het verschil tussen netwerk beveiligings groepen (Nsg's) en Azure Firewall?

De Azure Firewall-service is een aanvulling op de functionaliteit van de netwerk beveiligings groep. Samen bieden ze een betere "ingrijpende" netwerk beveiliging. Netwerkbeveiligingsgroepen bieden gedistribueerde filtering voor netwerklagen om het verkeer te beperken tot resources in virtuele netwerken in elk abonnement. Azure Firewall is een volledig stateful, gecentraliseerd netwerk firewall as-a-service, die beveiliging op netwerk-en toepassings niveau biedt voor verschillende abonnementen en virtuele netwerken.

## <a name="are-network-security-groups-nsgs-supported-on-the-azurefirewallsubnet"></a>Worden netwerk beveiligings groepen (Nsg's) ondersteund op de AzureFirewallSubnet?

Azure Firewall is een beheerde service met meerdere beveiligings lagen, waaronder platform beveiliging met NIC-niveau Nsg's (niet zichtbaar).  Nsg's op subnetniveau zijn niet vereist voor de AzureFirewallSubnet en zijn uitgeschakeld om te voor komen dat de service wordt onderbroken.

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
$publicip1 = Get-AzPublicIpAddress -Name "Public IP1 Name" -ResourceGroupName "RG Name"
$publicip2 = Get-AzPublicIpAddress -Name "Public IP2 Name" -ResourceGroupName "RG Name"
$azfw.Allocate($vnet,@($publicip1,$publicip2))

Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> U moet een firewall en een openbaar IP-adres toewijzen aan de oorspronkelijke resource groep en dit abonnement.

## <a name="what-are-the-known-service-limits"></a>Wat zijn de bekende service limieten?

Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)voor Azure Firewall service limieten.

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Kan Azure Firewall in een hub virtueel netwerk netwerk verkeer door sturen en filteren tussen twee spoke-virtuele netwerken?

Ja, u kunt Azure Firewall in een hub-virtueel netwerk gebruiken om verkeer tussen twee spoke-netwerken te routeren en te filteren. Subnetten in elk van de spoke-virtuele netwerken moeten een UDR hebben die naar het Azure Firewall wijst als standaard gateway, zodat dit scenario goed werkt.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Kan Azure Firewall netwerk verkeer tussen subnetten in hetzelfde virtuele netwerk of gekoppelde virtuele netwerken door sturen en filteren?

Ja. Het configureren van de Udr's voor het omleiden van verkeer tussen subnetten in hetzelfde VNET vereist echter extra aandacht. Wanneer het VNET-adres bereik als een doel voorvoegsel voor de UDR wordt gebruikt, stuurt dit ook alle verkeer van de ene machine naar een andere machine in hetzelfde subnet via de Azure Firewall-instantie. Als u dit wilt voor komen, moet u een route voor het subnet in de UDR toevoegen met het volgende hop-type **VNET**. Het beheer van deze routes kan omslachtig zijn en gevoelig voor fouten. De aanbevolen methode voor het segmenteren van interne netwerken is het gebruik van netwerk beveiligings groepen. hiervoor is geen Udr's vereist.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Azure Firewall uitgaande SNAT tussen particuliere netwerken?

Azure Firewall geen SNAT wanneer het doel-IP-adres een privé-IP-bereik is per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Als uw organisatie gebruikmaakt van een openbaar IP-adres bereik voor particuliere netwerken, Azure Firewall SNATs het verkeer naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet. U kunt Azure Firewall configureren om SNAT **niet** in te schakelen voor uw openbare IP-adresbereik. Raadpleeg [Azure Firewall SNAT voor privé-IP-adresbereiken](snat-private-range.md) voor meer informatie.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Wordt geforceerde tunneling/keten van een virtueel netwerk apparaat ondersteund?

Geforceerde tunneling wordt ondersteund bij het maken van een nieuwe firewall. U kunt een bestaande firewall niet configureren voor geforceerde tunneling. Zie [Geforceerde tunneling van Azure Firewall](forced-tunneling.md) voor meer informatie.

Azure Firewall moet een directe verbinding met internet hebben. Als uw AzureFirewallSubnet een standaardroute naar uw on-premises netwerk via BGP leert, moet u deze overschrijven met een UDR van 0.0.0.0/0 met de waarde **NextHopType** ingesteld op **Internet** om directe verbinding met internet te houden.

Als voor uw configuratie geforceerde tunneling naar een on-premises netwerk is vereist en u de doel-IP-voor voegsels voor uw Internet doelen kunt bepalen, kunt u deze bereiken met het on-premises netwerk als de volgende hop configureren via een door de gebruiker gedefinieerde route op het AzureFirewallSubnet. U kunt ook BGP gebruiken om deze routes te definiëren.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Zijn er beperkingen voor de resource groep van de firewall?

Ja. De firewall, het VNet en het open bare IP-adres moeten zich in dezelfde resource groep bestaan.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Moet ik ook een bijbehorende netwerk regel configureren om dat verkeer toe te staan bij het configureren van DNAT voor binnenkomend Internet netwerk verkeer?

Nee. NAT-regels voegen impliciet een bijbehorende netwerk regel toe om het vertaalde verkeer toe te staan. U kunt dit gedrag overschrijven door expliciet een verzameling netwerkregels toe te voegen met regels voor weigeren die overeenkomen met het omgezette verkeer. Zie [Verwerkingslogica voor Azure Firewall-regels](rule-processing.md) voor meer informatie over de verwerkingslogica voor Azure Firewall-regels.

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Hoe werken joker tekens in een toepassings regel doel-FQDN?

Joker tekens kunnen momenteel alleen worden gebruikt aan de linkerkant van de FQDN. Bijvoorbeeld ***. contoso.com** en ***contoso.com**.

Als u ***. contoso.com**configureert, is *anyvalue*. contoso.com, maar niet contoso.com (het domein Apex) toegestaan. Als u het domein Apex wilt toestaan, moet u het expliciet configureren als een doel-FQDN.

## <a name="what-does-provisioning-state-failed-mean"></a>Wat betekent de *inrichtings status: mislukt* ?

Wanneer er een configuratiewijziging wordt toegepast, probeert Azure Firewall alle onderliggende back-endexemplaren bij te werken. In zeldzame gevallen kan een van deze backend-exemplaren niet worden bijgewerkt met de nieuwe configuratie en wordt het update proces gestopt met een mislukte inrichtings status. Uw Azure Firewall is nog steeds operationeel, maar de toegepaste configuratie kan inconsistent zijn, waarbij sommige exemplaren de vorige configuratie hebben en andere de bijgewerkte regelset hebben. Als dit het geval is, kunt u proberen om de configuratie nog een keer bij te werken totdat de bewerking is geslaagd en uw firewall een *geslaagde* inrichtings status heeft.

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Hoe verwerkt Azure Firewall gepland onderhoud en niet-geplande fouten?
Azure Firewall bestaat uit verschillende back-end-knoop punten in een actief/actief-configuratie.  Voor elk gepland onderhoud hebben we verbindings verwerkings logica voor het op de juiste wijze bijwerken van knoop punten.  Updates worden gepland tijdens niet-kantoor uren voor elk van de Azure-regio's om het risico van onderbrekingen verder te beperken.  Voor ongeplande problemen instantiëren we een nieuw knoop punt om het knoop punt dat is mislukt te vervangen.  De verbinding met het nieuwe knoop punt wordt doorgaans binnen tien seconden na het tijdstip van de fout hersteld.

## <a name="how-does-connection-draining-work"></a>Hoe werkt de verwerkings stop voor verbindingen?

Voor elk gepland onderhoud worden back-verwerkings logica op de juiste wijze bijgewerkt. Azure Firewall wordt 90 seconden gewacht voordat bestaande verbindingen worden gesloten. Zo nodig kunnen clients automatisch verbinding maken met een ander back-end-knoop punt.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Is er een maximum aantal tekens voor een firewall naam?

Ja. Er is een limiet van 50 tekens voor de naam van een firewall.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Waarom is de subnetruimte van een/26 Azure Firewall vereist?

Azure Firewall moet meer exemplaren van virtuele machines inrichten terwijl deze worden geschaald. Een/26 adres ruimte zorgt ervoor dat er voldoende IP-adressen beschikbaar zijn voor de firewall om de schaal te verg Roten.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>Moet de grootte van het subnet van de firewall veranderen wanneer de service wordt geschaald?

Nee. Azure Firewall hebt geen subnet dat groter is dan/26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Hoe kan ik de door Voer van de firewall verhogen?

De aanvankelijke doorvoer capaciteit van Azure Firewall is 2,5-3 Gbps en wordt geschaald naar 30 Gbps. De schaal wordt automatisch aangepast op basis van het CPU-gebruik en de door voer.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Hoe lang duurt het om Azure Firewall uit te schalen?

Azure Firewall wordt geleidelijk geschaald wanneer de gemiddelde door Voer of het CPU-verbruik 60% is. Een standaard implementatie heeft een maximale door Voer van ongeveer 2,5-3 Gbps en begint met schalen wanneer het 60% van het aantal bereikt. Uitschalen neemt vijf tot zeven minuten in beslag. 

Bij het testen van de prestaties moet u ervoor zorgen dat u ten minste 10 tot 15 minuten test en nieuwe verbindingen start om te profiteren van de nieuwe firewall knooppunten.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Staat Azure Firewall standaard toegang tot Active Directory toe?

Nee. Azure Firewall blokkeert standaard Active Directory toegang. Configureer de AzureActiveDirectory-service label om toegang toe te staan. Zie [Azure Firewall-service Tags](service-tags.md)voor meer informatie.

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Kan ik een FQDN of IP-adres uitsluiten van Azure Firewall op bedreigingen gebaseerd filteren?

Ja, u kunt Azure PowerShell gebruiken om het te doen:

```azurepowershell
# Add a Threat Intelligence allow list to an Existing Azure Firewall

## Create the allow list with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name $firewallname -ResourceGroupName $RG
$fw.ThreatIntelWhitelist.IpAddresses = @($fw.ThreatIntelWhitelist.IpAddresses + $ipaddresses)
$fw.ThreatIntelWhitelist.fqdns = @($fw.ThreatIntelWhitelist.fqdns + $fqdns)


Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Waarom kan een TCP-ping en soort gelijke hulpprogram ma's verbinding maken met een doel-FQDN, zelfs wanneer er geen regel op Azure Firewall dat verkeer toestaat?

Een TCP-ping maakt geen verbinding met de doel-FQDN. Dit gebeurt omdat de transparante proxy van Azure Firewall luistert op poort 80/443 voor uitgaand verkeer. De TCP ping brengt een verbinding tot stand met de firewall, waarna het pakket wordt neergezet en de verbinding wordt geregistreerd. Dit gedrag heeft geen invloed op de beveiliging. Om Verwar ring te voor komen, kunnen we echter de mogelijke wijzigingen van dit gedrag onderzoeken.

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>Gelden er limieten voor het aantal IP-adressen dat door IP-groepen wordt ondersteund?

Ja. Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) voor meer informatie

## <a name="can-i-move-an-ip-group-to-another-resource-group"></a>Kan ik een IP-groep verplaatsen naar een andere resource groep?

Nee. het verplaatsen van een IP-groep naar een andere resource groep wordt momenteel niet ondersteund.

## <a name="what-is-the-tcp-idle-timeout-for-azure-firewall"></a>Wat is de time-out voor inactiviteit van TCP voor Azure Firewall?

Een standaard gedrag van een netwerk firewall is om ervoor te zorgen dat TCP-verbindingen in stand worden gehouden en ze onmiddellijk sluiten als er geen activiteit is. Azure Firewall TCP-time-out voor inactiviteit is vier minuten. Deze instelling kan niet worden geconfigureerd. Als een periode van inactiviteit langer is dan de time-outwaarde, is er geen garantie dat de TCP-of HTTP-sessie wordt onderhouden. Een veelvoorkomende procedure is het gebruik van een TCP-Keep-Alive. Deze procedure houdt de verbinding gedurende een langere periode actief. Zie de [.net-voor beelden](https://docs.microsoft.com/dotnet/api/system.net.servicepoint.settcpkeepalive?redirectedfrom=MSDN&view=netcore-3.1#System_Net_ServicePoint_SetTcpKeepAlive_System_Boolean_System_Int32_System_Int32_)voor meer informatie.

## <a name="can-i-deploy-azure-firewall-without-a-public-ip-address"></a>Kan ik Azure Firewall implementeren zonder een openbaar IP-adres?

Nee, momenteel moet u Azure Firewall implementeren met een openbaar IP-adres.

## <a name="where-does-azure-firewall-store-customer-data"></a>Waar worden klant gegevens Azure Firewall opgeslagen?

Azure Firewall verplaatst of opslaat klant gegevens niet uit de regio waarin deze zijn geïmplementeerd.
