---
title: StorSimple Snapshot Manager back-uptaken | Microsoft Documenten
description: Beschrijft hoe u de Module StorSimple Snapshot Manager MMC gebruiken om geplande, momenteel lopende en voltooide back-uptaken weer te geven en te beheren.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 094b996cd3227903995c7a74ef14ed8c0561f59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933336"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>StorSimple Snapshot Manager gebruiken om back-uptaken weer te geven en te beheren

## <a name="overview"></a>Overzicht
In het knooppunt **Taken** in het deelvenster **Bereik** worden **de**geplande , laatste **24 uur**en back-uptaken **uitgevoerd** die u interactief of via een geconfigureerd beleid hebt gestart. 

In deze zelfstudie wordt uitgelegd hoe u het knooppunt **Vacatures** gebruiken om informatie weer te geven over geplande, recente en momenteel draaiende back-uptaken. (De lijst met taken en bijbehorende informatie wordt weergegeven in het deelvenster **Resultaten.)** Daarnaast u met de rechtermuisknop op een vermelde taak klikken en een contextmenu bekijken met beschikbare acties.

## <a name="view-scheduled-jobs"></a>Geplande taken weergeven
Gebruik de volgende procedure om geplande back-uptaken weer te geven.

#### <a name="to-view-scheduled-jobs"></a>Geplande taken weergeven
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten. 
2. Vouw in het deelvenster **Bereik** het knooppunt **Taken** uit en klik op **Gepland**. De volgende informatie wordt weergegeven in het deelvenster **Resultaten:**
   
   * **Naam** – de naam van de geplande momentopname
   * **Volgende run** – de datum en tijd van de volgende geplande momentopname
   * **Laatste run** – de datum en tijd van de meest recente geplande momentopname
     
     > [!NOTE]
     > Voor eenmalige momentopnamen zijn de **volgende run** en **laatste run** hetzelfde.
     
     ![Geplande back-uptaken](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Als u extra acties voor een specifieke taak wilt uitvoeren, klikt u met de rechtermuisknop op de taaknaam in het deelvenster **Resultaten** en selecteert u in de menuopties.

## <a name="view-recent-jobs"></a>Recente vacatures weergeven
Gebruik de volgende procedure om back-ups weer te geven en taken te herstellen die in de afgelopen 24 uur zijn voltooid.

#### <a name="to-view-recent-jobs"></a>Recente vacatures bekijken
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Vouw in het deelvenster **Bereik** het knooppunt **Taken** uit en klik op **Laatste 24 uur**. In het deelvenster **Resultaten** worden back-uptaken van de afgelopen 24 uur weergegeven (tot maximaal 64 taken). De volgende informatie wordt weergegeven in het deelvenster **Resultaten,** afhankelijk van de **weergaveopties** die u opgeeft:
   
   * **Naam** – de naam van de geplande momentopname.
   * **Gestart** : de datum en het tijdstip waarop de momentopname begon.
   * **Gestopt** : de datum en het tijdstip waarop de momentopname is voltooid of beëindigd.
   * **Verstreken** – de hoeveelheid tijd tussen de **gestarte** en **gestopte** tijden.
   * **Status** – de status van de onlangs voltooide taak. **Succes** geeft aan dat de back-up is gemaakt. **Als mislukt,** wordt aangegeven dat de taak niet is uitgevoerd.
   * **Informatie** – de reden voor de storing.
   * **Verwerkte bytes (MB)** – de hoeveelheid gegevens uit de volumegroep die is verwerkt (in MB's). 
     
     ![Taken die in de afgelopen 24 uur zijn uitgevoerd](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Als u extra acties voor een specifieke taak wilt uitvoeren, klikt u met de rechtermuisknop op de taaknaam in het deelvenster **Resultaten** en selecteert u in de menuopties.
   
    ![Een taak verwijderen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Momenteel lopende taken weergeven
Gebruik de volgende procedure om taken weer te geven die momenteel worden uitgevoerd.

#### <a name="to-view-currently-running-jobs"></a>Huidige taken weergeven
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Vouw in het deelvenster **Bereik** het knooppunt **Taken** uit en klik op **Uitvoeren**. Afhankelijk van de **weergaveopties** die u opgeeft, worden de volgende gegevens weergegeven in het deelvenster **Resultaten:**
   
   * **Naam** – de naam van de geplande momentopname.
   * **Gestart** : de datum en het tijdstip waarop de momentopname begon.
   * **Controlepunt** – de huidige actie van de back-up.
   * **Status** – het percentage van voltooiing.
   * **Verstreken** - de hoeveelheid tijd die is verstreken sinds de back-up begon. 
   * **Gemiddelde doorvoer (MB)** – verhouding van de totale bytes van de verwerkte gegevens en die van de totale tijd die nodig is voor verwerking (MB's).
   * **Verwerkte bytes (MB)** – totale bytes aan verwerkte gegevens (in MB).
   * **Geschreven bytes (MB)** – totaal aantal geschreven bytes aan gegevens (in MB' s). Het bevat zowel de gegevens als de metagegevens en is dus meestal groter dan de verwerkte bytes.
     
     ![Taken die momenteel worden uitgevoerd](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Als u extra acties voor een specifieke taak wilt uitvoeren, klikt u met de rechtermuisknop op de taaknaam in het deelvenster **Resultaten** en selecteert u in de menuopties.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om uw StorSimple-oplossing te beheren.](storsimple-snapshot-manager-admin.md)
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om de back-upcatalogus te beheren.](storsimple-snapshot-manager-manage-backup-catalog.md)

