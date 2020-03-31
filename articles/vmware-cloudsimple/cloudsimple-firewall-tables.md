---
title: Azure VMware-oplossing door CloudSimple - Firewalltabellen
description: Meer informatie over CloudSimple private cloud firewall tabellen en firewall regels.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 89bef6cef48f2b972aa3f931008b0db84431b832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025041"
---
# <a name="firewall-tables-overview"></a>Overzicht van firewalltabellen

In een firewalltabel worden regels weergegeven voor het filteren van netwerkverkeer van en naar Private Cloud-bronnen. U firewalltabellen toepassen op een VLAN/subnet. De regels regelen het netwerkverkeer tussen een bronnetwerk of IP-adres en een doelnetwerk of IP-adres.

## <a name="firewall-rules"></a>Firewall-regels

In de volgende tabel worden de parameters in een firewallregel beschreven.

| Eigenschap | Details |
| ---------| --------|
| **Naam** | Een naam die op unieke wijze de firewallregel en het doel ervan identificeert. |
| **Prioriteit** | Een getal tussen 100 en 4096, met 100 als hoogste prioriteit. Regels worden in prioriteitsvolgorde verwerkt. Wanneer verkeer een regelovereenkomst tegenkomt, wordt de regelverwerking gestopt. Als gevolg hiervan worden regels met lagere prioriteiten met dezelfde kenmerken als regels met hogere prioriteiten niet verwerkt.  Zorg ervoor dat er geen tegenstrijdige regels zijn. |
| **Staatstracking** | Tracking kan stateloos zijn (Private Cloud, Internet of VPN) of stateful (Public IP).  |
| **Protocol** | Opties zijn Any, TCP of UDP. Als je ICMP nodig hebt, gebruik dan Any. |
| **Richting** | Hiermee wordt aangegeven of de regel van toepassing is op binnenkomend of uitgaand verkeer. |
| **Actie** | Toestaan of weigeren voor het type verkeer dat in de regel is gedefinieerd. |
| **Bron** | Een IP-adres, cidr-blok (classless inter-domain routing) (bijvoorbeeld 10.0.0.0/24) of Any.  Als u een bereik, een servicetag of toepassingsbeveiligingsgroep opgeeft, u minder beveiligingsregels maken. |
| **Bronpoort** | Haven waaruit netwerkverkeer afkomstig is.  U een afzonderlijke poort of bereik van poorten opgeven, zoals 443 of 8000-8080. Als u bereiken opgeeft, hoeft u minder beveiligingsregels te maken. |
| **Bestemming** | Een IP-adres, cidr-blok (classless inter-domain routing) (bijvoorbeeld 10.0.0.0/24) of Any.  Als u een bereik, een servicetag of toepassingsbeveiligingsgroep opgeeft, u minder beveiligingsregels maken.  |
| **Bestemmingspoort** | Poort waarnaar het netwerkverkeer stroomt.  U een afzonderlijke poort of bereik van poorten opgeven, zoals 443 of 8000-8080. Als u bereiken opgeeft, hoeft u minder beveiligingsregels te maken.|

### <a name="stateless"></a>Stateless

Een staatloze regel kijkt alleen naar afzonderlijke pakketten en filtert ze op basis van de regel.  
Er kunnen aanvullende regels nodig zijn voor de doorstroming van het verkeer in de omgekeerde richting.  Gebruik staatloze regels voor verkeer tussen de volgende punten:

* Subnetten van private clouds
* On-premises subnet en een Private Cloud-subnet
* Internetverkeer vanuit de Private Clouds

### <a name="stateful"></a>Stateful

 Een stateful regel is zich bewust van de verbindingen die er doorheen gaan. Voor bestaande verbindingen wordt een stroomrecord gemaakt. Communicatie wordt toegestaan of geweigerd op basis van de verbindingsstatus van de stroomrecord.  Gebruik dit regeltype voor openbare IP-adressen om verkeer van internet te filteren.

### <a name="default-rules"></a>Standaardregels

In elke firewalltabel worden de volgende standaardregels gemaakt.

|Prioriteit|Name|Staatstracking|Richting|Verkeerstype|Protocol|Bron|Bronpoort|Doel|Doelpoort|Actie|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|Stateful|Uitgaand|Openbaar IP- of internetverkeer|Alle|Alle|Alle|Alle|Alle|Toestaan|
|65001|deny-all-from-internet|Stateful|Inkomend|Openbaar IP- of internetverkeer|Alle|Alle|Alle|Alle|Alle|Weigeren|
|65002|allow-all-to-intranet|Stateless|Uitgaand|Privécloud intern of VPN-verkeer|Alle|Alle|Alle|Alle|Alle|Toestaan|
|65003|allow-all-from-intranet|Stateless|Inkomend|Privécloud intern of VPN-verkeer|Alle|Alle|Alle|Alle|Alle|Toestaan|

## <a name="next-steps"></a>Volgende stappen

* [Firewalltabellen en -regels instellen](firewall.md)
