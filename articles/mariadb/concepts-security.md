---
title: Beveiliging in Azure Database for MariaDB-één server
description: Een overzicht van de beveiligings functies in Azure Database for MariaDB-één server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 1a2d7ac449c131519473afaa80eb3a57c287eb28
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485183"
---
# <a name="security-in-azure-database-for-mariadb---single-server"></a>Beveiliging in Azure Database for MariaDB-één server

Er zijn meerdere beveiligings lagen die beschikbaar zijn om de gegevens op uw Azure Database for MariaDB-server te beveiligen. In dit artikel vindt u een overzicht van deze beveiligings opties.

## <a name="information-protection-and-encryption"></a>Gegevens beveiliging en-versleuteling

### <a name="in-transit"></a>In-transit
Azure Database for MariaDB uw gegevens beveiligen door in-transit gegevens te versleutelen met Transport Layer Security. Versleuteling (SSL/TLS) wordt standaard afgedwongen.

### <a name="at-rest"></a>Op rest
De Azure Database for MariaDB-service gebruikt de door FIPS 140-2 gevalideerde cryptografische module voor opslag versleuteling van gegevens in rust. Gegevens, met inbegrip van back-ups, worden versleuteld op schijf, met uitzonde ring van tijdelijke bestanden die worden gemaakt tijdens het uitvoeren van query's. De service maakt gebruik van de AES 256-bits code ring opgenomen in azure Storage-versleuteling en de sleutels worden beheerd door het systeem. Opslagversleuteling is altijd actief en kan niet worden uitgeschakeld.


## <a name="network-security"></a>Netwerkbeveiliging
Verbindingen met een Azure Database for MariaDB-server worden eerst gerouteerd via een regionale gateway. De gateway heeft een openbaar toegankelijk IP-adres, terwijl de IP-adressen van de server zijn beveiligd. Ga naar het artikel over de [connectiviteits architectuur](concepts-connectivity-architecture.md)voor meer informatie over de gateway.  

Een nieuw gemaakte Azure Database for MariaDB server heeft een firewall waarmee alle externe verbindingen worden geblokkeerd. Hoewel ze de gateway bereiken, zijn ze niet gemachtigd om verbinding te maken met de server. 

### <a name="ip-firewall-rules"></a>IP-firewall regels
IP-firewall regels verlenen toegang tot servers op basis van het oorspronkelijke IP-adres van elke aanvraag. Zie het [overzicht van firewall regels](concepts-firewall-rules.md) voor meer informatie.

### <a name="virtual-network-firewall-rules"></a>Firewallregels voor virtueel netwerk
Met Service-eind punten van een virtueel netwerk breidt u de connectiviteit van uw virtuele netwerk uit via de Azure-backbone. Met regels voor virtuele netwerken kunt u uw Azure Database for MariaDB-server in staat stellen verbindingen van geselecteerde subnetten in een virtueel netwerk toe te staan. Zie het overzicht van het [virtuele netwerk service-eind punt](concepts-data-access-security-vnet.md)voor meer informatie.


## <a name="access-management"></a>Toegangsbeheer

Tijdens het maken van de Azure Database for MariaDB-server geeft u referenties op voor een beheerder. Deze beheerder kan worden gebruikt om aanvullende MariaDB-gebruikers te maken.


## <a name="threat-protection"></a>Bescherming tegen bedreigingen

U kunt ervoor kiezen om [geavanceerde bedreigingen te beveiligen](concepts-data-access-and-security-threat-protection.md) , waarmee afwijkende activiteiten worden gedetecteerd die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot servers of aanvallen te maken.

[Controle logboek registratie](concepts-audit-logs.md) is beschikbaar om de activiteiten in uw data bases bij te houden. 


## <a name="next-steps"></a>Volgende stappen
- Firewall regels voor [IP-adressen](concepts-firewall-rules.md) of [virtuele netwerken](concepts-data-access-security-vnet.md) inschakelen