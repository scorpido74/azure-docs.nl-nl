---
title: Netwerkbeveiliging voor Azure Event Grid-resources
description: In dit artikel wordt beschreven hoe u toegang vanaf privéeindpunten configureert
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: ed3b70ad267252981110e7970bc5c5fad6cf4b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300152"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Netwerkbeveiliging voor Azure Event Grid-resources
In dit artikel wordt beschreven hoe u de volgende beveiligingsfuncties gebruiken met Azure Event Grid: 

- Servicetags voor uitgang (voorbeeld)
- IP Firewall-regels voor binnendringen (voorbeeld)
- Privéeindpunten voor binnendringen (voorbeeld)


## <a name="service-tags"></a>Servicetags
Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels van een bepaalde Azure-service. Microsoft beheert de adresvoorvoegsels die zijn gebruikt door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen, waardoor de complexiteit van frequente updates voor netwerkbeveiligingsregels wordt geminimaliseerd. Zie [Overzicht servicetags voor](../virtual-network/service-tags-overview.md)meer informatie over servicetags.

U servicetags gebruiken om netwerktoegangsbesturingselementen te definiëren in [netwerkbeveiligingsgroepen](../virtual-network/security-overview.md#security-rules) of [Azure Firewall.](../firewall/service-tags.md) Gebruik servicetags in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld **AzureEventGrid)** op te geven in het juiste *bron-* of *doelveld* van een regel, u het verkeer voor de bijbehorende service toestaan of weigeren.

| Servicetag | Doel | Kan u in-komende of uitgaande gebruiken? | Kan regionaal zijn? | Kan u gebruik maken van Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| AzureEventGrid | Azure-gebeurtenisraster. <br/><br/>*Let op:* Deze tag heeft alleen betrekking op Azure Event Grid-eindpunten in US South Central, US East, US East 2, US West 2 en US Central. | Beide | Nee | Nee |


## <a name="ip-firewall"></a>IP-firewall 
Azure Event Grid ondersteunt IP-gebaseerde toegangsbesturingselementen voor publicatie naar onderwerpen en domeinen. Met IP-gebaseerde besturingselementen u de uitgevers beperken tot een onderwerp of domein tot slechts een set goedgekeurde machines en cloudservices. Deze functie vormt een aanvulling op de [verificatiemechanismen](security-authentication.md) die worden ondersteund door Event Grid.

Standaard zijn onderwerp en domein toegankelijk vanaf internet, zolang de aanvraag wordt geleverd met geldige verificatie en autorisatie. Met IP-firewall u deze verder beperken tot alleen een set IP-adressen of IP-adresbereiken in [CIDR-notatie (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Uitgevers die afkomstig zijn van een ander IP-adres worden afgewezen en ontvangen een reactie van 403 (Verboden).


## <a name="private-endpoints"></a>Privéeindpunten
U [privéeindpunten](../private-link/private-endpoint-overview.md) gebruiken om het binnendringen van gebeurtenissen rechtstreeks van uw virtuele netwerk toe te staan om uw onderwerpen en domeinen veilig via een [privéverbinding](../private-link/private-link-overview.md) toe te staan zonder via het openbare internet te gaan. Een privéeindpunt is een speciale netwerkinterface voor een Azure-service in uw VNet. Wanneer u een privéeindpunt voor uw onderwerp of domein maakt, biedt dit veilige connectiviteit tussen clients op uw VNet en uw gebeurtenisrasterbron. Aan het privéeindpunt wordt een IP-adres toegewezen uit het IP-adresbereik van uw VNet. De verbinding tussen het privéeindpunt en de Gebeurtenisrasterservice maakt gebruik van een beveiligde privékoppeling.

![Architectuurdiagram](./media/network-security/architecture-diagram.png)

Als u privéeindpunten gebruikt voor uw eventgrid-bron, u:

- Beveilig toegang tot uw onderwerp of domein vanaf een VNet via het Backbone-netwerk van Microsoft, in tegenstelling tot het openbare internet.
- Maak veilig verbinding via on-premises netwerken die verbinding maken met het VNet via VPN of ExpressRoutes met private-peering.

Wanneer u een privéeindpunt maakt voor een onderwerp of domein in uw VNet, wordt een toestemmingsaanvraag ter goedkeuring verzonden naar de eigenaar van de bron. Als de gebruiker die het privéeindpunt aanvraagt, ook eigenaar is van de resource, wordt deze toestemmingsaanvraag automatisch goedgekeurd. Anders is de verbinding in **behandeling** totdat deze is goedgekeurd. Toepassingen in het VNet kunnen naadloos verbinding maken met de Gebeurtenisrasterservice via het privéeindpunt, met dezelfde verbindingstekenreeksen en autorisatiemechanismen die ze anders zouden gebruiken. Broneigenaren kunnen toestemmingsaanvragen en de privéeindpunten beheren via het tabblad **Privéeindpunten** voor de bron in de Azure-portal.

### <a name="connect-to-private-endpoints"></a>Verbinding maken met privéeindpunten
Uitgevers op een VNet die het privéeindpunt gebruiken, moeten dezelfde verbindingstekenreeks gebruiken voor het onderwerp of domein als clients die verbinding maken met het openbare eindpunt. DNS-resolutie leidt verbindingen van het VNet automatisch naar het onderwerp of domein via een privékoppeling. Event Grid maakt standaard een [privé-DNS-zone](../dns/private-dns-overview.md) die is gekoppeld aan het VNet met de benodigde update voor de privéeindpunten. Als u echter uw eigen DNS-server gebruikt, moet u mogelijk aanvullende wijzigingen aanbrengen in uw DNS-configuratie.

### <a name="dns-changes-for-private-endpoints"></a>DNS-wijzigingen voor privéeindpunten
Wanneer u een privéeindpunt maakt, wordt de DNS CNAME-record voor de bron `privatelink`bijgewerkt naar een alias in een subdomein met het voorvoegsel . Standaard wordt een privé-DNS-zone gemaakt die overeenkomt met het subdomein van de privékoppeling. 

Wanneer u de URL van het onderwerp of het domeineindpunt van buiten het VNet oplost met het privéeindpunt, wordt het probleem opgelost tot het openbare eindpunt van de service. De DNS-bronrecords voor 'topicA', wanneer deze van **buiten het VNet** worden opgelost als host van het privéeindpunt, zijn:

| Name                                          | Type      | Waarde                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<azure traffic manager profiel\>

U de toegang voor een client buiten het VNet weigeren of beheren via het openbare eindpunt met behulp van de [IP-firewall.](#ip-firewall) 

Wanneer het onderwerp of de URL van het domeineindpunt wordt opgelost vanuit het VNet dat het privéeindpunt host, wordt deze opgelost naar het IP-adres van het privéeindpunt. De DNS-bronrecords voor het onderwerp 'topicA', wanneer deze **vanuit het VNet** worden opgelost als host van het privéeindpunt, zijn:

| Name                                          | Type      | Waarde                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Deze benadering maakt toegang tot het onderwerp of domein mogelijk met dezelfde verbindingstekenreeks voor clients op de VNet die de privéeindpunten hosten en clients buiten het VNet.

Als u een aangepaste DNS-server in uw netwerk gebruikt, kunnen clients de FQDN voor het onderwerp of domeineindpunt oplossen aan het privé-eindpunt-IP-adres. Configureer uw DNS-server om uw subdomein voor privékoppelingen te delegeren `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` aan de privé-DNS-zone voor de VNet of configureer de A-records voor met het privé-eindpunt-IP-adres.

De aanbevolen DNS-zonenaam is `privatelink.eventgrid.azure.net`.

### <a name="private-endpoints-and-publishing"></a>Privéeindpunten en publicatie

In de volgende tabel worden de verschillende toestanden van de privé-eindpuntverbinding en de effecten op het publiceren beschreven:

| Verbindingsstatus   |  Met succes publiceren (ja/nee) |
| ------------------ | -------------------------------|
| Goedgekeurd           | Ja                            |
| Geweigerd           | Nee                             |
| In behandeling            | Nee                             |
| De verbinding verbroken       | Nee                             |

Om publicatie succesvol te laten verlopen, moet de status van de privé-eindpuntverbinding worden **goedgekeurd**. Als een verbinding wordt geweigerd, kan deze niet worden goedgekeurd met behulp van de Azure-portal. De enige mogelijkheid is om de verbinding te verwijderen en in plaats daarvan een nieuwe te maken.

## <a name="pricing-and-quotas"></a>Prijzen en quota
**Privéeindpunten** zijn alleen beschikbaar met onderwerpen en domeinen van premiumlagen. Met Event Grid kunnen maximaal 64 privéeindpuntverbindingen worden gemaakt per onderwerp of domein. Zie het artikel [Prijsprijsniveau bijwerken](update-tier.md) als u wilt upgraden van basislaag naar premiumlaag.

**IP Firewall-functie** is beschikbaar in zowel basis- als premiumlagen van Event Grid. Per onderwerp of domein kunnen maximaal 16 IP Firewall-regels worden gemaakt.


## <a name="next-steps"></a>Volgende stappen
U ip-firewall configureren voor uw Event Grid-bron om de toegang via het openbare internet te beperken vanuit slechts een selecte set IP-adressen of IP-adresbereiken. Zie [IP-firewall configureren](configure-firewall.md)voor stapsgewijze instructies.

U privéeindpunten configureren om de toegang alleen vanaf geselecteerde virtuele netwerken te beperken. Zie [Privéeindpunten configureren](configure-private-endpoints.md)voor stapsgewijze instructies.
