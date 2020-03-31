---
title: Minimale downtime-migratie - Azure Database voor MySQL
description: In dit artikel wordt beschreven hoe u een minimale downtime-migratie van een MySQL-database naar Azure Database voor MySQL uitvoeren met behulp van de Azure Database Migration Service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: df818fa4106aec341607d8142b2a672699b8e9d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063348"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimale downtime-migratie naar Azure Database voor MySQL
U MySQL-migraties naar Azure Database voor MySQL uitvoeren met minimale downtime met behulp van de onlangs geïntroduceerde **synchronisatiemogelijkheid** voor de [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Deze functionaliteit beperkt de hoeveelheid downtime die door de toepassing wordt opgelopen.

## <a name="overview"></a>Overzicht
Azure DMS voert een eerste belasting van uw on-premises uit naar Azure Database voor MySQL en synchroniseert vervolgens continu alle nieuwe transacties met Azure terwijl de toepassing actief blijft. Nadat de gegevens de doelazure-kant hebben ingehaald, stopt u de toepassing voor een kort moment (minimale downtime), wacht u op de laatste batch gegevens (vanaf het moment dat u de toepassing stopt totdat de toepassing effectief niet beschikbaar is om nieuw verkeer te verwerken) om te vangen in het doel en werk vervolgens uw verbindingstekenreeks bij om naar Azure te wijzen. Wanneer u klaar bent, is uw toepassing live op Azure!

![Continue synchronisatie met de Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Volgende stappen
- Bekijk de video [MySQL/PostgreSQL-apps eenvoudig migreren naar Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), die een demo bevat die laat zien hoe je MySQL-apps migreert naar Azure Database voor MySQL.
- Zie de zelfstudie [MySQL migreren naar Azure Database voor MySQL online met DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
