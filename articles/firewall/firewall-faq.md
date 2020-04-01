---
title: Veelgestelde vragen over Azure Firewall
description: Veelgestelde vragen voor Azure Firewall. Een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-bronnen beschermt.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 45276884d59ac8d1d876e2225ac02bb51c3f74fc
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437733"
---
# <a name="azure-firewall-faq"></a>Veelgestelde vragen over Azure Firewall

## <a name="what-is-azure-firewall"></a>Wat is Azure Firewall?

Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Het is een volledig stateful firewall-as-a-service met ingebouwde hoge beschikbaarheid en onbeperkte schaalbaarheid van de cloud. U kunt beleid voor toepassings- en netwerkconnectiviteit centraal maken, afdwingen en registreren voor abonnementen en virtuele netwerken.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Welke mogelijkheden worden ondersteund in Azure Firewall?

* Stateful firewall as a service
* Ingebouwde hoge beschikbaarheid met onbeperkte schaalbaarheid van de cloud
* Filteren op FQDN
* FQDN-tags
* Regels voor het filteren van netwerkverkeer
* Ondersteuning voor uitgaande SNAT
* Ondersteuning voor inkomende DNAT
* Toepassings- en netwerkconnectiviteitsbeleid centraal maken, afdwingen en logboeken maken voor Azure-abonnementen en VNET's
* Volledig geïntegreerd met Azure Monitor voor logboekregistratie en analyse

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Wat is het typische implementatiemodel voor Azure Firewall?

U Azure Firewall implementeren op elk virtueel netwerk, maar klanten implementeren het doorgaans op een centraal virtueel netwerk en zien andere virtuele netwerken ernaar in een hub-and-spoke-model. U vervolgens de standaardroute van de peered virtuele netwerken instellen om naar dit centrale virtuele firewallnetwerk te wijzen. Global VNet-peering wordt ondersteund, maar wordt niet aanbevolen vanwege potentiële prestatie- en latentieproblemen in verschillende regio's. Voor de beste prestaties implementeert u één firewall per regio.

Het voordeel van dit model is de mogelijkheid om centraal controle uit te oefenen op meerdere spaakvnET's over verschillende abonnementen. Er zijn ook kostenbesparingen omdat u geen firewall in elke VNet afzonderlijk hoeft te implementeren. De kostenbesparingen moeten worden gemeten ten opzichte van de associate peering kosten op basis van de klant verkeer patronen.

## <a name="how-can-i-install-the-azure-firewall"></a>Hoe kan ik de Azure Firewall installeren?

U Azure Firewall instellen met behulp van de Azure-portal, PowerShell, REST API of met behulp van sjablonen. Zie [Zelfstudie: Azure Firewall implementeren en configureren met behulp van de Azure-portal](tutorial-firewall-deploy-portal.md) voor stapsgewijze instructies.

## <a name="what-are-some-azure-firewall-concepts"></a>Wat zijn enkele Azure Firewall-concepten?

Azure Firewall ondersteunt regels en regelverzamelingen. Een regelverzameling is een set regels die dezelfde volgorde en prioriteit hebben. Regelverzamelingen worden uitgevoerd in volgorde van hun prioriteit. Verzamelingen van netwerkregels hebben een hogere prioriteit dan verzamelingen van toepassingsregels en alle regels worden afgemaakt.

Er zijn drie soorten regelverzamelingen:

