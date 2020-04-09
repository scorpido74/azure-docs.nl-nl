---
title: Wat is Azure Firewall?
description: Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 04/08/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 60d936d9c2785e4723cdc09e55927fe13af8d8a1
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892305"
---
# <a name="what-is-azure-firewall"></a>Wat is Azure Firewall?

![ICSA-certificering](media/overview/icsa-cert-firewall-small.png)

Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Het is een volledig stateful firewall als een service met ingebouwde hoge beschikbaarheid en onbeperkte cloudschaalbaarheid.

![Firewalloverzicht](media/overview/firewall-threat.png)

U kunt beleid voor toepassings- en netwerkconnectiviteit centraal maken, afdwingen en registreren voor abonnementen en virtuele netwerken. Azure Firewall maakt gebruik van een statisch openbaar IP-adres voor uw virtuele-netwerkresources zodat externe firewalls verkeer dat afkomstig is van uw virtuele netwerk kunnen identificeren.  De service is volledig geïntegreerd met Azure Monitor voor registratie en analyses.

Azure Firewall biedt de volgende functies:

## <a name="built-in-high-availability"></a>Ingebouwde hoge beschikbaarheid

Hoge beschikbaarheid is ingebouwd, dus er zijn geen extra load balancers nodig en er is niets dat u hoeft te configureren.

## <a name="availability-zones"></a>Beschikbaarheidszones

Azure Firewall kan tijdens de implementatie worden geconfigureerd om meerdere beschikbaarheidszones te omvatten voor een grotere beschikbaarheid. Met beschikbaarheidszones neemt uw beschikbaarheid toe tot 99,99% uptime. Zie de SLA (Azure Firewall [Service Level Agreement) voor](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)meer informatie. De SLA van 99,99% wordt aangeboden wanneer twee of meer beschikbaarheidszones worden geselecteerd.

U Azure Firewall ook koppelen aan een specifieke zone alleen om nabijheidsredenen, met behulp van de servicestandaard 99,95% SLA.

