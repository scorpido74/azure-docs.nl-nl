---
title: Overzicht van reverse DNS in Azure - Azure DNS
description: Ga in dit leerpad aan de slag met het leren hoe reverse DNS werkt en hoe het kan worden gebruikt in Azure
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: bf3da62e989f0e029efdc8e9c70f5f45e0ddd765
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932302"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Overzicht van reverse DNS en ondersteuning in Azure

In dit artikel vindt u een overzicht van hoe reverse DNS werkt en de omgekeerde DNS-scenario's die in Azure worden ondersteund.

## <a name="what-is-reverse-dns"></a>Wat is reverse DNS?

Met conventionele DNS-records kan een toewijzing van een DNS-naam (zoals 'www.contoso.com') naar een IP-adres (zoals 64.4.6.100) worden in kaart gebracht.  Reverse DNS maakt de vertaling van een IP-adres (64.4.6.100) terug naar een naam ('www.contoso.com').

Reverse DNS-records worden in verschillende situaties gebruikt. Reverse DNS-records worden bijvoorbeeld veel gebruikt bij de bestrijding van e-mailspam door de afzender van een e-mailbericht te verifiëren.  De ontvangende e-mailserver haalt de omgekeerde DNS-record van het IP-adres van de verzendende server op en controleert of die host is gemachtigd om e-mail te verzenden vanuit het oorspronkelijke domein. 

## <a name="how-reverse-dns-works"></a>Hoe reverse DNS werkt

Reverse DNS-records worden gehost in speciale DNS-zones, bekend als 'ARPA'-zones.  Deze zones vormen een aparte DNS-hiërarchie parallel met de normale hiërarchie hosting domeinen zoals 'contoso.com'.

Zo wordt de DNS-record 'www.contoso.com' geïmplementeerd met behulp van een DNS 'A'-record met de naam 'www' in de zone 'contoso.com'.  Dit A-record wijst naar het overeenkomstige IP-adres, in dit geval 64.4.6.100.  De reverse lookup wordt afzonderlijk geïmplementeerd, met behulp van een 'PTR' record genaamd '100' in de zone '6.4.64.in-addr.arpa' (merk op dat IP-adressen worden omgekeerd in ARPA-zones.)  Deze PTR-record, als deze correct is geconfigureerd, verwijst naar de naam 'www.contoso.com'.

Wanneer een organisatie een IP-adresblok toegewezen krijgt, krijgen ze ook het recht om de bijbehorende ARPA-zone te beheren. De ARPA-zones die overeenkomen met de IP-adresblokken die door Azure worden gebruikt, worden gehost en beheerd door Microsoft. Uw INTERNETPROVIDER kan de ARPA-zone voor uw eigen IP-adressen voor u hosten of u toestaan de ARPA-zone te hosten in een DNS-service van uw keuze, zoals Azure DNS.

> [!NOTE]
> Forward DNS lookups en reverse DNS lookups worden geïmplementeerd in afzonderlijke, parallelle DNS-hiërarchieën. De omgekeerde lookup voor 'www.contoso.com' wordt **niet** gehost in de zone 'contoso.com', maar wordt gehost in de ARPA-zone voor het bijbehorende IP-adresblok. Afzonderlijke zones worden gebruikt voor IPv4- en IPv6-adresblokken.

### <a name="ipv4"></a>IPv4

De naam van een Omgekeerde opzoekzone van IPv4 moet in de volgende indeling staan: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Wanneer u bijvoorbeeld een omgekeerde zone maakt voor het hosten van records voor hosts met IP's die zich in het voorvoegsel 192.0.2.0/24 bevinden, wordt de zonenaam gemaakt door het netwerkvoorvoegsel `.in-addr.arpa`van het adres (192.0.2) te isoleren en vervolgens de volgorde om te keren (2.0.192) en het achtervoegsel toe te voegen .

|Subnet, klasse|Netwerkvoorvoegsel  |Omgekeerd netwerkvoorvoegsel  |Standaard achtervoegsel  |Naam van omgekeerde zone |
|-------|----------------|------------|-----------------|---------------------------|
|Klasse A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Klasse B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Klasse C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Klasseloze IPv4-delegatie

In sommige gevallen is het IP-bereik dat aan een organisatie is toegewezen kleiner dan een klasse C -bereik (/24). In dit geval valt het IP-bereik niet `.in-addr.arpa` op een zonegrens binnen de zonehiërarchie en kan het dus niet worden gedelegeerd als onderliggende zone.

