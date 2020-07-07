---
title: Servers-Azure Database for MariaDB
description: In dit onderwerp vindt u overwegingen en richt lijnen voor het werken met Azure Database for MariaDB-servers.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 444d7f1574cf1517b01250bcb9d810731030182d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79527789"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Server concepten in Azure Database for MariaDB
In dit artikel vindt u overwegingen en richt lijnen voor het werken met Azure Database for MariaDB-servers.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Wat is een Azure Database for MariaDB-server?

Een Azure Database for MariaDB server is een centraal beheer punt voor meerdere data bases. Het is dezelfde MariaDB-server constructie die u mogelijk kent in de on-premises wereld. Met name de Azure Database for MariaDB-service wordt beheerd, levert prestatie garanties en biedt toegang en functies op server niveau.

Een Azure Database for MariaDB-server:

- Wordt gemaakt binnen een Azure-abonnement.
- Is de bovenliggende resource voor data bases.
- Biedt een naam ruimte voor data bases.
- Is een container met sterke levens duur semantiek: een server verwijderen en de Inge sloten data bases verwijdert.
- Groepeert bronnen in een regio.
- Biedt een verbindings eindpunt voor toegang tot de server en data base.
- Voorziet in het bereik voor beheer beleid dat van toepassing is op de bijbehorende data bases: aanmelden, firewall, gebruikers, rollen, configuraties, enzovoort.
- Is beschikbaar in de MariaDB-Engine versie 10,2. Zie [supported Azure database for MariaDB data base versions](./concepts-supported-versions.md)(Engelstalig) voor meer informatie.

Op een Azure Database for MariaDB-server kunt u een of meerdere databases maken. U kunt ervoor kiezen om één data base per server te maken voor het gebruik van alle resources of om meerdere data bases te maken om de resources te delen. De prijzen zijn gestructureerd per server, op basis van de configuratie van de prijs categorie, vCores en opslag (GB). Zie [prijs categorieën](./concepts-pricing-tiers.md)voor meer informatie.

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Hoe kan ik een Azure Database for MariaDB server beveiligen?

De volgende elementen helpen veilige toegang tot uw data base te garanderen.

|||
| :--| :--|
| **Verificatie en autorisatie** | Azure Database for MariaDB-server ondersteunt native MySQL-verificatie. U kunt verbinding maken met en verifiëren met een server met de beheerders aanmelding van de server. |
| **Protocol** | De service ondersteunt een op berichten gebaseerd protocol dat wordt gebruikt door MySQL. |
| **TCP/IP** | Het protocol wordt ondersteund via TCP/IP en via Unix-domein sockets. |
| **Firewall** | Ter bescherming van uw gegevens voor komt een firewall regel alle toegang tot uw database server, totdat u opgeeft welke computers zijn gemachtigd. Zie [Azure database for MariaDB Server firewall-regels](./concepts-firewall-rules.md). |
| **SSL** | De service biedt ondersteuning voor het afdwingen van SSL-verbindingen tussen uw toepassingen en uw database server. Zie [SSL-connectiviteit in uw toepassing configureren om veilig verbinding te maken met Azure database for MariaDB](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Hoe kan ik een server beheren?
U kunt Azure Database for MariaDB servers beheren door gebruik te maken van de Azure Portal of de Azure CLI.

## <a name="next-steps"></a>Volgende stappen
- Zie [overzicht van Azure database for MariaDB](./overview.md) voor een overzicht van de service
- Zie [service lagen](./concepts-pricing-tiers.md) voor informatie over specifieke resource quota en beperkingen op basis **van uw servicelaag**.

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
