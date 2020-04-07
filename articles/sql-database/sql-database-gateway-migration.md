---
title: Waarschuwing voor migratie van gatewayverkeer
description: Artikel biedt kennisgeving aan gebruikers over de migratie van IP-adressen van Azure SQL Database Gateways
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 9e3c33bb7493f07d9fbf19710f21d0114e7abec8
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757057"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migratie van Azure SQL Database-verkeer naar nieuwere gateways

Naarmate de Azure-infrastructuur verbetert, zal Microsoft regelmatig hardware vernieuwen om ervoor te zorgen dat we de best mogelijke klantervaring bieden. In de komende maanden zijn we van plan om Gateways toe te voegen die zijn gebouwd op nieuwere hardwaregeneraties, verkeer naar hen te migreren en uiteindelijk gateways te ontmantelen die in sommige regio's zijn gebouwd op oudere hardware.  

Klanten worden ruim van tevoren op de hoogte gesteld van elke wijziging in gateways die in elke regio beschikbaar zijn, via e-mail en in de Azure-portal. De meest actuele informatie wordt bewaard in de [ip-adressentabel azure SQL Database-gateway.](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)

## <a name="impact-of-this-change"></a>Impact van deze verandering

De eerste ronde van verkeersmigratie naar nieuwere gateways is gepland voor **14 oktober 2019** in de volgende regio's:
- Brazilië - zuid
- VS - west
- Europa -west
- VS - oost
- VS - centraal
- Azië - zuidoost
- VS - zuid-centraal
- Europa - noord
- VS - noord-centraal
- Japan - west
- Japan - oost
- VS - oost 2
- Azië - oost

De verkeersmigratie wijzigt het openbare IP-adres dat DNS voor uw SQL-database oplost.
U wordt beïnvloed als u:
- Hard gecodeerd het IP-adres voor een bepaalde Gateway in uw on-premises firewall
- Subnetten die Microsoft.SQL als Service Endpoint gebruiken, maar niet kunnen communiceren met de IP-adressen van de Gateway

U zult geen invloed hebben als u:
- Omleiding als verbindingsbeleid
- Verbindingen met SQL-database vanuit Azure en met servicetags
- Verbindingen die zijn gemaakt met ondersteunde versies van JDBC Driver voor SQL Server, hebben geen invloed. Zie [Microsoft JDBC Driver downloaden voor SQL Server voor](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)ondersteunde JDBC-versies.

## <a name="what-to-do-you-do-if-youre-affected"></a>Wat te doen als je getroffen bent

We raden u aan uitgaand verkeer toe te staan naar IP-adressen voor alle IP-adressen van de [Azure SQL Database-gateway](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) in de regio op TCP-poort 1433 en poortbereik 11000-11999. Deze aanbeveling is van toepassing op klanten die verbinding maken vanuit on-premises en ook op klanten die verbinding maken via Service Endpoints. Zie [Verbindingsbeleid voor](sql-database-connectivity-architecture.md#connection-policy)meer informatie over poortbereiken .

Verbindingen die zijn gemaakt met toepassingen met Microsoft JDBC Driver onder versie 4.0, mislukken mogelijk de validatie van certificaten. Lagere versies van Microsoft JDBC zijn gebaseerd op Common Name (CN) in het veld Onderwerp van het certificaat. De beperking is om ervoor te zorgen dat de eigenschap hostNameInCertificate is ingesteld op *.database.windows.net. Zie [Verbinding maken met versleuteling](/sql/connect/jdbc/connecting-with-ssl-encryption)voor meer informatie over het instellen van de eigenschap hostNameInCertificate.

Als de bovenstaande beperking niet werkt, dient u een ondersteuningsverzoek voor SQL Database in met de volgende URL:https://aka.ms/getazuresupport

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure SQL Connectivity Architecture](sql-database-connectivity-architecture.md)
