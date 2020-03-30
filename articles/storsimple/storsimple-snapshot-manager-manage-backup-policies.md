---
title: Back-upbeleid voor StorSimple Snapshot Manager | Microsoft Documenten
description: Beschrijft hoe u de MMC-module Van StorSimple Snapshot Manager gebruiken om het back-upbeleid te maken en te beheren dat geplande back-ups beheert.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 7b2df6c8384e78094d80a17d8982b0db9edf73e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933365"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>StorSimple Snapshot Manager gebruiken om back-upbeleid te maken en te beheren
## <a name="overview"></a>Overzicht
Een back-upbeleid maakt een planning voor het lokaal of in de cloud maken van back-ups van volumegegevens. Wanneer u een back-upbeleid maakt, u ook een bewaarbeleid opgeven. (U maximaal 64 momentopnamen behouden.) Zie [Back-uptypen](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) in de [StorSimple 8000-serie: een hybride cloudoplossing voor](storsimple-overview.md)meer informatie over back-upbeleid.

In deze zelfstudie wordt het volgende uitgelegd:

* Maak een back-upbeleid
* Een back-upbeleid bewerken
* Een back-upbeleid verwijderen

## <a name="create-a-backup-policy"></a>Maak een back-upbeleid
Gebruik de volgende procedure om een nieuw back-upbeleid te maken.

#### <a name="to-create-a-backup-policy"></a>Een back-upbeleid maken
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik in het deelvenster **Bereik** met de rechtermuisknop op **Back-upbeleid**en klik op **Back-upbeleid maken**.

    ![Maak een back-upbeleid](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Het dialoogvenster **Een beleid maken** wordt weergegeven.

    ![Een beleid maken - tabblad Algemeen](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Vul op het tabblad **Algemeen** de volgende informatie in:

   1. Typ in het tekstvak **Naam** een naam voor het beleid.
   2. Typ in het tekstvak **Volumegroep** de naam van de volumegroep die aan het beleid is gekoppeld.
   3. Selecteer **Lokale momentopname of** **Cloudmomentopname**.
   4. Selecteer het aantal momentopnamen dat u wilt behouden. Als u **Alles**selecteert, blijven 64 momentopnamen behouden (het maximum).
4. Klik op de **schema** tabblad.

    ![Een beleid maken - tabblad Plannen](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Voer op het tabblad **Planning** de volgende gegevens in:

   1. Klik **op het** selectievakje Inschakelen om de volgende back-up te plannen.
   2. Selecteer **onder Instellingen** **Eénkeer**, **Dagelijks**, **Wekelijks**of **Maandelijks**.
   3. Klik **in het** tekstvak Start op het agendapictogram en selecteer een begindatum.
   4. Onder **Geavanceerde instellingen**u optionele herhalingsschema's en een einddatum instellen.
   5. Klik op **OK**.

Nadat u een back-upbeleid hebt gemaakt, worden de volgende gegevens weergegeven in het deelvenster **Resultaten:**

* **Naam** – de naam van het back-upbeleid.
* **Typ** – lokale momentopname of cloudmomentopname.
* **Volumegroep** : de volumegroep die aan het beleid is gekoppeld.
* **Retentie** – het aantal bewaarde momentopnamen; het maximum is 64.
* **Gemaakt** : de datum waarop dit beleid is gemaakt.
* **Ingeschakeld** – of het beleid momenteel van kracht is: **True** geeft aan dat het van kracht is; **False** geeft aan dat het niet van kracht is.

## <a name="edit-a-backup-policy"></a>Een back-upbeleid bewerken
Gebruik de volgende procedure om een bestaand back-upbeleid te bewerken.

#### <a name="to-edit-a-backup-policy"></a>Een back-upbeleid bewerken
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik op het knooppunt **Back-upbeleid.** Alle back-upbeleidsregels worden weergegeven in het deelvenster **Resultaten.**
3. Klik met de rechtermuisknop op het beleid dat u wilt bewerken en klik vervolgens op **Bewerken**.

    ![Een back-upbeleid bewerken](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Wanneer het venster **Een beleids maken** wordt weergegeven, voert u de wijzigingen in en klikt u op **OK**.

## <a name="delete-a-backup-policy"></a>Een back-upbeleid verwijderen
Gebruik de volgende procedure om een back-upbeleid te verwijderen.

#### <a name="to-delete-a-backup-policy"></a>Een back-upbeleid verwijderen
1. Klik op het bureaubladpictogram om StorSimple Snapshot Manager te starten.
2. Klik **in** het deelvenster Bereik op het knooppunt **Back-upbeleid.** Alle back-upbeleidsregels worden weergegeven in het deelvenster **Resultaten.**
3. Klik met de rechtermuisknop op het back-upbeleid dat u wilt verwijderen en klik vervolgens op **Verwijderen**.
4. Wanneer het bevestigingsbericht wordt weergegeven, klikt u op **Ja**.

    ![Bevestiging van back-upbeleid verwijderen](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om uw StorSimple-oplossing te beheren.](storsimple-snapshot-manager-admin.md)
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om back-uptaken weer te geven en te beheren.](storsimple-snapshot-manager-manage-backup-jobs.md)
