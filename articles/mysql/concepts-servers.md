---
title: Server concepten-Azure Database for MySQL
description: In dit onderwerp vindt u overwegingen en richt lijnen voor het werken met Azure Database for MySQL-servers.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 9a2e2eb022d96af1437ea4189d11f5fa69339325
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769996"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Server concepten in Azure Database for MySQL

In dit artikel vindt u overwegingen en richt lijnen voor het werken met Azure Database for MySQL-servers.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Wat is een Azure Database for MySQL-server?

Een Azure Database for MySQL server is een centraal beheer punt voor meerdere data bases. Het is dezelfde MySQL-server constructie die u mogelijk kent in de on-premises wereld. Met name de Azure Database for MySQL-service wordt beheerd, levert prestatie garanties en biedt toegang en functies op server niveau.

Een Azure Database for MySQL-server:

- Wordt gemaakt binnen een Azure-abonnement.
- Is de bovenliggende resource voor data bases.
- Biedt een naam ruimte voor data bases.
- Is een container met sterke levens duur semantiek: een server verwijderen en de Inge sloten data bases verwijdert.
- Groepeert bronnen in een regio.
- Biedt een verbindings eindpunt voor toegang tot de server en data base.
- Voorziet in het bereik voor beheer beleid dat van toepassing is op de bijbehorende data bases: aanmelden, firewall, gebruikers, rollen, configuraties, enzovoort.
- Is beschikbaar in meerdere versies. Zie [supported Azure database for MySQL data base versions](./concepts-supported-versions.md)(Engelstalig) voor meer informatie.

Op een Azure Database voor MySQL-server kunt u een of meerdere databases maken. U kunt ervoor kiezen om één data base per server te maken voor het gebruik van alle resources of om meerdere data bases te maken om de resources te delen. De prijzen zijn gestructureerd per server, op basis van de configuratie van de prijs categorie, vCores en opslag (GB). Zie [prijs categorieën](./concepts-service-tiers.md)voor meer informatie.

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Hoe kan ik verbinding maken en verifiëren met een Azure Database for MySQL-server?

De volgende elementen helpen veilige toegang tot uw data base te garanderen.

|     |     |
| :-- | :-- |
| **Verificatie en autorisatie** | Azure Database for MySQL-server ondersteunt native MySQL-verificatie. U kunt verbinding maken met en verifiëren met een server met de beheerders aanmelding van de server. |
| **Protocol** | De service ondersteunt een op berichten gebaseerd protocol dat wordt gebruikt door MySQL. |
| **TCP/IP** | Het protocol wordt ondersteund via TCP/IP en via Unix-domein sockets. |
| **Firewall** | Ter bescherming van uw gegevens voor komt een firewall regel alle toegang tot uw database server, totdat u opgeeft welke computers zijn gemachtigd. Zie [Azure database for MySQL Server firewall-regels](./concepts-firewall-rules.md). |
| **-** | De service biedt ondersteuning voor het afdwingen van SSL-verbindingen tussen uw toepassingen en uw database server.  Zie [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (SSL-connectiviteit in uw toepassing configureren om veilig verbinding te maken met Azure-database voor MySQL) voor meer informatie. |

## <a name="how-do-i-manage-a-server"></a>Hoe kan ik een server beheren?

U kunt Azure Database for MySQL servers beheren door gebruik te maken van de Azure Portal of de Azure CLI.

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure database for MySQL](./overview.md) voor een overzicht van de service
- Zie [service lagen](./concepts-service-tiers.md) voor informatie over specifieke resource quota en beperkingen op basis **van uw servicelaag**.
- Zie [verbindings bibliotheken voor Azure database for MySQL](./concepts-connection-libraries.md)voor meer informatie over het maken van een verbinding met de service.
