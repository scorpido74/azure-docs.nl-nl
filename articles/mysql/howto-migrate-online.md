---
title: Migratie met minimale downtime-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u een minimale downtime-migratie van een MySQL-data base naar Azure Database for MySQL uitvoert met behulp van de Azure Database Migration Service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 978312c6238315cf836cf7c26a2ee63e452d3b85
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90884600"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimale migratie van downtime naar Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

U kunt MySQL-migraties uitvoeren om te Azure Database for MySQL met minimale downtime door gebruik te maken van de zojuist geïntroduceerde **voortdurende synchronisatie mogelijkheid** voor de [Azure database Migration service](https://aka.ms/get-dms) (DMS). Deze functionaliteit beperkt de hoeveelheid downtime die door de toepassing wordt gemaakt.

## <a name="overview"></a>Overzicht
Azure DMS voert een initiële belasting van uw on-premises naar Azure Database for MySQL uit en synchroniseert voortdurend nieuwe trans acties naar Azure terwijl de toepassing actief blijft. Nadat de gegevens op de doel-Azure-zijde zijn opgevangen, stopt u de toepassing voor een kort tijdstip (minimale downtime), wacht u tot de laatste batch met gegevens (vanaf het moment dat u de toepassing stopt totdat de toepassing daad werkelijk niet beschikbaar is om een nieuw verkeer te maken) en werkt u vervolgens uw connection string bij om naar Azure te verwijzen. Wanneer u klaar bent, is uw toepassing Live in Azure.

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Doorlopend synchroniseren met de Azure Database Migration Service":::

## <a name="next-steps"></a>Volgende stappen
- Bekijk de video [eenvoudig MySQL/PostgreSQL-apps migreren naar een door Azure beheerde service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), die een demonstratie bevat van het migreren van MySQL-apps naar Azure database for MySQL.
- Raadpleeg de zelf studie [MySQL migreren naar Azure database for MySQL online met behulp van DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
