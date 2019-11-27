---
title: Back-ups van SAP HANA data bases op virtuele machines van Azure beheren
description: In dit artikel leert u algemene taken voor het beheren en bewaken van SAP HANA-data bases die worden uitgevoerd op virtuele machines van Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: f76054c7c78c55a9754975267ee4fa3caab968a3
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74288345"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Back-ups van SAP HANA-data bases beheren en bewaken

In dit artikel worden algemene taken beschreven voor het beheren en bewaken van SAP HANA-data bases die worden uitgevoerd op een virtuele Azure-machine (VM) en waarvan een back-up is gemaakt naar een Azure Backup Recovery Services kluis door de [Azure backup](https://docs.microsoft.com/azure/backup/backup-overview) -service. U leert hoe u taken en waarschuwingen bewaken, een back-up op aanvraag kunt activeren, beleids regels kunt bewerken, de database beveiliging wilt stoppen en hervatten en de registratie van een VM kunt verwijderen uit back-ups.

Als u nog geen back-ups hebt geconfigureerd voor uw SAP HANA-data bases, raadpleegt u back-ups [maken SAP Hana data bases op Azure-vm's](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Hand matige back-uptaken controleren in de portal

Azure Backup worden alle hand matig geactiveerde taken weer gegeven in de sectie **back-uptaken** op Azure Portal.

![Sectie back-uptaken](./media/sap-hana-db-manage/backup-jobs.png)

De taken die u in deze Portal ziet, omvatten database detectie en-registratie, en back-up-en herstel bewerkingen. Geplande taken, waaronder logboek back-ups, worden niet weer gegeven in deze sectie. Hand matig geactiveerde back-ups van de SAP HANA systeem eigen clients (Studio/cockpit/DBA cockpit) worden hier ook niet weer gegeven.

![Lijst met back-uptaken](./media/sap-hana-db-manage/backup-jobs-list.png)

Ga voor meer informatie over bewaking naar [bewaking in het Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) en [bewaking met behulp van Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="view-backup-alerts"></a>Waarschuwingen voor back-ups weergeven

Waarschuwingen zijn een eenvoudige manier om back-ups van SAP HANA-data bases te bewaken. Waarschuwingen zorgen ervoor dat u zich kunt concentreren op de gebeurtenissen die u het meest vindt, zonder dat u in het grootst aan gebeurtenissen gaat die door een back-up worden gegenereerd. Met Azure Backup kunt u waarschuwingen instellen en ze kunnen als volgt worden bewaakt:

* Meld u aan bij [Azure Portal](https://portal.azure.com/).
* Selecteer **back-upwaarschuwingen**op het kluis dashboard.

  ![Back-upwaarschuwingen op het kluis dashboard](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* U kunt de volgende waarschuwingen weer geven:

  ![Lijst met back-upwaarschuwingen](./media/sap-hana-db-manage/backup-alerts-list.png)

* Klik op de waarschuwingen voor meer informatie:

  ![Meldingsdetails](./media/sap-hana-db-manage/alert-details.png)

Vandaag kunt Azure Backup de verzen ding van waarschuwingen via e-mail toestaan. Deze waarschuwingen zijn:

* Geactiveerd voor alle back-upfouten.
* Geconsolideerd op database niveau op fout code.
* Alleen verzonden voor de eerste back-upfout van een Data Base.

Naar meer informatie over bewaking, gaat u naar [bewaking in het Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) en [bewaking met behulp van Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="management-operations"></a>Beheerbewerkingen

Azure Backup maakt het beheer van een back-up van SAP HANA data base eenvoudig met een overvloed aan beheer bewerkingen die worden ondersteund. Deze bewerkingen worden uitgebreid beschreven in de volgende secties.

### <a name="run-an-ad-hoc-backup"></a>Een ad-hoc back-up uitvoeren

Back-ups worden uitgevoerd volgens het beleids schema. U kunt als volgt een back-up op aanvraag uitvoeren:

1. Klik in het menu kluis op **Back-upitems**.
2. Selecteer in **Back-upitems**de virtuele machine waarop de SAP Hana-data base wordt uitgevoerd en klik vervolgens op **Nu back-up maken**.
3. In **Nu back-up**kunt u het besturings element kalender gebruiken om de laatste dag te selecteren dat het herstel punt moet worden bewaard. Klik vervolgens op **OK**.
4. De portal meldingen bewaken. U kunt de voortgang van de taak in het kluis dashboard controleren > **back-uptaken** > worden **uitgevoerd**. Afhankelijk van de grootte van de data base kan het maken van de eerste back-up enige tijd duren.

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>SAP HANA systeem eigen client back-up uitvoeren op een Data Base waarvoor Azure Backup is ingeschakeld

Ga als volgt te werk als u een lokale back-up wilt maken (met behulp van HANA Studio/cockpit) van een Data Base waarvan een back-up wordt gemaakt met Azure Backup:

1. Wacht totdat alle volledige of logboek back-ups voor de Data Base zijn voltooid. Controleer de status in SAP HANA Studio/cockpit.
2. Schakel logboek back-ups uit en stel de back-catalogus in op het bestands systeem voor de relevante data base.
3. U doet dit door te dubbel klikken op **systemdb** > **configuratie** > **Data Base** > **filter (logboek)** te selecteren.
4. Stel **enable_auto_log_backup** in op **Nee**.
5. Stel **log_backup_using_backint** in op **False**.
6. Maak een ad-hoc volledige back-up van de data base.
7. Wacht tot de volledige back-up en catalogus back-up zijn voltooid.
8. De vorige instellingen herstellen voor Azure:
   * Stel **enable_auto_log_backup** in op **Ja**.
   * Stel **log_backup_using_backint** in op **waar**.

### <a name="edit-underlying-policy"></a>Onderliggend beleid bewerken

Wijzig het beleid om de back-upfrequentie of het Bewaar bereik te wijzigen:

* Ga in het kluis dashboard naar > **back-upbeleid** **beheren**

  ![Back-upbeleid in het kluis dashboard](./media/sap-hana-db-manage/backup-policies-dashboard.png)

* Kies het beleid dat u wilt bewerken:

  ![Lijst met back-upbeleid](./media/sap-hana-db-manage/backup-policies-list.png)

  ![Details van back-upbeleid](./media/sap-hana-db-manage/backup-policy-details.png)

>[!NOTE]
> Elke wijziging in de retentie periode wordt retro actief toegepast op alle oudere herstel punten, naast de nieuwe.
>
> Incrementeel back-upbeleid kan niet worden gebruikt voor SAP HANA-data bases. Incrementele back-up wordt momenteel niet ondersteund voor deze data bases.

### <a name="stop-protection-for-an-sap-hana-database"></a>De beveiliging van een SAP HANA data base stoppen

U kunt het beveiligen van een SAP HANA Data Base op een aantal manieren stoppen:

* Alle toekomstige back-uptaken stoppen en alle herstelpunten verwijderen.
* Alle toekomstige back-uptaken stoppen en de herstel punten intact laten.

Als u ervoor kiest herstel punten te verlaten, houdt u deze details in acht:

* Alle herstel punten blijven intact, alle weghalen stoppen bij het stoppen van de beveiliging bij het bewaren van gegevens.
* Er worden kosten in rekening gebracht voor het beveiligde exemplaar en de verbruikte opslag. Zie [Azure backup prijzen](https://azure.microsoft.com/pricing/details/backup/)voor meer informatie.
* Als u een gegevens bron verwijdert zonder back-ups te stoppen, zullen nieuwe back-ups mislukken.

Ga als volgt te werk om de beveiliging van een database te stoppen:

* Selecteer **Back-upitems**op het kluis dashboard.
* Onder **type back-upbeheer**selecteert u **SAP Hana in azure VM**

  ![SAP HANA selecteren in azure VM](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Selecteer de data base waarvoor u de beveiliging wilt stoppen:

  ![Data base selecteren om beveiliging te stoppen](./media/sap-hana-db-manage/select-database.png)

* Selecteer **back-up stoppen**in het menu Data Base.

  ![Back-up stoppen selecteren](./media/sap-hana-db-manage/stop-backup.png)

* Selecteer in het menu **back-up stoppen** of u gegevens wilt behouden of verwijderen. Als u wilt, kunt u een reden en opmerking opgeven.

  ![Selecteer gegevens behouden of verwijderen](./media/sap-hana-db-manage/retain-backup-data.png)

* Selecteer **back-up stoppen**.

### <a name="resume-protection-for-an-sap-hana-database"></a>De beveiliging hervatten voor een SAP HANA data base

Wanneer u de beveiliging voor de SAP HANA-data base stopt en u de optie **back-upgegevens behouden** selecteert, kunt u de beveiliging later hervatten. Als u de gegevens van de back-up niet behoudt, kunt u de beveiliging niet hervatten.

De beveiliging van een SAP HANA-data base hervatten:

* Open het back-upitem en selecteer **back-up hervatten**.

   ![Back-up hervatten selecteren](./media/sap-hana-db-manage/resume-backup.png)

* Selecteer een beleid in het menu **Back-upbeleid** en selecteer vervolgens **Opslaan**.

### <a name="upgrading-from-sap-hana-10-to-20"></a>Upgraden van SAP HANA 1,0 naar 2,0

Meer informatie over het maken van back-ups voor een SAP HANA-Data Base [na een upgrade van SAP HANA 1,0 naar 2,0](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20).

### <a name="upgrading-without-a-sid-change"></a>Upgraden zonder SID-wijziging

Meer informatie over het maken van een back-up van een SAP HANA-data base waarvan de [sid na de upgrade niet is gewijzigd](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change).

### <a name="unregister-an-sap-hana-database"></a>Registratie van een SAP HANA data base ongedaan maken

Hef de registratie van een SAP HANA-exemplaar op nadat u de beveiliging hebt uitgeschakeld, maar voordat u de kluis verwijdert:

* Selecteer op het kluis dashboard onder **beheren**de optie **back-upinfrastructuur**.

   ![Back-upinfrastructuur selecteren](./media/sap-hana-db-manage/backup-infrastructure.png)

* Selecteer het **type back-upbeheer** als **werk belasting in azure VM**

   ![Selecteer het type back-upbeheer als werk belasting in azure VM](./media/sap-hana-db-manage/backup-management-type.png)

* Selecteer in **beveiligde servers**het exemplaar dat u wilt verwijderen. Als u de kluis wilt verwijderen, moet u de registratie van alle servers/exemplaren ongedaan maken.

* Klik met de rechter muisknop op het beveiligde exemplaar en selecteer **verwijderen ongedaan maken**.

   ![Selecteer registratie ongedaan maken](./media/sap-hana-db-manage/unregister.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [oplossen van veelvoorkomende problemen bij het maken van back-ups van SAP Hana-data bases.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)

