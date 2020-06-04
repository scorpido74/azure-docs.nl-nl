---
title: Ondersteunde versies-Azure Database for MySQL
description: Meer informatie over de versies van de MySQL-server die worden ondersteund in de Azure Database for MySQL-service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 4d5b858e2384ffc7dd531444aaff17ca3739b408
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84337696"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Ondersteunde Azure Database for MySQL server versies

Azure Database for MySQL is ontwikkeld vanuit de [MySQL Community Edition](https://www.mysql.com/products/community/), met behulp van de InnoDB-engine.

MySQL maakt gebruik van het schema X. Y. Z. X is de primaire versie, Y de secundaire versie en Z de release van de fout correctie. Zie de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)voor meer informatie over het schema.

> [!NOTE]
> In de service wordt een gateway gebruikt om de verbindingen met serverinstanties om te leiden. Nadat de verbinding tot stand is gebracht, wordt in de MySQL-client de versie van MySQL weergegeven die in de gateway is ingesteld, niet de feitelijke versie die wordt uitgevoerd op de MySQL-serverinstantie. Als u de versie van uw MySQL-serverinstantie wilt vaststellen, gebruikt u de `SELECT VERSION();`-opdracht bij de MySQL-prompt.

Azure Database for MySQL ondersteunt momenteel de volgende versies:

## <a name="mysql-version-56"></a>MySQL-versie 5,6

Release van de fout oplossing: 5.6.47

Raadpleeg de opmerkingen bij de [versie](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) van MySQL voor meer informatie over verbeteringen en oplossingen in deze versie.

## <a name="mysql-version-57"></a>MySQL-versie 5,7

Release van de fout oplossing: 5.7.29

Raadpleeg de opmerkingen bij de [versie](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) van MySQL voor meer informatie over verbeteringen en oplossingen in deze versie.

## <a name="mysql-version-80"></a>MySQL-versie 8,0

Release van de fout oplossing: 8.0.15

Raadpleeg de opmerkingen bij de [versie](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) van MySQL voor meer informatie over verbeteringen en oplossingen in deze versie.

## <a name="managing-updates-and-upgrades"></a>Updates en upgrades beheren
De service beheert automatisch patches voor het oplossen van problemen met versie-updates. Bijvoorbeeld 5.7.20 installeren naar 5.7.21.  

Upgrades van de secundaire en primaire versie worden momenteel niet ondersteund. Een upgrade van MySQL 5.6 naar MySQL 5.7 wordt bijvoorbeeld niet ondersteund. Als u een upgrade wilt uitvoeren van 5.6 naar 5.7, gebruikt u [dump en herstel](./concepts-migrate-dump-restore.md) op een server die is gemaakt met de nieuwe versie van de engine.

## <a name="next-steps"></a>Volgende stappen

Zie [service lagen](./concepts-pricing-tiers.md) voor informatie over specifieke resource quota en beperkingen op basis **van uw servicelaag**.
