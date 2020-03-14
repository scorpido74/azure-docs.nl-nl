---
title: SSL-connectiviteit-Azure Database for MySQL
description: Informatie voor het configureren van Azure Database for MySQL en de bijbehorende toepassingen voor een juiste gebruik van SSL-verbindingen
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 7bc3a238ca2ff2861ec6fc65033738e741574321
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370845"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-connectiviteit in Azure Database for MySQL

Azure Database for MySQL ondersteunt het verbinden van uw database server met client toepassingen met behulp van Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw databaseserver en clienttoepassingen zorgt dat u bent beschermt tegen 'man in the middle'-aanvallen omdat de gegevensstroom tussen de server en uw toepassing wordt versleuteld.

## <a name="ssl-default-settings"></a>Standaard instellingen voor SSL

De database service moet standaard zodanig worden geconfigureerd dat SSL-verbindingen worden vereist bij het verbinden met MySQL.  U wordt aangeraden de SSL-optie zo mogelijk niet uit te scha kelen.

Wanneer u een nieuwe Azure Database for MySQL-server inricht via de Azure Portal en CLI, wordt het afdwingen van SSL-verbindingen standaard ingeschakeld. 

Verbindings reeksen voor verschillende programmeer talen worden weer gegeven in de Azure Portal. Deze verbindings reeksen bevatten de vereiste SSL-para meters om verbinding te maken met uw data base. Selecteer uw server in de Azure Portal. Selecteer onder de kop **instellingen** de **verbindings reeksen**. De SSL-para meter varieert op basis van de connector, bijvoorbeeld "SSL = True" of "sslmode = vereist" of "sslmode = required" en andere variaties.

Zie [SSL configureren](howto-configure-ssl.md)voor meer informatie over het in-of uitschakelen van SSL-verbinding tijdens het ontwikkelen van een toepassing.

## <a name="tls-connectivity-in-azure-database-for-mysql"></a>TLS-connectiviteit in Azure Database for MySQL

Azure Database for MySQL ondersteunt versleuteling voor clients die verbinding maken met uw database server met behulp van Transport Layer Security (TLS). TLS is een industrie standaard protocol dat beveiligde netwerk verbindingen tussen uw database server-en client toepassingen waarborgt, zodat u kunt voldoen aan de nalevings vereisten.

### <a name="tls-settings"></a>TLS-instellingen

Klanten kunnen nu de TLS-versie afdwingen voor de client die verbinding maakt met hun Azure Database for MySQL. Als u de optie TLS wilt gebruiken, gebruikt u de instelling **minimale TLS-versie** . De volgende waarden zijn toegestaan voor deze optie-instelling:

|  Minimale TLS-instelling             | TLS-versie wordt ondersteund                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (standaard) | Geen TLS vereist                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 en hoger |
| TLS1_1                           | TLS 1,1, TLS 1,2 en hoger          |
| TLS1_2                           | TLS-versie 1,2 en hoger           |


Als u bijvoorbeeld deze versie van een TLS-instelling instelt op TLS 1,0, betekent dit dat uw server verbindingen met clients toestaat die gebruikmaken van TLS 1,0, 1,1 en 1.2 +. U kunt dit ook instellen op 1,2 zodat u alleen verbindingen van clients toestaat die gebruikmaken van TLS 1,2 en alle verbindingen met TLS 1,0 en TLS 1,1 worden geweigerd.

> [!Note] 
> Azure Database for MySQL wordt standaard ingesteld op TLS uitgeschakeld voor alle nieuwe servers.
>
> Momenteel worden de TLS-versies ondersteund door Azure Database for MySQL TLS 1,0, 1,1 en 1,2.

Zie [TLS-instelling configureren](howto-tls-configurations.md)voor meer informatie over het instellen van de TLS-instelling voor uw Azure database for MySQL.

## <a name="next-steps"></a>Volgende stappen

[Verbindings bibliotheken voor Azure Database for MySQL](concepts-connection-libraries.md)
