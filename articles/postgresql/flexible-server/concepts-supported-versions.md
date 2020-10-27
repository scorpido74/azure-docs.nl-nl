---
title: Ondersteunde versies-Azure Database for PostgreSQL-flexibele server
description: Hierin worden de ondersteunde primaire en secundaire versies van PostgreSQL in Azure Database for PostgreSQL-flexibele server beschreven.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: b139e2f14379e0cd52ca72c347d49bcb4512c473
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542077"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Ondersteunde PostgreSQL primaire versies in Azure Database for PostgreSQL-flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

Azure Database for PostgreSQL-flexibele server ondersteunt momenteel de volgende primaire versies:

## <a name="postgresql-version-12"></a>PostgreSQL-versie 12

De huidige secundaire versie is 12,4. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/12/static/release-12-4.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-11"></a>PostgreSQL-versie 11

De huidige secundaire versie is 11,9. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/11/static/release-11-9.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-10-and-older"></a>PostgreSQL-versie 10 en ouder

PostgreSQL versie 10 en ouder voor Azure Database for PostgreSQL-flexibele server worden niet ondersteund. Gebruik de implementatie optie voor [één server](../concepts-supported-versions.md) als u oudere versies nodig hebt.

## <a name="managing-upgrades"></a>Upgrades beheren

Het PostgreSQL-project ondervindt regel matig kleine releases voor het oplossen van gemelde bugs. Azure Database for PostgreSQL Servert automatisch servers met secundaire releases tijdens de maandelijkse implementaties van de service.

Automation voor de upgrade van de primaire versie wordt nog niet ondersteund. Er is bijvoorbeeld momenteel geen automatische upgrade van PostgreSQL 11 naar PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->