---
title: OPTIES voor DNS-naamomzetting voor Linux VM's
description: Scenario's voor naamomzetting voor Linux-virtuele machines in Azure IaaS, inclusief geleverde DNS-services, hybride externe DNS en Bring Your Own DNS-server.
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: 3d5ecaf67dcff182c7dace474b7bda45cdfd5c58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969325"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>OPTIES voor DNS-naamomzetting voor virtuele Linux-machines in Azure
Azure biedt standaard DNS-naamomzetting voor alle virtuele machines die zich in één virtueel netwerk bevinden. U uw eigen DNS-naamomzettingsoplossing implementeren door uw eigen DNS-services te configureren op uw virtuele machines die Azure host. De volgende scenario's moeten u helpen bij het kiezen van de scenario's die werkt voor uw situatie.

* [Naamomzetting die Azure biedt](#name-resolution-that-azure-provides)
* [Naamomzetting met uw eigen DNS-server](#name-resolution-using-your-own-dns-server)

Het type naamomzetting dat u gebruikt, is afhankelijk van hoe uw virtuele machines en rolexemplaren met elkaar moeten communiceren.

In de volgende tabel worden scenario's en bijbehorende oplossingen voor naamsoplossing geïllustreerd:

| **Scenario** | **Oplossing** | **Achtervoegsel** |
| --- | --- | --- |
| Naamoplossing tussen rolinstanties of virtuele machines in hetzelfde virtuele netwerk |Naamomzetting die Azure biedt |hostname of volledig gekwalificeerde domeinnaam (FQDN) |
| Naamoplossing tussen rolinstanties of virtuele machines in verschillende virtuele netwerken |Door de klant beheerde DNS-servers die query's tussen virtuele netwerken doorsturen voor oplossing door Azure (DNS-proxy). Zie [Naamomzetting met uw eigen DNS-server](#name-resolution-using-your-own-dns-server). |Alleen FQDN |
| Oplossing van on-premises computers en servicenamen van rolinstanties of virtuele machines in Azure |Door de klant beheerde DNS-servers (bijvoorbeeld on-premises domeincontroller, lokale alleen-lezen domeincontroller of een DNS-secundaire synchronisatie met zoneoverdrachten). Zie [Naamomzetting met uw eigen DNS-server](#name-resolution-using-your-own-dns-server). |Alleen FQDN |
| Oplossing van Azure-hostnamen van on-premises computers |Query's doorsturen naar een door de klant beheerde DNS-proxyserver in het bijbehorende virtuele netwerk. De proxyserver stuurt query's door naar Azure voor oplossing. Zie [Naamomzetting met uw eigen DNS-server](#name-resolution-using-your-own-dns-server). |Alleen FQDN |
| Reverse DNS voor interne IP's |[Naamomzetting met uw eigen DNS-server](#name-resolution-using-your-own-dns-server) |N.v.t. |

## <a name="name-resolution-that-azure-provides"></a>Naamomzetting die Azure biedt
Samen met de resolutie van openbare DNS-namen biedt Azure interne naamomzetting voor virtuele machines en rolexemplaren die zich in hetzelfde virtuele netwerk bevinden. In virtuele netwerken die zijn gebaseerd op Azure Resource Manager, is het DNS-achtervoegsel consistent in het virtuele netwerk. de FQDN is niet nodig. DNS-namen kunnen worden toegewezen aan zowel netwerkinterfacekaarten (NIC's) als virtuele machines. Hoewel de naamresolutie die Azure biedt geen configuratie vereist, is dit niet de juiste keuze voor alle implementatiescenario's, zoals te zien is in de vorige tabel.

### <a name="features-and-considerations"></a>Kenmerken en overwegingen
**Functies:**

* Er is geen configuratie vereist om de naamomzetting te gebruiken die Azure biedt.
* De naamomzettingsservice die Azure biedt, is zeer beschikbaar. U hoeft geen clusters van uw eigen DNS-servers te maken en te beheren.
* De naamomzettingsservice die Azure biedt, kan samen met uw eigen DNS-servers worden gebruikt om zowel on-premises als Azure-hostnamen op te lossen.
* Naamresolutie wordt geleverd tussen virtuele machines in virtuele netwerken zonder dat de FQDN nodig is.
* U hostnamen gebruiken die uw implementaties het best beschrijven in plaats van met automatisch gegenereerde namen te werken.

**Overwegingen:**

* Het DNS-achtervoegsel dat Azure maakt, kan niet worden gewijzigd.
* U uw eigen records niet handmatig registreren.
* WINS en NetBIOS worden niet ondersteund.
* Hostnames moeten DNS-compatibel zijn.
    Namen mogen slechts 0-9, a-z en '-' gebruiken en ze kunnen niet beginnen of eindigen met een '-'. Zie RFC 3696 Sectie 2.
* DNS-queryverkeer wordt voor elke virtuele machine beperkt. Beperking mag geen invloed hebben op de meeste toepassingen.  Als de beperking van het verzoek wordt nageleefd, moet u ervoor zorgen dat caching aan clientzijde is ingeschakeld.  Zie [Het meeste ophalen uit naamomzetting die Azure biedt](#getting-the-most-from-name-resolution-that-azure-provides)voor meer informatie.

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Optimaal gebruik maken van de naamomzetting die Azure biedt
**Caching aan clientzijde:**

Sommige DNS-query's worden niet via het netwerk verzonden. Client-side caching helpt de latentie te verminderen en de veerkracht voor netwerkinconsistenties te verbeteren door terugkerende DNS-query's uit een lokale cache op te lossen. DNS-records bevatten een Time-To-Live (TTL), waarmee de cache de record zo lang mogelijk kan opslaan zonder dat dit gevolgen heeft voor de recordversheid. Als gevolg hiervan is client-side caching geschikt voor de meeste situaties.

Sommige Linux-distributies bevatten standaard geen caching. We raden u aan een cache toe te voegen aan elke virtuele Linux-machine nadat u hebt gecontroleerd of er nog geen lokale cache is.

Er zijn verschillende DNS-cachingpakketten beschikbaar, zoals dnsmasq. Hier volgen de stappen om dnsmasq te installeren op de meest voorkomende distributies:

**Ubuntu (maakt gebruik van resolvconf)**
  * Installeer het dnsmasq-pakket ("sudo apt-get install dnsmasq").

**SUSE (maakt gebruik van netconf)**:
1. Installeer het dnsmasq pakket ("sudo zypper install dnsmasq").
2. Schakel de dnsmasq-service in ("systemctl enable dnsmasq.service").
3. Start de dnsmasq-service ("systemctl start dnsmasq.service").
4. Bewerk "/etc/sysconfig/network/config", en verander NETCONFIG_DNS_FORWARDER="" in "dnsmasq".
5. Update resolv.conf ("netconfig update") om de cache in te stellen als de lokale DNS-resolver.

**CentOS door Rogue Wave Software (voorheen OpenLogic; maakt gebruik van NetworkManager)**
1. Installeer het dnsmasq pakket ("sudo yum install dnsmasq").
2. Schakel de dnsmasq-service in ("systemctl enable dnsmasq.service").
3. Start de dnsmasq-service ("systemctl start dnsmasq.service").
4. Voeg "prepend domain-name-servers 127.0.0.1;" toe aan "/etc/dhclient-eth0.conf".
5. De netwerkservice opnieuw starten ('servicenetwerk opnieuw opstarten') om de cache in te stellen als de lokale DNS-resolver

> [!NOTE]
> : Het 'dnsmasq'-pakket is slechts één van de vele DNS-caches die beschikbaar zijn voor Linux. Controleer voordat u het gebruikt de geschiktheid voor uw behoeften en of er geen andere cache is geïnstalleerd.
>
>

**Herpogingen aan clientzijde**

DNS is in de eerste plaats een UDP-protocol. Omdat het UDP-protocol geen garantie biedt voor de bezorging van berichten, verwerkt het DNS-protocol zelf de logica voor opnieuw proberen. Elke DNS-client (besturingssysteem) kan verschillende logica opnieuw proberen, afhankelijk van de voorkeur van de maker:

* Windows-besturingssystemen opnieuw proberen na een seconde en dan weer na nog eens twee, vier, en nog eens vier seconden.
* De standaard Linux setup wordt na vijf seconden opnieuw weergegeven.  U moet dit wijzigen om vijf keer met tussenpozen van één seconde opnieuw te proberen.  

Om de huidige instellingen op een Linux virtuele machine te controleren, 'cat /etc/resolv.conf', en kijken naar de 'opties' lijn, bijvoorbeeld:

    options timeout:1 attempts:5

Het bestand resolv.conf wordt automatisch gegenereerd en mag niet worden bewerkt. De specifieke stappen die de regel 'opties' toevoegen, verschillen per distributie:

**Ubuntu** (maakt gebruik van resolvconf)
1. Voeg de optieregel toe aan '/etc/resolveconf/resolv.conf.d/head'.
2. Voer 'resolvconf -u' uit om bij te werken.

**SUSE** (gebruikt netconf)
1. Voeg 'time-out:1 attempts:5' toe aan de parameter NETCONFIG_DNS_RESOLVER_OPTIONS="" in '/etc/sysconfig/network/config'.
2. Voer 'netconfig update' uit om bij te werken.

**CentOS door Rogue Wave Software (voorheen OpenLogic)** (maakt gebruik van NetworkManager)
1. Voeg 'RES_OPTIONS="time-out:1 attempts:5"' toe aan '/etc/sysconfig/network'.
2. Voer 'servicenetwerk opnieuw op' uit om bij te werken.

## <a name="name-resolution-using-your-own-dns-server"></a>Naamomzetting met uw eigen DNS-server
Uw behoeften aan naamomzetting kunnen verder gaan dan de functies die Azure biedt. U hebt bijvoorbeeld DNS-resolutie tussen virtuele netwerken nodig. Om dit scenario te dekken, u uw eigen DNS-servers gebruiken.  

DNS-servers binnen een virtueel netwerk kunnen DNS-query's doorsturen naar recursieve resolvers van Azure om hostnamen op te lossen die zich in hetzelfde virtuele netwerk bevinden. Een DNS-server die in Azure wordt uitgevoerd, kan bijvoorbeeld reageren op DNS-query's voor zijn eigen DNS-zonebestanden en alle andere query's doorsturen naar Azure. Met deze functionaliteit kunnen virtuele machines zowel uw vermeldingen in uw zonebestanden als hostnamen zien die Azure biedt (via de expediteer). Toegang tot de recursieve resolvers van Azure wordt geboden via het virtuele IP 168.63.129.16.

DNS forwarding maakt ook DNS-resolutie tussen virtuele netwerken mogelijk en stelt uw on-premises machines in staat om hostnamen op te lossen die Azure biedt. Als u de hostnaam van een virtuele machine wilt oplossen, moet de virtuele machine van de DNS-server zich in hetzelfde virtuele netwerk bevinden en worden geconfigureerd om hostnamequery's naar Azure door te sturen. Omdat het DNS-achtervoegsel in elk virtueel netwerk verschilt, u voorwaardelijke doorstuurregels gebruiken om DNS-query's naar het juiste virtuele netwerk te verzenden voor oplossing. De volgende afbeelding toont twee virtuele netwerken en een on-premises netwerk dat DNS-resolutie tussen virtuele netwerken doet met behulp van deze methode:

![DNS-resolutie tussen virtuele netwerken](./media/azure-dns/inter-vnet-dns.png)

Wanneer u naamomzetting gebruikt die Azure biedt, wordt het interne DNS-achtervoegsel aan elke virtuele machine geleverd met BEHULP van DHCP. Wanneer u uw eigen oplossing voor naamomzetting gebruikt, wordt dit achtervoegsel niet geleverd aan virtuele machines omdat het achtervoegsel andere DNS-architecturen verstoort. Als u wilt verwijzen naar machines per FQDN of om het achtervoegsel op uw virtuele machines te configureren, u PowerShell of de API gebruiken om het achtervoegsel te bepalen:

* Voor virtuele netwerken die worden beheerd door Azure Resource Manager, is het achtervoegsel beschikbaar via de bron van de [netwerkinterfacekaart.](https://msdn.microsoft.com/library/azure/mt163668.aspx) U de `azure network public-ip show <resource group> <pip name>` opdracht ook uitvoeren om de details van uw openbare IP weer te geven, waaronder de FQDN van de NIC.

Als het doorsturen van query's naar Azure niet aan uw behoeften voldoet, moet u uw eigen DNS-oplossing bieden.  Uw DNS-oplossing moet:

* Geef de juiste hostname-resolutie, bijvoorbeeld via [DDNS.](../../virtual-network/virtual-networks-name-resolution-ddns.md) Als u DDNS gebruikt, moet u mogelijk dns-recordopruiming uitschakelen. DHCP-leases van Azure zijn erg lang en het opruimen kan DNS-records voortijdig verwijderen.
* Zorg voor een passende recursieve oplossing om de oplossing van externe domeinnamen mogelijk te maken.
* Wees toegankelijk (TCP en UDP op poort 53) van de clients die het bedient en heb toegang tot het internet.
* Worden beveiligd tegen toegang vanaf het internet om bedreigingen van externe agenten te beperken.

> [!NOTE]
> Wanneer u virtuele machines in Azure [DNS-servers](../../virtual-network/virtual-networks-instance-level-public-ip.md) gebruikt, schakelt u IPv6 uit en wijst u een openbaar IP op instantieniveau toe aan elke virtuele DNS-server.  
>
>
