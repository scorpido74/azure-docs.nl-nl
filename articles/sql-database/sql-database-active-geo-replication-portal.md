---
title: 'Zelf studie: failover van geo-replicatie & in de portal'
description: Geo-replicatie configureren voor één of gegroepeerde Data base in Azure SQL Database met behulp van de Azure Portal en failover starten.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 59616fb217b28a8c47d9a5d13e2f4c1b9a8f6bb3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605226"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Actieve geo-replicatie configureren voor Azure SQL Database in de Azure Portal en failover initiëren

In dit artikel wordt beschreven hoe u [actieve geo-replicatie kunt configureren voor één en gegroepeerde Data bases](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) in Azure SQL database met behulp van de [Azure Portal](https://portal.azure.com) en om failover te initiëren.

Zie [Aanbevolen procedures voor het gebruik van failover-groepen met één en gegroepeerde Data bases](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)voor meer informatie over groepen van automatische failovers met één en gepoolde data base. Zie [Aanbevolen procedures voor het gebruik van failover-groepen met beheerde exemplaren](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances)voor meer informatie over groepen van automatische failovers met beheerde exemplaren.

## <a name="prerequisites"></a>Vereisten

Als u actieve geo-replicatie wilt configureren met behulp van de Azure Portal, hebt u de volgende resource nodig:

* Een Azure-SQL database: de primaire data base die u naar een andere geografische regio wilt repliceren.

> [!Note]
> Wanneer u Azure Portal gebruikt, kunt u alleen een secundaire data base maken binnen hetzelfde abonnement als het primaire. Als de secundaire data base verplicht moet zijn in een ander abonnement, gebruikt u [Create data base rest API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) of [ALTER data base Transact-SQL API](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Een secundaire data base toevoegen

Met de volgende stappen maakt u een nieuwe secundaire data base in een partner verband met geo-replicatie.  

Als u een secundaire Data Base wilt toevoegen, moet u de eigenaar van het abonnement of mede-eigenaar zijn.

De secundaire data base heeft dezelfde naam als de primaire data base en heeft standaard dezelfde servicelaag en berekenings grootte. De secundaire data base kan één data base of een gegroepeerde Data Base zijn. Zie voor meer informatie [het op DTU gebaseerde aankoop model](sql-database-service-tiers-dtu.md) en [vCore aankoop model](sql-database-service-tiers-vcore.md).
Nadat de secundaire data base is gemaakt en geseedd, begint de gegevens van de hoofd database naar de nieuwe secundaire data base.

> [!NOTE]
> Als de partner database al bestaat (bijvoorbeeld als gevolg van het beëindigen van een eerdere geo-replicatie relatie), mislukt de opdracht.

1. Blader in het [Azure Portal](https://portal.azure.com)naar de data base die u wilt instellen voor geo-replicatie.
2. Selecteer **geo-replicatie**op de pagina SQL database en selecteer vervolgens de regio om de secundaire data base te maken. U kunt een andere regio selecteren dan de regio die als host fungeert voor de primaire data base, maar we raden u aan om de [gekoppelde regio](../best-practices-availability-paired-regions.md)te kiezen.

    ![Geo-replicatie configureren](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Selecteer of configureer de server en prijs categorie voor de secundaire data base.

    ![Secundair configureren](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Desgewenst kunt u een secundaire data base toevoegen aan een elastische pool. Als u de secundaire data base in een groep wilt maken, klikt u op **elastische pool** en selecteert u een pool op de doel server. Er moet al een groep bestaan op de doel server. Met deze werk stroom wordt geen groep gemaakt.
5. Klik op **maken** om de secundaire toe te voegen.
6. De secundaire data base wordt gemaakt en het seeding proces begint.

    ![Secundair configureren](./media/sql-database-geo-replication-portal/seeding0.png)
7. Wanneer het seeding proces is voltooid, wordt de status van de secundaire Data Base weer gegeven.

    ![Seeding voltooid](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Een failover initiëren

De secundaire data base kan worden verwisseld om de primaire te worden.  

1. Blader in het [Azure Portal](https://portal.azure.com)naar de primaire data base in het samenwerkings verband met geo-replicatie.
2. Selecteer op de Blade SQL database **alle instellingen** > **geo-replicatie**.
3. Selecteer in de lijst met **secundaire** zones de data base waarvan u de nieuwe primaire wilt worden en klik op **geforceerde failover**.

    ![cluster](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Klik op **Ja** om de failover te starten.

De opdracht schakelt de secundaire data base onmiddellijk over naar de primaire rol. Dit proces moet normaal gesp roken binnen 30 seconden worden voltooid.

Er is een korte periode waarin beide data bases niet beschikbaar zijn (in de volg orde van 0 tot 25 seconden) terwijl de rollen worden overgeschakeld. Als de primaire data base meerdere secundaire data bases heeft, configureert de opdracht automatisch de andere gegevens in een verbinding met de nieuwe primaire. Het duurt minder dan een minuut om de gehele bewerking uit te voeren onder normale omstandigheden.

> [!NOTE]
> Deze opdracht is ontworpen voor snelle herstel van de data base in het geval van een storing. Er wordt een failover geactiveerd zonder gegevens synchronisatie (geforceerde failover).  Als de primaire online is en trans acties worden doorgevoerd wanneer de opdracht wordt uitgegeven, kan er gegevens verlies optreden.

## <a name="remove-secondary-database"></a>Secundaire data base verwijderen

Met deze bewerking wordt de replicatie permanent beëindigd en wordt de rol van de secundaire data base gewijzigd in een normale Lees-write-data base. Als de verbinding met de secundaire data base is verbroken, wordt de opdracht uitgevoerd, maar de secundaire wordt niet lezen-schrijven totdat de verbinding is hersteld.  

1. Blader in het [Azure Portal](https://portal.azure.com)naar de primaire data base in het samenwerkings verband met geo-replicatie.
2. Selecteer **geo-replicatie**op de pagina SQL database.
3. Selecteer in de lijst met **secundaire** zones de data base die u wilt verwijderen uit het relatie tussen geo-replicatie.
4. Klik op **Replicatie stoppen**.

    ![Secundaire verwijderen](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Er wordt een bevestigings venster geopend. Klik op **Ja** om de data base te verwijderen uit het samenwerkings verband met geo-replicatie. (Stel de eigenschap in op een Data Base voor lezen/schrijven die geen deel uitmaakt van een replicatie.)

## <a name="next-steps"></a>Volgende stappen

* Zie [actieve geo-replicatie](sql-database-active-geo-replication.md)voor meer informatie over actieve geo-replicatie.
* Zie [groepen met automatische failover](sql-database-auto-failover-group.md) voor meer informatie over groepen voor automatische failover
* Zie [overzicht van bedrijfs continuïteit](sql-database-business-continuity.md)voor een overzicht en scenario's voor bedrijfs continuïteit.
