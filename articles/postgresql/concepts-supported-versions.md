---
title: Ondersteunde versies-Azure Database for PostgreSQL-één server
description: Beschrijft de ondersteunde post gres primaire en secundaire versies in Azure Database for PostgreSQL-één server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792233"
---
# <a name="supported-postgresql-major-versions"></a>Ondersteunde PostgreSQL primaire versies
Micro soft streeft ernaar ondersteuning te bieden voor n-2 versies van de PostgreSQL-engine in Azure Database for PostgreSQL-één server. De versies zijn de huidige primaire versie op Azure (n) en de twee voor gaande primaire versies (-2).

Azure Database for PostgreSQL ondersteunt momenteel de volgende primaire versies:

## <a name="postgresql-version-11"></a>PostgreSQL-versie 11
De huidige secundaire versie is 11,5. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/11/static/release-11-5.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-10"></a>PostgreSQL-versie 10
De huidige secundaire versie is 10,10. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/10/static/release-10-10.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-96"></a>PostgreSQL-versie 9,6
De huidige secundaire release is 9.6.15. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-95"></a>PostgreSQL-versie 9,5
De huidige secundaire release is 9.5.19. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="managing-upgrades"></a>Upgrades beheren
Het PostgreSQL-project ondervindt regel matig kleine releases voor het oplossen van gemelde bugs. Azure Database for PostgreSQL Servert automatisch servers met secundaire releases tijdens de maandelijkse implementaties van de service. 

Automatische upgrade voor primaire versie wordt niet ondersteund. Er is bijvoorbeeld geen automatische upgrade van PostgreSQL 9.5 naar PostgreSQL 9.6. Als u een upgrade wilt uitvoeren naar de volgende primaire versie, maakt u een [databasedump en -herstel](./howto-migrate-using-dump-and-restore.md) op een server die is gemaakt met de nieuwe versie van de engine.

### <a name="version-syntax"></a>Versie syntaxis
Vóór PostgreSQL versie 10 wordt het [postgresql-versie beleid](https://www.postgresql.org/support/versioning/) beschouwd als een _primaire versie_ -upgrade, zodat het eerste _of_ tweede nummer toeneemt. Bijvoorbeeld: 9,5 tot 9,6 werd beschouwd als een _primaire_ versie-upgrade. Vanaf versie 10 wordt alleen een wijziging in het eerste getal beschouwd als een primaire versie-upgrade. Een voor beeld: 10,0 tot 10,1 is een _kleine_ release-upgrade. Versie 10 tot 11 is een _primaire_ versie-upgrade.

## <a name="next-steps"></a>Volgende stappen
Zie [het document extensies](concepts-extensions.md)voor meer informatie over ondersteunde postgresql-uitbrei dingen.
