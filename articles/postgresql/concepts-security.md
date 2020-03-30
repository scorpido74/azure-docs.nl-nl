---
title: Beveiliging in Azure-database voor PostgreSQL - Enkele server
description: Een overzicht van de beveiligingsfuncties in Azure Database voor PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 02dc9e1ad9ee46b1a400e44b6ef737e70571a17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972591"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Beveiliging in Azure-database voor PostgreSQL - Enkele server

Er zijn meerdere beveiligingslagen beschikbaar om de gegevens op uw Azure Database voor PostgreSQL-server te beschermen. In dit artikel worden deze beveiligingsopties beschreven.

## <a name="information-protection-and-encryption"></a>Informatiebescherming en versleuteling

### <a name="in-transit"></a>In-transit
Azure Database voor PostgreSQL beveiligt uw gegevens door gegevens onderweg te versleutelen met Transport Layer Security. Versleuteling (SSL/TLS) wordt standaard afgedwongen.

### <a name="at-rest"></a>Rust
De Azure Database for PostgreSQL-service maakt gebruik van de fips 140-2 gevalideerde cryptografische module voor opslagversleuteling van gegevens in rust. Gegevens, inclusief back-ups, worden versleuteld op schijf, met uitzondering van tijdelijke bestanden die zijn gemaakt tijdens het uitvoeren van query's. De service maakt gebruik van de AES 256-bits cipher die is opgenomen in Azure-opslagversleuteling en de sleutels worden beheerd door het systeem. Opslagversleuteling is altijd actief en kan niet worden uitgeschakeld.


## <a name="network-security"></a>Netwerkbeveiliging
Verbindingen met een Azure Database voor PostgreSQL-server worden eerst via een regionale gateway doorgestuurd. De gateway heeft een openbaar toegankelijk IP, terwijl de IP-adressen van de server worden beveiligd. Ga voor meer informatie over de gateway naar het artikel over de [connectiviteitsarchitectuur.](concepts-connectivity-architecture.md)  

Een nieuw gemaakte Azure Database voor PostgreSQL-server heeft een firewall die alle externe verbindingen blokkeert. Hoewel ze de gateway bereiken, mogen ze geen verbinding maken met de server. 

### <a name="ip-firewall-rules"></a>IP-firewallregels
IP-firewallregels verlenen toegang tot servers op basis van het oorspronkelijke IP-adres van elk verzoek. Zie het [overzicht van firewallregels](concepts-firewall-rules.md) voor meer informatie.

### <a name="virtual-network-firewall-rules"></a>Firewallregels voor virtueel netwerk
Eindpunten voor virtuele netwerkservices breiden uw virtuele netwerkconnectiviteit uit via de Azure-backbone. Met behulp van virtuele netwerkregels u uw Azure Database voor PostgreSQL-server inschakelen om verbindingen toe te staan van geselecteerde subnetten in een virtueel netwerk. Zie het eindpuntoverzicht van de [service voor virtuele netwerkservices](concepts-data-access-and-security-vnet.md)voor meer informatie.

### <a name="private-ip"></a>Privé IP-adres
Met Private Link u verbinding maken met uw Azure Database voor PostgreSQL Single-server in Azure via een privéeindpunt. Azure Private Link brengt in wezen Azure-services binnen uw privé-virtuele netwerk (VNet). De PaaS-bronnen zijn toegankelijk via het privé-IP-adres, net als elke andere bron in het VNet. Zie voor meer informatie het overzicht van de [privékoppeling](concepts-data-access-and-security-private-link.md)


## <a name="access-management"></a>Toegangsbeheer

Tijdens het maken van de Azure Database voor PostgreSQL-server geeft u referenties op voor een beheerdersrol. Deze beheerdersrol kan worden gebruikt om extra [PostgreSQL-rollen](https://www.postgresql.org/docs/current/user-manag.html)te maken.

U ook verbinding maken met de server met [Azure Active Directory (AAD)-verificatie.](concepts-aad-authentication.md)


## <a name="threat-protection"></a>Bescherming tegen bedreigingen

U zich aanmelden voor [Advanced Threat Protection,](concepts-data-access-and-security-threat-protection.md) dat afwijkende activiteiten detecteert die ongebruikelijke en mogelijk schadelijke pogingen om servers te openen of te exploiteren, aangeeft.

[Auditlogging](concepts-audit.md) is beschikbaar om activiteiten in uw databases bij te houden. 


## <a name="next-steps"></a>Volgende stappen
- Firewallregels voor [IP's](concepts-firewall-rules.md) of [virtuele netwerken inschakelen](concepts-data-access-and-security-vnet.md)
- Meer informatie over [Azure Active Directory-verificatie](concepts-aad-authentication.md) in Azure Database voor PostgreSQL
