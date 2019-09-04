---
title: Wat is Azure Firewall?
description: Meer informatie over Azure Firewall-functies.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 09/4/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: c685b2314d15e431ccac3470fd337ca92697e1a5
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241168"
---
# <a name="what-is-azure-firewall"></a>Wat is Azure Firewall?

Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Het is een volledig stateful firewall als een service met ingebouwde hoge Beschik baarheid en een onbeperkte schaal baarheid van de Cloud.

![Firewalloverzicht](media/overview/firewall-threat.png)

U kunt beleid voor toepassings- en netwerkconnectiviteit centraal maken, afdwingen en registreren voor abonnementen en virtuele netwerken. Azure Firewall maakt gebruik van een statisch openbaar IP-adres voor uw virtuele-netwerkresources zodat externe firewalls verkeer dat afkomstig is van uw virtuele netwerk kunnen identificeren.  De service is volledig geïntegreerd met Azure Monitor voor registratie en analyses.

Azure Firewall biedt de volgende functies:

## <a name="built-in-high-availability"></a>Ingebouwde hoge beschikbaarheid

Hoge Beschik baarheid is ingebouwd, zodat er geen extra load balancers zijn vereist en er niets hoeft te worden geconfigureerd.

## <a name="availability-zones"></a>Beschikbaarheidszones

Azure Firewall kunnen tijdens de implementatie worden geconfigureerd om meerdere Beschikbaarheidszones voor een hogere Beschik baarheid te beslaan. Met Beschikbaarheidszones wordt uw Beschik baarheid verhoogd tot 99,99% uptime. Zie de Azure Firewall- [Service Level Agreement (Sla)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)voor meer informatie. De SLA voor de uptime van 99,99% wordt aangeboden wanneer er twee of meer Beschikbaarheidszones zijn geselecteerd.

U kunt Azure Firewall ook koppelen aan een specifieke zone, net om redenen voor nabijheid, met behulp van Service Standard 99,95% SLA.

