---
title: Kennisgeving van migratie van Gateway verkeer voor Azure SQL Database
description: Artikel geeft gebruikers informatie over de migratie van IP-adressen van Azure SQL Database gateways
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 37c6c8e0f26d0281c3f6e76cb891fe14eb2ef04e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690003"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database verkeer migratie naar nieuwere gateways

Naarmate de Azure-infra structuur wordt verbeterd, zal micro soft hardware periodiek vernieuwen om ervoor te zorgen dat we de best mogelijke klant ervaring bieden. In de komende maanden kunnen we gateways toevoegen die zijn gebouwd op nieuwere hardware-generaties, verkeer naar hen migreren en uiteindelijk buiten gebruik stellen van gateways die zijn gebouwd op oudere hardware in sommige regio's.  

Klanten worden op de hoogte gesteld via e-mail en in het Azure Portal goed van elke wijziging aan gateways die beschikbaar zijn in elke regio. De meest recente informatie wordt bewaard in de tabel met [IP-adressen van de Azure SQL database gateway](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) .

## <a name="impact-of-this-change"></a>Impact van deze wijziging

De eerste afronding van verkeer migratie naar nieuwere gateways is gepland voor **14 oktober 2019** in de volgende regio's:
- Brazilië - zuid
- US - west
- Europa -west
- US - oost
- US - centraal
- Azië - zuidoost
- US - zuid-centraal
- Europa - noord
- US - noord-centraal
- Japan - west
- Japan - oost
- US - oost 2
- Azië - oost

Bij de migratie van verkeer wordt het open bare IP-adres gewijzigd dat DNS voor uw SQL Database verhelpt.
U hebt de volgende gevolgen:
- Het IP-adres voor een bepaalde gateway in uw on-premises firewall is vastgelegd.
- Subnetten die gebruikmaken van micro soft. SQL als een service-eind punt, maar niet kunnen communiceren met de IP-adressen van de gateway

U hebt de volgende gevolgen:
- Omleiding als verbindings beleid
- Verbindingen met SQL Database van binnen Azure en met behulp van service Tags
- Verbindingen die zijn gemaakt met ondersteunde versies van het JDBC-stuur programma voor SQL Server, zien geen invloed. Zie [micro soft JDBC-stuur programma voor SQL Server downloaden](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)voor een ondersteunde versie van JDBC.

## <a name="what-to-do-you-do-if-youre-affected"></a>Wat u moet doen als dit van invloed is

We raden u aan om uitgaand verkeer naar IP-adressen toe te staan voor alle [IP-adressen van de Azure SQL database gateway](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) in de regio op TCP-poort 1433 en poort bereik 11000-11999. Deze aanbeveling is van toepassing op clients die vanaf on-premises verbinding maken en die verbinding maken via service-eind punten. Zie [verbindings beleid](sql-database-connectivity-architecture.md#connection-policy)voor meer informatie over poortbereiken.

Voor verbindingen die zijn gemaakt via toepassingen met het micro soft JDBC-stuur programma onder versie 4,0, kan het certificaat niet worden gevalideerd. Lagere versies van micro soft JDBC zijn afhankelijk van de algemene naam (CN) in het veld onderwerp van het certificaat. De beperking is om ervoor te zorgen dat de eigenschap hostNameInCertificate is ingesteld op *. database.windows.net. Zie [verbinding maken met SSL-versleuteling](/sql/connect/jdbc/connecting-with-ssl-encryption)voor meer informatie over het instellen van de eigenschap hostNameInCertificate.

Als de bovenstaande oplossing niet werkt, kunt u een ondersteunings aanvraag voor SQL Database indienen via de volgende URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure SQL-connectiviteits architectuur](sql-database-connectivity-architecture.md)
