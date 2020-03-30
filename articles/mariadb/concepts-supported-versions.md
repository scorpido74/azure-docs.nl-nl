---
title: Ondersteunde versies - Azure Database voor MariaDB
description: Ontdek welke versies van de MariaDB-server worden ondersteund in de Azure Database for MariaDB-service.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 361ba17532d27a7020be1b6874993da999f48604
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527704"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Ondersteunde Azure-database voor MariaDB-serverversies

Azure Database voor MariaDB is ontwikkeld vanuit de open-source [MariaDB Server](https://downloads.mariadb.org/), met behulp van de InnoDB-engine.

MariaDB maakt gebruik van de X.Y.Z naamgevingsregeling. X is de belangrijkste versie, Y is de kleine versie, en Z is de patch versie.

> [!NOTE]
> In de service wordt een gateway gebruikt om de verbindingen met serverinstanties om te leiden. Nadat de verbinding is gemaakt, geeft de MySQL-client de versie van MariaDB-set in de gateway weer, niet de werkelijke versie die wordt uitgevoerd op uw MariaDB-serverinstantie. Als u de versie van uw MariaDB-serverinstantie wilt bepalen, gebruikt u de `SELECT VERSION();` opdracht.

Azure Database voor MariaDB ondersteunt momenteel de volgende versie:

## <a name="mariadb-version-102"></a>MariaDB Versie 10.2

Patchversie: 10.2.25

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) voor meer informatie over verbeteringen en oplossingen in deze versie.

## <a name="mariadb-version-103"></a>MariaDB Versie 10.3

Patchversie: 10.3.16

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) voor meer informatie over verbeteringen en oplossingen in deze versie.

## <a name="managing-updates-and-upgrades"></a>Updates en upgrades beheren
De service beheert automatisch upgrades voor patchupdates. Bijvoorbeeld 10.2.21 tot 10.2.23.  

Upgrades van de secundaire en primaire versie worden momenteel niet ondersteund. Een upgrade van MariaDB 10.2 naar MariaDB 10.3 wordt bijvoorbeeld niet ondersteund. Als u wilt upgraden van 10,2 naar 10,3, neemt u een [gooi en herstelt](./howto-migrate-dump-restore.md) u deze naar een server die is gemaakt met de nieuwe engineversie.

## <a name="next-steps"></a>Volgende stappen

- Zie [Servicelagen](./concepts-pricing-tiers.md)voor informatie over specifieke resourcequota en -beperkingen op basis van uw **servicelaag.**