Er zijn geen extra kosten verbonden aan een firewall die is geïmplementeerd in een beschikbaarheidszone. Er zijn echter extra kosten verbonden aan inkomende en uitgaande gegevensoverdrachten die zijn gekoppeld aan beschikbaarheidszones. Zie [Details van de bandbreedteprijzen](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie.

Azure Firewall Availability Zones zijn beschikbaar in regio's die beschikbaarheidszones ondersteunen. Zie [Wat zijn beschikbaarheidszones in Azure voor](../availability-zones/az-overview.md#services-support-by-region) meer informatie?

> [!NOTE]
> Beschikbaarheidszones kunnen alleen worden geconfigureerd tijdens de implementatie. U een bestaande firewall niet zo configureren als beschikbaarheidszones.

Zie [Wat zijn beschikbaarheidszones in Azure voor](../availability-zones/az-overview.md) meer informatie over beschikbaarheidszones?

## <a name="unrestricted-cloud-scalability"></a>Onbeperkte cloudschaalbaarheid

U kunt Azure Firewall omhoog schalen zoveel als nodig is om te voldoen aan veranderende netwerkverkeersstromen, zodat u geen budget hoeft te reserveren voor het drukste verkeer.

## <a name="application-fqdn-filtering-rules"></a>Regels voor het filteren van de FQDN van toepassingen

U uitgaand HTTP/S-verkeer of Azure SQL-verkeer (voorbeeld) beperken tot een opgegeven lijst met volledig gekwalificeerde domeinnamen (FQDN) inclusief wildcards. Deze functie vereist geen SSL-beëindiging.

## <a name="network-traffic-filtering-rules"></a>Regels voor het filteren van netwerkverkeer

U kunt de netwerkfilterregels *toestaan* of *weigeren* centraal maken op IP-adres van bron en doel, poort en protocol. Azure Firewall is volledig stateful, wat betekent dat het legitieme pakketten voor verschillende soorten verbindingen kan onderscheiden. Regels worden afgedwongen en vastgelegd voor meerdere abonnementen en virtuele netwerken.

## <a name="fqdn-tags"></a>FQDN-tags

FQDN-tags maken het gemakkelijk voor u om bekend Azure-servicenetwerkverkeer via uw firewall toe te staan. Stel dat u Windows Update-netwerkverkeer wilt toestaan in de firewall. U maakt een toepassingsregel die de Windows Update-tag bevat. Het netwerkverkeer van Windows Update kan nu door uw firewall.

## <a name="service-tags"></a>Servicetags

Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels die het maken van beveiligingsregel vereenvoudigt. U geen eigen servicetag maken en u ook niet opgeven welke IP-adressen in een tag zijn opgenomen. Microsoft beheert de adresvoorvoegsels die de servicetag omvat en werkt de servicetag automatisch bij wanneer adressen veranderen.

## <a name="threat-intelligence"></a>Informatie over bedreigingen

Filtering op basis van bedreigingsinformatie kan worden ingeschakeld voor uw firewall om verkeer van/naar bekende kwaadaardige IP-adressen en domeinen te waarschuwen en te weigeren. De IP-adressen en domeinen zijn afkomstig uit de Microsoft Threat Intelligence-feed.

## <a name="outbound-snat-support"></a>Ondersteuning voor uitgaande SNAT

Alle uitgaande IP-adressen van virtueel netwerkverkeer worden geconverteerd naar de openbare IP van Azure Firewall (Source Network Address Translation). U kunt verkeer dat afkomstig is uit uw virtuele netwerk naar externe internetbestemmingen identificeren en toestaan. Azure Firewall is niet SNAT wanneer het doel-IP een privé-IP-bereik is per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Als uw organisatie een openbaar IP-adresbereik gebruikt voor privénetwerken, wordt Het verkeer naar een van de privé-IP-adressen van firewalls in AzureFirewallSubnet weergegeven. U Azure Firewall configureren om **niet** uw openbare IP-adresbereik van SNAT te maken. Zie [Azure Firewall SNAT private IP-adresbereiken voor](snat-private-range.md)meer informatie.

## <a name="inbound-dnat-support"></a>Ondersteuning voor inkomende DNAT

Binnenkomend internetnetwerkverkeer naar uw firewall openbaar IP-adres wordt vertaald (Destination Network Address Translation) en gefilterd naar de privé IP-adressen op uw virtuele netwerken.

## <a name="multiple-public-ip-addresses"></a>Meerdere openbare IP-adressen

U meerdere openbare IP-adressen (tot 100) koppelen aan uw firewall.

Dit maakt de volgende scenario's mogelijk:

- **DNAT** - U meerdere standaardpoortexemplaren vertalen naar uw backendservers. Als u bijvoorbeeld twee openbare IP-adressen hebt, u TCP-poort 3389 (RDP) voor beide IP-adressen vertalen.
- **SNAT** - Er zijn extra poorten beschikbaar voor uitgaande SNAT-verbindingen, waardoor de kans op snat-poortuitputting wordt verminderd. Op dit moment selecteert Azure Firewall willekeurig het openbare IP-adres van de bron dat moet worden gebruikt voor een verbinding. Als u downstreamfiltering op uw netwerk hebt, moet u alle openbare IP-adressen toestaan die aan uw firewall zijn gekoppeld.

## <a name="azure-monitor-logging"></a>Logboekregistratie van Azure Monitor

Alle gebeurtenissen zijn geïntegreerd met Azure Monitor, zodat u logboeken kunt archiveren in een opslagaccount, gebeurtenissen kunt streamen naar uw Event Hub of deze kunt verzenden naar Azure Monitor-logboeken.

## <a name="certifications"></a>Certificeringen

Azure Firewall is Pci (Payment Card Industry), Service Organization Controls (SOC), International Organization for Standardization (ISO) en ICSA Labs compliant. Zie [Azure Firewall-nalevingscertificeringen](compliance-certifications.md)voor meer informatie.


## <a name="known-issues"></a>Bekende problemen

Azure Firewall heeft de volgende bekende problemen:

|Probleem  |Beschrijving  |Oplossing  |
|---------|---------|---------|
Netwerkfilterregels voor niet-TCP/UDP-protocollen (bijvoorbeeld ICMP) werken niet voor internetverkeer|Netwerkfilterregels voor niet-TCP/UDP-protocollen werken niet met SNAT naar uw openbare IP-adres. Niet-TCP/UDP-protocollen worden ondersteund tussen spoke-subnetten en VNets.|Azure Firewall maakt gebruik van de standaardversie van Standard Load Balancer, [die momenteel geen ondersteuning biedt voor SNAT voor IP-protocollen](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). We verkennen opties om dit scenario te ondersteunen in een toekomstige release.|
|Ontbrekende PowerShell- en CLI-ondersteuning voor ICMP|Azure PowerShell en CLI ondersteunen ICMP niet als een geldig protocol in netwerkregels.|Het is nog steeds mogelijk om ICMP als protocol te gebruiken via de portal en de REST API. We werken eraan om binnenkort ICMP toe te voegen aan PowerShell en CLI.|
|FQDN-tags vereisen instelling van een protocol: poort|Toepassingsregels met FQDN-tags vereisen poort: protocoldefinitie.|U kunt **https** gebruiken als de waarde voor poort:protocol. We werken eraan om dit veld optioneel te maken wanneer FQDN-tags worden gebruikt.|
|Een firewall verplaatsen naar een andere brongroep of abonnement wordt niet ondersteund|Een firewall verplaatsen naar een andere brongroep of -abonnement wordt niet ondersteund.|Ondersteuning van deze functionaliteit staat op de planning. Om een firewall naar een andere resourcegroep of ander abonnement verplaatsen, moet u het huidige exemplaar verwijderen en deze vervolgens opnieuw maken in de nieuwe resourcegroep of het nieuwe abonnement.|
|Waarschuwingen voor bedreigingsinformatie kunnen worden gemaskeerd|Netwerkregels met bestemming 80/443 voor uitgaande filtering maskers threat intelligence alerts wanneer geconfigureerd om alleen te waarschuwen modus.|Maak uitgaande filtering voor 80/443 met behulp van toepassingsregels. Of wijzig de modus bedreigingsinformatie in **Waarschuwen en Weigeren.**|
|Azure Firewall gebruikt Azure DNS alleen voor naamomzetting|Azure Firewall lost alleen FQDN's op met Azure DNS. Een aangepaste DNS-server wordt niet ondersteund. Er is geen invloed op DNS-resolutie op andere subnetten.|We werken eraan om deze beperking te versoepelen.|
|Azure Firewall SNAT/DNAT werkt niet voor privé-IP-bestemmingen|Azure Firewall SNAT/DNAT-ondersteuning is beperkt tot internetuitgangen/binnendringen. SNAT/DNAT werkt momenteel niet voor privé IP-bestemmingen. Bijvoorbeeld, sprak met het woord.|Dit is een huidige beperking.|
|Kan eerste openbare IP-configuratie niet verwijderen|Elk openbaar IP-adres van Azure Firewall is toegewezen aan een *IP-configuratie.*  De eerste IP-configuratie wordt toegewezen tijdens de firewall-implementatie en bevat meestal ook een verwijzing naar het firewallsubnet (tenzij expliciet anders geconfigureerd via een sjabloonimplementatie). U deze IP-configuratie niet verwijderen omdat deze de firewall zou de-toewijzen. U nog steeds het openbare IP-adres wijzigen of verwijderen dat aan deze IP-configuratie is gekoppeld als de firewall ten minste één ander openbaar IP-adres beschikbaar heeft om te gebruiken.|Dit is standaard.|
|Beschikbaarheidszones kunnen alleen worden geconfigureerd tijdens de implementatie.|Beschikbaarheidszones kunnen alleen worden geconfigureerd tijdens de implementatie. U beschikbaarheidszones niet configureren nadat een firewall is geïmplementeerd.|Dit is standaard.|
|SNAT op binnenkomende verbindingen|Naast DNAT zijn verbindingen via het openbare IP-adres van de firewall (binnenkomend) gekoppeld aan een van de firewall private IP's. Deze eis vandaag (ook voor Actieve/Actieve NVA's) om symmetrische routing te garanderen.|Als u de oorspronkelijke bron voor HTTP/S wilt behouden, u [XFF-headers](https://en.wikipedia.org/wiki/X-Forwarded-For) gebruiken. Gebruik bijvoorbeeld een service zoals [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) of Azure Application [Gateway](../application-gateway/rewrite-http-headers.md) voor de firewall. U WAF ook toevoegen als onderdeel van Azure Front Door en chain aan de firewall.
|ONDERSTEUNING voor SQL FQDN-filtering alleen in de proxymodus (poort 1433)|Voor Azure SQL Database, Azure SQL Data Warehouse en Azure SQL Managed Instance:<br><br>Tijdens de preview wordt SQL FQDN-filtering alleen in proxy-modus ondersteund (poort 1433).<br><br>Voor Azure SQL IaaS:<br><br>Als u niet-standaardpoorten gebruikt, u deze poorten opgeven in de toepassingsregels.|Voor SQL in omleidingsmodus (de standaardverbinding als verbinding vanuit Azure) u in plaats daarvan toegang filteren met behulp van de SQL-servicetag als onderdeel van Azure Firewall-netwerkregels.
|Uitgaand verkeer op TCP-poort 25 is niet toegestaan| Uitgaande SMTP-verbindingen die TCP-poort 25 gebruiken, worden geblokkeerd. Poort 25 wordt voornamelijk gebruikt voor niet-geverifieerde e-mailbezorging. Dit is het standaardplatformgedrag voor virtuele machines. Zie meer problemen [met uitgaande SMTP-connectiviteit in Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md)voor meer informatie. In tegenstelling tot virtuele machines is het momenteel echter niet mogelijk om deze functionaliteit in te schakelen op Azure Firewall.|Volg de aanbevolen methode om e-mail te verzenden zoals gedocumenteerd in het artikel over probleemoplossing voor SMTP. Of sluit de virtuele machine uit die uitgaande SMTP-toegang nodig heeft van uw standaardroute naar de firewall. Configureer in plaats daarvan uitgaande toegang rechtstreeks naar het internet.
|Actieve FTP wordt niet ondersteund|Active FTP is uitgeschakeld op Azure Firewall om te beschermen tegen FTP-bounceaanvallen met de opdracht FTP-poort.|U in plaats daarvan Passieve FTP gebruiken. U moet nog steeds expliciet TCP-poorten 20 en 21 op de firewall openen.
|SNAT-poortgebruiksstatistiek toont 0%|De azure firewall SNAT-poortgebruiksstatistiek kan het gebruik van 0% weergeven, zelfs wanneer SNAT-poorten worden gebruikt. In dit geval geeft het gebruik van de statistiek als onderdeel van de statistiek firewallstatus een onjuist resultaat.|Dit probleem is opgelost en de uitrol naar de productie is gericht voor mei 2020. In sommige gevallen lost firewall-herschikking het probleem op, maar het is niet consistent. Als tussenoplossing gebruikt u alleen de status van de firewall om te zoeken naar *status=gedegradeerd*, niet voor *status=ongezond*. Port uitputting zal worden weergegeven als *gedegradeerd*. *Niet gezond* is gereserveerd voor toekomstig gebruik wanneer de meer statistieken zijn die van invloed zijn op de firewallstatus.
|DNAT wordt niet ondersteund met Forced Tunneling ingeschakeld|Firewalls die zijn geïmplementeerd met Geforceerde tunneling ingeschakeld, kunnen geen inkomende toegang vanaf het internet ondersteunen vanwege asymmetrische routering.|Dit is door het ontwerp vanwege asymmetrische routing. Het retourpad voor binnenkomende verbindingen gaat via de on-premises firewall, die de verbinding niet heeft zien tot stand zijn gebracht.
|Uitgaande passieve FTP werkt niet voor firewalls met meerdere openbare IP-adressen.|Passive FTP legt verschillende verbindingen voor besturingen en datakanalen. Wanneer een firewall met meerdere openbare IP-adressen uitgaande gegevens verzendt, selecteert deze willekeurig een van de openbare IP-adressen voor het bron-IP-adres. FTP mislukt wanneer gegevens en controlekanalen verschillende bron-IP-adressen gebruiken.|Er is een expliciete SNAT-configuratie gepland. Overweeg in de tussentijd om in deze situatie één IP-adres te gebruiken.|
|Metriek NetworkRuleHit mist een protocoldimensie|Met de metname ApplicationRuleHit kan op filtering gebaseerd protocol worden gefilterd, maar deze mogelijkheid ontbreekt in de bijbehorende NetworkRuleHit-statistiek.|Een oplossing wordt onderzocht.|
|Configuratie-updates kunnen gemiddeld vijf minuten duren.|Een Azure Firewall-configuratie-update kan gemiddeld drie tot vijf minuten duren en parallelle updates worden niet ondersteund.|Een oplossing wordt onderzocht.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Azure Firewall implementeren en configureren met Azure Portal](tutorial-firewall-deploy-portal.md)
- [Azure Firewall implementeren met behulp van een sjabloon](deploy-template.md)
- [Een Azure Firewall-testomgeving maken](scripts/sample-create-firewall-test.md)
