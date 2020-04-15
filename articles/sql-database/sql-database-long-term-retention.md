---
title: Langetermijnretentie van back-ups
description: Ontdek hoe Azure SQL Database het opslaan van volledige databaseback-ups voor maximaal 10 jaar ondersteunt via het langetermijnbewaarbeleid.
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
ms.openlocfilehash: d015eea21bcfa499d6751e024a882a7316b7f1a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380763"
---
# <a name="azure-sql-database-long-term-retention"></a>Azure SQL Database langetermijnretentie

Veel toepassingen hebben wettelijke, nalevings- of andere zakelijke doeleinden waarvoor u databaseback-ups moet behouden na de automatische [back-ups](sql-database-automated-backups.md)van De ASQL Database. Door de LTR-functie (Long-Term Retention) te gebruiken, u bepaalde SQL-databasevolledige back-ups maximaal 10 jaar opslaan in Azure Blob-opslag met georedundante opslag met leestoegang. U vervolgens elke back-up herstellen als een nieuwe database. Zie [Redundantie azure storage](../storage/common/storage-redundancy.md)voor meer informatie over redundantie voor Azure Storage. 

Lange tijd bewaren kan worden ingeschakeld voor afzonderlijke en gepoolde databases en is in beperkte openbare preview voor Azure SQL Database beheerde exemplaren. 

> [!NOTE]
> U SQL Agent-taken gebruiken om [alleen-kopiëren-databaseback-ups](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) te plannen als alternatief voor LTR na 35 dagen.


## <a name="how-sql-database-long-term-retention-works"></a>Hoe langetermijnretentie van SQL Database werkt

