---
title: SSL-connectiviteit-Azure Database for MariaDB
description: Informatie voor het configureren van Azure Database for MariaDB en de bijbehorende toepassingen voor een juiste gebruik van SSL-verbindingen
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 36532575645d135a7abe7239798b6f2abc4246f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477065"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>SSL-connectiviteit in Azure Database for MariaDB
Azure Database for MariaDB ondersteunt het verbinden van uw database server met client toepassingen met behulp van Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw databaseserver en clienttoepassingen zorgt dat u bent beschermt tegen 'man in the middle'-aanvallen omdat de gegevensstroom tussen de server en uw toepassing wordt versleuteld.

## <a name="default-settings"></a>Standaardinstellingen
De database service moet standaard zodanig worden geconfigureerd dat SSL-verbindingen worden vereist wanneer verbinding wordt gemaakt met MariaDB.  U wordt aangeraden de SSL-optie zo mogelijk niet uit te scha kelen.

Wanneer u een nieuwe Azure Database for MariaDB-server inricht via de Azure Portal en CLI, wordt het afdwingen van SSL-verbindingen standaard ingeschakeld.

Verbindings reeksen voor verschillende programmeer talen worden weer gegeven in de Azure Portal. Deze verbindings reeksen bevatten de vereiste SSL-para meters om verbinding te maken met uw data base. Selecteer uw server in de Azure Portal. Selecteer onder de kop **instellingen** de **verbindings reeksen**. De SSL-para meter varieert op basis van de connector, bijvoorbeeld "SSL = True" of "sslmode = vereist" of "sslmode = required" en andere variaties.

Zie [SSL configureren](howto-configure-ssl.md)voor meer informatie over het in-of uitschakelen van SSL-verbinding tijdens het ontwikkelen van een toepassing.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [firewall regels voor servers](concepts-firewall-rules.md)
- Meer informatie over het [configureren van SSL](howto-configure-ssl.md).
