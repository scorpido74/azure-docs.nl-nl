---
title: Kennisgeving van migratie van Gateway verkeer
description: Artikel geeft gebruikers informatie over de migratie van IP-adressen van Azure SQL Database gateways
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1 
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 30e57736d0b0e40eb01573d6acca2c618dcf6ee3
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87759696"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database verkeer migratie naar nieuwere gateways
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Naarmate de Azure-infra structuur wordt verbeterd, zal micro soft hardware periodiek vernieuwen om ervoor te zorgen dat we de best mogelijke klant ervaring bieden. In de komende maanden kunnen we gateways toevoegen die zijn gebouwd op nieuwere hardware-generaties, verkeer naar hen migreren en uiteindelijk buiten gebruik stellen van gateways die zijn gebouwd op oudere hardware in sommige regio's.  

Klanten worden op de hoogte gesteld via e-mail en in het Azure Portal goed van elke wijziging aan gateways die beschikbaar zijn in elke regio. De meest recente informatie wordt bewaard in de tabel met [IP-adressen van de Azure SQL database gateway](connectivity-architecture.md#gateway-ip-addresses) .

## <a name="status-updates"></a>Status updates

# <a name="in-progress"></a>[Actief](#tab/in-progress-ip)
### <a name="september-2020"></a>September 2020

Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's. Deze SQL-gateways beginnen het accepteren van klant verkeer op 10 september 2020:

- West-Centraal VS: 13.78.248.43 
- Zuid-Afrika-noord: 102.133.120.2  

Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's. Deze SQL-gateways beginnen het accepteren van klant verkeer op 1 september 2020:

- Europa-noord: 13.74.104.113 
- West-VS2:40.78.248.10 
- Europa-west: 52.236.184.163 
- Zuid-Centraal VS: 20.45.121.1, 20.49.88.1 

Bestaande SQL-gateways gaan het verkeer accepteren in de volgende regio's. Deze SQL-gateways beginnen het accepteren van klant verkeer op 1 september 2020. :
- Japan-Oost: 40.79.184.8, 40.79.192.5

### <a name="august-2020"></a>2020 augustus

Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's:

- Australië-oost: 13.70.112.9
- Canada-centraal: 52.246.152.0, 20.38.144.1 
- VS-West 2:40.78.240.8

Deze SQL-gateways beginnen het accepteren van klant verkeer op 10 augustus 2020. 

# <a name="completed"></a>[Voltooid](#tab/completed-ip)

De volgende gateway migraties zijn voltooid: 

### <a name="october-2019"></a>Oktober 2019
- Brazil South
- VS - west
- Europa -west
- VS - oost
- Central US
- Azië - zuidoost
- South Central US
- Europa - noord
- VS - noord-centraal
- Japan - west
- Japan East
- VS - oost 2
- Azië - oost

---

## <a name="impact-of-this-change"></a>Impact van deze wijziging

Verkeer migratie kan het open bare IP-adres wijzigen dat DNS voor uw data base in Azure SQL Database verhelpt.
U hebt mogelijk gevolgen voor het volgende:

- Het IP-adres voor een bepaalde gateway in uw on-premises firewall is vastgelegd.
- Subnetten die gebruikmaken van micro soft. SQL als een service-eind punt, maar niet kunnen communiceren met de IP-adressen van de gateway
- De [redundante zone configuratie](high-availability-sla.md#zone-redundant-configuration) voor uw data base gebruiken

U hebt de volgende gevolgen:
 
- Omleiding als verbindings beleid
- Verbindingen met SQL Database van binnen Azure en met behulp van service Tags
- Verbindingen die zijn gemaakt met ondersteunde versies van het JDBC-stuur programma voor SQL Server, zien geen invloed. Zie [micro soft JDBC-stuur programma voor SQL Server downloaden](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)voor een ondersteunde versie van JDBC.

## <a name="what-to-do-you-do-if-youre-affected"></a>Wat u moet doen als dit van invloed is

We raden u aan om uitgaand verkeer naar IP-adressen toe te staan voor alle [gateway-IP-adressen](connectivity-architecture.md#gateway-ip-addresses) in de regio op TCP-poort 1433 en poort bereik 11000-11999. Deze aanbeveling is van toepassing op clients die vanaf on-premises verbinding maken en die verbinding maken via service-eind punten. Zie [verbindings beleid](connectivity-architecture.md#connection-policy)voor meer informatie over poortbereiken.

Voor verbindingen die zijn gemaakt via toepassingen met het micro soft JDBC-stuur programma onder versie 4,0, kan het certificaat niet worden gevalideerd. Lagere versies van micro soft JDBC zijn afhankelijk van de algemene naam (CN) in het veld onderwerp van het certificaat. De beperking is om ervoor te zorgen dat de eigenschap hostNameInCertificate is ingesteld op *. database.windows.net. Zie [verbinding maken met versleuteling](/sql/connect/jdbc/connecting-with-ssl-encryption)voor meer informatie over het instellen van de eigenschap hostNameInCertificate.

Als de bovenstaande oplossing niet werkt, kunt u een ondersteunings aanvraag indienen voor SQL Database of een door SQL beheerd exemplaar met behulp van de volgende URL:https://aka.ms/getazuresupport

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure SQL-connectiviteits architectuur](connectivity-architecture.md)
