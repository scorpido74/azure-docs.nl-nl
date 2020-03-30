---
title: Problemen met sap HANA-databases back-upfouten oplossen
description: Beschrijft hoe u veelvoorkomende fouten oplossen die kunnen optreden wanneer u Azure Backup gebruikt om een back-up te maken van SAP HANA-databases.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 6520f106011b632da2725f456aeb278c7748ddc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459307"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Back-ups van SAP HANA-databases op Azure oplossen

In dit artikel vindt u informatie over het oplossen van problemen voor het maken van back-ups van SAP HANA-databases op virtuele Azure-machines. Zie [Scenario-ondersteuning](sap-hana-backup-support-matrix.md#scenario-support)voor meer informatie over de SAP HANA-back-upscenario's die we momenteel ondersteunen.

## <a name="prerequisites-and-permissions"></a>Voorwaarden en machtigingen

Raadpleeg de [vereisten](tutorial-backup-sap-hana-db.md#prerequisites) en [wat het preregistratiescript doet](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) secties voor het configureren van back-ups.

## <a name="common-user-errors"></a>Veelvoorkomende gebruikersfouten

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Foutbericht**      | <span style="font-weight:normal">Azure-back-up heeft geen vereiste rolbevoegdheden om back-ups uit te voeren</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | De rol kan zijn overschreven.                          |
| **Aanbevolen actie** | Als u het probleem wilt oplossen, voert u het script uit in het deelvenster **DB ontdekken** of downloadt u het [hier.](https://aka.ms/scriptforpermsonhana) U ook de rol 'SAP_INTERNAL_HANA_SUPPORT' toevoegen aan de Workload Backup User (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Foutbericht      | <span style="font-weight:normal">Kan geen verbinding maken met hana-systeem</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | De SAP HANA-instantie kan uitzijn.<br/>De vereiste machtigingen voor Azure-back-up om te communiceren met de HANA-database zijn niet ingesteld. |
| **Aanbevolen actie** | Controleer of de SAP HANA-database is geopend. Als de database actief is, controleert u of alle vereiste machtigingen zijn ingesteld. Als een van de machtigingen ontbreekt, voert u het [voorregistratiescript](https://aka.ms/scriptforpermsonhana) uit om de ontbrekende machtigingen toe te voegen. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameOngeldig

| Foutbericht      | <span style="font-weight:normal">De opgegeven SAP HANA-instantie is ongeldig of kan niet worden gevonden</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Er kan geen back-up worden van meerdere SAP HANA-exemplaren op één Azure-vm. |
| **Aanbevolen actie** | Voer het [voorregistratiescript](https://aka.ms/scriptforpermsonhana) uit op het SAP HANA-exemplaar dat u een back-up wilt maken. Als het probleem zich nog steeds voordoet, neemt u contact op met de ondersteuning van Microsoft. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Foutbericht      | <span style="font-weight:normal">De opgegeven SAP HANA-bewerking wordt niet ondersteund</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Azure-back-up voor SAP HANA biedt geen ondersteuning voor incrementele back-up en acties die worden uitgevoerd op SAP HANA-native clients (Studio/ Cockpit/ DBA Cockpit) |
| **Aanbevolen actie** | Voor meer informatie, zie [hier](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Foutbericht      | <span style="font-weight:normal">Deze SAP HANA-database ondersteunt het gevraagde back-uptype niet</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Azure-back-up biedt geen ondersteuning voor incrementele back-up en back-up met behulp van momentopnamen |
| **Aanbevolen actie** | Voor meer informatie, zie [hier](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Foutbericht      | <span style="font-weight:normal">Back-up logboekketen is verbroken</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | De adresbestemming voor een back-up van het logboek is mogelijk bijgewerkt van backint naar bestandssysteem of de backint uitvoerbaar kan zijn gewijzigd |
| **Aanbevolen actie** | Een volledige back-up activeren om het probleem op te lossen                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore

| Foutbericht      | <span style="font-weight:normal">De bron- en doelsystemen voor herstel zijn onverenigbaar</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Het doelsysteem voor herstel is onverenigbaar met de bron |
| **Aanbevolen actie** | Raadpleeg de SAP Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) voor meer informatie over de hersteltypen die vandaag worden ondersteund |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeGedetecteerd

| Foutbericht      | <span style="font-weight:normal">SDC naar MDC-upgrade gedetecteerd</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | De SAP HANA-instantie is geüpgraded van SDC naar MDC. Back-ups mislukken na de update. |
| **Aanbevolen actie** | Volg de stappen in de [sectie Upgraden van SAP HANA 1.0 naar 2.0](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) om het probleem op te lossen |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration UserErrorInvalidBackintConfiguration UserErrorInvalidBackintConfiguration UserError

| Foutbericht      | <span style="font-weight:normal">Gedetecteerde ongeldige backint-configuratie</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | De backparameters zijn onjuist opgegeven voor Azure-back-up |
| **Aanbevolen actie** | Controleer of de volgende parameters (backint) zijn ingesteld:<br/>\*[catalog_backup_using_backint:waar]<br/>\*[enable_accumulated_catalog_backup:false]<br/>\*[parallel_data_backup_backint_channels:1]<br/>\*[log_backup_timeout_s:900)]<br/>\*[backint_response_timeout:7200]<br/>Als backint-gebaseerde parameters aanwezig zijn in HOST, verwijdert u deze. Als parameters niet aanwezig zijn op HOST-niveau, maar handmatig zijn gewijzigd op databaseniveau, moet u deze terugzetten naar de juiste waarden zoals eerder beschreven. Of voer stopbeveiliging uit [en bewaar back-upgegevens](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) van de Azure-portal en selecteer **back-up hervatten.** |

## <a name="restore-checks"></a>Controle herstellen

### <a name="single-container-database-sdc-restore"></a>SDC (Single Container Database) herstellen

Zorg voor ingangen terwijl u een enkele containerdatabase (SDC) voor HANA herstelt naar een andere SDC-machine. De database naam moet worden gegeven met kleine letters en met "sdc" toegevoegd tussen haakjes. De HANA-instantie wordt weergegeven in hoofdletters.

Stel dat een SDC HANA-exemplaar "H21" wordt ondersteund. Op de pagina back-upitems wordt de naam van het back-upitem weergegeven als **'h21(sdc)".** Als u probeert deze database te herstellen naar een andere doel-SDC, bijvoorbeeld H11, moeten volgende ingangen worden verstrekt.

![Herstelde SDC-databasenaam](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Houd rekening met de volgende punten:

- Standaard wordt de herstelde db-naam gevuld met de naam van het back-upitem. In dit geval h21(sdc).
- Als u het doel als H11 selecteert, wordt de herstelde db-naam NIET automatisch gewijzigd. **Het moet worden bewerkt tot h11(sdc)**. Wat SDC betreft, zal de herstelde db-naam de doelinstantie-ID zijn met kleine letters en 'sdc' tussen haakjes.
- Aangezien SDC slechts één database kan hebben, moet u ook op het selectievakje klikken om het overschrijven van de bestaande databasegegevens met de gegevens van het herstelpunt toe te staan.
- Linux is case-gevoelig. Dus wees voorzichtig om de zaak te behouden.

### <a name="multiple-container-database-mdc-restore"></a>MDC (Multiple Container Database) herstellen

In meerdere containerdatabases voor HANA is de standaardconfiguratie SYSTEMDB + 1 of meer Tenant DBs. Het herstellen van een volledig SAP HANA-exemplaar betekent dat zowel SYSTEMDB als Tenant DBs worden hersteld. Men herstelt SYSTEMDB eerst en gaat dan verder voor Tenant DB. Systeem DB betekent in wezen om de systeeminformatie over het geselecteerde doel te overschrijven. Met dit herstel wordt ook de BackInt-gerelateerde informatie in de doelinstantie overschreven. Dus nadat het systeem DB is hersteld naar een doelinstantie, voert u het pre-registratiescript opnieuw uit. Alleen dan zal de volgende huurder DB herstelt slagen.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Upgraden van SAP HANA 1.0 naar 2.0

Als u SAP HANA 1.0-databases beschermt en wilt upgraden naar 2.0, voert u de volgende stappen uit:

- [Stop de beveiliging](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) met het bewaren van gegevens voor oude SDC-database.
- Voer de upgrade uit. Na voltooiing is de HANA nu MDC met een systeem DB en huurder DB(s)
- Rerun [pre-registratie script](https://aka.ms/scriptforpermsonhana) met de juiste details van (sid en mdc).
- Registreer de extensie opnieuw voor dezelfde machine in azure portal (Back-up -> bekijk details -> Selecteer de relevante Azure VM-> Opnieuw registreren).
- Klik op DB's opnieuw ontdekken voor dezelfde vm. Deze actie moet de nieuwe DB's in stap 2 weergeven met de juiste details (SYSTEMDB en Tenant DB, niet SDC).
- Configureer back-up voor deze nieuwe databases.

## <a name="upgrading-without-an-sid-change"></a>Upgraden zonder SID-wijziging

Upgrades naar OS of SAP HANA die geen SID-wijziging veroorzaken, kunnen worden verwerkt zoals hieronder beschreven:

- [Beveiliging stoppen](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) met bewaren gegevens voor de database
- Voer de upgrade uit.
- Voer het [preregistratiescript](https://aka.ms/scriptforpermsonhana)opnieuw uit. Meestal hebben we gezien upgrade proces verwijdert de nodige rollen. Als u het preregistratiescript uitvoert, u alle vereiste rollen verifiëren.
- [De beveiliging](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) van de database opnieuw hervatten

## <a name="re-registration-failures"></a>Fouten bij de herregistratie

Controleer op een of meer van de volgende symptomen voordat u de bewerking opnieuw registreren activeert:

- Alle bewerkingen (zoals back-up, herstel en back-up) mislukken op de VM met een van de volgende foutcodes: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Als het gebied **Back-upstatus** voor het back-upitem **niet bereikbaar**wordt weergegeven, sluit u alle andere oorzaken uit die kunnen leiden tot dezelfde status:

  - Gebrek aan toestemming om back-upgerelateerde bewerkingen uit te voeren op de VM
  - De VM wordt afgesloten, dus back-ups kunnen niet plaatsvinden
  - Netwerkproblemen

Deze symptomen kunnen zich voordoen om een of meer van de volgende redenen:

- Een extensie is verwijderd of verwijderd van de portal.
- De VM is hersteld terug in de tijd door middel van in-place schijf te herstellen.
- De VM werd voor een langere periode afgesloten, zodat de extensieconfiguratie ervan is verlopen.
- De VM is verwijderd en een andere vm is gemaakt met dezelfde naam en in dezelfde brongroep als de verwijderde VM.

In de voorgaande scenario's raden we u aan een bewerking opnieuw te registreren op de VM.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [veelgestelde vragen](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) over het maken van back-ups van SAP HANA-databases op Azure VM's.
