---
title: Servers - Azure Database voor PostgreSQL - Single Server
description: In dit artikel vindt u overwegingen en richtlijnen voor het configureren en beheren van Azure Database voor PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: d86170a53b4bfbe712bbca12db4d6063214aba21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768160"
---
# <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - één server
In dit artikel vindt u overwegingen en richtlijnen voor het werken met Azure Database voor PostgreSQL - Single Server.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Wat is een Azure Database for PostgreSQL-server?
Een server in de Azure Database for PostgreSQL - Single Server-implementatieoptie is een centraal beheerpunt voor meerdere databases. Het is dezelfde PostgreSQL-serverconstructie die u mogelijk kent in de on-premises wereld. Concreet wordt de PostgreSQL-service beheerd, biedt het prestatiegaranties, legt toegang en functies op serverniveau bloot.

Een Azure-database voor PostgreSQL-server:

- Is gemaakt binnen een Azure-abonnement.
- Is de bovenliggende bron voor databases.
- Biedt een naamruimte voor databases.
- Is een container met een sterke levenslange semantiek - verwijder een server en verwijdert de opgenomen databases.
- Collocates resources in een regio.
- Biedt een verbindingseindpunt voor server- en databasetoegang 
- Biedt de ruimte voor beheerbeleid dat van toepassing is op de databases: login, firewall, gebruikers, rollen, configuraties, enz.
- Is beschikbaar in meerdere versies. Zie voor meer informatie [ondersteunde PostgreSQL-databaseversies](concepts-supported-versions.md).
- Is uitbreidbaar door gebruikers. Zie [PostgreSQL-extensies](concepts-extensions.md)voor meer informatie .

Binnen een Azure Database voor PostgreSQL-server u één of meerdere databases maken. U kunt kiezen voor het maken van één database per server om gebruik te maken van alle resources of voor meerdere databases om de resources te delen. De prijzen zijn gestructureerd per server, gebaseerd op de configuratie van de prijscategorie, vCores en opslag (GB). Zie [Prijsniveaus voor](./concepts-pricing-tiers.md)meer informatie .

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Hoe maak ik verbinding en verifieer ik met een Azure Database voor PostgreSQL-server?
De volgende elementen zorgen voor een veilige toegang tot uw database:

|||
|:--|:--|
| **Verificatie en autorisatie** | Azure Database voor PostgreSQL-server ondersteunt native PostgreSQL-verificatie. U verbinding maken en verifiëren met de server met de administronering s.you can connect and authenticate to server with the server's admin login. |
| **Protocol** | De service ondersteunt een op berichten gebaseerd protocol dat wordt gebruikt door PostgreSQL. |
| **TCP/IP** | Het protocol wordt ondersteund via TCP/IP en via Unix-domeinsockets. |
| **Firewall** | Om uw gegevens te beschermen, voorkomt een firewallregel alle toegang tot uw server en de databases ervan, totdat u opgeeft welke computers toestemming hebben. Zie [Azure Database voor PostgreSQL Server-firewallregels](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Uw server beheren
U Azure Database voor PostgreSQL-servers beheren met behulp van de [Azure-portal](https://portal.azure.com) of de [Azure CLI.](/cli/azure/postgres)

Tijdens het maken van een server stelt u de referenties in voor uw beheerdersgebruiker. De beheerdersgebruiker is de gebruiker met de hoogste bevoegdheid die u op de server hebt. Het behoort tot de rol azure_pg_admin. Deze rol heeft geen volledige superusermachtigingen. 

Het kenmerk PostgreSQL-superuser wordt toegewezen aan de azure_superuser, die behoort tot de beheerde service. Je hebt geen toegang tot deze rol.

Een Azure Database voor PostgreSQL-server heeft standaarddatabases: 
- **postgres** - Een standaarddatabase waarmee u verbinding maken zodra uw server is gemaakt.
- **azure_maintenance** - Deze database wordt gebruikt om de processen die de beheerde service bieden, te scheiden van gebruikersacties. U hebt geen toegang tot deze database.
- **azure_sys** - Een database voor het queryarchief. Deze database verzamelt geen gegevens wanneer queryarchief is uitgeschakeld. dit is de standaardinstelling. Zie het overzicht van de [Querystore](concepts-query-store.md)voor meer informatie.


## <a name="server-parameters"></a>Serverparameters
De PostgreSQL-serverparameters bepalen de configuratie van de server. In Azure Database voor PostgreSQL kan de lijst met parameters worden bekeken en bewerkt met de Azure-portal of de Azure CLI. 

Als beheerde service voor Postgres zijn de configureerbare parameters in Azure Database voor PostgreSQL een subset van de parameters in een lokale Postgres-instantie (Zie de [PostgreSQL-documentatie](https://www.postgresql.org/docs/9.6/static/runtime-config.html)voor meer informatie over Postgres-parameters). Uw Azure Database voor PostgreSQL-server is ingeschakeld met standaardwaarden voor elke parameter bij het maken. Sommige parameters waarvoor een server opnieuw moet worden opgestart of dat wijzigingen van kracht moeten worden, kunnen niet door de gebruiker worden geconfigureerd.


## <a name="next-steps"></a>Volgende stappen
- Zie Azure Database for [PostgreSQL-overzicht voor](overview.md)een overzicht van de service voor een overzicht van de service.
- Zie [Servicelagen](concepts-pricing-tiers.md)voor informatie over specifieke resourcequota en -beperkingen op basis van uw **servicelaag.**
- Zie [Verbindingsbibliotheken voor Azure Database voor PostgreSQL voor](concepts-connection-libraries.md)informatie over verbinding maken met de service.
- Serverparameters weergeven en bewerken via [Azure portal](howto-configure-server-parameters-using-portal.md) of [Azure CLI](howto-configure-server-parameters-using-cli.md).
