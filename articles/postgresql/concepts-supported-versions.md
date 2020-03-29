---
title: Ondersteunde versies - Azure Database voor PostgreSQL - Single Server
description: Beschrijft de ondersteunde postgres-hoofd- en secundaire versies in Azure Database voor PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792233"
---
# <a name="supported-postgresql-major-versions"></a>Ondersteunde PostgreSQL-hoofdversies
Microsoft streeft ernaar n-2-versies van de PostgreSQL-engine in Azure Database voor PostgreSQL - Single Server te ondersteunen. De versies zouden de huidige hoofdversie op Azure (n) en de twee voorgaande hoofdversies (-2) zijn.

Azure Database voor PostgreSQL ondersteunt momenteel de volgende belangrijke versies:

## <a name="postgresql-version-11"></a>PostgreSQL versie 11
De huidige kleine release is 11,5. Raadpleeg de [PostgreSQL-documentatie](https://www.postgresql.org/docs/11/static/release-11-5.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-10"></a>PostgreSQL versie 10
De huidige kleine release is 10.10 uur. Raadpleeg de [PostgreSQL-documentatie](https://www.postgresql.org/docs/10/static/release-10-10.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-96"></a>PostgreSQL versie 9.6
De huidige kleine release is 9.6.15. Raadpleeg de [PostgreSQL-documentatie](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-95"></a>PostgreSQL versie 9.5
De huidige kleine release is 9.5.19. Raadpleeg de [PostgreSQL-documentatie](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="managing-upgrades"></a>Upgrades beheren
Het PostgreSQL-project geeft regelmatig kleine releases uit om gerapporteerde bugs op te lossen. Azure Database voor PostgreSQL patcht automatisch servers met kleine releases tijdens de maandelijkse implementaties van de service. 

Automatische upgrade voor primaire versie wordt niet ondersteund. Er is bijvoorbeeld geen automatische upgrade van PostgreSQL 9.5 naar PostgreSQL 9.6. Als u een upgrade wilt uitvoeren naar de volgende primaire versie, maakt u een [databasedump en -herstel](./howto-migrate-using-dump-and-restore.md) op een server die is gemaakt met de nieuwe versie van de engine.

### <a name="version-syntax"></a>Syntaxis van de versie
Vóór PostgreSQL versie 10 beschouwde het [PostgreSQL-versiebeleid](https://www.postgresql.org/support/versioning/) een _belangrijke versie-upgrade_ als een toename van het eerste _of_ tweede getal. Bijvoorbeeld, 9,5 tot 9,6 werd beschouwd als een _belangrijke_ versie upgrade. Vanaf versie 10 wordt alleen een wijziging in het eerste nummer beschouwd als een belangrijke versie-upgrade. Bijvoorbeeld, 10.0 tot 10.1 is een _kleine_ release upgrade. Versie 10 tot en met 11 is een _belangrijke_ versie-upgrade.

## <a name="next-steps"></a>Volgende stappen
Zie [het extensiesdocument voor](concepts-extensions.md)informatie over ondersteunde PostgreSQL-extensies.