Er zijn geen extra kosten verbonden aan een firewall die is geïmplementeerd in een beschikbaarheids zone. Er zijn echter extra kosten voor inkomende en uitgaande gegevens overdrachten die zijn gekoppeld aan Beschikbaarheidszones. Zie [prijs informatie voor band breedte](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie.

Azure Firewall Beschikbaarheidszones zijn beschikbaar in regio's die ondersteuning bieden voor Beschikbaarheidszones. Zie [Wat zijn Beschikbaarheidszones in azure?](../availability-zones/az-overview.md#services-support-by-region) voor meer informatie.

> [!NOTE]
> Beschikbaarheidszones kan alleen worden geconfigureerd tijdens de implementatie. U kunt een bestaande firewall niet configureren om Beschikbaarheidszones op te kunnen bevatten.

Zie [Wat zijn Beschikbaarheidszones in azure?](../availability-zones/az-overview.md) voor meer informatie over Beschikbaarheidszones.

## <a name="unrestricted-cloud-scalability"></a>Onbeperkte cloudschaalbaarheid

U kunt Azure Firewall omhoog schalen zoveel als nodig is om te voldoen aan veranderende netwerkverkeersstromen, zodat u geen budget hoeft te reserveren voor het drukste verkeer.

## <a name="application-fqdn-filtering-rules"></a>Regels voor het filteren van de FQDN van toepassingen

U kunt uitgaande HTTP/S-verkeer of Azure SQL-verkeer (preview) beperken tot een opgegeven lijst met FQDN-namen (FULLy Qualified Domain names), inclusief joker tekens. Voor deze functie is geen SSL-beëindiging vereist.

## <a name="network-traffic-filtering-rules"></a>Regels voor het filteren van netwerkverkeer

U kunt de netwerkfilterregels *toestaan* of *weigeren* centraal maken op IP-adres van bron en doel, poort en protocol. Azure Firewall is volledig stateful, wat betekent dat het legitieme pakketten voor verschillende soorten verbindingen kan onderscheiden. Regels worden afgedwongen en vastgelegd voor meerdere abonnementen en virtuele netwerken.

## <a name="fqdn-tags"></a>FQDN-tags

Met FQDN-tags kunt u eenvoudig bekend netwerkverkeer voor Azure-services toestaan in uw firewall. Stel dat u Windows Update-netwerkverkeer wilt toestaan in de firewall. U maakt een toepassingsregel die de Windows Update-tag bevat. Het netwerkverkeer van Windows Update kan nu door uw firewall.

## <a name="service-tags"></a>Servicetags

Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels die het maken van beveiligingsregel vereenvoudigt. U kunt geen eigen servicetag maken en opgeven welke IP-adressen zijn opgenomen in een tag. Microsoft beheert de adresvoorvoegsels die de servicetag omvat en werkt de servicetag automatisch bij wanneer adressen veranderen.

## <a name="threat-intelligence"></a>Bedreigingsinformatie

Filteren op basis van bedreigingsinformatie kan voor uw firewall worden ingeschakeld voor waarschuwingen over of het weigeren van verkeer van en naar bekende kwaadaardige IP-adressen en domeinen. De IP-adressen en domeinen zijn afkomstig uit de feed Bedreigingsinformatie van Microsoft.

## <a name="outbound-snat-support"></a>Ondersteuning voor uitgaande SNAT

Alle uitgaande IP-adressen van virtueel netwerkverkeer worden geconverteerd naar de openbare IP van Azure Firewall (Source Network Address Translation). U kunt verkeer dat afkomstig is uit uw virtuele netwerk naar externe internetbestemmingen identificeren en toestaan. Azure Firewall geen SNAT wanneer de doel-IP een privé-IP-bereik is per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Als uw organisatie gebruikmaakt van een openbaar IP-adres bereik voor particuliere netwerken, verkrijgt Azure Firewall het verkeer naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet.

## <a name="inbound-dnat-support"></a>Ondersteuning voor inkomende DNAT

Het inkomende netwerkverkeer op het openbare IP-adres van de firewall wordt omgezet (Destination Network Address Translation) en gefilterd op het privé-IP-adres in uw virtuele netwerken.

## <a name="multiple-public-ip-addresses"></a>Meerdere open bare IP-adressen

> [!IMPORTANT]
> Azure Firewall met meerdere open bare IP-adressen is beschikbaar via de Azure Portal, Azure PowerShell, Azure CLI, REST en sjablonen.


U kunt meerdere open bare IP-adressen (Maxi maal 100) koppelen aan uw firewall.

Hiermee worden de volgende scenario's ingeschakeld:

- **DNAT** : u kunt meerdere exemplaren van de standaard poort naar uw back-endservers omzetten. Als u bijvoorbeeld twee open bare IP-adressen hebt, kunt u TCP-poort 3389 (RDP) voor beide IP-adressen omzetten.
- **SNAT** : er zijn extra poorten beschikbaar voor uitgaande SNAT-verbindingen, waardoor de kans op een SNAT-poort ontstaat. Op dit moment Azure Firewall het open bare IP-adres van de bron wille keurig selecteren dat moet worden gebruikt voor een verbinding. Als u een stroomafwaarts filter op uw netwerk hebt, moet u alle open bare IP-adressen die zijn gekoppeld aan uw firewall toestaan.

## <a name="azure-monitor-logging"></a>Logboekregistratie van Azure Monitor

Alle gebeurtenissen zijn geïntegreerd met Azure Monitor, zodat u logboeken kunt archiveren in een opslagaccount, gebeurtenissen kunt streamen naar uw Event Hub of deze kunt verzenden naar Azure Monitor-logboeken.

## <a name="pci-soc-and-iso-compliant"></a>Compatibel met PCI, SOC en ISO

Azure Firewall zijn betaal kaart Industry (PCI), service organisatie Controls (SOC) en International Organization for Standardization (ISO)-compatibel. Het biedt momenteel ondersteuning voor SOC 1 type 2, SOC 2, SOC 3, PCI DSS en ISO 27001, 27018, 20000-1, 22301, 9001, 27017.

Zie de [micro soft compliance Guide (Engelstalig](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide)) voor meer informatie.

## <a name="known-issues"></a>Bekende problemen

Azure Firewall heeft de volgende bekende problemen:

|Probleem  |Description  |Oplossing  |
|---------|---------|---------|
Netwerkfilterregels voor niet-TCP/UDP-protocollen (bijvoorbeeld ICMP) werken niet voor internetverkeer|Netwerkfilterregels voor niet-TCP/UDP-protocollen werken niet met SNAT naar uw openbare IP-adres. Niet-TCP/UDP-protocollen worden ondersteund tussen spoke-subnetten en VNets.|Azure Firewall maakt gebruik van de standaardversie van Standard Load Balancer, [die momenteel geen ondersteuning biedt voor SNAT voor IP-protocollen](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). We verkennen opties om dit scenario in een toekomstige versie te ondersteunen.|
|Ontbrekende PowerShell- en CLI-ondersteuning voor ICMP|Azure PowerShell en CLI bieden geen ondersteuning voor ICMP als een geldig protocol in netwerkregels.|Het is nog steeds mogelijk om ICMP als protocol te gebruiken via de portal en de REST API. We werken binnenkort met het toevoegen van ICMP in Power shell en CLI.|
|FQDN-tags vereisen instelling van een protocol: poort|Voor toepassings regels met FQDN-Tags is poort: protocol definitie vereist.|U kunt **https** gebruiken als de waarde voor poort:protocol. We werken samen om dit veld optioneel te maken wanneer FQDN-labels worden gebruikt.|
|Het verplaatsen van een firewall naar een andere resource groep of een ander abonnement wordt niet ondersteund|Het is niet mogelijk om een firewall te verplaatsen naar een andere resource groep of een ander abonnement.|Ondersteuning van deze functionaliteit staat op de planning. Om een firewall naar een andere resourcegroep of ander abonnement verplaatsen, moet u het huidige exemplaar verwijderen en deze vervolgens opnieuw maken in de nieuwe resourcegroep of het nieuwe abonnement.|
|Poortbereik in netwerk- en toepassingsregels|Het aantal poorten is beperkt tot 64.000 omdat hoge poorten zijn gereserveerd voor beheer en statustests. |We werken eraan om deze beperking te versoepelen.|
|Waarschuwingen over Threat Intelligence kunnen gemaskerd worden|Netwerk regels met het doel 80/443 voor uitgaande filtering maskes Threat Intelligence-waarschuwingen wanneer deze zijn geconfigureerd voor de modus alleen-waarschuwingen.|Maak uitgaande filtering voor 80/443 met behulp van toepassings regels. Of wijzig de Threat Intelligence-modus in **waarschuwing en weigeren**.|
|Azure Firewall gebruikt alleen Azure DNS voor naam omzetting|Azure Firewall herstelt FQDN alleen met behulp van Azure DNS. Een aangepaste DNS-server wordt niet ondersteund. Er is geen invloed op de DNS-omzetting op andere subnetten.|We werken eraan om deze beperking te versoepelen.|
|Azure Firewall SNAT/DNAT werkt niet voor particuliere IP-doelen|Azure Firewall-ondersteuning voor SNAT/DNAT is beperkt tot uitstaand Internet/inkomend verkeer. SNAT/DNAT werkt momenteel niet voor particuliere IP-doelen. Bijvoorbeeld: spoke to spoke.|Dit is een huidige beperking.|
|Kan de eerste open bare IP-configuratie niet verwijderen|Elk Azure Firewall openbaar IP-adres wordt toegewezen aan een *IP-configuratie*.  De eerste IP-configuratie wordt toegewezen tijdens de implementatie van de firewall en bevat doorgaans een verwijzing naar het subnet van de firewall (tenzij expliciet anders is geconfigureerd via een sjabloon implementatie). U kunt deze IP-configuratie niet verwijderen omdat hiermee de firewall opnieuw wordt toegewezen. U kunt het open bare IP-adres dat is gekoppeld aan deze IP-configuratie, nog steeds wijzigen of verwijderen als de firewall ten minste één ander openbaar IP-adres beschikbaar is voor gebruik.|Dit is standaard.|
|Beschikbaarheids zones kunnen alleen worden geconfigureerd tijdens de implementatie.|Beschikbaarheids zones kunnen alleen worden geconfigureerd tijdens de implementatie. U kunt Beschikbaarheidszones niet configureren nadat er een firewall is geïmplementeerd.|Dit is standaard.|
|SNAT op binnenkomende verbindingen|Naast DNAT worden verbindingen via het open bare IP-adres van de firewall (inkomend) omgezet op een van de privé Ip's van de firewall. Deze vereiste is vandaag (ook voor actieve/actieve Nva's) om symmetrische route ring te garanderen.|Als u de oorspronkelijke bron voor HTTP/S wilt behouden, kunt u [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) -headers gebruiken. Gebruik bijvoorbeeld een service zoals [Azure front deur](../frontdoor/front-door-http-headers-protocol.md#front-door-service-to-backend) voor de firewall. U kunt ook WAF toevoegen als onderdeel van Azure front deur en keten aan de firewall.
|SQL FQDN-filtering alleen ondersteuning in proxy modus (poort 1433)|Voor Azure SQL Database, Azure SQL Data Warehouse en Azure SQL Managed instance:<br><br>Tijdens de preview wordt SQL FQDN-filtering alleen ondersteund in de proxy modus (poort 1433).<br><br>Voor Azure SQL IaaS:<br><br>Als u niet-standaard poorten gebruikt, kunt u die poorten opgeven in de toepassings regels.|Voor SQL in de omleidings modus, wat de standaard instelling is als u vanuit Azure verbinding maakt, kunt u in plaats daarvan de toegang filteren met behulp van de SQL-service-tag als onderdeel van Azure Firewall netwerk regels.
|Uitgaand verkeer op TCP-poort 25 is niet toegestaan| Uitgaande SMTP-verbindingen die gebruikmaken van TCP-poort 25 worden geblokkeerd. Poort 25 wordt hoofd zakelijk gebruikt voor niet-geverifieerde e-mail bezorging. Dit is het standaard platform gedrag voor virtuele machines. Zie meer problemen [met uitgaande SMTP-connectiviteit oplossen in azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md)voor meer informatie. Maar in tegens telling tot virtuele machines is het momenteel niet mogelijk om deze functionaliteit in te scha kelen op Azure Firewall.|Volg de aanbevolen methode voor het verzenden van e-mail zoals beschreven in het artikel problemen oplossen met SMTP. U kunt ook de virtuele machine uitsluiten waarvoor uitgaande SMTP-toegang is vereist van uw standaard route naar de firewall, en in plaats daarvan de uitgaande toegang rechtstreeks op Internet configureren.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Azure Firewall implementeren en configureren met Azure Portal](tutorial-firewall-deploy-portal.md)
- [Azure Firewall implementeren met behulp van een sjabloon](deploy-template.md)
- [Een Azure Firewall-testomgeving maken](scripts/sample-create-firewall-test.md)
