---
title: Wat is Azure Firewall?
description: Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 05/06/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 9d5fc95c5845b9a75666860ce8900676972a16bc
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864093"
---
# <a name="what-is-azure-firewall"></a>Wat is Azure Firewall?

![ICSA-certificering](media/overview/icsa-cert-firewall-small.png)

Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Het is een volledig stateful firewall als een service met ingebouwde hoge beschikbaarheid en onbeperkte cloudschaalbaarheid.

![Firewalloverzicht](media/overview/firewall-threat.png)

U kunt beleid voor toepassings- en netwerkconnectiviteit centraal maken, afdwingen en registreren voor abonnementen en virtuele netwerken. Azure Firewall maakt gebruik van een statisch openbaar IP-adres voor uw virtuele-netwerkresources zodat externe firewalls verkeer dat afkomstig is van uw virtuele netwerk kunnen identificeren.  De service is volledig geïntegreerd met Azure Monitor voor registratie en analyses.

Azure Firewall biedt de volgende functies:

## <a name="built-in-high-availability"></a>Ingebouwde hoge beschikbaarheid

Hoge Beschik baarheid is ingebouwd, zodat er geen extra load balancers zijn vereist en er niets hoeft te worden geconfigureerd.

## <a name="availability-zones"></a>Beschikbaarheidszones

Azure Firewall kunnen tijdens de implementatie worden geconfigureerd om meerdere Beschikbaarheidszones voor een hogere Beschik baarheid te beslaan. Met Beschikbaarheidszones wordt uw Beschik baarheid verhoogd tot 99,99% uptime. Zie de Azure Firewall- [Service Level Agreement (Sla)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)voor meer informatie. De SLA voor de uptime van 99,99% wordt aangeboden wanneer er twee of meer Beschikbaarheidszones zijn geselecteerd.

U kunt Azure Firewall ook koppelen aan een specifieke zone, net om redenen voor nabijheid, met behulp van Service Standard 99,95% SLA.

Er zijn geen extra kosten verbonden aan een firewall die is geïmplementeerd in een beschikbaarheids zone. Er zijn echter extra kosten voor inkomende en uitgaande gegevens overdrachten die zijn gekoppeld aan Beschikbaarheidszones. Zie [prijs informatie voor band breedte](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie.

Azure Firewall Beschikbaarheidszones zijn beschikbaar in regio's die ondersteuning bieden voor Beschikbaarheidszones. Zie [regio's die Beschikbaarheidszones in azure ondersteunen](../availability-zones/az-region.md) voor meer informatie.

> [!NOTE]
> Beschikbaarheidszones kan alleen worden geconfigureerd tijdens de implementatie. U kunt een bestaande firewall niet configureren om Beschikbaarheidszones op te kunnen bevatten.

