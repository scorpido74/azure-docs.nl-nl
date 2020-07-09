---
title: Migratie met minimale downtime naar Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u een minimale downtime-migratie uitvoert van een PostgreSQL-Data Base naar Azure Database for PostgreSQL-één-server met behulp van de Azure Database Migration Service.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 0b7c6392fbd795a078e9ec8f61281d95cf6363bc
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86112388"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Migratie met minimale downtime naar Azure Database for PostgreSQL-één server
U kunt PostgreSQL-migraties uitvoeren om te Azure Database for PostgreSQL met minimale downtime door gebruik te maken van de zojuist geïntroduceerde **voortdurende synchronisatie mogelijkheid** voor de [Azure database Migration service](https://aka.ms/get-dms) (DMS). Deze functionaliteit beperkt de hoeveelheid downtime die door de toepassing wordt gemaakt.

## <a name="overview"></a>Overzicht
Azure DMS voert een initiële belasting van uw on-premises naar Azure Database for PostgreSQL uit en synchroniseert voortdurend nieuwe trans acties naar Azure terwijl de toepassing actief blijft. Nadat de gegevens op de doel-Azure-zijde zijn opgevangen, stopt u de toepassing voor een kort tijdstip (minimale downtime), wacht u tot de laatste batch met gegevens (vanaf het moment dat u de toepassing stopt totdat de toepassing daad werkelijk niet beschikbaar is om een nieuw verkeer te maken) en werkt u vervolgens uw connection string bij om naar Azure te verwijzen. Wanneer u klaar bent, is uw toepassing Live in Azure.

![Doorlopend synchroniseren met de Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Volgende stappen
- Bekijk de modernisering van de video- [apps met Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), die een demonstratie bevat over het migreren van postgresql-apps naar Azure database for PostgreSQL.
- Raadpleeg de zelf studie [migratie postgresql om online te Azure database for PostgreSQL met behulp van DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
