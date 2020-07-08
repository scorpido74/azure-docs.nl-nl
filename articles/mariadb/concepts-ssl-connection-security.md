---
title: SSL/TLS-connectiviteit-Azure Database for MariaDB
description: Informatie voor het configureren van Azure Database for MariaDB en de bijbehorende toepassingen voor een juiste gebruik van SSL-verbindingen
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 4111b0b01690097535412205b60619172e2c100a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84416652"
---
# <a name="ssltls-connectivity-in-azure-database-for-mariadb"></a>SSL/TLS-connectiviteit in Azure Database for MariaDB
Azure Database for MariaDB ondersteunt het verbinden van uw database server met client toepassingen met behulp van Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw databaseserver en clienttoepassingen zorgt dat u bent beschermt tegen 'man in the middle'-aanvallen omdat de gegevensstroom tussen de server en uw toepassing wordt versleuteld.

## <a name="default-settings"></a>Standaardinstellingen
De database service moet standaard zodanig worden geconfigureerd dat SSL-verbindingen worden vereist wanneer verbinding wordt gemaakt met MariaDB.  U wordt aangeraden de SSL-optie zo mogelijk niet uit te scha kelen.

Wanneer u een nieuwe Azure Database for MariaDB-server inricht via de Azure Portal en CLI, wordt het afdwingen van SSL-verbindingen standaard ingeschakeld.

Verbindings reeksen voor verschillende programmeer talen worden weer gegeven in de Azure Portal. Deze verbindings reeksen bevatten de vereiste SSL-para meters om verbinding te maken met uw data base. Selecteer uw server in de Azure Portal. Selecteer onder de kop **instellingen** de **verbindings reeksen**. De SSL-para meter varieert op basis van de connector, bijvoorbeeld "SSL = True" of "sslmode = vereist" of "sslmode = required" en andere variaties.

Zie [SSL configureren](howto-configure-ssl.md)voor meer informatie over het in-of uitschakelen van SSL-verbinding tijdens het ontwikkelen van een toepassing.

## <a name="tls-enforcement-in-azure-database-for-mariadb"></a>TLS-afdwinging in Azure Database for MariaDB

Azure Database for MariaDB ondersteunt versleuteling voor clients die verbinding maken met uw database server met behulp van Transport Layer Security (TLS). TLS is een industrie standaard protocol dat beveiligde netwerk verbindingen tussen uw database server-en client toepassingen waarborgt, zodat u kunt voldoen aan de nalevings vereisten.

### <a name="tls-settings"></a>TLS-instellingen

Azure Database for MariaDB biedt de mogelijkheid om de TLS-versie voor de client verbindingen af te dwingen. Als u de TLS-versie wilt afdwingen, gebruikt u de instelling **minimale TLS-versie** optie. De volgende waarden zijn toegestaan voor deze optie-instelling:

|  Minimale TLS-instelling             | TLS-versie van client wordt ondersteund                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (standaard) | Geen TLS vereist                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 en hoger         |
| TLS1_1                           | TLS 1,1, TLS 1,2 en hoger              |
| TLS1_2                           | TLS-versie 1,2 en hoger                  |


Als u bijvoorbeeld de waarde van de minimum versie van TLS-instelling instelt op TLS 1,0, betekent dit dat uw server verbindingen toestaat van clients die gebruikmaken van TLS 1,0, 1,1 en 1.2 +. U kunt dit ook instellen op 1,2 zodat u alleen verbindingen van clients met TLS 1.2 + kunt toestaan en alle verbindingen met TLS 1,0 en TLS 1,1 worden geweigerd.

> [!Note] 
> Azure Database for MariaDB wordt standaard ingesteld op TLS uitgeschakeld voor alle nieuwe servers. 
>
> Momenteel worden de TLS-versies ondersteund door Azure Database for MariaDB TLS 1,0, 1,1 en 1,2. Wanneer het beleid is afgedwongen voor een specifieke minimale TLS-versie, kunt u deze niet wijzigen in uitgeschakeld.

Zie [TLS-instelling configureren](howto-tls-configurations.md)voor meer informatie over het instellen van de TLS-instelling voor uw Azure database for MariaDB.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [firewall regels voor servers](concepts-firewall-rules.md)
- Meer informatie over het [configureren van SSL](howto-configure-ssl.md)
- Meer informatie over het [configureren van TLS](howto-tls-configurations.md)
