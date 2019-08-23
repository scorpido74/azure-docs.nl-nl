---
title: Ondersteunde versies in Azure Database for MariaDB
description: Beschrijft de ondersteunde versies in Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 950294094584958e83f6a16630a6e1f897785e46
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897290"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Ondersteunde Azure Database for MariaDB server versies

Azure Database for MariaDB is ontwikkeld op basis van de open-source [MariaDB-server](https://downloads.mariadb.org/)met behulp van de InnoDB-engine. 

MariaDB maakt gebruik van het naamgevings schema X. Y. Z. X is de primaire versie, Y de secundaire versie en Z de versie van de patch.

> [!NOTE]
> In de service wordt een gateway gebruikt om de verbindingen met Server exemplaren om te leiden. Nadat de verbinding tot stand is gebracht, geeft de MySQL-client de versie van MariaDB die in de gateway is ingesteld, niet de daad werkelijke versie die wordt uitgevoerd op uw MariaDB Server-exemplaar. Gebruik de `SELECT VERSION();` opdracht om de versie van uw MariaDB-Server exemplaar te bepalen.

Azure Database for MariaDB ondersteunt momenteel de volgende versie:

## <a name="mariadb-version-102"></a>MariaDB-versie 10,2

Patch versie: 10.2.23

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/) voor meer informatie over verbeteringen en oplossingen in MariaDB 10.2.23.

## <a name="mariadb-version-103"></a>MariaDB-versie 10,3

Patch versie: 10.3.14

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/) voor meer informatie over verbeteringen en oplossingen in MariaDB 10.3.14.

## <a name="managing-updates-and-upgrades"></a>Updates en upgrades beheren
De service beheert automatisch upgrades voor patch-updates. Bijvoorbeeld 10.2.21 naar 10.2.23.  

Upgrades van de secundaire en primaire versie worden momenteel niet ondersteund. Een upgrade van MariaDB 10,2 naar MariaDB 10,3 wordt bijvoorbeeld niet ondersteund. Als u een upgrade wilt uitvoeren van 10,2 naar 10,3, neemt u een [dump op en herstelt](./howto-migrate-dump-restore.md) u deze op een server die is gemaakt met de nieuwe engine versie.

## <a name="next-steps"></a>Volgende stappen

- Zie [service lagen](./concepts-pricing-tiers.md)voor informatie over specifieke resource quota en beperkingenop basis van uw servicelaag.
