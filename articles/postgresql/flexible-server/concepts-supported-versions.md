---
title: Ondersteunde versies-Azure Database for PostgreSQL-flexibele server
description: Hierin worden de ondersteunde primaire en secundaire versies van post gres in Azure Database for PostgreSQL-flexibele server beschreven.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 9d651a38361ccaa7f8788fb4a375b39b2953228b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934890"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Ondersteunde PostgreSQL primaire versies in Azure Database for PostgreSQL-flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

Azure Database for PostgreSQL-flexibele server ondersteunt momenteel de volgende primaire versies:

## <a name="postgresql-version-12"></a>PostgreSQL-versie 12

De huidige secundaire versie is 12,1. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/12/static/release-12-1.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-11"></a>PostgreSQL-versie 11

De huidige secundaire versie is 11,8. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/11/static/release-11-8.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-10-and-older"></a>PostgreSQL-versie 10 en ouder

PostgreSQL versie 10 en ouder voor Azure Database for PostgreSQL-flexibele server worden niet ondersteund. Gebruik de implementatie optie voor [één server](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) als u oudere versies nodig hebt.

## <a name="managing-upgrades"></a>Upgrades beheren

Het PostgreSQL-project ondervindt regel matig kleine releases voor het oplossen van gemelde bugs. Azure Database for PostgreSQL Servert automatisch servers met secundaire releases tijdens de maandelijkse implementaties van de service.

Het automatisch bijwerken van de primaire versie wordt nog niet ondersteund. Er is bijvoorbeeld momenteel geen automatische upgrade van PostgreSQL 11 naar PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->
