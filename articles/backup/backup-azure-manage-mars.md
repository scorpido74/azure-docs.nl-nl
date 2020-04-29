---
title: Back-ups van de MARS-agent beheren en controleren
description: Meer informatie over het beheren en bewaken van back-ups van agents van Microsoft Azure Recovery Services (MARS) met behulp van de Azure Backup-service.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: a88ec4dc9283114e06eed424172dbb958850c2e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025098"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Back-ups van de agent voor Microsoft Azure Recovery Services (MARS) beheren met behulp van de Azure Backup-Service

In dit artikel wordt beschreven hoe u bestanden en mappen beheert waarvan een back-up is gemaakt met de Microsoft Azure Recovery Services-agent.

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

4. Standaard worden alle **submappen** in de geselecteerde mappen uitgesloten. U kunt dit wijzigen door **Ja** of **Nee**te selecteren. U kunt de bestands typen die moeten worden uitgesloten, bewerken en opgeven, zoals hieronder wordt weer gegeven:

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
  - Azure Backup service blijft alle bestaande herstel punten behouden.  
  - U kunt de back-upgegevens voor niet-verlopen herstel punten herstellen.
  - Als u besluit de beveiliging te hervatten, kunt u de optie *back-upschema opnieuw inschakelen* gebruiken. Daarna blijven gegevens bewaard op basis van het nieuwe Bewaar beleid.
- **Stop de beveiliging en verwijder de back-upgegevens**.
  - Met deze optie worden alle toekomstige back-uptaken gestopt om uw gegevens te beschermen en alle herstel punten te verwijderen.
  - U ontvangt een waarschuwing voor het verwijderen van back-upgegevens met een bericht *dat uw gegevens voor dit back-upitem zijn verwijderd. Deze gegevens zijn tijdelijk beschikbaar gedurende 14 dagen, waarna deze permanent worden verwijderd* en aanbevolen actie *het back-upitem opnieuw beveiligen binnen 14 dagen om uw gegevens te herstellen.*
  - Als u de beveiliging wilt hervatten, moet u binnen 14 dagen opnieuw beveiligen vanaf de verwijderings bewerking.

### <a name="stop-protection-and-retain-backup-data"></a>Beveiliging stoppen en back-upgegevens behouden

1. Open de MARS-beheer console, ga naar het **deel venster acties**en **Selecteer back-up plannen**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-manage-mars/mars-actions.png)
1. Selecteer op de pagina **beleids item selecteren** **de optie een back-upschema voor uw bestanden en mappen wijzigen** en klik op **volgende**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Op de pagina **een geplande back-up wijzigen of stoppen** selecteert **u stoppen met het gebruik van dit back-upschema, maar behoud de opgeslagen back-ups totdat een schema opnieuw wordt geactiveerd**. Selecteer vervolgens **volgende**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Bekijk de informatie in de **geplande back-up onderbreken** en klik op **volt ooien**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. Controleer in **back-upproces wijzigen** of de back-up voor het plannen van uw planning de status geslaagd heeft en klik op **sluiten** om te volt ooien.

### <a name="stop-protection-and-delete-backup-data"></a>Beveiliging stoppen en back-upgegevens verwijderen

