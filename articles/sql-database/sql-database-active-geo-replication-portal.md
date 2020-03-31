---
title: 'Zelfstudie: Geo-replicatie & failover in portal'
description: Configureer georeplicatie voor een enkele of gepoolde database in Azure SQL Database met behulp van de Azure-portal en start failover.
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
ms.openlocfilehash: 601c537a51e29ae1f107127e1b83c07448eee9ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256430"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Actieve georeplicatie configureren voor Azure SQL Database in de Azure-portal en failover starten

In dit artikel ziet u hoe u [actieve georeplicatie configureert voor afzonderlijke en samengevoegde databases](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) in Azure SQL Database met behulp van de [Azure-portal](https://portal.azure.com) en failover starten.

Zie Aanbevolen procedures voor [het gebruik van failovergroepen met enkele en samengevoegde databases](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)voor informatie over groepen met automatische failover met één en gepoolde databases. Zie [Aanbevolen procedures voor het gebruik van failovergroepen met beheerde instanties](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances)voor informatie over groepen voor automatisch failoveren met beheerde instanties .

## <a name="prerequisites"></a>Vereisten

Als u actieve georeplicatie wilt configureren met behulp van de Azure-portal, hebt u de volgende bron nodig:

* Een Azure SQL-database: de primaire database die u wilt repliceren naar een andere geografische regio.

> [!Note]
> Wanneer u Azure-portal gebruikt, u alleen een secundaire database maken binnen hetzelfde abonnement als het primaire. Als secundaire database in een ander abonnement moet zijn, gebruikt u [Create Database REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) of ALTER DATABASE [Transact-SQL API](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Een secundaire database toevoegen

Met de volgende stappen wordt een nieuwe secundaire database gemaakt in een partnerschap voor georeplicatie.  

Als u een secundaire database wilt toevoegen, moet u de eigenaar of mede-eigenaar van het abonnement zijn.

De secundaire database heeft dezelfde naam als de primaire database en heeft standaard dezelfde servicelaag en rekengrootte. De secundaire database kan een enkele database of een samengevoegde database zijn. Zie voor meer informatie [het op DTU gebaseerde inkoopmodel](sql-database-service-tiers-dtu.md) en [vCore-gebaseerd inkoopmodel](sql-database-service-tiers-vcore.md).
Nadat het secundaire is gemaakt en gezaaid, beginnen gegevens te repliceren van de primaire database naar de nieuwe secundaire database.

> [!NOTE]
> Als de partnerdatabase al bestaat (bijvoorbeeld als gevolg van het beëindigen van een eerdere georeplicatierelatie), mislukt de opdracht.

1. Blader in de [Azure-portal](https://portal.azure.com)naar de database die u wilt instellen voor georeplicatie.
2. Selecteer op de pagina **SQL-database georeplicatie**en selecteer vervolgens het gebied om de secundaire database te maken. U een andere regio selecteren dan de regio die de primaire database host, maar we raden het [gekoppelde gebied](../best-practices-availability-paired-regions.md)aan.

    ![Geo-replicatie configureren](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Selecteer of configureer de server- en prijscategorie voor de secundaire database.

    ![Secundair configureren](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Optioneel u een secundaire database toevoegen aan een elastische groep. Als u de secundaire database in een groep wilt maken, klikt u op **elastische groep** en selecteert u een groep op de doelserver. Er moet al een pool op de doelserver bestaan. Deze werkstroom maakt geen pool.
5. Klik **op Maken** om het secundaire toe te voegen.
6. De secundaire database wordt gemaakt en het zaaiproces begint.

    ![Secundair configureren](./media/sql-database-geo-replication-portal/seeding0.png)
7. Wanneer het zaaiproces is voltooid, geeft de secundaire database de status ervan weer.

    ![Zaaien compleet](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Een failover starten

De secundaire database kan worden overgeschakeld om de primaire te worden.  

1. Blader in de [Azure-portal](https://portal.azure.com)naar de primaire database in de partnerschap voor georeplicatie.
2. Selecteer op het SQL Database-blad **Alle instellingen** > **geo-replicatie**.
3. Selecteer in de **lijst SECONDARIES** de database die u de nieuwe primaire wilt worden en klik op **Failover**.

    ![Failover](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Klik **op Ja** om de failover te starten.

De opdracht schakelt de secundaire database onmiddellijk over naar de primaire rol. Dit proces moet normaal gesproken binnen 30 sec of minder worden voltooid.

Er is een korte periode waarin beide databases niet beschikbaar zijn (in de volgorde van 0 tot 25 seconden) terwijl de rollen worden geschakeld. Als de primaire database meerdere secundaire databases heeft, configureert de opdracht automatisch de andere secondaries om verbinding te maken met het nieuwe primaire. De hele operatie moet onder normale omstandigheden minder dan een minuut in beslag nemen.

> [!NOTE]
> Deze opdracht is ontworpen voor een snel herstel van de database in geval van een storing. Het activeert failover zonder gegevenssynchronisatie (gedwongen failover).  Als de primaire online is en het plegen van transacties wanneer de opdracht wordt uitgegeven, kan er gegevensverlies optreden.

## <a name="remove-secondary-database"></a>Secundaire database verwijderen

Deze bewerking beëindigt de replicatie definitief in de secundaire database en wijzigt de rol van het secundaire in een reguliere lees-schrijfdatabase. Als de verbinding met de secundaire database is verbroken, slaagt de opdracht, maar wordt de secundaire pas lezen-schrijven nadat de verbinding is hersteld.  

1. Blader in de [Azure-portal](https://portal.azure.com)naar de primaire database in de partnerschap voor georeplicatie.
2. Selecteer georeplicatie op de pagina **SQL-database**.
3. Selecteer in de **secondaries-lijst** de database die u wilt verwijderen uit het partnerschap voor georeplicatie.
4. Klik **op Replicatie stoppen**.

    ![Secundair verwijderen](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Er wordt een bevestigingsvenster geopend. Klik **op Ja** om de database te verwijderen uit het partnerschap voor georeplicatie. (Stel deze in op een lees-schrijfdatabase die geen deel uitmaakt van replicatie.)

## <a name="next-steps"></a>Volgende stappen

* Zie [Actieve georeplicatie](sql-database-active-geo-replication.md)voor meer informatie over actieve georeplicatie.
* Zie Groepen automatisch failoveren voor meer informatie over groepen voor automatisch [failoveren](sql-database-auto-failover-group.md)
* Zie [Bedrijfscontinuïteitsoverzicht](sql-database-business-continuity.md)voor een overzicht van bedrijfscontinuïteiten.
