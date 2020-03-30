---
title: Ondersteunde versies - Azure Database voor MySQL
description: Ontdek welke versies van de MySQL-server worden ondersteund in de Azure Database for MySQL-service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 197b3100190711a51cfe125fe1214a59c18e1491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536969"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Ondersteunde Azure Database voor MySQL-serverversies

Azure Database voor MySQL is ontwikkeld vanuit [MySQL Community Edition](https://www.mysql.com/products/community/), met behulp van de InnoDB-engine.

MySQL maakt gebruik van het X.Y.Z-naamgevingsschema. X is de belangrijkste versie, Y is de kleine versie, en Z is de bug fix release. Zie de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)voor meer informatie over het schema.

> [!NOTE]
> In de service wordt een gateway gebruikt om de verbindingen met serverinstanties om te leiden. Nadat de verbinding tot stand is gebracht, wordt in de MySQL-client de versie van MySQL weergegeven die in de gateway is ingesteld, niet de feitelijke versie die wordt uitgevoerd op de MySQL-serverinstantie. Als u de versie van uw MySQL-serverinstantie wilt vaststellen, gebruikt u de `SELECT VERSION();`-opdracht bij de MySQL-prompt.

Azure Database voor MySQL ondersteunt momenteel de volgende versies:

## <a name="mysql-version-56"></a>MySQL-versie 5.6

Bug fix release: 5.6.45

Raadpleeg de [MySQL-releasenotes](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-45.html) voor meer informatie over verbeteringen en oplossingen in deze versie.

## <a name="mysql-version-57"></a>MySQL-versie 5.7

Bug fix release: 5.7.27

Raadpleeg de [MySQL-releasenotes](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-27.html) voor meer informatie over verbeteringen en oplossingen in deze versie.

## <a name="mysql-version-80"></a>MySQL-versie 8.0

Bug fix release: 8.0.15

Raadpleeg de [MySQL-releasenotes](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) voor meer informatie over verbeteringen en oplossingen in deze versie.

## <a name="managing-updates-and-upgrades"></a>Updates en upgrades beheren
De service beheert automatisch patchen voor bug fix versie updates. Bijvoorbeeld 5,7,20 tot 5,7,21.  

Upgrades van de secundaire en primaire versie worden momenteel niet ondersteund. Een upgrade van MySQL 5.6 naar MySQL 5.7 wordt bijvoorbeeld niet ondersteund. Als u een upgrade wilt uitvoeren van 5.6 naar 5.7, gebruikt u [dump en herstel](./concepts-migrate-dump-restore.md) op een server die is gemaakt met de nieuwe versie van de engine.

## <a name="next-steps"></a>Volgende stappen

Zie [Servicelagen](./concepts-pricing-tiers.md) voor informatie over specifieke resourcequota en -beperkingen op basis van uw **servicelaag**
