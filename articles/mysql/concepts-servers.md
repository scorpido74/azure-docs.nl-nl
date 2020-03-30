---
title: Serverconcepten - Azure Database voor MySQL
description: In dit onderwerp worden overwegingen en richtlijnen weergegeven voor het werken met Azure Database voor MySQL-servers.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 300470b2e8fb10fda7cfc59517cef00c07bf0632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537003"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Serverconcepten in Azure Database voor MySQL

In dit artikel vindt u overwegingen en richtlijnen voor het werken met Azure Database voor MySQL-servers.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Wat is een Azure Database voor MySQL-server?

Een Azure Database voor MySQL-server is een centraal beheerpunt voor meerdere databases. Het is dezelfde MySQL-serverconstructie die u mogelijk kent in de on-premises wereld. Met name de Azure Database for MySQL-service wordt beheerd, biedt prestatiegaranties en legt toegang en functies op serverniveau bloot.

Een Azure-database voor MySQL-server:

- Is gemaakt binnen een Azure-abonnement.
- Is de bovenliggende bron voor databases.
- Biedt een naamruimte voor databases.
- Is een container met een sterke levenslange semantiek - verwijder een server en verwijdert de opgenomen databases.
- Collocates resources in een regio.
- Biedt een verbindingseindpunt voor server- en databasetoegang.
- Biedt de ruimte voor beheerbeleid dat van toepassing is op de databases: login, firewall, gebruikers, rollen, configuraties, enz.
- Is beschikbaar in meerdere versies. Zie [Ondersteunde Azure Database voor MySQL-databaseversies voor](./concepts-supported-versions.md)meer informatie.

Op een Azure Database voor MySQL-server kunt u een of meerdere databases maken. U ervoor kiezen om één database per server te maken om alle bronnen te gebruiken of om meerdere databases te maken om de bronnen te delen. De prijzen zijn gestructureerd per server, gebaseerd op de configuratie van de prijscategorie, vCores en opslag (GB). Zie [Prijsniveaus voor](./concepts-service-tiers.md)meer informatie .

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Hoe maak ik verbinding en verifieer ik met een Azure Database voor MySQL-server?

De volgende elementen zorgen voor een veilige toegang tot uw database.

|     |     |
| :-- | :-- |
| **Verificatie en autorisatie** | Azure Database voor MySQL-server ondersteunt native MySQL-verificatie. U verbinding maken en verifiëren met een server met de beheerdersaanmelding van de server. |
| **Protocol** | De service ondersteunt een op berichten gebaseerd protocol dat door MySQL wordt gebruikt. |
| **TCP/IP** | Het protocol wordt ondersteund via TCP/IP en via Unix-domeinsockets. |
| **Firewall** | Om uw gegevens te beschermen, voorkomt een firewallregel alle toegang tot uw databaseserver totdat u opgeeft welke computers toestemming hebben. Zie [Azure Database voor MySQL Server-firewallregels](./concepts-firewall-rules.md). |
| **SSL** | De service ondersteunt het afdwingen van SSL-verbindingen tussen uw toepassingen en uw databaseserver.  Zie [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (SSL-connectiviteit in uw toepassing configureren om veilig verbinding te maken met Azure-database voor MySQL) voor meer informatie. |

## <a name="how-do-i-manage-a-server"></a>Hoe beheer ik een server?

U Azure Database voor MySQL-servers beheren met behulp van de Azure-portal of de Azure CLI.

## <a name="next-steps"></a>Volgende stappen

- Zie Azure Database for [MySQL-overzicht voor](./overview.md) een overzicht van de service
- Zie [Servicelagen](./concepts-service-tiers.md) voor informatie over specifieke resourcequota en -beperkingen op basis van uw **servicelaag**
- Zie [Verbindingsbibliotheken voor Azure Database voor MySQL voor](./concepts-connection-libraries.md)informatie over het maken van verbinding met de service.
