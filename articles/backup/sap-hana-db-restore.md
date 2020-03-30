---
title: SAP HANA-databases herstellen in Azure VM's
description: Ontdek in dit artikel hoe u SAP HANA-databases herstellen die worden uitgevoerd op Azure Virtual Machines.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287916"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>SAP HANA-databases herstellen in Azure VM's

In dit artikel wordt beschreven hoe sap HANA-databases die worden uitgevoerd op Azure Virtual Machine (VM) kunnen worden hersteld, waarvan de Azure Backup-service een back-up heeft gemaakt van een Azure Backup Recovery Services-kluis. Herstelt kan worden gebruikt om kopieën van de gegevens voor dev / test scenario's of om terug te keren naar een eerdere staat.

Zie [Back-ups maken van SAP HANA-databases in Azure VM's](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)voor meer informatie over het maken van back-ups van SAP HANA-databases.

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Herstellen naar een punt in de tijd of naar een herstelpunt

Azure Backup kan SAP HANA-databases die op Azure VM's worden uitgevoerd als volgt herstellen:

* Herstellen naar een specifieke datum of tijd (naar de tweede) met behulp van logboekback-ups. Azure Backup bepaalt automatisch de juiste volledige, differentiële back-ups en de keten van logboekback-ups die moeten worden hersteld op basis van de geselecteerde tijd.

* Herstellen naar een specifieke volledige of differentiële back-up om te herstellen naar een specifiek herstelpunt.

## <a name="prerequisites"></a>Vereisten

Noteer het volgende voordat u een database herstelt:

* U de database alleen herstellen naar een SAP HANA-exemplaar dat zich in dezelfde regio bevindt

* De doelinstantie moet worden geregistreerd bij dezelfde kluis als de bron

* Azure Backup kan niet twee verschillende SAP HANA-exemplaren op dezelfde VM identificeren. Daarom is het niet mogelijk om gegevens van de ene instantie naar de andere op dezelfde vm te herstellen

* Controleer de status van de **status Back-upgereedheid** voor het doel SAP HANA:

  * Open de kluis waarin het doel SAP HANA-exemplaar is geregistreerd

  * Kies **Back-up** op het dashboard van de kluis onder **Aan de slag**

![Back-up in het dashboard van de kluis](media/sap-hana-db-restore/getting-started-backup.png)

* In **Backup**, onder **Wat wilt u back-up?** kies **SAP HANA in Azure VM**

![SAP HANA kiezen in Azure VM](media/sap-hana-db-restore/sap-hana-backup.png)

* Klik **onder DB's ontdekken in VM's** op **Details weergeven**

![Details weergeven](media/sap-hana-db-restore/view-details.png)

* De **gereedheid voor back-ups** van de doel-vm controleren

![Beveiligde servers](media/sap-hana-db-restore/protected-servers.png)

* Voor meer informatie over de hersteltypen die SAP HANA ondersteunt, raadpleegt u de SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Een database herstellen

* Open de kluis waarin de te herstellen SAP HANA-database is geregistreerd

* Kies **Back-upitems** op het kluisdashboard onder **Beveiligde items**

![Back-upitems](media/sap-hana-db-restore/backup-items.png)

* Selecteer in **Back-upitems**onder **Type Back-upbeheer** **SAP HANA in Azure VM**

![Type back-upbeheer](media/sap-hana-db-restore/backup-management-type.png)

* Selecteer de database die moet worden hersteld

 ![Database om te herstellen](media/sap-hana-db-restore/database-to-restore.png)

* Bekijk het databasemenu. Het biedt informatie over databaseback-up, waaronder:

  * De oudste en laatste herstelpunten

  * De status van de logboekback-up van de afgelopen 24 en 72 uur voor de database

![Databasemenu](media/sap-hana-db-restore/database-menu.png)

* **D-herstel selecteren**

* Geef **onder Configuratie herstellen**op waar (of hoe) gegevens moeten worden hersteld:

  * **Alternatieve locatie:** herstel de database naar een alternatieve locatie en bewaar de oorspronkelijke brondatabase.

  * **Db overschrijven**: De gegevens herstellen naar dezelfde SAP HANA-instantie als de oorspronkelijke bron. Met deze optie wordt de oorspronkelijke database overschrijft.

![Configuratie herstellen](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Herstellen naar alternatieve locatie

* Selecteer **Alternatieve locatie**in het menu **Configuratie herstellen** onder Waar **te herstellen**.

![Herstellen naar alternatieve locatie](media/sap-hana-db-restore/restore-alternate-location.png)

* Selecteer de SAP HANA-hostnaam en -instantienaam waaraan u de database wilt herstellen.
* Controleer of de doel-SAP HANA-instantie klaar is voor herstel door ervoor te zorgen dat de **back-upgereedheid wordt gegarandeerd.** Raadpleeg de [sectie voorwaarden](#prerequisites) voor meer informatie.
* Geef in het vak **Herstelde databasenaam** de naam van de doeldatabase op.

> [!NOTE]
> Herstel van single database container (SDC) moet deze [controles](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)volgen .

* Selecteer indien van toepassing **Overschrijven als de DB met dezelfde naam al bestaat op geselecteerde HANA-instantie**.
* Selecteer **OK**.

![Configuratie herstellen - laatste scherm](media/sap-hana-db-restore/restore-configuration-last.png)

* Selecteer in **Herstelpunt selecteren**de optie **Logboeken (Punt in tijd)** om te [herstellen naar een specifiek punt in de tijd](#restore-to-a-specific-point-in-time). Of selecteer **Volledig & differentieel** om te herstellen naar een specifiek [herstelpunt](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Herstellen en overschrijven

* Selecteer in het menu **Configuratie herstellen** onder **Waar herstellen**de optie DB > OK **overschrijven**.**OK**

![DB overschrijven](media/sap-hana-db-restore/overwrite-db.png)

* Selecteer in **Herstelpunt selecteren**de optie **Logboeken (Punt in tijd)** om te [herstellen naar een specifiek punt in de tijd](#restore-to-a-specific-point-in-time). Of selecteer **Volledig & differentieel** om te herstellen naar een specifiek [herstelpunt](#restore-to-a-specific-recovery-point).

### <a name="restore-to-a-specific-point-in-time"></a>Herstellen naar een specifiek tijdstip

Als u **Logboeken (Punt in tijd)** als hersteltype hebt geselecteerd, gaat u als volgt te werk:

* Selecteer een herstelpunt in de logboekgrafiek en selecteer **OK** om het herstelpunt te kiezen.

![Herstelpunt](media/sap-hana-db-restore/restore-point.png)

* Selecteer **Herstellen** in het menu **Herstellen** om de hersteltaak te starten.

![Herstellen selecteren](media/sap-hana-db-restore/restore-restore.png)

* Houd de voortgang van het herstellen bij in het gebied **Meldingen** of volg deze door **Taken herstellen** in het databasemenu te selecteren.

![Getriggerd herstellen is voltooid](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Herstellen naar een specifiek herstelpunt

Als u **Volledig & differentieel** als hersteltype hebt geselecteerd, gaat u als volgt te werk:

* Selecteer een herstelpunt in de lijst en selecteer **OK** om het herstelpunt te kiezen.

![Specifiek herstelpunt herstellen](media/sap-hana-db-restore/specific-recovery-point.png)

* Selecteer **Herstellen** in het menu **Herstellen** om de hersteltaak te starten.

![Hersteltaak starten](media/sap-hana-db-restore/restore-specific.png)

* Houd de voortgang van het herstellen bij in het gebied **Meldingen** of volg deze door **Taken herstellen** in het databasemenu te selecteren.

![Voortgang herstellen](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> In Multiple Database Container (MDC) herstelt nadat het systeem DB is hersteld naar een doelinstantie, moet men het pre-registratiescript opnieuw uitvoeren. Alleen dan zal de volgende huurder DB herstelt slagen. Voor meer informatie verwijzen naar [Probleemoplossing – MDC Restore](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het](sap-hana-db-manage.md) beheren van SAP HANA-databases waar een back-up van wordt gemaakt met Azure Backup
