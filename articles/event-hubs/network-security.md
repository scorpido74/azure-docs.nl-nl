---
title: Netwerkbeveiliging voor Azure Event Hubs
description: In dit artikel wordt beschreven hoe u toegang vanaf privéeindpunten configureert
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: ab85cdb2854de5c147c68afd8e4fe5e17ac2899b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477939"
---
# <a name="network-security-for-azure-event-hubs"></a>Netwerkbeveiliging voor Azure Event Hubs 
In dit artikel wordt beschreven hoe u de volgende beveiligingsfuncties gebruiken met Azure Event Hubs: 

- Servicetags
- IP Firewall-regels
- Eindpunten van netwerkservice
- Privéeindpunten (voorbeeld)


## <a name="service-tags"></a>Servicetags
Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels van een bepaalde Azure-service. Microsoft beheert de adresvoorvoegsels die zijn gebruikt door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen, waardoor de complexiteit van frequente updates voor netwerkbeveiligingsregels wordt geminimaliseerd. Zie [Overzicht servicetags voor](../virtual-network/service-tags-overview.md)meer informatie over servicetags.

U servicetags gebruiken om netwerktoegangsbesturingselementen te definiëren in [netwerkbeveiligingsgroepen](../virtual-network/security-overview.md#security-rules) of [Azure Firewall.](../firewall/service-tags.md) Gebruik servicetags in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld **EventHub)** op te geven in het juiste *bron-* of *doelveld* van een regel, u het verkeer voor de desbetreffende service toestaan of weigeren.

| Servicetag | Doel | Kan u in-komende of uitgaande gebruiken? | Kan regionaal zijn? | Kan u gebruik maken van Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Azure-gebeurtenishubs. | Uitgaand | Ja | Ja |


## <a name="ip-firewall"></a>IP-firewall 
Standaard zijn naamruimten van Event Hubs toegankelijk vanaf internet, zolang de aanvraag wordt geleverd met geldige verificatie en autorisatie. Met IP-firewall u deze verder beperken tot alleen een set IPv4-adressen of IPv4-adresbereiken in [CIDR-notatie (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Deze functie is handig in scenario's waarin Azure Event Hubs alleen toegankelijk moeten zijn vanaf bepaalde bekende sites. Met firewallregels u regels configureren om verkeer te accepteren dat afkomstig is van specifieke IPv4-adressen. Als u bijvoorbeeld Gebeurtenishubs gebruikt met [Azure Express Route][expresroute], u een **firewallregel** maken om verkeer toe te staan vanaf alleen uw IP-adressen met on-premises infrastructuur. 

De IP-firewallregels worden toegepast op het naamruimteniveau van Gebeurtenishubs. Daarom zijn de regels van toepassing op alle verbindingen van clients die een ondersteund protocol gebruiken. Elke verbindingspoging vanaf een IP-adres dat niet overeenkomt met een toegestane IP-regel op de naamruimte van gebeurtenishubs, wordt afgewezen als ongeautoriseerd. In het antwoord wordt geen IP-regel vermeld. IP-filterregels worden op volgorde toegepast en de eerste regel die overeenkomt met het IP-adres bepaalt de actie Accepteren of weigeren.

Zie [IP-firewall configureren voor een gebeurtenishub voor](event-hubs-ip-filtering.md) meer informatie

## <a name="network-service-endpoints"></a>Eindpunten van netwerkservice
De integratie van Event Hubs met [Virtual Network (VNet) Service Endpoints][vnet-sep] maakt veilige toegang tot messaging-mogelijkheden mogelijk van workloads zoals virtuele machines die gebonden zijn aan virtuele netwerken, waarbij het netwerkverkeer wordt beveiligd op beide uiteinden.

Eenmaal geconfigureerd om gebonden te zijn aan ten minste één eindpunt van de virtuele netwerksubnetservice, accepteert de respectievelijke naamruimte van Gebeurtenishubs niet langer verkeer van overal, maar geautoriseerde subnetten in virtuele netwerken. Vanuit het perspectief van het virtuele netwerk configureert het binden van een naamruimte van Gebeurtenishubs aan een serviceeindpunt een geïsoleerde netwerktunnel van het virtuele netwerksubnet naar de berichtenservice. 

Het resultaat is een privé- en geïsoleerde relatie tussen de workloads die aan het subnet zijn gebonden en de respectievelijke naamruimte van Gebeurtenishubs, ondanks het waarneembare netwerkadres van het eindpunt van de berichtenservice dat zich in een openbaar IP-bereik bevindt. Er is een uitzondering op dit gedrag. Als u standaard een serviceeindpunt `denyall` inschakelt, wordt de regel in de [IP-firewall](event-hubs-ip-filtering.md) die aan het virtuele netwerk is gekoppeld, ingeschakeld. U specifieke IP-adressen toevoegen in de IP-firewall om toegang tot het openbare eindpunt van de Gebeurtenishub mogelijk te maken. 

> [!IMPORTANT]
> Virtuele netwerken worden ondersteund in de lagen **Standard** en **Dedicated** van Event Hubs. Het wordt niet ondersteund in de **basislaag.**

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Geavanceerde beveiligingsscenario's mogelijk gemaakt door VNet-integratie 

Oplossingen die strakke en gecompartimenteerde beveiliging vereisen en waar virtuele netwerksubnetten de segmentatie tussen de gecompartimenteerde services bieden, moeten nog steeds communicatiepaden worden nodig tussen services die zich in die compartimenten bevinden.

Elke directe IP-route tussen de compartimenten, inclusief die met HTTPS via TCP/IP, brengt het risico met zich mee dat kwetsbaarheden vanaf de netwerklaag worden gebruikt. Messaging-diensten bieden geïsoleerde communicatiepaden, waar berichten zelfs naar de schijf worden geschreven terwijl ze tussen partijen overstappen. Workloads in twee afzonderlijke virtuele netwerken die beide gebonden zijn aan dezelfde instantie Event Hubs, kunnen efficiënt en betrouwbaar communiceren via berichten, terwijl de respectieve integriteit van de netwerkisolatiegrens behouden blijft.
 
Dat betekent dat uw beveiligingsgevoelige cloudoplossingen niet alleen toegang krijgen tot azure toonaangevende betrouwbare en schaalbare asynchrone messaging-mogelijkheden, maar ze kunnen nu messaging gebruiken om communicatiepaden te maken tussen beveiligde oplossingscompartimenten die zijn inherent veiliger dan wat haalbaar is met een peer-to-peer communicatiemodus, inclusief HTTPS en andere TLS-beveiligde socketprotocollen.

### <a name="bind-event-hubs-to-virtual-networks"></a>Gebeurtenishubs binden aan virtuele netwerken

**Virtuele netwerkregels** zijn de beveiligingsfunctie voor firewalls die bepaalt of uw Azure Event Hubs naamruimte verbindingen van een bepaald virtueel netwerksubnet accepteert.

Het binden van een naamruimte voor gebeurtenishubs aan een virtueel netwerk is een proces in twee stappen. U moet eerst een eindpunt van de **virtuele netwerkservice** maken op het subnet van een virtueel netwerk en dit inschakelen voor **Microsoft.EventHub,** zoals uitgelegd in het artikel [serviceendpoint-overzicht][vnet-sep]. Zodra u het serviceeindpunt hebt toegevoegd, bindt u de naamruimte van gebeurtenishubs eraan met een **virtuele netwerkregel**.

De virtuele netwerkregel is een koppeling van de naamruimte van gebeurtenishubs met een virtueel netwerksubnet. Hoewel de regel bestaat, krijgen alle workloads die aan het subnet zijn gebonden, toegang tot de naamruimte van gebeurtenishubs. Event Hubs zelf maakt nooit uitgaande verbindingen, hoeft geen toegang te krijgen en krijgt daarom nooit toegang tot je subnet door deze regel in te schakelen.

Zie [Eindpunten voor virtuele netwerkservice configureren voor een gebeurtenishub voor](event-hubs-service-endpoints.md) meer informatie

## <a name="private-endpoints"></a>Privéeindpunten

[Azure Private Link-service](../private-link/private-link-overview.md) stelt u in staat om toegang te krijgen tot Azure Services (bijvoorbeeld Azure Event Hubs, Azure Storage en Azure Cosmos DB) en Azure gehoste klant/partnerservices via een **privéeindpunt** in uw virtuele netwerk.

Een privéeindpunt is een netwerkinterface die u privé en veilig verbindt met een service die wordt aangedreven door Azure Private Link. Het privéeindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service effectief in uw VNet wordt opgenomen. Al het verkeer naar de service kan worden doorgestuurd via het privéeindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute- of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U verbinding maken met een instantie van een Azure-bron, zodat u het hoogste niveau van granulariteit in toegangscontrole hebt.

> [!NOTE]
> Deze functie wordt alleen ondersteund met de **specifieke** laag. Zie [Overzicht van evenementhubs dedicated](event-hubs-dedicated-overview.md)voor meer informatie over de specifieke laag. 
>
> Deze functie is momenteel in **preview**. 


Zie [Privéeindpunten configureren voor een gebeurtenishub voor](private-link-service.md) meer informatie


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [IP-firewall configureren voor een gebeurtenishub](event-hubs-ip-filtering.md)
- [Eindpunten voor virtuele netwerkservice configureren voor een gebeurtenishub](event-hubs-service-endpoints.md)
- [Privéeindpunten configureren voor een gebeurtenishub](private-link-service.md)