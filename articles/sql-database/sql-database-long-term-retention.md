---
title: Azure SQL Database back-ups Maxi maal tien jaar opslaan
description: Meer informatie over hoe Azure SQL Database ondersteuning biedt voor het opslaan van volledige database back-ups gedurende Maxi maal tien jaar.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 5d6f0797802a622ada1916752bc35c1bae2cde9f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689509"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Azure SQL Database back-ups Maxi maal tien jaar opslaan

Veel toepassingen hebben wettelijke, nalevings-of andere zakelijke doel einden waarvoor u de back-ups van de Data Base wilt behouden na de 7-35 dagen die worden meegeleverd met Azure SQL Database [automatische back-ups](sql-database-automated-backups.md). Door gebruik te maken van de functie voor lange termijn retentie (LTR) kunt u voor Maxi maal tien jaar opgegeven SQL database volledige back-ups in [Ra-GRS-](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) Blob-opslag. U kunt elke back-up vervolgens herstellen als een nieuwe database.

> [!NOTE]
> LTR kan worden ingeschakeld voor afzonderlijke en gepoolde data bases. Het is nog niet beschikbaar voor instance-data bases in beheerde exemplaren. U kunt SQL Agent-taken gebruiken voor het plannen van [back-ups met alleen-kopiëren](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) als alternatief voor een oplossing van meer dan 35 dagen.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Hoe SQL Database lange termijn retentie werkt

Lange termijn retentie van back-ups (LTR) maakt gebruik van de volledige database back-ups die [automatisch worden gemaakt](sql-database-automated-backups.md) voor het inschakelen van punt-tijd herstel (PITR). Als een LTR-beleid is geconfigureerd, worden deze back-ups gekopieerd naar verschillende blobs voor lange termijn opslag. De Kopieer bewerking is een achtergrond taak die geen invloed heeft op de prestaties van de data base. De LTR-back-ups worden bewaard gedurende een periode die door het LTR-beleid is ingesteld. Het LTR-beleid voor elke SQL database kan ook bepalen hoe vaak de LTR-back-ups worden gemaakt. Om die flexibiliteit in te scha kelen, kunt u het beleid definiëren met een combi natie van vier para meters: wekelijks retentie van back-ups (W), maandelijkse back-up (M), jaarlijks back-upbewaaring (Y) en week van jaar (WeekOfYear). Als u W opgeeft, wordt één back-up elke week gekopieerd naar de lange termijn opslag. Als u M opgeeft, wordt één back-up in de eerste week van elke maand gekopieerd naar de lange termijn opslag. Als u Y opgeeft, wordt één back-up in de week die is opgegeven door WeekOfYear gekopieerd naar de lange termijn opslag. Elke back-up wordt opgeslagen in de lange termijn opslag voor de periode die door deze para meters is opgegeven. Elke wijziging van het LTR-beleid is van toepassing op de toekomstige back-ups. Als de opgegeven WeekOfYear bijvoorbeeld in het verleden ligt wanneer het beleid is geconfigureerd, wordt het volgende jaar de eerste LTR-back-up gemaakt. 

Voor beelden van het LTR-beleid:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   De derde volledige back-up van elk jaar wordt vijf jaar bewaard.
   
- W = 0, M = 3, Y = 0

   De eerste volledige back-up van elke maand wordt drie maanden bewaard.

- W = 12, M = 0, Y = 0

   Elke wekelijkse volledige back-up wordt twaalf weken bewaard.

- W = 6, M = 12, Y = 10, WeekOfYear = 16

   Elke wekelijkse volledige back-up wordt zes weken bewaard. Met uitzonde ring van de eerste volledige back-up van elke maand, die gedurende 12 maanden wordt bewaard. Met uitzonde ring van de volledige back-up die is uitgevoerd op een zestiende week van het jaar, die gedurende tien jaar wordt bewaard. 

In de volgende tabel ziet u de uitgebracht en de verval datum van de back-ups op lange termijn voor het volgende beleid:

W = 12 weken (84 dagen), M = 12 maanden (365 dagen), Y = 10 jaar (3650 dagen), WeekOfYear = 15 (week na 15 april)

   ![LTR-voor beeld](./media/sql-database-long-term-retention/ltr-example.png)



Als u het bovenstaande beleid wijzigt en W = 0 (geen wekelijkse back-ups) instelt, worden de uitgebracht van back-upkopieën gewijzigd, zoals wordt weer gegeven in de bovenstaande tabel door de gemarkeerde datums. De hoeveelheid opslag die nodig is om deze back-ups te bewaren, vermindert dienovereenkomstig. 

> [!IMPORTANT]
> De timing van de afzonderlijke LTR-back-ups wordt bepaald door Azure SQL Database. U kunt niet hand matig een LTR-back-up maken of de timing van het maken van de back-up regelen. Na het configureren van een LTR-beleid kan het tot zeven dagen duren voordat de eerste LTR-back-up wordt weer gegeven in de lijst met beschik bare back-ups.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Geo-replicatie en lange termijn retentie van back-ups

Als u gebruikmaakt van actieve geo-replicatie of failover-groepen als uw bedrijfs continuïteits oplossing, moet u voor bereide failovers voorbereiden en hetzelfde LTR-beleid configureren voor de geo-secundaire data base. Uw LTR-opslag kosten worden niet verhoogd omdat er geen back-ups worden gegenereerd op basis van de secundaire zones. Alleen wanneer de secundaire primair wordt, worden de back-ups gemaakt. Het zorgt voor een niet-onderbroken generatie van de LTR-back-ups wanneer de failover wordt geactiveerd en de primaire naar de secundaire regio wordt verplaatst. 

> [!NOTE]
> Wanneer de oorspronkelijke primaire data base herstelt van een storing die de failover heeft veroorzaakt, wordt deze een nieuwe secundaire. Daarom wordt het maken van de back-up niet hervat en wordt het bestaande LTR-beleid pas van kracht nadat het opnieuw wordt ingesteld als primair. 

## <a name="configure-long-term-backup-retention"></a>Langetermijnretentie van back-ups configureren

Zie voor meer informatie over het configureren van de lange termijn retentie met behulp van de Azure Portal of Power shell [Azure SQL database lange termijn retentie van back-ups beheren](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Data base herstellen vanuit LTR-back-up

Als u een Data Base wilt herstellen vanuit de LTR-opslag, kunt u een specifieke back-up selecteren op basis van de tijds tempel. De data base kan worden hersteld naar een bestaande server onder hetzelfde abonnement als de oorspronkelijke data base. Voor informatie over het herstellen van een Data Base vanuit een LTR-back-up met behulp van de Azure Portal of Power shell, Zie [Azure SQL database lange termijn retentie van back-ups beheren](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Volgende stappen

Omdat database back-ups gegevens beveiligen tegen onbedoelde beschadiging of verwijdering, vormen ze een essentieel onderdeel van een strategie voor bedrijfs continuïteit en herstel na nood gevallen. Zie [overzicht van bedrijfs continuïteit](sql-database-business-continuity.md)voor meer informatie over de andere SQL database oplossingen voor bedrijfs continuïteit.
