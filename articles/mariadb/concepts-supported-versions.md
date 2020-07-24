---
title: Ondersteunde versies-Azure Database for MariaDB
description: Meer informatie over welke versies van de MariaDB-server worden ondersteund in de Azure Database for MariaDB-service.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: 0b495bf21b1f78e5e6a9285f67e1769d2d2a0db1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065695"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Ondersteunde Azure Database for MariaDB server versies

Azure Database for MariaDB is ontwikkeld op basis van de open-source [MariaDB-server](https://downloads.mariadb.org/)met behulp van de InnoDB-engine.

MariaDB maakt gebruik van het naamgevings schema X. Y. Z. X is de primaire versie, Y de secundaire versie en Z de versie van de patch.

> [!NOTE]
> In de service wordt een gateway gebruikt om de verbindingen met serverinstanties om te leiden. Nadat de verbinding tot stand is gebracht, geeft de MySQL-client de versie van MariaDB die in de gateway is ingesteld, niet de daad werkelijke versie die wordt uitgevoerd op uw MariaDB Server-exemplaar. Gebruik de opdracht om de versie van uw MariaDB-Server exemplaar te bepalen `SELECT VERSION();` .

Azure Database for MariaDB ondersteunt momenteel de volgende versie:

## <a name="mariadb-version-102"></a>MariaDB-versie 10,2

Patch versie: 10.2.32

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/mariadb-10232-release-notes/) voor meer informatie over verbeteringen en oplossingen in deze versie.

## <a name="mariadb-version-103"></a>MariaDB-versie 10,3

Patch versie: 10.3.23

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/mariadb-10323-release-notes/) voor meer informatie over verbeteringen en oplossingen in deze versie.

## <a name="managing-updates-and-upgrades"></a>Updates en upgrades beheren
De service beheert automatisch upgrades voor patch-updates. Bijvoorbeeld 10.2.21 naar 10.2.23.  

Upgrades van de secundaire en primaire versie worden momenteel niet ondersteund. Een upgrade van MariaDB 10.2 naar MariaDB 10.3 wordt bijvoorbeeld niet ondersteund. Als u een upgrade wilt uitvoeren van 10,2 naar 10,3, neemt u een [dump op en herstelt](./howto-migrate-dump-restore.md) u deze op een server die is gemaakt met de nieuwe engine versie.

## <a name="next-steps"></a>Volgende stappen

- Zie [service lagen](./concepts-pricing-tiers.md)voor informatie over specifieke resource quota en beperkingen op basis **van uw servicelaag**.
