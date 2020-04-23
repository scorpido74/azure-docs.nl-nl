---
title: Back-ups van MARS-agent beheren en bewaken
description: Meer informatie over het beheren en bewaken van MARS-back-ups (Microsoft Azure Recovery Services) met behulp van de Azure Backup-service.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: a88ec4dc9283114e06eed424172dbb958850c2e9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025098"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Back-ups van Microsoft Azure Recovery Services (MARS) beheren met behulp van de Azure Backup-service

In dit artikel wordt beschreven hoe u bestanden en mappen beheert waarvan een back-up wordt uitgevoerd met de Microsoft Azure Recovery Services Agent.

## <a name="modify-a-backup-policy"></a>Een back-upbeleid wijzigen

Wanneer u het back-upbeleid wijzigt, u nieuwe items toevoegen, bestaande items uit back-upverwijderen of een back-up van bestanden uitsluiten met uitsluitingsinstellingen.

- **Items toevoegen** Gebruik deze optie alleen voor het toevoegen van nieuwe items om een back-up te maken. Als u bestaande items wilt verwijderen, gebruikt u De optie **Items verwijderen** of **Uitsluitingsinstellingen.**  
- **Items verwijderen** Gebruik deze optie om te voorkomen dat er een back-up van items wordt gemaakt.
  - **Uitsluitingsinstellingen gebruiken** voor het verwijderen van alle items binnen een volume in plaats van **Items verwijderen**.
  - Als u alle selecties in een volume opruimt, worden oude back-ups van de items bewaard volgens de bewaarinstellingen op het moment van de laatste back-up, zonder ruimte voor wijziging.
  - Het opnieuw selecteren van deze items, leidt tot een eerste volledige back-up en nieuwe beleidswijzigingen worden niet toegepast op oude back-ups.
  - Als u het volledige volume uitschakelt, behoudt u een back-up uit het verleden zonder dat er ruimte is voor het wijzigen van het bewaarbeleid.
- **Uitsluitingsinstellingen** gebruiken deze optie om uit te sluiten dat er een back-up van bepaalde items wordt gemaakt.

### <a name="add-new-items-to-existing-policy"></a>Nieuwe items toevoegen aan bestaand beleid

1. Klik **in Acties**op **Back-up plannen**.

    ![Een back-up van de Windows Server plannen](./media/backup-configure-vault/schedule-first-backup.png)

2. Selecteer op het tabblad **Beleidsitem selecteren** en selecteer **Back-upschema wijzigen voor uw bestanden en mappen** en klik op **Volgende**.

    ![Beleidsitems selecteren](./media/backup-azure-manage-mars/select-policy-items.png)

