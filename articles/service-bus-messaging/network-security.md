---
title: Netwerkbeveiliging voor Azure Service Bus
description: In dit artikel worden netwerkbeveiligingsfuncties beschreven, zoals servicetags, IP-firewallregels, serviceeindpunten en privéeindpunten.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: aschhab
ms.openlocfilehash: 95f8c2a3b47b59bab7df909be43dacdb1f9c58f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479278"
---
# <a name="network-security-for-azure-service-bus"></a>Netwerkbeveiliging voor Azure Service Bus 
In dit artikel wordt beschreven hoe u de volgende beveiligingsfuncties gebruiken met Azure Service Bus: 

- Servicetags
- IP Firewall-regels
- Eindpunten van netwerkservice
- Privéeindpunten (voorbeeld)


## <a name="service-tags"></a>Servicetags
Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels van een bepaalde Azure-service. Microsoft beheert de adresvoorvoegsels die zijn gebruikt door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen, waardoor de complexiteit van frequente updates voor netwerkbeveiligingsregels wordt geminimaliseerd. Zie [Overzicht servicetags voor](../virtual-network/service-tags-overview.md)meer informatie over servicetags.

U servicetags gebruiken om netwerktoegangsbesturingselementen te definiëren in [netwerkbeveiligingsgroepen](../virtual-network/security-overview.md#security-rules) of [Azure Firewall.](../firewall/service-tags.md) Gebruik servicetags in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld **ServiceBus)** op te geven in het juiste *bron-* of *bestemmingsveld* van een regel, u het verkeer voor de desbetreffende service toestaan of weigeren.

| Servicetag | Doel | Kan u in-komende of uitgaande gebruiken? | Kan regionaal zijn? | Kan u gebruik maken van Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | Azure Service Bus-verkeer dat de Premium-servicelaag gebruikt. | Uitgaand | Ja | Ja |


## <a name="ip-firewall"></a>IP-firewall 
Standaard zijn naamruimten van servicebus toegankelijk vanaf internet, zolang de aanvraag wordt geleverd met geldige verificatie en autorisatie. Met IP-firewall u deze verder beperken tot alleen een set IPv4-adressen of IPv4-adresbereiken in [CIDR-notatie (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Deze functie is handig in scenario's waarin Azure Service Bus alleen toegankelijk mag zijn vanaf bepaalde bekende sites. Met firewallregels u regels configureren om verkeer te accepteren dat afkomstig is van specifieke IPv4-adressen. Als u bijvoorbeeld Service Bus gebruikt met [Azure Express Route][express-route], u een **firewallregel** maken om verkeer toe te staan vanaf alleen uw on-premises infrastructuur IP-adressen of adressen van een bedrijfsNAT-gateway. 

De IP-firewallregels worden toegepast op het naamruimteniveau servicebus. Daarom zijn de regels van toepassing op alle verbindingen van clients die een ondersteund protocol gebruiken. Elke verbindingspoging vanaf een IP-adres dat niet overeenkomt met een toegestane IP-regel op de naamruimte servicebus, wordt afgewezen als ongeautoriseerd. In het antwoord wordt geen IP-regel vermeld. IP-filterregels worden op volgorde toegepast en de eerste regel die overeenkomt met het IP-adres bepaalt de actie Accepteren of weigeren.

Zie [IP-firewall configureren voor een naamruimte van servicebus voor](service-bus-ip-filtering.md) meer informatie

## <a name="network-service-endpoints"></a>Eindpunten van netwerkservice
De integratie van Service Bus met [VNet-serviceeindpunten (Virtual Network)](service-bus-service-endpoints.md) maakt veilige toegang tot berichtenmogelijkheden mogelijk van workloads zoals virtuele machines die gebonden zijn aan virtuele netwerken, waarbij het netwerkverkeer pad aan beide uiteinden wordt beveiligd.

Zodra de naamruimte van de servicebus is geconfigureerd om te zijn gekoppeld aan ten minste één eindpunt van de virtuele netwerksubnetservice, accepteert de desbetreffende servicebusnaamruimte geen verkeer meer van overal, maar van geautoriseerd virtueel netwerk(en). Vanuit het perspectief van het virtuele netwerk configureert het binden van een servicebusnaamruimte aan een serviceeindpunt een geïsoleerde netwerktunnel van het virtuele netwerksubnet naar de berichtenservice.

Het resultaat is een privé- en geïsoleerde relatie tussen de workloads die aan het subnet zijn gebonden en de respectievelijke naamruimte van servicebus, ondanks het waarneembare netwerkadres van het eindpunt van de berichtendienst dat zich in een openbaar IP-bereik bevindt.

> [!IMPORTANT]
> Virtuele netwerken worden alleen ondersteund in naamruimten [van Premium-servicebus.](service-bus-premium-messaging.md)
> 
> Wanneer u VNet-serviceeindpunten gebruikt met Service Bus, moet u deze eindpunten niet inschakelen in toepassingen die standaard- en Premium-servicebusnaamruimten combineren. Omdat standaardlaag geen VNets ondersteunt. Het eindpunt is beperkt tot alleen naamruimten voor premiumlagen.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Geavanceerde beveiligingsscenario's mogelijk gemaakt door VNet-integratie 

Oplossingen die strakke en gecompartimenteerde beveiliging vereisen en waar virtuele netwerksubnetten de segmentatie tussen de gecompartimenteerde services bieden, hebben over het algemeen nog steeds communicatiepaden nodig tussen diensten die in die compartimenten aanwezig zijn.

Elke directe IP-route tussen de compartimenten, inclusief die met HTTPS via TCP/IP, brengt het risico met zich mee dat kwetsbaarheden vanaf de netwerklaag worden gebruikt. Messaging-diensten bieden volledig geïsoleerde communicatiepaden, waar berichten zelfs worden geschreven op schijf als ze de overgang tussen partijen. Workloads in twee afzonderlijke virtuele netwerken die beide gebonden zijn aan dezelfde Service Bus-instantie, kunnen efficiënt en betrouwbaar communiceren via berichten, terwijl de respectieve integriteit van de netwerkisolatiegrens behouden blijft.
 
Dat betekent dat uw beveiligingsgevoelige cloudoplossingen niet alleen toegang krijgen tot azure toonaangevende betrouwbare en schaalbare asynchrone messaging-mogelijkheden, maar ze kunnen nu messaging gebruiken om communicatiepaden te maken tussen beveiligde oplossingscompartimenten die zijn inherent veiliger dan wat haalbaar is met een peer-to-peer communicatiemodus, inclusief HTTPS en andere TLS-beveiligde socketprotocollen.

### <a name="bind-service-bus-to-virtual-networks"></a>Servicebus binden aan virtuele netwerken

*Virtuele netwerkregels* zijn de beveiligingsfunctie voor firewalls die bepaalt of uw Azure Service Bus-server verbindingen van een bepaald virtueel netwerksubnet accepteert.

Het binden van een naamruimte van een servicebus aan een virtueel netwerk is een proces in twee stappen. U moet eerst een eindpunt van de **virtual network-service** maken op een subnet van een virtueel netwerk en het inschakelen voor **Microsoft.ServiceBus,** zoals uitgelegd in het [eindpuntoverzicht van](service-bus-service-endpoints.md)de service. Zodra u het serviceeindpunt hebt toegevoegd, bindt u de naamruimte servicebus eraan met een **virtuele netwerkregel**.

De virtuele netwerkregel is een koppeling van de naamruimte servicebus met een virtueel netwerksubnet. Hoewel de regel bestaat, krijgen alle workloads die aan het subnet zijn gebonden, toegang tot de naamruimte servicebus. Service Bus zelf maakt nooit uitgaande verbindingen, hoeft geen toegang te krijgen en krijgt daarom nooit toegang tot uw subnet door deze regel in te schakelen.

Zie [Eindpunten voor virtuele netwerkservice configureren voor een naamruimte voor servicebus](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>Privéeindpunten

Azure Private Link Service stelt u in staat om toegang te krijgen tot Azure-services (bijvoorbeeld Azure Service Bus, Azure Storage en Azure Cosmos DB) en Azure gehoste klant-/partnerservices via een **privéeindpunt** in uw virtuele netwerk.

Een privéeindpunt is een netwerkinterface die u privé en veilig verbindt met een service die wordt aangedreven door Azure Private Link. Het privéeindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service effectief in uw VNet wordt opgenomen. Al het verkeer naar de service kan worden doorgestuurd via het privéeindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute- of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U verbinding maken met een instantie van een Azure-bron, zodat u het hoogste niveau van granulariteit in toegangscontrole hebt.

Zie Wat is Azure Private Link voor meer [informatie?](../private-link/private-link-overview.md)

> [!NOTE]
> Deze functie wordt ondersteund met de **premiumlaag** van Azure Service Bus. Zie het artikel Service Bus [Premium en Standard messaging-lagen](service-bus-premium-messaging.md) voor meer informatie over de premiumlaag.
>
> Deze functie is momenteel in **preview**. 


Zie [Privéeindpunten configureren voor een naamruimte servicebus voor](private-link-service.md) meer informatie


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [IP-firewall configureren voor een naamruimte van servicebus](service-bus-ip-filtering.md)
- [Eindpunten voor virtuele netwerkservice configureren voor een naamruimte van een servicebus](service-bus-service-endpoints.md)
- [Privéeindpunten configureren voor een naamruimte van een servicebus](private-link-service.md)
