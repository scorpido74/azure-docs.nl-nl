---
title: SSL-connectiviteit-Azure Database for MySQL
description: Informatie voor het configureren van Azure Database for MySQL en de bijbehorende toepassingen voor een juiste gebruik van SSL-verbindingen
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d677e7c80d98b15a638b00c5b8f4d390a492c7fa
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770812"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-connectiviteit in Azure Database for MySQL
Azure Database for MySQL ondersteunt het verbinden van uw database server met client toepassingen met behulp van Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw databaseserver en clienttoepassingen zorgt dat u bent beschermt tegen 'man in the middle'-aanvallen omdat de gegevensstroom tussen de server en uw toepassing wordt versleuteld.

## <a name="default-settings"></a>Standaard instellingen
De database service moet standaard zodanig worden geconfigureerd dat SSL-verbindingen worden vereist bij het verbinden met MySQL.  U wordt aangeraden de SSL-optie zo mogelijk niet uit te scha kelen. 

Wanneer u een nieuwe Azure Database for MySQL-server inricht via de Azure Portal en CLI, wordt het afdwingen van SSL-verbindingen standaard ingeschakeld. 

Verbindings reeksen voor verschillende programmeer talen worden weer gegeven in de Azure Portal. Deze verbindings reeksen bevatten de vereiste SSL-para meters om verbinding te maken met uw data base. Selecteer uw server in de Azure Portal. Selecteer onder de kop **instellingen** de **verbindings reeksen**. De SSL-para meter varieert op basis van de connector, bijvoorbeeld "SSL = True" of "sslmode = vereist" of "sslmode = required" en andere variaties.

Zie [SSL configureren](howto-configure-ssl.md)voor meer informatie over het in-of uitschakelen van SSL-verbinding tijdens het ontwikkelen van een toepassing. 

## <a name="next-steps"></a>Volgende stappen
[Verbindings bibliotheken voor Azure Database for MySQL](concepts-connection-libraries.md)
