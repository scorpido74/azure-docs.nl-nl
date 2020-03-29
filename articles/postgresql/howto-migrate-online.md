---
title: Minimale downtime-migratie naar Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u een minimale downtime-migratie van een PostgreSQL-database naar Azure Database voor PostgreSQL - Single Server uitvoeren met behulp van de Azure Database Migration Service.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 93cd390889c023adf1c30a8470e1c2298598439e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65067514"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Minimale downtime-migratie naar Azure Database voor PostgreSQL - Single Server
U PostgreSQL-migraties naar Azure Database voor PostgreSQL uitvoeren met minimale downtime met behulp van de onlangs geïntroduceerde **synchronisatiemogelijkheid** voor de [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Deze functionaliteit beperkt de hoeveelheid downtime die door de toepassing wordt opgelopen.

## <a name="overview"></a>Overzicht
Azure DMS voert een eerste belasting van uw on-premises uit naar Azure Database voor PostgreSQL en synchroniseert vervolgens continu alle nieuwe transacties met Azure terwijl de toepassing actief blijft. Nadat de gegevens de doelazure-kant hebben ingehaald, stopt u de toepassing voor een kort moment (minimale downtime), wacht u op de laatste batch gegevens (vanaf het moment dat u de toepassing stopt totdat de toepassing effectief niet beschikbaar is om nieuw verkeer te verwerken) om te vangen in het doel en werk vervolgens uw verbindingstekenreeks bij om naar Azure te wijzen. Wanneer u klaar bent, is uw toepassing live op Azure!

![Continue synchronisatie met de Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Volgende stappen
- Bekijk de [video-app modernisering met Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), die een demo bevat die laat zien hoe PostgreSQL-apps migreren naar Azure Database voor PostgreSQL.
- Zie de zelfstudie [Remigreren PostgreSQL naar Azure Database voor PostgreSQL online met Behulp van DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