Zie [regio's en Beschikbaarheidszones in azure](../availability-zones/az-overview.md) voor meer informatie over Beschikbaarheidszones.

## <a name="unrestricted-cloud-scalability"></a>Onbeperkte cloudschaalbaarheid

U kunt Azure Firewall omhoog schalen zoveel als nodig is om te voldoen aan veranderende netwerkverkeersstromen, zodat u geen budget hoeft te reserveren voor het drukste verkeer.

## <a name="application-fqdn-filtering-rules"></a>Regels voor het filteren van de FQDN van toepassingen

U kunt uitgaande HTTP/S-verkeer of Azure SQL-verkeer (preview) beperken tot een opgegeven lijst met FQDN-namen (FULLy Qualified Domain names), inclusief joker tekens. Voor deze functie is geen TLS-beëindiging vereist.

## <a name="network-traffic-filtering-rules"></a>Regels voor het filteren van netwerkverkeer

U kunt de netwerkfilterregels *toestaan* of *weigeren* centraal maken op IP-adres van bron en doel, poort en protocol. Azure Firewall is volledig stateful, wat betekent dat het legitieme pakketten voor verschillende soorten verbindingen kan onderscheiden. Regels worden afgedwongen en vastgelegd voor meerdere abonnementen en virtuele netwerken.

## <a name="fqdn-tags"></a>FQDN-tags

Met FQDN-Tags kunt u eenvoudig bekende Azure service-netwerk verkeer toestaan via uw firewall. Stel dat u Windows Update-netwerkverkeer wilt toestaan in de firewall. U maakt een toepassingsregel die de Windows Update-tag bevat. Het netwerkverkeer van Windows Update kan nu door uw firewall.

## <a name="service-tags"></a>Servicetags

Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels die het maken van beveiligingsregel vereenvoudigt. U kunt geen eigen servicetag maken en opgeven welke IP-adressen zijn opgenomen in een tag. Microsoft beheert de adresvoorvoegsels die de servicetag omvat en werkt de servicetag automatisch bij wanneer adressen veranderen.

## <a name="threat-intelligence"></a>Informatie over bedreigingen

Op bedreigingen gebaseerd filteren kan worden ingeschakeld voor uw firewall om verkeer van/naar bekende schadelijke IP-adressen en domeinen te Signa lering en te weigeren. De IP-adressen en domeinen zijn afkomstig van de micro soft Threat Intelligence-feed.

## <a name="outbound-snat-support"></a>Ondersteuning voor uitgaande SNAT

Alle uitgaande IP-adressen van virtueel netwerkverkeer worden geconverteerd naar de openbare IP van Azure Firewall (Source Network Address Translation). U kunt verkeer dat afkomstig is uit uw virtuele netwerk naar externe internetbestemmingen identificeren en toestaan. Azure Firewall geen SNAT wanneer de doel-IP een privé-IP-bereik is per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Als uw organisatie gebruikmaakt van een openbaar IP-adres bereik voor particuliere netwerken, verkrijgt Azure Firewall het verkeer naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet. U kunt Azure Firewall zodanig configureren dat uw open bare IP-adres bereik **niet** kan worden gesnat. Zie [Azure firewall SNAT-particuliere IP-adresbereiken](snat-private-range.md)voor meer informatie.

## <a name="inbound-dnat-support"></a>Ondersteuning voor inkomende DNAT

Binnenkomend Internet netwerk verkeer naar uw firewall openbaar IP-adres is vertaald (netwerkadresomzetting) en gefilterd op de privé-IP-adressen in uw virtuele netwerken.

## <a name="multiple-public-ip-addresses"></a>Meerdere open bare IP-adressen

U kunt meerdere open bare IP-adressen (Maxi maal 250) koppelen aan uw firewall.

Hiermee worden de volgende scenario's ingeschakeld:

- **DNAT** : u kunt meerdere exemplaren van de standaard poort naar uw back-endservers omzetten. Als u bijvoorbeeld twee open bare IP-adressen hebt, kunt u TCP-poort 3389 (RDP) voor beide IP-adressen omzetten.
- **SNAT** : er zijn extra poorten beschikbaar voor uitgaande SNAT-verbindingen, waardoor de kans op een SNAT-poort ontstaat. Op dit moment Azure Firewall het open bare IP-adres van de bron wille keurig selecteren dat moet worden gebruikt voor een verbinding. Als u een stroomafwaarts filter op uw netwerk hebt, moet u alle open bare IP-adressen die zijn gekoppeld aan uw firewall toestaan. Overweeg het gebruik van een [openbaar IP-adres voorvoegsel](../virtual-network/public-ip-address-prefix.md) om deze configuratie te vereenvoudigen.

## <a name="azure-monitor-logging"></a>Logboekregistratie van Azure Monitor

Alle gebeurtenissen zijn geïntegreerd met Azure Monitor, zodat u logboeken kunt archiveren in een opslagaccount, gebeurtenissen kunt streamen naar uw Event Hub of deze kunt verzenden naar Azure Monitor-logboeken.

## <a name="certifications"></a>Certificeringen

Azure Firewall is Payment Card Industry (PCI), service organisatie Controls (SOC), International Organization for Standardization (ISO) en ICSA Labs-compatibel. Zie [Azure firewall nalevings certificeringen](compliance-certifications.md)voor meer informatie.


## <a name="known-issues"></a>Bekende problemen

Azure Firewall heeft de volgende bekende problemen:

|Probleem  |Beschrijving  |Oplossing  |
|---------|---------|---------|
Netwerkfilterregels voor niet-TCP/UDP-protocollen (bijvoorbeeld ICMP) werken niet voor internetverkeer|Netwerk filter regels voor niet-TCP/UDP-protocollen werken niet met SNAT voor uw open bare IP-adres. Niet-TCP/UDP-protocollen worden ondersteund tussen spoke-subnetten en VNets.|Azure Firewall maakt gebruik van de standaardversie van Standard Load Balancer, [die momenteel geen ondersteuning biedt voor SNAT voor IP-protocollen](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). We verkennen opties om dit scenario in een toekomstige versie te ondersteunen.|
|Ontbrekende PowerShell- en CLI-ondersteuning voor ICMP|Azure PowerShell en CLI bieden geen ondersteuning voor ICMP als een geldig protocol in netwerk regels.|Het is nog steeds mogelijk om ICMP als protocol te gebruiken via de portal en de REST API. We werken binnenkort met het toevoegen van ICMP in Power shell en CLI.|
|FQDN-tags vereisen instelling van een protocol: poort|Voor toepassings regels met FQDN-Tags is poort: protocol definitie vereist.|U kunt **https** gebruiken als de waarde voor poort:protocol. We werken samen om dit veld optioneel te maken wanneer FQDN-labels worden gebruikt.|
|Het verplaatsen van een firewall naar een andere resource groep of een ander abonnement wordt niet ondersteund|Het is niet mogelijk om een firewall te verplaatsen naar een andere resource groep of een ander abonnement.|Ondersteuning van deze functionaliteit staat op de planning. Om een firewall naar een andere resourcegroep of ander abonnement verplaatsen, moet u het huidige exemplaar verwijderen en deze vervolgens opnieuw maken in de nieuwe resourcegroep of het nieuwe abonnement.|
|Waarschuwingen over Threat Intelligence kunnen gemaskerd worden|Netwerk regels met het doel 80/443 voor uitgaande filtering maskes Threat Intelligence-waarschuwingen wanneer deze zijn geconfigureerd voor de modus alleen-waarschuwingen.|Maak uitgaande filtering voor 80/443 met behulp van toepassings regels. Of wijzig de Threat Intelligence-modus in **waarschuwing en weigeren**.|
|Azure Firewall gebruikt alleen Azure DNS voor naam omzetting|Azure Firewall herstelt FQDN alleen met behulp van Azure DNS. Een aangepaste DNS-server wordt niet ondersteund. Er is geen invloed op de DNS-omzetting op andere subnetten.|We werken eraan om deze beperking te versoepelen.|
|Azure Firewall DNAT werkt niet voor particuliere IP-doelen|De DNAT-ondersteuning van Azure Firewall is beperkt tot uitstaand Internet/inkomend verkeer. DNAT werkt momenteel niet voor particuliere IP-doelen. Bijvoorbeeld: spoke to spoke.|Dit is een huidige beperking.|
|Kan de eerste open bare IP-configuratie niet verwijderen|Elk Azure Firewall openbaar IP-adres wordt toegewezen aan een *IP-configuratie*.  De eerste IP-configuratie wordt toegewezen tijdens de implementatie van de firewall en bevat doorgaans een verwijzing naar het subnet van de firewall (tenzij expliciet anders is geconfigureerd via een sjabloon implementatie). U kunt deze IP-configuratie niet verwijderen omdat hiermee de firewall opnieuw wordt toegewezen. U kunt het open bare IP-adres dat is gekoppeld aan deze IP-configuratie, nog steeds wijzigen of verwijderen als de firewall ten minste één ander openbaar IP-adres beschikbaar is voor gebruik.|Dit is standaard.|
|Beschikbaarheids zones kunnen alleen worden geconfigureerd tijdens de implementatie.|Beschikbaarheids zones kunnen alleen worden geconfigureerd tijdens de implementatie. U kunt Beschikbaarheidszones niet configureren nadat er een firewall is geïmplementeerd.|Dit is standaard.|
|SNAT op binnenkomende verbindingen|Naast DNAT worden verbindingen via het open bare IP-adres van de firewall (inkomend) omgezet op een van de privé Ip's van de firewall. Deze vereiste is vandaag (ook voor actieve/actieve Nva's) om symmetrische route ring te garanderen.|Als u de oorspronkelijke bron voor HTTP/S wilt behouden, kunt u [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) -headers gebruiken. Gebruik bijvoorbeeld een service zoals [Azure front deur](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) of [Azure-toepassing gateway](../application-gateway/rewrite-http-headers.md) vóór de firewall. U kunt ook WAF toevoegen als onderdeel van Azure front deur en keten aan de firewall.
|SQL FQDN-filtering alleen ondersteuning in proxy modus (poort 1433)|Voor Azure SQL Database, Azure SQL Data Warehouse en Azure SQL Managed instance:<br><br>Tijdens de preview wordt SQL FQDN-filtering alleen ondersteund in de proxy modus (poort 1433).<br><br>Voor Azure SQL IaaS:<br><br>Als u werkt met niet-standaard poorten, kunt u die poorten opgeven in de toepassings regels.|Voor SQL in de omleidings modus (de standaard instelling als u verbinding maakt vanuit Azure), kunt u in plaats daarvan de toegang filteren met behulp van de SQL-service-tag als onderdeel van Azure Firewall netwerk regels.
|Uitgaand verkeer op TCP-poort 25 is niet toegestaan| Uitgaande SMTP-verbindingen die gebruikmaken van TCP-poort 25 worden geblokkeerd. Poort 25 wordt hoofd zakelijk gebruikt voor niet-geverifieerde e-mail bezorging. Dit is het standaard platform gedrag voor virtuele machines. Zie meer problemen [met uitgaande SMTP-connectiviteit oplossen in azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md)voor meer informatie. Maar in tegens telling tot virtuele machines is het momenteel niet mogelijk om deze functionaliteit in te scha kelen op Azure Firewall. Opmerking: als u geverifieerde SMTP (poort 587) of SMTP wilt toestaan via een andere poort dan 25, moet u een netwerk regel configureren en niet een toepassings regel als SMTP-inspectie niet wordt ondersteund.|Volg de aanbevolen methode voor het verzenden van e-mail berichten, zoals beschreven in het artikel problemen oplossen met SMTP. U kunt ook de virtuele machine uitsluiten waarvoor uitgaande SMTP-toegang nodig is van uw standaard route naar de firewall. In plaats daarvan configureert u uitgaande toegang rechtstreeks op internet.
|Actieve FTP wordt niet ondersteund|Active FTP is uitgeschakeld op Azure Firewall om te beschermen tegen aanvallen met FTP-beveiliging met de opdracht FTP-poort.|U kunt in plaats daarvan passieve FTP gebruiken. U moet nog steeds expliciet TCP-poorten 20 en 21 openen op de firewall.
|De metrische gegevens voor het SNAT-poort gebruik tonen 0%|De metrische gegevens over het Azure Firewall SNAT-poort gebruik kunnen 0% gebruik tonen, zelfs wanneer er een SNAT-poort wordt gebruikt. In dit geval biedt het gebruik van de metriek als onderdeel van de status metric van de firewall een onjuist resultaat.|Dit probleem is opgelost en de implementatie voor productie is bedoeld voor mei 2020. In sommige gevallen wordt het probleem opgelost door de firewall opnieuw te implementeren, maar het is niet consistent. Als een tussenliggende tijdelijke oplossing gebruikt u alleen de status van de firewall om te zoeken naar *status = verslechterd*, niet voor *status = slecht*. Poort uitputting wordt weer gegeven als *gedegradeerd*. *Niet in orde* is gereserveerd voor toekomstig gebruik wanneer de meer meet waarden van invloed zijn op de status van de firewall.
|DNAT wordt niet ondersteund als geforceerde tunneling is ingeschakeld|Firewalls die zijn geïmplementeerd met geforceerde tunneling, bieden geen ondersteuning voor inkomende toegang via internet vanwege asymmetrische route ring.|Dit is door het ontwerp vanwege asymmetrische route ring. Het retour traject voor binnenkomende verbindingen gaat via de on-premises firewall, die geen verbinding heeft gemaakt.
|Uitgaande passieve FTP werkt niet voor firewalls met meerdere open bare IP-adressen.|Met passieve FTP worden verschillende verbindingen tot stand gebracht voor besturings elementen en gegevens kanalen. Wanneer een firewall met meerdere open bare IP-adressen gegevens uitgaand verzendt, wordt wille keurig een van de open bare IP-adressen geselecteerd voor het bron-IP-adres. FTP mislukt wanneer gegevens-en besturings kanalen gebruikmaken van verschillende bron-IP-adressen.|Er wordt een expliciete SNAT-configuratie gepland. In de tussen tijd kunt u in deze situatie één IP-adres gebruiken.|
|Er ontbreekt een protocol dimensie in de NetworkRuleHit-waarde|Met de metrische ApplicationRuleHit-gegevens kunt u filteren op basis van een protocol, maar deze mogelijkheid ontbreekt in de bijbehorende NetworkRuleHit-metriek.|Er wordt een oplossing onderzocht.|
|NAT-regels met poorten tussen 64000 en 65535 worden niet ondersteund|Azure Firewall staat elke poort toe in het 1-65535-bereik in netwerk-en toepassings regels, maar NAT-regels ondersteunen alleen poorten in het bereik van 1-63999.|Dit is een huidige beperking.
|Configuratie-updates kunnen gemiddeld vijf minuten duren.|Een Azure Firewall configuratie-update kan gemiddeld drie tot vijf minuten duren en parallelle updates worden niet ondersteund.|Er wordt een oplossing onderzocht.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Azure Firewall implementeren en configureren met Azure Portal](tutorial-firewall-deploy-portal.md)
- [Azure Firewall implementeren met behulp van een sjabloon](deploy-template.md)
- [Een Azure Firewall-testomgeving maken](scripts/sample-create-firewall-test.md)
