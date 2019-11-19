---
title: Back-ups van de MARS-agent beheren en controleren
description: Meer informatie over het beheren en bewaken van back-ups van agents van Microsoft Azure Recovery Services (MARS) met behulp van de Azure Backup-service.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: edb672f97f467378176bf6cdf04fe8e22cc5e51b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173069"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Back-ups van de agent voor Microsoft Azure Recovery Services (MARS) beheren met behulp van de Azure Backup-Service

In dit artikel wordt beschreven hoe u bestanden en mappen beheert waarvan een back-up is gemaakt met de Microsoft Azure Recovery Services-agent.

## <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Het back-upbeleid geeft aan wanneer moment opnamen van de gegevens moeten worden gemaakt om herstel punten te maken en hoe lang herstel punten moeten worden bewaard. U configureert het back-upbeleid met behulp van de MARS-agent.

Maak als volgt een beleid:

1. Nadat u de MARS-agent hebt gedownload en geregistreerd, start u de agent console. U vindt deze door te zoeken naar **Microsoft Azure Backup** op uw machine.  
2. Klik in **acties**op **back-up plannen**.

    ![Een back-up van de Windows Server plannen](./media/backup-configure-vault/schedule-first-backup.png)
3. Klik op **volgende**in de wizard Back-up plannen > **aan**de slag te gaan.
4. In **items selecteren waarvan u een back-up wilt maken**, klikt u op **items toevoegen**.

    ![Items selecteren waarvan u een back-up wilt maken](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. Selecteer bij **items selecteren**de waarde waarvan u een back-up wilt maken en klik op **OK**.

    ![Geselecteerde items waarvan een back-up moet worden gemaakt](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. Klik op de pagina **items selecteren waarvan u een back-up wilt maken** op **volgende**.
7. Geef op de pagina **back-upschema opgeven** op wanneer u dagelijks of wekelijks back-ups wilt maken. Klik op **Volgende**.

    - Wanneer er een back-up wordt gemaakt, wordt er een herstel punt aangemaakt.
    - Het aantal herstel punten dat in uw omgeving is gemaakt, is afhankelijk van het back-upschema.

8. U kunt dagelijks back-ups plannen, Maxi maal drie keer per dag. De scherm opname bevat bijvoorbeeld twee dagelijkse back-ups, een om middernacht en één om 6:00 uur.

    ![Dagelijks schema](./media/backup-configure-vault/day-schedule.png)

9. U kunt ook wekelijkse back-ups uitvoeren. De scherm afbeelding toont bijvoorbeeld de back-ups die zijn gemaakt op elke tweede zondag & woensdag om 9:30 uur en 1:00 uur.

    ![Wekelijks schema](./media/backup-configure-vault/week-schedule.png)

10. Geef op de pagina **retentie beleid selecteren** op hoe u historische kopieën van uw gegevens opslaat. Klik op **Volgende**.

    - De Bewaar instellingen geven op welke herstel punten moeten worden opgeslagen en hoelang ze moeten worden opgeslagen.
    - Wanneer u bijvoorbeeld een dagelijkse Bewaar instelling instelt, geeft u aan dat op het tijdstip dat is opgegeven voor de dagelijkse retentie, het laatste herstel punt wordt bewaard gedurende het opgegeven aantal dagen. U kunt ook een maandelijks Bewaar beleid opgeven om aan te geven dat het herstel punt dat op de 30e van elke maand wordt gemaakt, 12 maanden moet worden bewaard.
    - Dagelijks en wekelijks Bewaar periode van het herstel punt vallen doorgaans samen met het back-upschema. Als de back-up wordt geactiveerd volgens schema, wordt het herstel punt dat is gemaakt door de back-up opgeslagen voor de duur die is aangegeven in het dagelijkse of wekelijkse Bewaar beleid.
    - Een voor beeld:-dagelijkse back-ups op middernacht en 6:00 uur worden gedurende zeven dagen bewaard.
            -Back-ups die zijn gemaakt op zaterdag om middernacht en 6:00 uur, worden vier weken bewaard.
            -Back-ups die zijn gemaakt op zaterdag in de afgelopen week van de maand om middernacht en 6:00 uur, worden twaalf maanden bewaard.
            -Back-ups die zijn gemaakt op een zaterdag in de afgelopen week van maart worden tien jaar bewaard.

    ![Voor beeld van Bewaar periode](./media/backup-configure-vault/retention-example.png)

11. In **eerste back-uptype kiezen** beslist u of u de eerste back-up via het netwerk wilt maken of offline back-ups wilt gebruiken (zie dit [artikel](backup-azure-backup-import-export.md)voor meer informatie over offline back-up). Als u de eerste back-up via het netwerk wilt maken, selecteert u **automatisch via het netwerk** en klikt u op **volgende**.

    ![eerste back-uptype](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

12. Lees de informatie in **bevestiging**en klik vervolgens op **volt ooien**.
    ![het type back-up bevestigen](./media/backup-azure-manage-mars/confirm-backup-type.png)

13. Nadat u de wizard voor het maken van een back-upschema hebt doorlopen, klikt u op **Sluiten**.
  ![Bevestig het wijzigen van het back-upproces](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

U moet een beleid maken op elke computer waarop de agent is geïnstalleerd.

## <a name="modify-a-backup-policy"></a>Een back-upbeleid wijzigen

Wanneer u het back-upbeleid wijzigt, kunt u nieuwe items toevoegen, bestaande items verwijderen uit een back-up of bestanden uitsluiten van het maken van een back-up met behulp van uitsluitings instellingen.

- **Items toevoegen** gebruik deze optie alleen voor het toevoegen van nieuwe items om een back-up te maken. Als u bestaande items wilt verwijderen, gebruikt u de optie **items verwijderen** of **uitsluitings instellingen** .  
- **Items verwijderen** gebruik deze optie om items te verwijderen waarvan u een back-up wilt maken.
  - Gebruik **uitsluitings instellingen** voor het verwijderen van alle items binnen een volume in plaats van **items te verwijderen**.
  - Als alle selecties in een volume worden gewist, worden oude back-ups van de items bewaard op het moment van de laatste back-up, zonder aanpassings bereik.
  - Als u deze items opnieuw selecteert, worden er geen back-ups gemaakt van de eerste back-up en nieuwe beleids wijzigingen.
  - Als u de selectie van het hele volume uitschakelt, blijft de vorige back-up zonder enige bereik voor het wijzigen van het Bewaar beleid.
- **Instellingen voor uitsluiting** gebruik deze optie om specifieke items uit te sluiten waarvan u een back-up wilt maken.
  
### <a name="add-new-items-to-existing-policy"></a>Nieuwe items toevoegen aan het bestaande beleid

1. Klik in **acties**op **back-up plannen**.

    ![Een back-up van de Windows Server plannen](./media/backup-configure-vault/schedule-first-backup.png)

2. Selecteer in het tabblad **beleids item selecteren** **de optie back-upschema voor uw bestanden en mappen wijzigen** en klik op **volgende**.

    ![Beleids items selecteren](./media/backup-azure-manage-mars/select-policy-items.png)

3. Selecteer **wijzigingen in back-upitems of tijden** **wijzigen of stoppen** in het tabblad planning maken en klik op **volgende**.

    ![Back-up wijzigen of plannen](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Klik in het tabblad **items selecteren voor back-up** op **items toevoegen** om de items toe te voegen waarvan u een back-up wilt maken.

    ![Back-upitems wijzigen of plannen](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Selecteer in het venster **items selecteren** de optie vliegt of mappen die u wilt toevoegen en klik op **OK**.

    ![De items selecteren](./media/backup-azure-manage-mars/select-item.png)

6. Voer de volgende stappen uit en klik op **volt ooien** om de bewerking te volt ooien.

### <a name="add-exclusion-rules-to-existing-policy"></a>Uitsluitings regels toevoegen aan het bestaande beleid

U kunt uitsluitings regels toevoegen om bestanden en mappen over te slaan waarvan u geen back-up wilt maken. U kunt dit doen wanneer u een nieuw beleid definieert of een bestaand beleid wijzigt.

1. Klik in het deel venster acties op **back-up plannen**. Ga naar **items selecteren waarvan u een back-up wilt maken** en klik op **uitsluitings instellingen**.

    ![De items selecteren](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. Klik in **uitsluitings instellingen**op **uitsluiting toevoegen**.

    ![De items selecteren](./media/backup-azure-manage-mars/add-exclusion.png)

3. In **items selecteren**die u wilt uitsluiten, bladert u naar de bestanden en mappen en selecteert u de items die u wilt uitsluiten en klikt u op **OK**.

    ![De items selecteren](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Standaard worden alle **submappen** in de geselecteerde mappen uitgesloten. U kunt dit wijzigen door **Ja** of **Nee**te selecteren. U kunt de bestands typen die u wilt uitsluiten, bewerken, zoals hieronder wordt weer gegeven:

    ![De items selecteren](./media/backup-azure-manage-mars/subfolders-type.png)

5. Voer de volgende stappen uit en klik op **volt ooien** om de bewerking te volt ooien.

### <a name="remove-items-from-existing-policy"></a>Items uit bestaand beleid verwijderen

1. Klik in het deel venster acties op **back-up plannen**. Ga naar **items selecteren waarvan u een back-up wilt maken**. Selecteer in de lijst de bestanden en mappen die u wilt verwijderen uit het back-upschema en klik op **items verwijderen**.

    ![De items selecteren](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> Ga voorzichtig te werk wanneer u een volume volledig uit het beleid verwijdert.  Als u het opnieuw wilt toevoegen, wordt het beschouwd als een nieuw volume. Bij de volgende geplande back-up wordt een eerste back-up (volledige back-up) in plaats van incrementele back-up uitgevoerd. Als u later tijdelijk items moet verwijderen en toevoegen, kunt u het beste **uitsluitings instellingen** gebruiken in plaats van **items verwijderen** om een incrementele back-up te garanderen in plaats van een volledige back-up.

2. Voer de volgende stappen uit en klik op **volt ooien** om de bewerking te volt ooien.

## <a name="stop-protecting-files-and-folder-backup"></a>De beveiliging van bestanden en mappen stoppen

Er zijn twee manieren om het maken van back-ups van bestanden en mappen te stoppen:

- **Stop de beveiliging en behoud de back-upgegevens**.
  - Met deze optie worden alle toekomstige back-uptaken van beveiliging gestopt.
  - Azure Backup-Service behoudt de herstel punten waarvan een back-up is gemaakt op basis van het Bewaar beleid.
  - U kunt de back-upgegevens voor niet-verlopen herstel punten herstellen.
  - Als u besluit de beveiliging te hervatten, kunt u de optie *back-upschema opnieuw inschakelen* gebruiken. Daarna blijven gegevens bewaard op basis van het nieuwe Bewaar beleid.
- **Stop de beveiliging en verwijder de back-upgegevens**.
  - Met deze optie worden alle toekomstige back-uptaken gestopt om uw gegevens te beschermen en alle herstel punten te verwijderen.
  - U ontvangt een waarschuwing voor het verwijderen van back-upgegevens met een bericht *dat uw gegevens voor dit back-upitem zijn verwijderd. Deze gegevens zijn tijdelijk beschikbaar gedurende 14 dagen, waarna deze permanent worden verwijderd* en aanbevolen actie *het back-upitem opnieuw beveiligen binnen 14 dagen om uw gegevens te herstellen.*
  - Als u de beveiliging wilt hervatten, moet u binnen 14 dagen opnieuw beveiligen vanaf de verwijderings bewerking.

### <a name="stop-protection-and-retain-backup-data"></a>Beveiliging stoppen en back-upgegevens behouden

1. Open de MARS-beheer console, ga naar het **deel venster acties**en **Selecteer back-up plannen**.
    een geplande back-up ![wijzigen of stoppen.](./media/backup-azure-manage-mars/mars-actions.png)
1. Selecteer op de pagina **beleids item selecteren** **de optie een back-upschema voor uw bestanden en mappen wijzigen en** Klik op **volgende**.
    een geplande back-up ![wijzigen of stoppen.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Op de pagina **een geplande back-up wijzigen of stoppen** selecteert **u stoppen met het gebruik van dit back-upschema, maar behoud de opgeslagen back-ups totdat een schema opnieuw wordt geactiveerd**. Selecteer vervolgens **Volgende**.  
    een geplande back-up ![wijzigen of stoppen.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Bekijk in **geplande back-up onderbreken** de informatie door op **volt ooien** te klikken ![een geplande back-up te wijzigen of te stoppen.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. Controleer in **back-upproces wijzigen** of de status van het plannen van back-ups is geslaagd en klik op **sluiten** om te volt ooien.

### <a name="stop-protection-and-delete-backup-data"></a>Beveiliging stoppen en back-upgegevens verwijderen

1. Open de MARS-beheer console, ga naar het deel venster **acties** en selecteer **back-up plannen**.
2. Op de pagina **een geplande back-up wijzigen of stoppen** selecteert **u stoppen met het gebruik van dit back-upschema en alle opgeslagen back-ups verwijderen**. Selecteer vervolgens **Volgende**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Selecteer **volt ooien**op de pagina **een geplande back-up stoppen** .

    ![Een geplande back-up stoppen.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. U wordt gevraagd een beveiligings pincode (persoonlijk identificatie nummer) in te voeren, die u hand matig moet genereren. Als u dit wilt doen, meldt u zich eerst aan bij de Azure Portal.
5. Ga naar **Recovery Services kluis** > **instellingen** > **Eigenschappen**.
6. Onder **BEVEILIGINGS pincode**selecteert u **genereren**. Deze pincode kopiëren. De pincode is slechts vijf minuten geldig.
7. Plak de pincode in de beheer console en selecteer **OK**.

    ![Een beveiligings pincode genereren.](./media/backup-azure-delete-vault/security-pin.png)

8. Op de pagina **voortgang van back-up wijzigen** wordt het volgende bericht weer gegeven: *verwijderde back-upgegevens worden 14 dagen bewaard. Na die tijd worden de back-upgegevens permanent verwijderd.*  

    ![Verwijder de back-upinfrastructuur.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Nadat u de on-premises back-upitems hebt verwijderd, voert u de volgende stappen uit in de portal.

## <a name="re-enable-protection"></a>Beveiliging opnieuw inschakelen

Als u de beveiliging hebt gestopt terwijl u de gegevens behoudt en hebt besloten om de beveiliging te hervatten, kunt u het back-upschema opnieuw inschakelen met behulp van het back-upbeleid wijzigen.

1. Selecteer bij **acties** de optie **back-up plannen**.
1. Selecteer **back-upschema opnieuw inschakelen. U kunt ook back-upitems of TINES wijzigen** en op **volgende**klikken.
    de back-upinfrastructuur ![verwijderen.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Klik in **items selecteren om een back-up te maken**op **volgende**.
    de back-upinfrastructuur ![verwijderen.](./media/backup-azure-manage-mars/re-enable-next.png)
1. In **back-upschema opgeven**geeft u het back-upschema op en klikt u op **volgende**.
1. Geef in **Bewaar beleid selecteren**de Bewaar duur op en klik op **volgende**.
1. Controleer ten slotte op het scherm voor **conformiteit** de details van het beleid en klik op **volt ooien**.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [ondersteunings matrix voor Mars](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)voor meer informatie over ondersteunde scenario's en beperkingen.
- Meer informatie over het [Bewaar gedrag van back-upbeleid op aanvraag](backup-configure-vault.md#on-demand-backup-policy-retention-behavior).
