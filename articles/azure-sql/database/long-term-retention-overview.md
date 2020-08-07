---
title: Langetermijnretentie van back-ups
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Meer informatie over hoe Azure SQL Database & Azure SQL Managed instance ondersteuning biedt voor Maxi maal tien jaar back-ups van de volledige data base via het Bewaar beleid voor de lange termijn.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 4b2324c480ef81ef241f4d639c22c2ed4dd1545b
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808844"
---
# <a name="long-term-retention---azure-sql-database-and-azure-sql-managed-instance"></a>Lange termijn retentie-Azure SQL Database en Azure SQL Managed instance

Veel toepassingen hebben wettelijke, nalevings-of andere zakelijke doel einden waarvoor u database back-ups wilt behouden die groter zijn dan de 7-35 dagen die worden verschaft door Azure SQL Database en Azure SQL Managed instance [automatische back-ups](automated-backups-overview.md). Door gebruik te maken van de functie voor lange termijn retentie (LTR) kunt u de volledige back-ups van de opgegeven SQL Database en SQL Managed instance in Azure Blob-opslag met geografisch redundante opslag met lees toegang voor Maxi maal tien jaar opslaan. U kunt vervolgens een back-up herstellen als een nieuwe data base.

Daarnaast introduceert SQL Managed instance [Configureer bare opslag redundantie van back-ups](automated-backups-overview.md#backup-storage-redundancy) , waarmee u flexibiliteit kunt kiezen tussen lokaal redundante (LRS), zone redundante (ZRS) of geografisch redundante (RA-GRS) [opslag-blobs](../../storage/common/storage-redundancy.md). Deze optie is momenteel alleen beschikbaar tijdens het proces voor het maken van een beheerd exemplaar en kan niet worden gewijzigd nadat de resource is ingericht.

Lange tijd retentie kan worden ingeschakeld voor Azure SQL Database en heeft een beperkte open bare Preview voor Azure SQL Managed instance. Dit artikel bevat een conceptueel overzicht van de lange termijn retentie. Als u lange termijn retentie wilt configureren, raadpleegt u [Azure SQL database LTR configureren](long-term-backup-retention-configure.md) en het [Azure SQL Managed instance-LTR configureren](../managed-instance/long-term-backup-retention-configure.md). 

> [!NOTE]
> U kunt SQL Agent-taken gebruiken voor het plannen van [back-ups met alleen-kopiëren](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) als alternatief voor een oplossing van meer dan 35 dagen.


## <a name="how-long-term-retention-works"></a>Hoe lange termijn retentie werkt
     
Lange termijn retentie van back-ups (LTR) maakt gebruik van de volledige database back-ups die [automatisch worden gemaakt](automated-backups-overview.md) voor het inschakelen van punt-tijd herstel (PITR). Als een LTR-beleid is geconfigureerd, worden deze back-ups gekopieerd naar verschillende blobs voor lange termijn opslag. De kopie is een achtergrond taak die geen invloed heeft op de prestaties van de data base-workload. Het LTR-beleid voor elke data base in SQL Database kan ook opgeven hoe vaak de LTR-back-ups worden gemaakt.

Als u LTR wilt inschakelen, kunt u een beleid definiëren met behulp van een combi natie van vier para meters: wekelijks retentie van back-ups (W), maandelijkse back-up (M), jaarlijks back-upbewaaring (Y) en week van jaar (WeekOfYear). Als u W opgeeft, wordt één back-up elke week gekopieerd naar de lange termijn opslag. Als u M opgeeft, wordt de eerste back-up van elke maand gekopieerd naar de lange termijn opslag. Als u Y opgeeft, wordt één back-up in de week die is opgegeven door WeekOfYear gekopieerd naar de lange termijn opslag. Als de opgegeven WeekOfYear zich in het verleden bevindt toen het beleid werd geconfigureerd, wordt de eerste LTR-back-up in het volgende jaar gemaakt. Elke back-up wordt opgeslagen in de lange termijn opslag op basis van de beleids parameters die worden geconfigureerd wanneer de LTR-back-up wordt gemaakt.

> [!NOTE]
> Elke wijziging van het LTR-beleid is alleen van toepassing op toekomstige back-ups. Als een voor beeld van een wekelijkse back-upbewaaring (W), een maandelijkse back-up (M) of een jaarlijkse back-upbewaaring (Y) is gewijzigd, wordt de nieuwe Bewaar instelling alleen van toepassing op nieuwe back-ups. Het bewaren van bestaande back-ups wordt niet gewijzigd. Als u van plan bent oude LTR-back-ups te verwijderen voordat de Bewaar periode verloopt, moet u [de back-ups hand matig verwijderen](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

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

   ![LTR-voor beeld](./media/long-term-retention-overview/ltr-example.png)


Als u het bovenstaande beleid wijzigt en W = 0 (geen wekelijkse back-ups) instelt, worden de uitgebracht van back-upkopieën gewijzigd, zoals wordt weer gegeven in de bovenstaande tabel door de gemarkeerde datums. De hoeveelheid opslag die nodig is om deze back-ups te bewaren, vermindert dienovereenkomstig. 

> [!IMPORTANT]
> De timing van afzonderlijke LTR-back-ups wordt bepaald door Azure. U kunt niet hand matig een LTR-back-up maken of de timing van het maken van de back-up regelen. Na het configureren van een LTR-beleid kan het tot zeven dagen duren voordat de eerste LTR-back-up wordt weer gegeven in de lijst met beschik bare back-ups.  


## <a name="geo-replication-and-long-term-backup-retention"></a>Geo-replicatie en lange termijn retentie van back-ups

Als u gebruikmaakt van actieve geo-replicatie of failover-groepen als uw bedrijfs continuïteits oplossing, moet u voor bereide failovers voorbereiden en hetzelfde LTR-beleid configureren voor de secundaire data base of het tweede exemplaar. Uw LTR-opslag kosten worden niet verhoogd omdat er geen back-ups worden gegenereerd op basis van de secundaire zones. De back-ups worden alleen gemaakt wanneer de secundaire primair wordt en de back-ups worden gemaakt. Het zorgt voor een niet-onderbroken generatie van de LTR-back-ups wanneer de failover wordt geactiveerd en de primaire naar de secundaire regio wordt verplaatst. 

> [!NOTE]
> Wanneer de oorspronkelijke primaire data base herstelt van een storing die de failover heeft veroorzaakt, wordt deze een nieuwe secundaire. Daarom wordt het maken van de back-up niet hervat en wordt het bestaande LTR-beleid pas van kracht nadat het opnieuw wordt ingesteld als primair. 

## <a name="sql-managed-instance-support"></a>Ondersteuning voor SQL Managed instance

Voor een lange termijn retentie van back-ups met Azure SQL Managed instance gelden de volgende beperkingen:

- **Beperkte open bare preview** -deze preview is alleen beschikbaar voor EA-en CSP-abonnementen en is onderhevig aan beperkte Beschik baarheid.  
- [**Alleen Power shell**](../managed-instance/long-term-backup-retention-configure.md) : er is momenteel geen ondersteuning voor Azure Portal. LTR moet zijn ingeschakeld met behulp van Power shell. 

Als u de inschrijving wilt aanvragen, maakt u een [ondersteunings ticket voor Azure](https://azure.microsoft.com/support/create-ticket/). Voor het probleem type SELECT Technical issue, voor service Kies SQL Managed instance en voor het probleem type Selecteer **back-up, herstel en bedrijfs continuïteit/lange termijn retentie van back-ups**. In uw aanvraag moet u aangeven dat u wilt worden inge schreven als beperkte open bare preview-versie voor een SQL Managed instance.

## <a name="configure-long-term-backup-retention"></a>Langetermijnretentie van back-ups configureren

U kunt lange termijn retentie van back-ups configureren met behulp van de Azure Portal en Power shell voor Azure SQL Database en Power shell voor Azure SQL Managed instance. Als u een Data Base wilt herstellen vanuit de LTR-opslag, kunt u een specifieke back-up selecteren op basis van de tijds tempel. De data base kan worden hersteld naar een bestaande server of een beheerd exemplaar onder hetzelfde abonnement als de oorspronkelijke data base.

Voor informatie over het configureren van lange termijn retentie of het herstellen van een Data Base vanuit een back-up voor SQL Database met behulp van de Azure Portal of Power shell, Zie [beheren Azure SQL database lange termijn retentie van back-ups](long-term-backup-retention-configure.md)

Zie voor meer informatie over het configureren van een lange termijn retentie of het herstellen van een Data Base van een back-up voor een SQL Managed instance met behulp van Power shell de [Azure SQL Managed instance retentie van back-ups op lange termijn beheren](../managed-instance/long-term-backup-retention-configure.md).

Als u een Data Base wilt herstellen vanuit de LTR-opslag, kunt u een specifieke back-up selecteren op basis van de tijds tempel. De data base kan worden hersteld naar een bestaande server onder hetzelfde abonnement als de oorspronkelijke data base. Zie voor meer informatie over het herstellen van een Data Base uit een LTR-back-up, met behulp van de Azure Portal of Power shell [Azure SQL database lange termijn retentie van back-ups beheren](long-term-backup-retention-configure.md). In uw aanvraag moet u aangeven dat u wilt worden inge schreven in de beperkte open bare preview van LTR voor SQL Managed instance.

## <a name="next-steps"></a>Volgende stappen

Omdat database back-ups gegevens beveiligen tegen onbedoelde beschadiging of verwijdering, vormen ze een essentieel onderdeel van een strategie voor bedrijfs continuïteit en herstel na nood gevallen. Zie [overzicht van bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md)voor meer informatie over de andere SQL database oplossingen voor bedrijfs continuïteit.
 
