---
title: Back-ups van SAP HANA-databases beheren in Azure VM's
description: Lees in dit artikel algemene taken voor het beheren en bewaken van SAP HANA-databases die worden uitgevoerd op virtuele Azure-machines.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 89fd7f23163d301817e767771257d9bc6f4ed526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480059"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Back-ups van SAP HANA-databases beheren en bewaken

In dit artikel worden veelvoorkomende taken beschreven voor het beheren en bewaken van SAP HANA-databases die worden uitgevoerd op een virtuele Azure-machine (VM) en waarvan een back-up wordt gemaakt van een Azure Backup Recovery Services-kluis door de [Azure Backup-service.](https://docs.microsoft.com/azure/backup/backup-overview) U leert hoe u taken en waarschuwingen controleren, een on-demand back-up activeren, beleid bewerken, databasebeveiliging stoppen en hervatten en het registreren van een VM uitschrijven van back-ups.

Zie [Back-ups maken van SAP HANA-databases in Azure VM's](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)als u back-ups nog niet hebt geconfigureerd voor uw SAP HANA-databases.

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Handmatige back-uptaken in de portal controleren

Azure Backup toont alle handmatig geactiveerde taken in de sectie **Back-uptaken** op Azure-portal.

![Sectie Back-uptaken](./media/sap-hana-db-manage/backup-jobs.png)

De taken die u in deze portal ziet, zijn databasedetectie en -registratie en back-up- en herstelbewerkingen. Geplande taken, waaronder logboekback-ups, worden niet weergegeven in deze sectie. Handmatig geactiveerde back-ups van de SAP HANA native clients (Studio/ Cockpit/ DBA Cockpit) komen hier ook niet opdagen.

![Lijst met back-uptaken](./media/sap-hana-db-manage/backup-jobs-list.png)

Ga voor meer informatie over monitoring naar [Controleren in de Azure-portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) en [Bewaking met Azure Monitor.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)

## <a name="view-backup-alerts"></a>Waarschuwingen voor back-ups weergeven

Waarschuwingen zijn een eenvoudig middel om back-ups van SAP HANA-databases te monitoren. Waarschuwingen helpen u zich te concentreren op de gebeurtenissen waar u het meest om geeft zonder te verdwalen in de veelheid aan gebeurtenissen die een back-up genereert. Azure Backup stelt u in staat om waarschuwingen in te stellen en deze kunnen als volgt worden gecontroleerd:

* Meld u aan bij [Azure Portal](https://portal.azure.com/).
* Selecteer **back-upmeldingen in**het kluisdashboard .

  ![Back-upwaarschuwingen op het vault-dashboard](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* U de waarschuwingen zien:

  ![Lijst met back-upwaarschuwingen](./media/sap-hana-db-manage/backup-alerts-list.png)

* Klik op de waarschuwingen voor meer details:

  ![Meldingsdetails](./media/sap-hana-db-manage/alert-details.png)

Vandaag de dag maakt Azure Backup het verzenden van waarschuwingen via e-mail mogelijk. Deze waarschuwingen zijn:

* Geactiveerd voor alle back-upfouten.
* Geconsolideerd op databaseniveau door foutcode.
* Alleen verzonden voor de eerste back-upstoring van een database.

Ga voor meer informatie over controle naar [Controleren in de Azure-portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) en [Bewaking met Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="management-operations"></a>Beheerbewerkingen

Azure Backup maakt het beheer van een back-up SAP HANA-database eenvoudig met een overvloed aan beheerbewerkingen die het ondersteunt. Deze bewerkingen worden in de volgende secties nader besproken.

### <a name="run-an-on-demand-backup"></a>Een on-demand back-up uitvoeren

Back-ups worden uitgevoerd in overeenstemming met het beleidsschema. U een back-up op aanvraag als volgt uitvoeren:

1. Klik in het kluismenu op **Back-upitems**.
2. Selecteer **in Back-upitems**de VM waarop de SAP HANA-database wordt uitgevoerd en klik nu op **Back-up**.
3. Gebruik **in Nu back-up**het agendabesturingselement om de laatste dag te selecteren waarop het herstelpunt moet worden behouden. Klik vervolgens op **OK**.
4. Controleer de portalmeldingen. U de voortgang van de taak in het kluisdashboard controleren > **Back-uptaken** > **in uitvoering**. Afhankelijk van de grootte van uw database kan het maken van de eerste back-up even duren.

### <a name="hana-native-client-integration"></a>HANA native client integratie

Nu on-demand volledige back-ups geactiveerd van een van de HANA native clients zal verschijnen als een volledige back-up op de **back-up items** pagina.

![Laatste back-ups worden uitgevoerd](./media/sap-hana-db-manage/last-backups.png)

Deze ad-hoc volledige back-ups worden ook weergegeven in de lijst met herstelpunten voor herstel.

![Lijst met herstelpunten](./media/sap-hana-db-manage/list-restore-points.png)

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Sap HANA native clientback-up uitvoeren in een database met Azure-back-up ingeschakeld

Als u een lokale back-up wilt maken (met HANA Studio / Cockpit) van een database waarvan een back-up wordt gemaakt met Azure Backup, gaat u als volgt te werk:

1. Wacht tot alle volledige of log back-ups voor de database te voltooien. Controleer de status in SAP HANA Studio/ Cockpit.
2. Schakel logboekback-ups uit en stel de back-upcatalogus in op het bestandssysteem voor relevante database.
3. Dubbelklik hiervoor op **systemdb** > **Configuration** > **Select Database** > **Filter (Log).**
4. Stel **enable_auto_log_backup** in op **Nee.**
5. Stel **log_backup_using_backint** **in op False**.
6. Neem een on-demand volledige back-up van de database.
7. Wacht tot de volledige back-up en catalogusback-up is voltooid.
8. De vorige instellingen terugzetten naar die voor Azure:
   * Stel **enable_auto_log_backup** **in op Ja**.
   * Stel **log_backup_using_backint** **in op True**.

### <a name="change-policy"></a>Beleid wijzigen

U het onderliggende beleid voor een SAP HANA-back-upitem wijzigen.

* Ga in het dashboard van de kluis naar **Back-upitems:**

  ![Back-upitems selecteren](./media/sap-hana-db-manage/backup-items.png)

* **SAP HANA kiezen in Azure VM**

  ![SAP HANA kiezen in Azure VM](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Het back-upitem kiezen waarvan u het onderliggende beleid wilt wijzigen
* Klik op het bestaande back-upbeleid

  ![Bestaand back-upbeleid selecteren](./media/sap-hana-db-manage/existing-backup-policy.png)

* Wijzig het beleid en kies uit de lijst. Maak indien nodig [een nieuw back-upbeleid.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy)

  ![Beleid kiezen uit vervolgkeuzelijst](./media/sap-hana-db-manage/choose-backup-policy.png)

* De wijzigingen opslaan

  ![De wijzigingen opslaan](./media/sap-hana-db-manage/save-changes.png)

* Beleidswijziging heeft invloed op alle bijbehorende back-upitems en activeert bijbehorende **configureprotectiontaken.**

>[!NOTE]
> Elke wijziging in de bewaartermijn zal achteraf worden toegepast op alle oudere herstelpunten naast de nieuwe.
>
> Incrementeel back-upbeleid kan niet worden gebruikt voor SAP HANA-databases. Incrementele back-up wordt momenteel niet ondersteund voor deze databases.

### <a name="modify-policy"></a>Beleid wijzigen

Wijzig het beleid om back-uptypen, frequenties en bewaarbereik te wijzigen.

>[!NOTE]
>Elke wijziging in de bewaartermijn wordt met terugwerkende kracht toegepast op alle oudere herstelpunten, naast de nieuwe.

1. Ga in het dashboard van de kluis naar **> Back-upbeleid beheren** en kies het beleid dat u wilt bewerken.

   ![Het te bewerken beleid kiezen](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Selecteer **Wijzigen**.

   ![Wijzigen selecteren](./media/sap-hana-db-manage/modify-policy.png)

1. Kies de frequentie voor de back-uptypen.

   ![Back-upfrequentie kiezen](./media/sap-hana-db-manage/choose-frequency.png)

Beleidswijziging heeft invloed op alle bijbehorende back-upitems en activeert bijbehorende **configurebeveiligingstaken.**

### <a name="inconsistent-policy"></a>Inconsistent beleid

Af en toe kan een beleidsbewerking wijzigen leiden tot een **inconsistente** beleidsversie voor sommige back-upitems. Dit gebeurt wanneer de bijbehorende **beveiligingstaak configureren** mislukt voor het back-upitem nadat een wijzigingsbeleidsbewerking is geactiveerd. Het wordt als volgt weergegeven in de weergave back-upitem:

![Inconsistent beleid](./media/sap-hana-db-manage/inconsistent-policy.png)

U de beleidsversie voor alle getroffen items in één klik herstellen:

![Beleidsversie vastzetten](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Stop de beveiliging van een SAP HANA-database

U op een aantal manieren stoppen met het beschermen van een SAP HANA-database:

* Alle toekomstige back-uptaken stoppen en alle herstelpunten verwijderen.
* Stop alle toekomstige back-uptaken en laat de herstelpunten intact.

Als u ervoor kiest om herstelpunten achter te laten, houdt u rekening met deze gegevens:

* Alle herstelpunten blijven voor altijd intact, alle snoeien stopt bij stopbescherming met bewarende gegevens.
* Er worden kosten in rekening gebracht voor de beveiligde instantie en de verbruikte opslag. Zie [Azure Backup-prijzen](https://azure.microsoft.com/pricing/details/backup/)voor meer informatie.
* Als u een gegevensbron verwijdert zonder back-ups te stoppen, mislukken nieuwe back-ups.

Ga als volgt te werk om de beveiliging van een database te stoppen:

* Selecteer **back-upitems op**het kluisdashboard .
* Selecteer **SAP HANA in Azure VM** onder Type **Back-upbeheer**

  ![SAP HANA selecteren in Azure VM](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Selecteer de database waarvoor u de beveiliging wilt stoppen op:

  ![Database selecteren om beveiliging te stoppen](./media/sap-hana-db-manage/select-database.png)

* Selecteer **back-up stoppen**in het databasemenu.

  ![Back-up stoppen selecteren](./media/sap-hana-db-manage/stop-backup.png)

* Selecteer in het menu **Back-up stoppen** of u gegevens wilt bewaren of verwijderen. Als u wilt, geef een reden en commentaar.

  ![Gegevens behouden of verwijderen selecteren](./media/sap-hana-db-manage/retain-backup-data.png)

* Selecteer **Back-up stoppen**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Hervatting van de beveiliging van een SAP HANA-database

Wanneer u de beveiliging van de SAP HANA-database stopt, u, als u de optie **Back-upgegevens behouden** selecteert, de beveiliging later hervatten. Als u de back-upgegevens niet bewaart, u de beveiliging niet hervatten.

Ga als lid van het nieuwe bedrijf over de bescherming van een SAP HANA-database:

* Open het back-upitem en selecteer **Back-up hervatten**.

   ![Back-up hervatten selecteren](./media/sap-hana-db-manage/resume-backup.png)

* Selecteer een beleid in het menu **Back-upbeleid** en selecteer vervolgens **Opslaan**.

### <a name="upgrading-from-sap-hana-10-to-20"></a>Upgraden van SAP HANA 1.0 naar 2.0

Meer informatie over het voortzetten van back-ups voor een SAP HANA-database [na een upgrade van SAP HANA 1.0 naar 2.0](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20).

### <a name="upgrading-without-a-sid-change"></a>Upgraden zonder SID-wijziging

Meer informatie over het voortzetten van de back-up van een SAP HANA-database waarvan [de SID niet is gewijzigd na de upgrade.](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change)

### <a name="unregister-an-sap-hana-instance"></a>Het registreren van een SAP HANA-exemplaar ongedaan maken

Registreer een SAP HANA-exemplaar nadat u de beveiliging hebt uitgeschakeld, maar voordat u de kluis verwijdert:

* Selecteer **back-upinfrastructuur**onder **Beheren**.

   ![Back-upinfrastructuur selecteren](./media/sap-hana-db-manage/backup-infrastructure.png)

* Het **type Back-upbeheer** selecteren als **werkbelasting in Azure VM**

   ![Het type Back-upbeheer selecteren als werkbelasting in Azure VM](./media/sap-hana-db-manage/backup-management-type.png)

* Selecteer **in Beveiligde servers**de instantie die u wilt uitschrijven. Als u de kluis wilt verwijderen, moet u alle servers/instanties uitschrijven.

* Klik met de rechtermuisknop op de beveiligde instantie en selecteer **Uitschrijven .**

   ![Het uitschrijven selecteren](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Extensie opnieuw registreren op de SAP HANA-server VM

Soms kan de werkbelastingextensie op de VM om de een of andere reden worden beïnvloed. In dergelijke gevallen beginnen alle bewerkingen die op de VM worden geactiveerd, te mislukken. Het kan dan nodig zijn om de extensie opnieuw te registreren op de VM. Als u de bewerking opnieuw registreert, wordt de back-upextensie voor werkbelasting op de VM opnieuw geïnstalleerd om bewerkingen voort te zetten.

Gebruik deze optie met de nodige voorzichtigheid: wanneer deze wordt geactiveerd op een VM met een reeds gezonde extensie, zorgt deze bewerking ervoor dat de extensie opnieuw wordt gestart. Dit kan ertoe leiden dat alle lopende taken mislukken. Controleer op een of meer van de [symptomen](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) voordat u de bewerking opnieuw registreert.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [oplossen van veelvoorkomende problemen bij het maken van back-ups van SAP HANA-databases.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
