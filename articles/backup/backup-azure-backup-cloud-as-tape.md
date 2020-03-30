---
title: Uw tape-infrastructuur vervangen
description: Ontdek hoe Azure Backup tape-achtige semantiek biedt waarmee u back-ups maken en gegevens herstellen in Azure
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: aeda1cefc84d425855c40b793f8334936541e63f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425099"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Uw opslag op lange termijn verplaatsen van tape naar de Azure-cloud

Klanten van Azure Backup and System Center Data Protection Manager kunnen:

* Back-ups maken van gegevens in schema's die het beste passen bij de behoeften van de organisatie.
* Bewaar de back-upgegevens voor langere tijd.
* Maak Azure een deel van hun lange termijn retentie behoeften (in plaats van tape).

In dit artikel wordt uitgelegd hoe klanten back-up- en bewaarbeleid kunnen inschakelen. Klanten die tapes gebruiken om hun langetermijnbehoeften aan te pakken, hebben nu een krachtig en levensvatbaar alternatief met de beschikbaarheid van deze functie. De functie is ingeschakeld in de nieuwste versie van de Azure Backup (die [hier](https://aka.ms/azurebackup_agent)beschikbaar is). DPM-klanten van System Center moeten updaten naar ten minste DPM 2012 R2 UR5 voordat ze DPM gebruiken met de Azure Backup-service.

## <a name="what-is-the-backup-schedule"></a>Wat is het back-upschema?

Het back-upschema geeft de frequentie van de back-upbewerking aan. De instellingen in het volgende scherm geven bijvoorbeeld aan dat er dagelijks om 18.00 uur en om middernacht back-ups worden gemaakt.

![Dagelijks schema](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Klanten kunnen ook een wekelijkse back-up plannen. De instellingen in het volgende scherm geven bijvoorbeeld aan dat elke alternatieve zondag & woensdag om 9:30 en 01:00 uur back-ups worden gemaakt.

![Weekschema](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Wat is het retentiebeleid?

Het bewaarbeleid bepaalt de duur waarvoor de back-up moet worden opgeslagen. In plaats van alleen een "vast beleid" voor alle back-uppunten op te geven, kunnen klanten verschillende bewaarbeleidsregels opgeven op basis van wanneer de back-up wordt genomen. Het back-uppunt dat dagelijks wordt genomen, dat als operationeel herstelpunt fungeert, blijft bijvoorbeeld 90 dagen behouden. Het back-uppunt dat aan het einde van elk kwartaal voor controledoeleinden wordt gebruikt, blijft voor een langere duur behouden.

![Bewaarbeleid](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Het totale aantal "retentiepunten" dat in dit beleid is opgegeven, is 90 (dagelijkse punten) + 40 (één per kwartaal gedurende 10 jaar) = 130.

## <a name="example--putting-both-together"></a>Voorbeeld – Beide samenstellen

![Voorbeeldscherm](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Dagelijks bewaarbeleid**: Back-ups die dagelijks worden gemaakt, worden zeven dagen bewaard.
2. **Wekelijks bewaarbeleid**: Back-ups die om middernacht en 18.00 uur zaterdag zijn genomen, worden vier weken bewaard.
3. **Maandelijks bewaarbeleid**: Back-ups die om middernacht en 18.00 uur op de laatste zaterdag van elke maand zijn gemaakt, blijven gedurende 12 maanden behouden.
4. **Jaarlijks retentiebeleid**: Back-ups genomen om middernacht op de laatste zaterdag van elke maart worden bewaard voor 10 jaar.

Het totale aantal "bewaarpunten" (punten waaruit een klant gegevens kan herstellen) in het voorgaande diagram wordt als volgt berekend:

* twee punten per dag gedurende zeven dagen = 14 herstelpunten
* twee punten per week gedurende vier weken = 8 herstelpunten
* twee punten per maand gedurende 12 maanden = 24 herstelpunten
* één punt per jaar per 10 jaar = 10 herstelpunten

Het totale aantal herstelpunten is 56.

> [!NOTE]
> Met Azure Backup u tot 9999 herstelpunten per beveiligde instantie maken. Een beveiligd exemplaar is een computer, een server (fysiek of virtueel) of een workload waarvan een back-up wordt gemaakt in Azure.
>

## <a name="advanced-configuration"></a>Geavanceerde configuratie

Door in het vorige scherm op **Wijzigen** te klikken, hebben klanten nog meer flexibiliteit bij het opgeven van bewaarschema's.

![Wijzigen](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Azure Backup:

* [Kennismaking met Azure Backup](backup-introduction-to-azure-backup.md)
* [Azure Backup proberen](backup-try-azure-backup-in-10-mins.md)