In plaats daarvan wordt een ander mechanisme gebruikt om de controle over individuele reverse lookup (PTR)-records over te dragen naar een speciale DNS-zone. Met dit mechanisme wordt een onderliggende zone voor elk IP-bereik gedelegeerd en wordt elk IP-adres in het bereik afzonderlijk toegewezen aan die onderliggende zone met behulp van CNAME-records.

Stel dat een organisatie het IP-bereik 192.0.2.128/26 van de ISP krijgt. Dit komt neer op 64 IP-adressen, van 192.0.2.128 tot 192.0.2.191. Reverse DNS voor dit bereik wordt als volgt geïmplementeerd:
- De organisatie maakt een omgekeerde lookup zone genaamd 128-26.2.0.192.in-addr.arpa. Het voorvoegsel '128-26' vertegenwoordigt het netwerksegment dat is toegewezen aan de organisatie binnen het bereik van klasse C (/24).
- De ISP maakt NS-records om de DNS-delegatie voor de bovenstaande zone in te stellen vanuit de bovenliggende zone klasse C. Het maakt ook CNAME-records in de bovenliggende reverse lookupzone (klasse C) en brengt elk IP-adres in het IP-bereik in kaart aan de nieuwe zone die door de organisatie is gemaakt:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- De organisatie beheert vervolgens de afzonderlijke PTR-records binnen hun onderliggende zone.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Een omgekeerde lookup voor het IP-adres '192.0.2.129' query's voor een PTR-record met de naam '129.2.0.192.in-addr.arpa'. Deze query wordt via de CNAME in de bovenliggende zone naar de PTR-record in de onderliggende zone opgelost.

### <a name="ipv6"></a>IPv6

De naam van een omgekeerde opzoekzone van IPv6 moet in de volgende vorm staan:`<IPv6 network prefix in reverse order>.ip6.arpa`

Bijvoorbeeld,. Bij het maken van een omgekeerde zone voor het hosten van records voor hosts met IP's die zich in het voorvoegsel 2001:db8:1000:abdc::/64 bevinden, wordt de zonenaam gemaakt door het netwerkvoorvoegsel van het adres te isoleren (2001:db8:abdc::). Vervolgens het IPv6-netwerkvoorvoegsel uitbreiden om [nulcompressie](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx)te verwijderen, als het werd gebruikt om het IPv6-adresvoorvoegsel in te korten (2001:0db8:abdc:0000::). Draai de volgorde om, met behulp van een periode als de scheidingsteken tussen elk hexadecimaal getal in het voorvoegsel, om het omgekeerde netwerkvoorvoegsel (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) te bouwen en het achtervoegsel `.ip6.arpa`toe te voegen .


|Netwerkvoorvoegsel  |Uitgevouwen en omgekeerde netwerkvoorvoegsel |Standaard achtervoegsel |Naam van omgekeerde zone  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Azure-ondersteuning voor reverse DNS

Azure ondersteunt twee afzonderlijke scenario's met betrekking tot reverse DNS:

**Hosting van de omgekeerde opzoekzone die overeenkomt met uw IP-adresblok.**
Azure DNS kan worden gebruikt om [uw omgekeerde opzoekzones te hosten en de PTR-records te beheren voor elke reverse DNS-lookup](dns-reverse-dns-hosting.md), voor zowel IPv4 als IPv6.  Het proces van het maken van de arpa-zone (reverse lookup), het instellen van de delegatie en het configureren van PTR-records is hetzelfde als voor reguliere DNS-zones.  De enige verschillen zijn dat de delegatie moet worden geconfigureerd via uw ISP in plaats van uw DNS-registrar, en alleen de PTR-record type moet worden gebruikt.

**Configureer de omgekeerde DNS-record voor het IP-adres dat is toegewezen aan uw Azure-service.** Azure stelt u in staat om [de omgekeerde opzoeking te configureren voor de IP-adressen die zijn toegewezen aan uw Azure-service.](dns-reverse-dns-for-azure-services.md)  Deze omgekeerde lookup wordt door Azure geconfigureerd als een PTR-record in de bijbehorende ARPA-zone.  Deze ARPA-zones, die overeenkomen met alle IP-bereiken die door Azure worden gebruikt, worden gehost door Microsoft

## <a name="next-steps"></a>Volgende stappen

Zie [reverse DNS lookup op Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)voor meer informatie over reverse DNS.
<br>
Meer informatie over het [hosten van de omgekeerde opzoekzone voor uw IP-bereik dat is toegewezen in Azure DNS.](dns-reverse-dns-for-azure-services.md)
<br>
Meer informatie over het [beheren van reverse DNS-records voor uw Azure-services](dns-reverse-dns-for-azure-services.md).