* *Toepassingsregels*: Configureer volledig gekwalificeerde domeinnamen (FQDN's) die toegankelijk zijn via een subnet.
* *Netwerkregels:* configureer regels die bronadressen, protocollen, doelpoorten en bestemmingsadressen bevatten.
* *NAT-regels*: Configureer DNAT-regels om binnenkomende internetverbindingen toe te staan.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Ondersteunt Azure Firewall inkomende verkeersfiltering?

Azure Firewall ondersteunt inkomende en uitgaande filtering. Binnenkomende beveiliging wordt meestal gebruikt voor niet-HTTP/S-protocollen. Bijvoorbeeld RDP-, SSH- en FTP-protocollen. Gebruik een firewall voor webtoepassingen, zoals Azure [Web Application Firewall (WAF)](../web-application-firewall/overview.md)voor de beste binnenkomende HTTP/S-beveiliging.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Welke logboek- en analyseservices worden ondersteund door de Azure Firewall?

Azure Firewall is geïntegreerd met Azure Monitor voor het bekijken en analyseren van firewalllogboeken. Logboeken kunnen worden verzonden naar Log Analytics, Azure Storage of Event Hubs. Ze kunnen worden geanalyseerd in Log Analytics of door verschillende tools zoals Excel en Power BI. Zie [Zelfstudie: Azure Firewall-logboeken controleren](tutorial-diagnostics.md)voor meer informatie.

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Hoe werkt Azure Firewall anders dan bestaande services zoals NVA's in de marketplace?

Azure Firewall is een eenvoudige firewallservice die bepaalde klantscenario's kan aanpakken. De verwachting is dat u een mix van NVA's van derden en Azure Firewall zult hebben. Beter samenwerken is een kernprioriteit.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Wat is het verschil tussen Application Gateway WAF en Azure Firewall?

De Web Application Firewall (WAF) is een functie van Application Gateway die gecentraliseerde inkomende bescherming van uw webapplicaties biedt tegen veelvoorkomende exploits en kwetsbaarheden. Azure Firewall biedt binnenkomende bescherming voor niet-HTTP/S-protocollen (bijvoorbeeld RDP, SSH, FTP), uitgaande bescherming op netwerkniveau voor alle poorten en protocollen en bescherming op toepassingsniveau voor uitgaande HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Wat is het verschil tussen Network Security Groups (NSGs) en Azure Firewall?

De Azure Firewall-service is een aanvulling op de functionaliteit van de netwerkbeveiligingsgroep. Samen zorgen ze voor een betere "defensie-diepgaande" netwerkbeveiliging. Netwerkbeveiligingsgroepen bieden gedistribueerde netwerklaagverkeersfiltering om verkeer te beperken tot bronnen binnen virtuele netwerken in elk abonnement. Azure Firewall is een volledig stateful, gecentraliseerde netwerkfirewall as-a-service, die bescherming op netwerk- en toepassingsniveau biedt voor verschillende abonnementen en virtuele netwerken.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Worden Network Security Groups (NSG's) ondersteund op het Subnet Azure Firewall?

Azure Firewall is een beheerde service met meerdere beveiligingslagen, waaronder platformbeveiliging met NIC-niveau NSG's (niet zichtbaar).  NSG's op subnetniveau zijn niet vereist op het subnet Azure Firewall en zijn uitgeschakeld om ervoor te zorgen dat de service niet wordt onderbroken.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hoe stel ik Azure Firewall in met mijn serviceeindpunten?

Voor veilige toegang tot PaaS-services raden we serviceeindpunten aan. U ervoor kiezen serviceeindpunten in te schakelen in het subnet Azure Firewall en deze uit te schakelen op de verbonden virtuele netwerken die zijn gesproken. Op deze manier profiteert u van beide functies: service endpoint beveiliging en centrale logging voor al het verkeer.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Wat is de prijs voor Azure Firewall?

Zie [Azure Firewall-prijzen](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Hoe kan ik Azure Firewall stoppen en starten?

U Azure PowerShell gebruiken om methoden *te detoewijzen* en *toe te wijzen.*

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
> U moet een firewall en openbaar IP opnieuw toewijzen aan de oorspronkelijke brongroep en -abonnement.

## <a name="what-are-the-known-service-limits"></a>Wat zijn de bekende servicelimieten?

Zie [Azure-abonnements- en servicelimieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)voor Azure Firewall-servicelimieten.

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Kan Azure Firewall in een virtueel hubnetwerk netwerkverkeer doorsturen en filteren tussen twee gesproken virtuele netwerken?

Ja, u Azure Firewall gebruiken in een virtueel hubnetwerk om verkeer tussen twee gesproken virtuele netwerken te routeren en te filteren. Subnetten in elk van de gesproken virtuele netwerken moeten een UDR hebben die naar de Azure Firewall wijst als standaardgateway om dit scenario goed te laten werken.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Kan Azure Firewall netwerkverkeer doorsturen en filteren tussen subnetten in hetzelfde virtuele netwerk of virtuele netwerken met één van collega's?

Ja. Het configureren van de UDR's om verkeer tussen subnetten in dezelfde VNET om te leiden vereist echter extra aandacht. Hoewel het gebruik van het VNET-adresbereik als doelvoorvoegsel voor de UDR voldoende is, leidt dit ook al het verkeer van de ene machine naar de andere in hetzelfde subnet via het Azure Firewall-exemplaar. Om dit te voorkomen, neemt u een route voor het subnet in de UDR op met een volgend hoptype **VNET**. Het beheren van deze routes kan omslachtig en foutgevoelig zijn. De aanbevolen methode voor interne netwerksegmentatie is het gebruik van netwerkbeveiligingsgroepen, waarvoor geen UDR's nodig zijn.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Is Azure Firewall outbound SNAT tussen private netwerken?

Azure Firewall is niet SNAT wanneer het doel-IP-adres een privé-IP-bereik is per [IANA RFC 1918.](https://tools.ietf.org/html/rfc1918) Als uw organisatie een openbaar IP-adresbereik gebruikt voor privénetwerken, wordt het verkeer naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet gesind. U Azure Firewall configureren om **niet** uw openbare IP-adresbereik van SNAT te maken. Zie [Azure Firewall SNAT private IP-adresbereiken voor](snat-private-range.md)meer informatie.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Wordt gedwongen tunneling/chaining aan een Network Virtual Appliance ondersteund?

Gedwongen tunneling wordt ondersteund. Zie [Azure Firewall forced tunneling (preview) voor](forced-tunneling.md)meer informatie. 

Azure Firewall moet een directe internetverbinding hebben. Als uw AzureFirewallSubnet via BGP een standaardroute naar uw on-premises netwerk leert, moet u dit overschrijven met een 0.0.0.0/0 UDR met de **NextHopType-waarde** ingesteld als **internet** om directe internetverbinding te behouden.

Als uw configuratie gedwongen tunneling naar een on-premises netwerk vereist en u de doel-IP-voorvoegsels voor uw internetbestemmingen bepalen, u deze bereiken configureren met het on-premises netwerk als de volgende hop via een door de gebruiker gedefinieerde route op het AzureFirewallSubnet. U ook BGP gebruiken om deze routes te definiëren.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Zijn er beperkingen voor firewallbronnengroep?

Ja. De firewall, VNet en het openbare IP-adres moeten allemaal in dezelfde brongroep zitten.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Moet ik bij het configureren van DNAT voor binnenkomend internetnetwerkverkeer ook een bijbehorende netwerkregel configureren om dat verkeer toe te staan?

Nee. NAT-regels voegen impliciet een bijbehorende netwerkregel toe om het vertaalde verkeer toe te staan. U kunt dit gedrag overschrijven door expliciet een verzameling netwerkregels toe te voegen met regels voor weigeren die overeenkomen met het omgezette verkeer. Zie [Verwerkingslogica voor Azure Firewall-regels](rule-processing.md) voor meer informatie over de verwerkingslogica voor Azure Firewall-regels.

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Hoe werken jokertekens in een toepassingsregel doel FQDN?

Als u ***.contoso.com**configureert, staat het *elke waarde*toe .contoso.com, maar niet contoso.com (de domeintop). Als u de domeintop wilt toestaan, moet u deze expliciet configureren als een doel-FQDN.

## <a name="what-does-provisioning-state-failed-mean"></a>Wat betekent *provisioning: Mislukt?*

Wanneer een configuratiewijziging wordt toegepast, probeert Azure Firewall alle onderliggende backend-exemplaren bij te werken. In zeldzame gevallen kan een van deze backend-exemplaren niet worden bijgewerkt met de nieuwe configuratie en wordt het updateproces gestopt met een mislukte inrichtingsstatus. Uw Azure Firewall is nog steeds operationeel, maar de toegepaste configuratie kan in een inconsistente status zijn, waarbij sommige gevallen de vorige configuratie hebben waarin andere de bijgewerkte regel hebben ingesteld. Als dit gebeurt, probeert u uw configuratie nog een keer bij te werken totdat de bewerking is geslaagd en uw firewall in *de status Geslaagde* inrichting is.

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Hoe gaat Azure Firewall om met gepland onderhoud en ongeplande fouten?
Azure Firewall bestaat uit verschillende backendnodes in een actief actieve configuratie.  Voor elk gepland onderhoud hebben we de logica voor het aftappen van verbindingen om knooppunten op een elegante manier bij te werken.  Updates worden gepland tijdens niet-kantooruren voor elk van de Azure-regio's om het risico op verstoring verder te beperken.  Voor ongeplande problemen, we instantiate een nieuw knooppunt ter vervanging van de mislukte knooppunt.  De verbinding met het nieuwe knooppunt wordt doorgaans hersteld binnen 10 seconden vanaf het moment van de storing.

## <a name="how-does-connection-draining-work"></a>Hoe werkt het aftappen van verbindingen?

Voor elk gepland onderhoud wordt backend-knooppunten van de verbinding op een elegante manier bijgewerkt. Azure Firewall wacht 90 seconden tot bestaande verbindingen zijn gesloten. Indien nodig kunnen clients automatisch de verbinding met een ander backend-knooppunt herstellen.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Is er een tekenlimiet voor een firewallnaam?

Ja. Er is een limiet van 50 tekens voor een firewallnaam.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Waarom heeft Azure Firewall een subnetgrootte van /26 nodig?

Azure Firewall moet meer virtuele machine-exemplaren inrichten omdat deze wordt geschaald. Een /26-adresruimte zorgt ervoor dat de firewall voldoende IP-adressen beschikbaar heeft om de schaling te accommoderen.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>Moet de subnetgrootte van de firewall worden gewijzigd naarmate de service wordt geschaald?

Nee. Azure Firewall heeft geen subnet nodig dat groter is dan /26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Hoe kan ik de doorvoer van mijn firewall verhogen?

De initiële doorvoercapaciteit van Azure Firewall is 2,5 - 3 Gbps en wordt geschaald naar 30 Gbps. Het schaalt uit op basis van CPU-gebruik en doorvoer. Neem contact op met ondersteuning om de doorvoercapaciteit van uw firewall te vergroten.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Hoe lang duurt het voordat Azure Firewall is uitgeschaald?

Het duurt vijf tot zeven minuten voordat Azure Firewall is uitgeschaald. Neem contact op met ondersteuning om de initiële doorvoercapaciteit van uw firewall te vergroten als u uitbarstingen hebt die een snellere automatische schaal vereisen.

Bij het testen van de automatische firewall moet rekening worden gehouden met de volgende punten:

- De single TCP-stroomprestaties zijn beperkt tot 1,4 Gbps. Een prestatietest moet dus meerdere TCP-stromen vaststellen.
- Prestatietools moeten voortdurend nieuwe verbindingen voor hen tot stand brengen om verbinding te maken met de opgeschaalde backend firewall-exemplaren. Als de test eenmaal bij het begin verbindingen tot verbindingen maakt, worden deze alleen verbonden met de oorspronkelijke backend-exemplaren. Hoewel de firewall wordt geschaald, ziet u geen hogere prestaties omdat de verbindingen zijn gekoppeld aan de eerste exemplaren.


## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Biedt Azure Firewall standaard toegang tot Active Directory?

Nee. Azure Firewall blokkeert standaard active directory-toegang. Als u toegang wilt verlenen, configureert u de AzureActiveDirectory-servicetag. Zie [Azure Firewall-servicetags](service-tags.md)voor meer informatie .

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Kan ik een FQDN of een IP-adres uitsluiten van Azure Firewall Threat Intelligence-gebaseerde filtering?

Ja, u Azure PowerShell gebruiken om dit te doen:

```azurepowershell
# Add a Threat Intelligence Whitelist to an Existing Azure Firewall

## Create the Whitelist with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist.FQDNs = @("fqdn1", "fqdn2", …)
$fw.ThreatIntelWhitelist.IpAddress = @("ip1", "ip2", …)

Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Waarom kunnen een TCP-ping en vergelijkbare hulpprogramma's met succes verbinding maken met een doel-FQDN, zelfs als er geen regel op Azure Firewall dat verkeer toestaat?

Een TCP-ping maakt niet echt verbinding met de doel-FQDN. Dit gebeurt omdat de transparante proxy van Azure Firewall luistert op poort 80/443 voor uitgaand verkeer. De TCP-ping maakt een verbinding met de firewall, die vervolgens het pakket laat vallen en de verbinding registreert. Dit gedrag heeft geen impact op de beveiliging. Echter, om verwarring te voorkomen onderzoeken we mogelijke wijzigingen in dit gedrag. 