1. Open de MARS-beheer console, ga naar het deel venster **acties** en selecteer **back-up plannen**.
2. Op de pagina **een geplande back-up wijzigen of stoppen** selecteert **u stoppen met het gebruik van dit back-upschema en alle opgeslagen back-ups verwijderen**. Selecteer vervolgens **volgende**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Selecteer **volt ooien**op de pagina **een geplande back-up stoppen** .

    ![Een geplande back-up stoppen.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. U wordt gevraagd een beveiligings pincode (persoonlijk identificatie nummer) in te voeren, die u hand matig moet genereren. Als u dit wilt doen, meldt u zich eerst aan bij de Azure Portal.
5. Ga naar **Recovery Services** > **Settings** > **Eigenschappen**van de kluis instellingen.
6. Onder **BEVEILIGINGS pincode**selecteert u **genereren**. Deze pincode kopiëren. De pincode is slechts vijf minuten geldig.
7. Plak de pincode in de beheer console en selecteer **OK**.

    ![Een beveiligings pincode genereren.](./media/backup-azure-delete-vault/security-pin.png)

8. Op de pagina **voortgang van back-up wijzigen** wordt het volgende bericht weer gegeven: *verwijderde back-upgegevens worden 14 dagen bewaard. Na die tijd worden de back-upgegevens permanent verwijderd.*  

    ![Verwijder de back-upinfrastructuur.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Nadat u de on-premises back-upitems hebt verwijderd, voert u de volgende stappen uit in de portal.

## <a name="re-enable-protection"></a>Beveiliging opnieuw inschakelen

Als u de beveiliging hebt gestopt terwijl u de gegevens behoudt en hebt besloten om de beveiliging te hervatten, kunt u het back-upschema opnieuw inschakelen met behulp van het back-upbeleid wijzigen.

1. Selecteer bij **acties** de optie **back-up plannen**.
1. Selecteer **back-upschema opnieuw inschakelen. U kunt ook back-upitems of tijden wijzigen** en op **volgende**klikken.<br>

    ![Verwijder de back-upinfrastructuur.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Klik in **items selecteren om een back-up te maken**op **volgende**.

    ![Verwijder de back-upinfrastructuur.](./media/backup-azure-manage-mars/re-enable-next.png)
1. In **back-upschema opgeven**geeft u het back-upschema op en klikt u op **volgende**.
1. Geef in **Bewaar beleid selecteren**de Bewaar duur op en klik op **volgende**.
1. Controleer ten slotte de details van het beleid in het **bevestigings** scherm en klik op **volt ooien**.

## <a name="re-generate-passphrase"></a>Wachtwoordzin opnieuw genereren

Een wachtwoordzin wordt gebruikt voor het versleutelen en ontsleutelen van gegevens tijdens het maken van een back-up of het herstellen van uw on-premises of lokale computer met de MARS-agent naar of van Azure. Als u de wachtwoordzin kwijtraakt of verg eten bent, kunt u de wachtwoordzin opnieuw genereren (mits uw computer nog steeds is geregistreerd bij de Recovery Services kluis en de back-up is geconfigureerd) door de volgende stappen te volgen:

- Ga in de Mars agent-console naar het **deel venster** > acties en**Wijzig de eigenschappen** >. Ga vervolgens naar het **tabblad versleuteling**.<br>
- Selecteer selectie vakje **wachtwoordzin wijzigen** .<br>
- Voer een nieuwe wachtwoordzin in of klik op **wachtwoordzin genereren**.
- Klik op **Bladeren** om de nieuwe wachtwoordzin op te slaan.

    ![Wachtwoordzin genereren.](./media/backup-azure-manage-mars/passphrase.png)
- Klik op **OK** om de wijzigingen toe te passen.  Als de [beveiligings functie](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) is ingeschakeld op de Azure portal voor de Recovery Services kluis, wordt u gevraagd om de beveiligings pincode in te voeren. Volg de stappen in dit [artikel](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations)om de pincode te ontvangen.<br>
- Plak de beveiligings pincode uit de portal en klik op **OK** om de wijzigingen toe te passen.<br>

    ![Wachtwoordzin genereren.](./media/backup-azure-manage-mars/passphrase2.png)
- Zorg ervoor dat de wachtwoordzin veilig wordt opgeslagen op een andere locatie (anders dan de bron machine), bij voor keur in het Azure Key Vault. Houd alle wachtwoordzin bij als er een back-up van meerdere machines met de MARS-agents wordt gemaakt.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [ondersteunings matrix voor de Mars-agent](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)voor meer informatie over ondersteunde scenario's en beperkingen.
- Meer informatie over het [Bewaar gedrag van back-upbeleid op aanvraag](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
