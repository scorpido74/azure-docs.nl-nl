---
title: SAP HANA data bases herstellen op virtuele Azure-machines
description: In dit artikel vindt u informatie over het herstellen van SAP HANA-data bases die worden uitgevoerd op Azure Virtual Machines.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74287916"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>SAP HANA data bases herstellen op virtuele Azure-machines

In dit artikel wordt beschreven hoe u SAP HANA-data bases die worden uitgevoerd op een virtuele machine (VM) van Azure kunt herstellen. de Azure Backup-service heeft een back-up gemaakt van een Azure Backup Recovery Services kluis. Herstel bewerkingen kunnen worden gebruikt om kopieën te maken van de gegevens voor dev/test-scenario's of om terug te gaan naar een eerdere status.

Zie back-up maken van [SAP Hana data bases op Azure-vm's](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)voor meer informatie over het maken van back-ups van SAP Hana-data bases.

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Herstellen naar een bepaald tijdstip of naar een herstel punt

Azure Backup kunt SAP HANA-data bases die worden uitgevoerd op virtuele Azure-machines als volgt herstellen:

* Herstel naar een specifieke datum of tijd (naar de tweede) met behulp van logboek back-ups. Azure Backup bepaalt automatisch de juiste volledige, differentiële back-ups en de keten van logboek back-ups die nodig zijn om te herstellen op basis van de geselecteerde tijd.

* Herstel naar een specifieke volledige of een differentiële back-up om naar een specifiek herstel punt te herstellen.

## <a name="prerequisites"></a>Vereisten

Let op het volgende voordat u een Data Base herstelt:

* U kunt de data base alleen herstellen naar een SAP HANA-exemplaar dat zich in dezelfde regio bevindt

* Het doel exemplaar moet zijn geregistreerd bij dezelfde kluis als de bron

* Azure Backup kan niet twee verschillende SAP HANA exemplaren op dezelfde VM identificeren. Het terugzetten van gegevens van het ene naar het andere exemplaar op dezelfde VM is daarom niet mogelijk

* Om ervoor te zorgen dat het doel SAP HANA exemplaar gereed is voor herstel, controleert u de status van de **back-up** :

  * Open de kluis waarin het doel SAP HANA exemplaar is geregistreerd

  * Kies **back-up** in het kluis dashboard, onder **aan**de slag

![Back-up in het kluis dashboard](media/sap-hana-db-restore/getting-started-backup.png)

* Klik in **back-up**, onder **waarvan wilt u een back-up maken?** Kies **SAP Hana in azure VM**

![SAP HANA kiezen in een Azure-VM](media/sap-hana-db-restore/sap-hana-backup.png)

* Klik onder **Db's detecteren in vm's** op **Details weer geven**

![Details weergeven](media/sap-hana-db-restore/view-details.png)

* De voor **bereiding van de back-up** van de doel-VM controleren

![Beveiligde servers](media/sap-hana-db-restore/protected-servers.png)

* Raadpleeg de SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) voor meer informatie over de typen herstel die SAP Hana ondersteunt.

## <a name="restore-a-database"></a>Een database herstellen

* Open de kluis waarin de SAP HANA data base moet worden hersteld

* Kies op het kluis dashboard onder **beveiligde items** **Back-upitems**

![Back-upitems](media/sap-hana-db-restore/backup-items.png)

* In **Back-upitems**, onder **type back-upbeheer** selecteert u **SAP Hana in azure VM**

![Type back-upbeheer](media/sap-hana-db-restore/backup-management-type.png)

* Selecteer de data base die u wilt herstellen

 ![Te herstellen data base](media/sap-hana-db-restore/database-to-restore.png)

* Bekijk het database menu. Het bevat informatie over back-up van de data base, waaronder:

  * De oudste en meest recente herstel punten

  * De back-upstatus van het logboek voor de afgelopen 24 en 72 uur voor de data base

![Menu Data Base](media/sap-hana-db-restore/database-menu.png)

* **Data Base herstellen** selecteren

