---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48a3326dbe0e9eed4a5490e720248555586d189c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67176087"
---
### <a name="to-take-a-backup"></a>Een back-up maken

1. Ga naar uw StorSimple-apparaatbeheerservice. Selecteer uw apparaat in de lijst in tabelvorm met apparaten en klik hierop. Klik daarna op **Alle instellingen**. Ga op de blade **Instellingen** naar **Instellingen > Beheren > Back-upbeleid**.

    ![Back-upbeleid-toevoegen](./media/storsimple-8000-take-backup/step8takebu1.png)

2. Klik op de blade **Back-upbeleid** op **+ Beleid toevoegen**.

    ![Back-upbeleid-toevoegen](./media/storsimple-8000-take-backup/step8takebu2.png)

3. Geef op de blade **Back-upbeleid maken** een naam op voor uw back-upbeleid die tussen de 3 en 150 tekens lang is.

4. Selecteer de volumes waarvan u een back-up wilt maken. Als u meer dan één volume selecteert, worden deze volumes samen gegroepeerd om zo een crashconsistente back-up te maken.

    ![Back-upbeleid-toevoegen](./media/storsimple-8000-take-backup/step8takebu4.png)

5. Op de blade **Eerste schema toevoegen**:

    1. Selecteer het type back-up. Selecteer **Lokale momentopname** voor sneller herstellen. Selecteer **Cloudmomentopname** voor gegevenstolerantie.
    2. Geef de back-upfrequentie op in minuten, uren, dagen of weken.
    3. Selecteer een bewaartijd. Hoe lang deze moet zijn, hangt af van hoe vaak een back-up wordt uitgevoerd. Als dagelijks een back-up wordt uitgevoerd, kunt u de bewaartijd instellen op weken. Als maandelijks een back-up wordt gemaakt, kunt u de bewaartijd instellen op maanden.
    4. Selecteer de begintijd en -datum voor het back-upbeleid.
    5. Klik op **OK** om het back-upbeleid te maken.

        ![Back-upbeleid-toevoegen](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. Klik op **Maken** om te beginnen met het maken van het back-upbeleid. U krijgt een melding wanneer het back-upbeleid is gemaakt. De lijst met back-upbeleidsregels wordt ook bijgewerkt.
      
      ![Back-upbeleid-toevoegen](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      U hebt nu een back-upbeleid voor geplande back-ups van uw volumegegevens.