LTR (Long-term backup retention (LTR) maakt gebruik van de volledige databaseback-ups die automatisch worden [gemaakt](sql-database-automated-backups.md) om point-time restore (PITR) mogelijk te maken. Als een LTR-beleid is geconfigureerd, worden deze back-ups gekopieerd naar verschillende blobs voor langdurige opslag. De kopie is een achtergrondtaak die geen invloed heeft op de prestaties van de databasewerkbelasting. Het LTR-beleid voor elke SQL-database kan ook aangeven hoe vaak de LTR-back-ups worden gemaakt.

Om LTR in te schakelen, u een beleid definiëren met een combinatie van vier parameters: wekelijkse back-upretentie (W), maandelijkse back-upretentie (M), jaarlijkse back-upretentie (Y) en week van het jaar (WeekOfYear). Als u W opgeeft, wordt elke week één back-up gekopieerd naar de opslag op lange termijn. Als u M opgeeft, wordt de eerste back-up van elke maand gekopieerd naar de opslag op lange termijn. Als u Y opgeeft, wordt één back-up tijdens de week die door WeekOfYear is opgegeven, gekopieerd naar de opslag op lange termijn. Als de opgegeven WeekOfYear zich in het verleden bevindt wanneer het beleid is geconfigureerd, wordt de eerste LTR-back-up in het volgende jaar gemaakt. Elke back-up wordt bewaard in de opslag op lange termijn volgens de beleidsparameters die zijn geconfigureerd wanneer de LTR-back-up wordt gemaakt.

> [!NOTE]
> Elke wijziging in het LTR-beleid is alleen van toepassing op toekomstige back-ups. Als bijvoorbeeld wekelijkse back-upretentie (W), maandelijkse back-upretentie (M) of jaarlijkse back-upretentie (Y) wordt gewijzigd, is de nieuwe bewaarinstelling alleen van toepassing op nieuwe back-ups. Het behoud van bestaande back-ups wordt niet gewijzigd. Als het uw bedoeling is om oude LTR-back-ups te verwijderen voordat de bewaartermijn verstrijkt, moet u [de back-ups handmatig verwijderen.](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups)
> 

Voorbeelden van het LTR-beleid:

-  W=0, M=0, Y=5, WeekOfYear=3

   De derde volledige back-up van elk jaar wordt vijf jaar bewaard.
   
- W=0, M=3, Y=0

   De eerste volledige back-up van elke maand wordt drie maanden bewaard.

- W=12, M=0, Y=0

   Elke wekelijkse volledige back-up wordt 12 weken bewaard.

- W=6, M=12, Y=10, WeekOfYear=16

   Elke wekelijkse volledige back-up wordt zes weken bewaard. Behalve de eerste volledige back-up van elke maand, die zal worden bewaard voor 12 maanden. Behalve de volledige back-up genomen op de 16e week van het jaar, die zal worden bewaard voor 10 jaar. 

De volgende tabel illustreert de cadans en het verstrijken van de langetermijnback-ups voor het volgende beleid:

W=12 weken (84 dagen), M=12 maanden (365 dagen), Y=10 jaar (3650 dagen), WeekOfYear=15 (week na 15 april)

   ![Ltr voorbeeld](./media/sql-database-long-term-retention/ltr-example.png)



Als u het bovenstaande beleid wijzigt en W=0 (geen wekelijkse back-ups) instelt, verandert de cadans van back-ups zoals weergegeven in de bovenstaande tabel door de gemarkeerde datums. Het opslagbedrag dat nodig is om deze back-ups te behouden, zou dienovereenkomstig verminderen. 

> [!IMPORTANT]
> De timing van de afzonderlijke LTR-back-ups wordt beheerd door Azure SQL Database. U niet handmatig een LTR-back-up maken of de timing van de back-upmaken beheren. Na het configureren van een LTR-beleid kan het tot 7 dagen duren voordat de eerste LTR-back-up wordt weergegeven op de lijst met beschikbare back-ups.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Georeplicatie en behoud van back-upop lange termijn

Als u actieve georeplicatie- of failovergroepen gebruikt als uw bedrijfscontinuïteitsoplossing, moet u zich voorbereiden op eventuele failovers en hetzelfde LTR-beleid configureren voor de geosecundaire database. Uw LTR-opslagkosten zullen niet stijgen omdat back-ups niet worden gegenereerd vanuit de secondaries. Pas wanneer het secundaire primair wordt, worden de back-ups gemaakt. Het zorgt voor niet-onderbroken generatie van de LTR-back-ups wanneer de failover wordt geactiveerd en de primaire wordt verplaatst naar het secundaire gebied. 

> [!NOTE]
> Wanneer de oorspronkelijke primaire database herstelt van een storing die de failover heeft veroorzaakt, wordt deze een nieuwe secundaire. Daarom wordt de back-up niet hervat en wordt het bestaande LTR-beleid pas van kracht als het de primaire wordt. 

## <a name="managed-instance-support"></a>Ondersteuning voor beheerde instantie

Het gebruik van lange termijn back-upretentie met een Azure SQL Database beheerde exemplaren heeft de volgende beperkingen:

- **Beperkte openbare preview** - Deze preview is alleen beschikbaar voor EA- en CSP-abonnementen en is onderhevig aan beperkte beschikbaarheid.  
- [**PowerShell alleen**](sql-database-managed-instance-long-term-backup-retention-configure.md) - Er is momenteel geen Azure-portalondersteuning. LTR moet zijn ingeschakeld met PowerShell. 

Als u inschrijving wilt aanvragen, maakt u een [Azure-ondersteuningsticket](https://azure.microsoft.com/support/create-ticket/) onder het ondersteuningsonderwerp **Back-up, Herstel en Bedrijfscontinuïteit / Behoud van back-ups op lange termijn.**


## <a name="configure-long-term-backup-retention"></a>Langetermijnretentie van back-ups configureren

Zie [Azure SQL Database-behoud](sql-database-long-term-backup-retention-configure.md)op lange termijn beheren voor meer informatie over het configureren van langetermijnretentie met behulp van de Azure-portal of PowerShell.

## <a name="restore-database-from-ltr-backup"></a>Database herstellen vanuit LTR-back-up

Als u een database uit de LTR-opslag wilt herstellen, u een specifieke back-up selecteren op basis van de tijdstempel. De database kan worden hersteld naar elke bestaande server onder hetzelfde abonnement als de oorspronkelijke database. Zie [Azure SQL Database-back-upbehoud](sql-database-long-term-backup-retention-configure.md)beheren voor meer informatie over het herstellen van uw database vanuit een LTR-back-up via de Azure-portal of PowerShell.

## <a name="next-steps"></a>Volgende stappen

Omdat databaseback-ups gegevens beschermen tegen onbedoelde beschadiging of verwijdering, zijn ze een essentieel onderdeel van elke bedrijfscontinuïteits- en noodherstelstrategie. Zie [Bedrijfscontinuïteitsoverzicht](sql-database-business-continuity.md)voor meer informatie over de andere bedrijfscontinuïteitsoplossingen van SQL Database.
