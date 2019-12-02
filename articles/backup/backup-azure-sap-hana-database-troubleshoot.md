---
title: Back-upfouten van SAP HANA databases oplossen
description: Hierin wordt beschreven hoe u veelvoorkomende fouten oplost die zich kunnen voordoen wanneer u Azure Backup gebruikt om back-ups te maken van SAP HANA-data bases.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: e8bb1d3328f95b647a788c53afe3ac1455eefa13
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665335"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Problemen met back-ups van SAP HANA-data bases in azure oplossen

Dit artikel bevat informatie over het oplossen van problemen met het maken van back-ups van SAP HANA-data bases op Azure virtual machines. Voor meer informatie over de SAP HANA back-upscenario's die momenteel worden ondersteund, raadpleegt u [scenario ondersteuning](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Vereisten en machtigingen

Raadpleeg de secties [vereisten](tutorial-backup-sap-hana-db.md#prerequisites) en [instellen van machtigingen](tutorial-backup-sap-hana-db.md#setting-up-permissions) voor het configureren van back-ups.

## <a name="common-user-errors"></a>Veelvoorkomende gebruikers fouten

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Foutbericht      | <span style="font-weight:normal">Kan geen verbinding maken met het HANA-systeem</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Het SAP HANA-exemplaar is mogelijk niet actief.<br/>De vereiste machtigingen voor Azure backup voor interactie met de HANA-Data Base zijn niet ingesteld. |
| **Aanbevolen actie** | Controleer of de SAP HANA-data base actief is. Als de data base actief is, controleert u of alle vereiste machtigingen zijn ingesteld. Als een van de machtigingen ontbreekt, voert u het script voor de [voorafgaande registratie](https://aka.ms/scriptforpermsonhana) uit om de ontbrekende machtigingen toe te voegen. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Foutbericht      | <span style="font-weight:normal">Het opgegeven SAP HANA exemplaar is ongeldig of kan niet worden gevonden</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Er kan geen back-up worden gemaakt van meerdere SAP HANA exemplaren op één virtuele Azure-machine. |
| **Aanbevolen actie** | Voer het [script voor de voorafgaande registratie](https://aka.ms/scriptforpermsonhana) uit op het SAP Hana-exemplaar waarvan u een back-up wilt maken. Neem contact op met micro soft ondersteuning als het probleem zich blijft voordoen. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Foutbericht      | <span style="font-weight:normal">De opgegeven SAP HANA bewerking wordt niet ondersteund</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Azure backup voor SAP HANA biedt geen ondersteuning voor incrementele back-ups en acties die worden uitgevoerd op SAP HANA systeem eigen clients (Studio/cockpit/DBA cockpit) |
| **Aanbevolen actie** | Zie [hier](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)voor meer informatie. |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Foutbericht      | <span style="font-weight:normal">Deze SAP HANA-Data Base biedt geen ondersteuning voor het aangevraagde back-uptype</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Azure Backup biedt geen ondersteuning voor incrementele back-ups en back-ups met behulp van moment opnamen |
| **Aanbevolen actie** | Zie [hier](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)voor meer informatie. |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Foutbericht      | <span style="font-weight:normal">De keten van het back-uplogboek is verbroken</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Het doel van de logboek back-up is mogelijk bijgewerkt van backint naar File System of het uitvoer bare bestand backint is gewijzigd |
| **Aanbevolen actie** | Een volledige back-up activeren om het probleem op te lossen                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore

| Foutbericht      | <span style="font-weight:normal">De bron-en doel systemen voor herstel zijn niet compatibel</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Het doel systeem voor herstel is niet compatibel met de bron |
| **Aanbevolen actie** | Raadpleeg SAP Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) voor meer informatie over de typen herstel die momenteel worden ondersteund |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Foutbericht      | <span style="font-weight:normal">Upgrade van dit SDC naar MDC is gedetecteerd</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | Het SAP HANA-exemplaar is bijgewerkt van dit SDC naar MDC. Back-ups mislukken na de update. |
| **Aanbevolen actie** | Volg de stappen die worden beschreven in de [sectie upgrades van SAP HANA 1,0 naar 2,0](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) om het probleem op te lossen |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Foutbericht      | <span style="font-weight:normal">Ongeldige backint-configuratie gedetecteerd</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Mogelijke oorzaken**    | De para meters voor back-ups zijn onjuist opgegeven voor Azure backup |
| **Aanbevolen actie** | Controleer of de volgende (backint)-para meters zijn ingesteld:<br/>\* [catalog_backup_using_backint: True]<br/>\* [enable_accumulated_catalog_backup: False]<br/>\* [parallel_data_backup_backint_channels: 1]<br/>\* [log_backup_timeout_s: 900)]<br/>\* [backint_response_timeout: 7200]<br/>Als de backint-para meters aanwezig zijn op de HOST, verwijdert u deze. Als de para meters niet aanwezig zijn op het niveau van de HOST, maar die hand matig zijn gewijzigd op database niveau, moet u deze herstellen naar de juiste waarden zoals eerder beschreven. Of voer de [beveiliging stoppen uit en behoud back-upgegevens](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) van de Azure Portal en selecteer vervolgens **back-up hervatten**. |

## <a name="restore-checks"></a>Controles herstellen

### <a name="single-container-database-sdc-restore"></a>Dit SDC-herstel (single container data base)

Voer de invoer uit tijdens het herstellen van één container database (dit SDC) voor HANA naar een andere dit SDC-computer. De naam van de data base moet worden opgegeven met kleine letters en ' dit SDC ' toegevoegd tussen haakjes. De HANA-instantie wordt weer gegeven in hoofd letters.

Stel dat er een back-up wordt gemaakt van een dit SDC HANA-exemplaar ' h21 '. Op de pagina Back-upitems wordt de naam van het back-upartikel weer gegeven als **' h21 (dit SDC) '** . Als u probeert om deze data base te herstellen naar een andere doel-dit SDC, zegt u H11. vervolgens moet u de volgende invoer invoeren.

![Dit SDC herstellen](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Houd rekening met de volgende punten:

- De naam van de herstelde DB wordt standaard gevuld met de naam van het back-upitem, bijvoorbeeld h21 (dit SDC)
- Als u het doel als H11 selecteert, wordt de herstelde database naam niet automatisch gewijzigd. **Deze moet worden bewerkt in H11 (dit SDC)** . Met betrekking tot dit SDC wordt de herstelde database naam de doel exemplaar-ID met kleine letters en ' dit SDC ' toegevoegd tussen haakjes.
- Aangezien dit SDC slechts één data base kan hebben, moet u ook op het selectie vakje klikken om de bestaande database gegevens met de herstel punt gegevens te overschrijven.
- Linux is hoofdletter gevoelig. Zorg er daarom voor dat u de case behoudt.

### <a name="multiple-container-database-mdc-restore"></a>Multiple container data base (MDC) Restore

In meerdere container databases voor HANA is de standaard configuratie SYSTEMDB + 1 of meer Tenant Db's. Als u een volledig SAP HANA exemplaar herstelt, moet u zowel de SYSTEMDB als de Tenant Db's herstellen. Eén herstelt SYSTEMDB eerst en gaat vervolgens verder met de Tenant-data base. Systeem-DB betekent in wezen dat de systeem gegevens op het geselecteerde doel worden overschreven. Deze herstel bewerking overschrijft ook de BackInt-gerelateerde informatie in het doel exemplaar. Nadat de systeem database is hersteld naar een doel exemplaar, moet er daarom opnieuw een pre-registratie script worden uitgevoerd. Alleen dan zullen de volgende Tenant-DB-herstel bewerkingen slagen.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Upgraden van SAP HANA 1,0 naar 2,0

Als u SAP HANA 1,0-data bases beveiligt en een upgrade naar 2,0 wilt uitvoeren, voert u de onderstaande stappen uit:

- [Stop de beveiliging](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) met behoud van gegevens voor de oude dit SDC-data base.
- Voer het script voor de [voorafgaande registratie](https://aka.ms/scriptforpermsonhana) opnieuw uit met de juiste details van (sid en MDC).
- Extensie opnieuw registreren (back-up > Details weer geven-> de relevante Azure-VM selecteren-> opnieuw registreren).
- Klik op Db's opnieuw detecteren voor dezelfde VM. Bij deze actie moet de nieuwe Db's in stap 2 worden weer gegeven met de juiste Details (SYSTEMDB en Tenant database, niet dit SDC).
- Beveilig deze nieuwe data bases.

## <a name="upgrading-without-an-sid-change"></a>Upgraden zonder SID-wijziging

Upgrades naar besturings systeem of SAP HANA die geen SID-wijziging veroorzaken, kunnen worden verwerkt, zoals hieronder wordt beschreven:

- [Beveiliging stoppen](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) met het bewaren van gegevens voor de data base
- Voer het [script voor de voorafgaande registratie](https://aka.ms/scriptforpermsonhana) opnieuw uit
- De beveiliging voor de data base opnieuw [hervatten](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database)

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [Veelgestelde vragen](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) over het maken van back-ups van SAP Hana-data bases op Azure-vm's]
