---
title: Servers - Azure Database voor MariaDB
description: In dit onderwerp worden overwegingen en richtlijnen weergegeven voor het werken met Azure Database voor MariaDB-servers.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 444d7f1574cf1517b01250bcb9d810731030182d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527789"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Serverconcepten in Azure Database voor MariaDB
In dit artikel vindt u overwegingen en richtlijnen voor het werken met Azure Database voor MariaDB-servers.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Wat is een Azure Database voor MariaDB-server?

Een Azure Database voor MariaDB-server is een centraal beheerpunt voor meerdere databases. Het is dezelfde MariaDB server constructie die u misschien bekend bent met in de on-premises wereld. Met name de Azure Database for MariaDB-service wordt beheerd, biedt prestatiegaranties en legt toegang en functies op serverniveau bloot.

Een Azure-database voor MariaDB-server:

- Is gemaakt binnen een Azure-abonnement.
- Is de bovenliggende bron voor databases.
- Biedt een naamruimte voor databases.
- Is een container met een sterke levenslange semantiek - verwijder een server en verwijdert de opgenomen databases.
- Collocates resources in een regio.
- Biedt een verbindingseindpunt voor server- en databasetoegang.
- Biedt de ruimte voor beheerbeleid dat van toepassing is op de databases: login, firewall, gebruikers, rollen, configuraties, enz.
- Is beschikbaar in MariaDB motor versie 10.2. Zie [Ondersteunde Azure Database voor MariaDB-databaseversies voor](./concepts-supported-versions.md)meer informatie.

Op een Azure Database for MariaDB-server kunt u een of meerdere databases maken. U ervoor kiezen om één database per server te maken om alle bronnen te gebruiken of om meerdere databases te maken om de bronnen te delen. De prijzen zijn gestructureerd per server, gebaseerd op de configuratie van de prijscategorie, vCores en opslag (GB). Zie [Prijsniveaus voor](./concepts-pricing-tiers.md)meer informatie .

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Hoe beveilig ik een Azure Database voor MariaDB-server?

De volgende elementen zorgen voor een veilige toegang tot uw database.

|||
| :--| :--|
| **Verificatie en autorisatie** | Azure Database voor MariaDB-server ondersteunt native MySQL-verificatie. U verbinding maken en verifiëren met een server met de beheerdersaanmelding van de server. |
| **Protocol** | De service ondersteunt een op berichten gebaseerd protocol dat door MySQL wordt gebruikt. |
| **TCP/IP** | Het protocol wordt ondersteund via TCP/IP en via Unix-domeinsockets. |
| **Firewall** | Om uw gegevens te beschermen, voorkomt een firewallregel alle toegang tot uw databaseserver totdat u opgeeft welke computers toestemming hebben. Zie [Azure Database for MariaDB Server firewallregels](./concepts-firewall-rules.md). |
| **SSL** | De service ondersteunt het afdwingen van SSL-verbindingen tussen uw toepassingen en uw databaseserver. Zie [SSL-connectiviteit configureren in uw toepassing om veilig verbinding te maken met Azure Database voor MariaDB.](./howto-configure-ssl.md) |

## <a name="how-do-i-manage-a-server"></a>Hoe beheer ik een server?
U Azure Database voor MariaDB-servers beheren met behulp van de Azure-portal of de Azure CLI.

## <a name="next-steps"></a>Volgende stappen
- Zie Azure Database for [MariaDB-overzicht voor](./overview.md) een overzicht van de service
- Zie [Servicelagen](./concepts-pricing-tiers.md) voor informatie over specifieke resourcequota en -beperkingen op basis van uw **servicelaag**

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
