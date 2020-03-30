---
title: SSL-connectiviteit - Azure-database voor MariaDB
description: Informatie voor het configureren van Azure Database voor MariaDB en bijbehorende toepassingen om SSL-verbindingen correct te gebruiken
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 36532575645d135a7abe7239798b6f2abc4246f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477065"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>SSL-connectiviteit in Azure Database voor MariaDB
Azure Database voor MariaDB ondersteunt het verbinden van uw databaseserver met clienttoepassingen met behulp van Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw databaseserver en clienttoepassingen zorgt dat u bent beschermt tegen 'man in the middle'-aanvallen omdat de gegevensstroom tussen de server en uw toepassing wordt versleuteld.

## <a name="default-settings"></a>Standaardinstellingen
Standaard moet de databaseservice zijn geconfigureerd om SSL-verbindingen te vereisen wanneer u verbinding maakt met MariaDB.  We raden u aan om te voorkomen dat de SSL-optie waar mogelijk wordt uitgeschakeld.

Bij het inrichten van een nieuwe Azure Database voor MariaDB-server via de Azure-portal en CLI is handhaving van SSL-verbindingen standaard ingeschakeld.

Verbindingstekenreeksen voor verschillende programmeertalen worden weergegeven in de Azure-portal. Deze verbindingstekenreeksen bevatten de vereiste SSL-parameters om verbinding te maken met uw database. Selecteer uw server in de Azure-portal. Selecteer onder de kop **Instellingen** de **tekenreeksen verbinding**. De SSL-parameter varieert op basis van de connector, bijvoorbeeld "ssl=true" of "sslmode=require" of "sslmode=required" en andere varianten.

Raadpleeg Hoe u SSL-verbinding instellen of uitschakelen bij het ontwikkelen van toepassingen, [raadpleegt Hoe SSL te configureren.](howto-configure-ssl.md)

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [firewallregels voor servers](concepts-firewall-rules.md)
- Meer informatie over het [configureren van SSL](howto-configure-ssl.md).