* Onder **configuratie herstellen**, geeft u op waar (of hoe) u gegevens wilt herstellen:

  * **Alternatieve locatie**: herstel de Data Base op een andere locatie en behoud de oorspronkelijke bron database.

  * **Db overschrijven**: herstel de gegevens naar hetzelfde SAP Hana exemplaar als de oorspronkelijke bron. Met deze optie wordt de oorspronkelijke data base overschreven.

![Configuratie herstellen](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Herstellen naar alternatieve locatie

* Selecteer in het menu **herstellen configuratie** , onder **waar u wilt herstellen**, **alternatieve locatie**.

![Herstellen naar alternatieve locatie](media/sap-hana-db-restore/restore-alternate-location.png)

* Selecteer de SAP HANA hostnaam en de naam van het exemplaar waarnaar u de Data Base wilt herstellen.
* Controleer of het doel SAP HANA exemplaar gereed is voor herstel door de **back-up gereedheid** te garanderen. Raadpleeg de [sectie vereisten](#prerequisites) voor meer informatie.
* Geef in het vak **Herstelde databasenaam** de naam van de doeldatabase op.

> [!NOTE]
> Individuele database-container (dit SDC) moet deze [controles](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)volgen.

* Indien van toepassing, selecteert u **overschrijven als de data base met dezelfde naam al bestaat in het geselecteerde Hana-exemplaar**.
* Selecteer **OK**.

![Configuratie herstellen-laatste scherm](media/sap-hana-db-restore/restore-configuration-last.png)

* Selecteer in **herstel punt selecteren** **Logboeken (tijdstip)** om [naar een bepaald punt in de tijd te herstellen](#restore-to-a-specific-point-in-time). Of selecteer **volledige & differentieel** om [terug te zetten naar een specifiek herstel punt](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Herstellen en overschrijven

* Selecteer in het menu **configuratie herstellen** onder **herstel van herstellen**de optie **Data Base** > overschrijven**OK**.

![DB overschrijven](media/sap-hana-db-restore/overwrite-db.png)

* Selecteer in **herstel punt selecteren** **Logboeken (tijdstip)** om [naar een bepaald punt in de tijd te herstellen](#restore-to-a-specific-point-in-time). Of selecteer **volledige & differentieel** om [terug te zetten naar een specifiek herstel punt](#restore-to-a-specific-recovery-point).

### <a name="restore-to-a-specific-point-in-time"></a>Herstellen naar een bepaald punt in de tijd

Ga als volgt te werk als u **Logboeken** hebt geselecteerd (tijdstip) als herstel type:

* Selecteer een herstel punt in de logboek grafiek en selecteer **OK** om het herstel punt te kiezen.

![Herstelpunt](media/sap-hana-db-restore/restore-point.png)

* Selecteer **Herstellen** in het menu **Herstellen** om de hersteltaak te starten.

![Selecteer herstellen](media/sap-hana-db-restore/restore-restore.png)

* Volg de voortgang van de herstel bewerking in het gebied **meldingen** of volg deze door **taken herstellen** te selecteren in het menu Data Base.

![De herstel bewerking is geactiveerd](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Herstellen naar een specifiek herstel punt

Ga als volgt te werk als u **volledige & differentieel** hebt geselecteerd als het herstel type:

* Selecteer een herstel punt in de lijst en selecteer **OK** om het herstel punt te kiezen.

![Specifiek herstel punt herstellen](media/sap-hana-db-restore/specific-recovery-point.png)

* Selecteer **Herstellen** in het menu **Herstellen** om de hersteltaak te starten.

![Herstel taak starten](media/sap-hana-db-restore/restore-specific.png)

* Volg de voortgang van de herstel bewerking in het gebied **meldingen** of volg deze door **taken herstellen** te selecteren in het menu Data Base.

![Voortgang van herstellen](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> In meerdere database container (MDC) herstel bewerkingen nadat de systeem database is hersteld naar een doel exemplaar, moet het script vooraf worden uitgevoerd. Alleen dan zullen de volgende Tenant-DB-herstel bewerkingen slagen. Raadpleeg voor meer informatie over het [oplossen van problemen – MDC herstellen](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het](sap-hana-db-manage.md) beheren van SAP Hana databases waarvan een back-up is gemaakt met Azure backup