3. Selecteer in **Het tabblad Back-up wijzigen of stoppen met plannen** de optie Wijzigingen aanbrengen in **back-upitems of -tijden** en klik op **Volgende**.

    ![Back-up wijzigen of plannen](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Klik in Het tabblad **Items selecteren op Back-up** **op Items toevoegen** om de items toe te voegen waarvan u een back-up wilt maken.

    ![Back-uptoevoegen van items wijzigen of plannen](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Selecteer in het venster **Items selecteren** de optie vliegen of mappen die u wilt toevoegen en klik op **OK**.

    ![De items selecteren](./media/backup-azure-manage-mars/select-item.png)

6. Voer de volgende stappen uit en klik op **Voltooien** om de bewerking te voltooien.

### <a name="add-exclusion-rules-to-existing-policy"></a>Uitsluitingsregels toevoegen aan bestaand beleid

U uitsluitingsregels toevoegen om bestanden en mappen over te slaan waarvan geen back-up van u wordt opgenomen. U dit doen bij het definiëren van een nieuw beleid of het wijzigen van een bestaand beleid.

1. Klik in het deelvenster Handelingen op **Back-up plannen**. Ga naar **Items selecteren om back-up te maken** en klik op **Uitsluitingsinstellingen**.

    ![De items selecteren](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. Klik in **Uitsluitingsinstellingen**op **Uitsluiting toevoegen**.

    ![De items selecteren](./media/backup-azure-manage-mars/add-exclusion.png)

3. Blader **in Items selecteren om uit te sluiten,** blader door de bestanden en mappen en selecteer items die u wilt uitsluiten en klik op **OK**.

    ![De items selecteren](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Standaard zijn alle **submappen** binnen de geselecteerde mappen uitgesloten. U dit wijzigen door **Ja** of **Nee te**selecteren. U de bestandstypen bewerken en opgeven die u wilt uitsluiten, zoals hieronder wordt weergegeven:

    ![De items selecteren](./media/backup-azure-manage-mars/subfolders-type.png)

5. Voer de volgende stappen uit en klik op **Voltooien** om de bewerking te voltooien.

### <a name="remove-items-from-existing-policy"></a>Items verwijderen uit bestaand beleid

1. Klik in het deelvenster Handelingen op **Back-up plannen**. Ga naar **Items selecteren om een back-up te maken.** Selecteer in de lijst de bestanden en mappen die u uit de back-upplanning wilt verwijderen en klik op **Items verwijderen**.

    ![De items selecteren](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Ga voorzichtig te werk wanneer u een volume volledig uit het beleid verwijdert.  Als u het opnieuw moet toevoegen, dan zal het als nieuw volume worden behandeld. De volgende geplande back-up voert een initial backup (volledige back-up) uit in plaats van Incrementele back-up. Als u items tijdelijk moet verwijderen en later wilt toevoegen, wordt aanbevolen om **uitsluitingsinstellingen** te gebruiken in plaats van **Items verwijderen** om incrementele back-up te garanderen in plaats van volledige back-up.

2. Voer de volgende stappen uit en klik op **Voltooien** om de bewerking te voltooien.

## <a name="stop-protecting-files-and-folder-backup"></a>Stoppen met het beveiligen van bestanden en mapback-ups

Er zijn twee manieren om de back-up van bestanden en mappen niet meer te beveiligen:

- **Stop de beveiliging en bewaar back-upgegevens.**
  - Met deze optie worden alle toekomstige back-uptaken van de beveiliging gestopt.
  - Azure Backup-service blijft alle bestaande herstelpunten behouden.  
  - U de back-upgegevens herstellen voor niet-verlopen herstelpunten.
  - Als u besluit de beveiliging te hervatten, u de optie *Back-upschema opnieuw inschakelen.* Daarna worden gegevens bewaard op basis van het nieuwe bewaarbeleid.
- **Stop de beveiliging en verwijder back-upgegevens**.
  - Met deze optie worden alle toekomstige back-uptaken gestopt om uw gegevens te beschermen en worden alle herstelpunten verwijderd.
  - U ontvangt een e-mail met een waarschuwing voor back-upgegevens met een bericht *Uw gegevens voor dit back-upitem zijn verwijderd. Deze gegevens zijn tijdelijk beschikbaar voor 14 dagen, waarna ze permanent worden verwijderd* en aanbevolen actie Bescherm het *back-upitem binnen 14 dagen opnieuw om uw gegevens te herstellen.*
  - Als u de beveiliging wilt hervatten, moet u de beveiliging binnen 14 dagen na de verwijderingsbewerking opnieuw beveiligen.

### <a name="stop-protection-and-retain-backup-data"></a>Beveiliging stoppen en back-upgegevens bewaren

1. Open de MARS-beheerconsole, ga naar het **deelvenster Acties**en **selecteer Back-up plannen**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-manage-mars/mars-actions.png)
1. Selecteer op de pagina **Beleidsitem selecteren** de optie **Een back-upschema voor uw bestanden en mappen wijzigen** en klik op **Volgende**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Selecteer op de pagina **Een geplande back-up wijzigen of stoppen** de optie Stoppen met het gebruik van dit **back-upschema, maar bewaar de opgeslagen back-ups totdat een schema opnieuw is geactiveerd.** Selecteer vervolgens **Volgende**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Controleer in **Geplande back-up onderbreken** de informatie en klik op **Voltooien**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. Controleer **in Het back-upproces wijzigen** of de planningsback-upstatus in de successtatus staat en klik op **dicht** bij het voltooien.

### <a name="stop-protection-and-delete-backup-data"></a>Beveiliging stoppen en back-upgegevens verwijderen

1. Open de MARS-beheerconsole, ga naar het deelvenster **Acties** en selecteer **Back-up plannen**.
2. Selecteer op de pagina **Een geplande back-up wijzigen of stoppen** de optie Stoppen met het gebruik van dit **back-upschema en verwijder alle opgeslagen back-ups.** Selecteer vervolgens **Volgende**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Selecteer Op de pagina **Een geplande back-up stoppen** de optie **Voltooien**.

    ![Stop een geplande back-up.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. U wordt gevraagd een beveiligingspincode (persoonlijk identificatienummer) in te voeren, die u handmatig moet genereren. Meld u hiervoor eerst aan bij de Azure-portal.
5. Ga naar**Eigenschappen**van de > **kluisinstellingen** >  **van Herstelservices**.
6. Selecteer **Onder Beveiligingspincode**de optie **Genereren**. Kopieer deze pincode. De pincode is slechts vijf minuten geldig.
7. Plak de pincode in de beheerconsole en selecteer **OK**.

    ![Een beveiligingspincode genereren.](./media/backup-azure-delete-vault/security-pin.png)

8. Op de pagina **Back-upvoortgang wijzigen** wordt het volgende bericht weergegeven: *verwijderde back-upgegevens worden gedurende 14 dagen bewaard. Na die tijd worden back-upgegevens permanent verwijderd.*  

    ![Verwijder de back-upinfrastructuur.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Nadat u de on-premises back-upitems hebt verwijderd, voert u de volgende stappen van de portal uit.

## <a name="re-enable-protection"></a>Bescherming opnieuw inschakelen

Als u de beveiliging hebt gestopt terwijl u gegevens bewaart en hebt besloten de beveiliging te hervatten, u het back-upschema opnieuw inschakelen met behulp van het back-upbeleid wijzigen.

1. Selecteer **bij Acties** **Back-up plannen**.
1. Selecteer **Back-upplanning opnieuw inschakelen. U ook back-upitems of -tijden wijzigen** en op **Volgende**klikken.<br>

    ![Verwijder de back-upinfrastructuur.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Klik **in Items selecteren om een back-up te maken**op **Volgende**.

    ![Verwijder de back-upinfrastructuur.](./media/backup-azure-manage-mars/re-enable-next.png)
1. Geef **in Back-upplanning opgeven**het back-upschema op en klik op **Volgende**.
1. Geef **in Bewaarbeleid selecteren**de bewaarduur op en klik op **Volgende**.
1. Tot slot bekijk je in het **bevestigingsscherm** de beleidsdetails en klik op **Voltooien**.

## <a name="re-generate-passphrase"></a>Wachtwoordzin opnieuw genereren

Een wachtwoordzin wordt gebruikt om gegevens te versleutelen en te decoderen terwijl u een back-up maakt of uw on-premises of lokale machine herstelt met behulp van de MARS-agent van of naar Azure. Als u de wachtwoordzin hebt verloren of vergeten, u de wachtwoordzin opnieuw genereren (op voorwaarde dat uw machine nog steeds is geregistreerd bij de Vault voor Herstelservices en de back-up is geconfigureerd) door de volgende stappen te volgen:

- Ga vanuit de console van de MARS-agent naar Eigenschappen **van deelvenster** > **wijzigen >.** Ga dan naar **het tabblad Versleuteling**.<br>
- Schakel het selectievakje **Wachtwoordzin wijzigen** in.<br>
- Voer een nieuwe wachtwoordzin in of klik op **Wachtwoordzin genereren**.
- Klik **op Bladeren** om de nieuwe wachtwoordzin op te slaan.

    ![Passzin genereren.](./media/backup-azure-manage-mars/passphrase.png)
- Klik op **OK** om wijzigingen toe te passen.  Als de [beveiligingsfunctie](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) is ingeschakeld op de Azure-portal voor de Vault voor Herstelservices, wordt u gevraagd de beveiligingspincode in te voeren. Volg de stappen in dit [artikel](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations)om de pincode te ontvangen.<br>
- Plak de beveiligingspincode van de portal en klik op **OK** om de wijzigingen toe te passen.<br>

    ![Passzin genereren.](./media/backup-azure-manage-mars/passphrase2.png)
- Zorg ervoor dat de wachtwoordzin veilig wordt opgeslagen op een alternatieve locatie (andere dan de bronmachine), bij voorkeur in de Azure Key Vault. Houd alle wachtwoordzinnen bij als er meerdere machines worden ondersteund met de MARS-agenten.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [ondersteuningsmatrix voor de MARS-agent voor](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)informatie over ondersteunde scenario's en beperkingen.
- Meer informatie over [on demand-beleidbehoudgedrag](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)op aanvraag .
