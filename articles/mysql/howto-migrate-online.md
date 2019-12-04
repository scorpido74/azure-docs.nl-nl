---
title: Migratie met minimale downtime-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u een minimale downtime-migratie van een MySQL-data base naar Azure Database for MySQL uitvoert met behulp van de Azure Database Migration Service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 9f166323cc72cd22cc4dd28babdfd056100a32e0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774140"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimale migratie van downtime naar Azure Database for MySQL
U kunt MySQL-migraties uitvoeren om te Azure Database for MySQL met minimale downtime door gebruik te maken van de zojuist geïntroduceerde **voortdurende synchronisatie mogelijkheid** voor de [Azure database Migration service](https://aka.ms/get-dms) (DMS). Deze functionaliteit beperkt de hoeveelheid downtime die door de toepassing wordt gemaakt.

## <a name="overview"></a>Overzicht
Azure DMS voert een initiële belasting van uw on-premises naar Azure Database for MySQL uit en synchroniseert voortdurend nieuwe trans acties naar Azure terwijl de toepassing actief blijft. Nadat de gegevens op de doel-Azure-zijde zijn opgevangen, stopt u de toepassing voor een kort tijdstip (minimale downtime), wacht u tot de laatste batch met gegevens (vanaf het moment dat u de toepassing stopt totdat de toepassing daad werkelijk niet beschikbaar is voor het uitvoeren van een nieuw verkeer) in het doel en werk vervolgens uw connection string om naar Azure te verwijzen. Wanneer u klaar bent, is uw toepassing Live in Azure.

![Doorlopend synchroniseren met de Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Volgende stappen
- Bekijk de video [eenvoudig MySQL/PostgreSQL-apps migreren naar een door Azure beheerde service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), die een demonstratie bevat van het migreren van MySQL-apps naar Azure database for MySQL.
- Raadpleeg de zelf studie [MySQL migreren naar Azure database for MySQL online met behulp van DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
