---
title: Beveiliging - Azure-database voor MariaDB
description: Een overzicht van de beveiligingsfuncties in Azure Database voor MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8f41fe1005e96b428337bc73b9d468962a079596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527823"
---
# <a name="security-in-azure-database-for-mariadb"></a>Beveiliging in Azure Database for MariaDB

Er zijn meerdere beveiligingslagen beschikbaar om de gegevens op uw Azure-database voor MariaDB-server te beschermen. In dit artikel worden deze beveiligingsopties beschreven.

## <a name="information-protection-and-encryption"></a>Informatiebescherming en versleuteling

### <a name="in-transit"></a>In-transit
Azure Database voor MariaDB beveiligt uw gegevens door gegevens onderweg te versleutelen met Transport Layer Security. Versleuteling (SSL/TLS) wordt standaard afgedwongen.

### <a name="at-rest"></a>Rust
De Azure Database for MariaDB-service maakt gebruik van de fips 140-2 gevalideerde cryptografische module voor opslagversleuteling van gegevens in rust. Gegevens, inclusief back-ups, worden versleuteld op schijf, met uitzondering van tijdelijke bestanden die zijn gemaakt tijdens het uitvoeren van query's. De service maakt gebruik van de AES 256-bits cipher die is opgenomen in Azure-opslagversleuteling en de sleutels worden beheerd door het systeem. Opslagversleuteling is altijd actief en kan niet worden uitgeschakeld.


## <a name="network-security"></a>Netwerkbeveiliging
Verbindingen met een Azure Database voor MariaDB-server worden eerst via een regionale gateway doorgestuurd. De gateway heeft een openbaar toegankelijk IP, terwijl de IP-adressen van de server worden beveiligd. Ga voor meer informatie over de gateway naar het artikel over de [connectiviteitsarchitectuur.](concepts-connectivity-architecture.md)  

Een nieuw gemaakte Azure Database voor MariaDB-server heeft een firewall die alle externe verbindingen blokkeert. Hoewel ze de gateway bereiken, mogen ze geen verbinding maken met de server. 

### <a name="ip-firewall-rules"></a>IP-firewallregels
IP-firewallregels verlenen toegang tot servers op basis van het oorspronkelijke IP-adres van elk verzoek. Zie het [overzicht van firewallregels](concepts-firewall-rules.md) voor meer informatie.

### <a name="virtual-network-firewall-rules"></a>Firewallregels voor virtueel netwerk
Eindpunten voor virtuele netwerkservices breiden uw virtuele netwerkconnectiviteit uit via de Azure-backbone. Met behulp van virtuele netwerkregels u uw Azure Database voor MariaDB-server inschakelen om verbindingen toe te staan van geselecteerde subnetten in een virtueel netwerk. Zie het eindpuntoverzicht van de [service voor virtuele netwerkservices](concepts-data-access-security-vnet.md)voor meer informatie.


## <a name="access-management"></a>Toegangsbeheer

Tijdens het maken van de Azure Database voor MariaDB-server geeft u referenties op voor een beheerdersgebruiker. Deze beheerder kan worden gebruikt om extra MariaDB-gebruikers te maken.


## <a name="threat-protection"></a>Bescherming tegen bedreigingen

U zich aanmelden voor [Advanced Threat Protection,](concepts-data-access-and-security-threat-protection.md) dat afwijkende activiteiten detecteert die ongebruikelijke en mogelijk schadelijke pogingen om servers te openen of te exploiteren, aangeeft.

[Auditlogging](concepts-audit-logs.md) is beschikbaar om activiteiten in uw databases bij te houden. 


## <a name="next-steps"></a>Volgende stappen
- Firewallregels voor [IP's](concepts-firewall-rules.md) of [virtuele netwerken inschakelen](concepts-data-access-security-vnet.md)