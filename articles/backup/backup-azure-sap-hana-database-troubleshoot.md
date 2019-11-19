---
title: Back-upfouten van SAP HANA databases oplossen
description: Hierin wordt beschreven hoe u veelvoorkomende fouten oplost die zich kunnen voordoen wanneer u Azure Backup gebruikt om back-ups te maken van SAP HANA-data bases.
ms.topic: conceptual
ms.date: 08/03/2019
ms.openlocfilehash: cbffa7415f315fd396e57afa355d2415c4612eb5
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172749"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Problemen met back-ups van SAP HANA-data bases in azure oplossen

Dit artikel bevat informatie over het oplossen van problemen met het maken van back-ups van SAP HANA-data bases op Azure virtual machines. De volgende sectie bevat belang rijke conceptuele gegevens die nodig zijn voor het opsporen van een veelvoorkomende fout in SAP HANA back-up.

## <a name="prerequisites"></a>Vereisten

Zorg er als onderdeel van de [vereisten](backup-azure-sap-hana-database.md#prerequisites)voor dat het script voor de voorafgaande registratie is uitgevoerd op de virtuele machine waarop Hana is geïnstalleerd.

### <a name="setting-up-permissions"></a>Machtigingen instellen

Wat is het voor registratie script:

1. Maakt AZUREWLBACKUPHANAUSER in het HANA-systeem en voegt deze vereiste rollen en machtigingen toe:
    - DATABASE beheerder: Maak tijdens het terugzetten een nieuwe Db's.
    - CATALOGUS gelezen: voor het lezen van de back-catalogus.
    - SAP_INTERNAL_HANA_SUPPORT: voor toegang tot een paar persoonlijke tabellen.
2. Voegt een sleutel toe aan Hdbuserstore voor de HANA-invoeg toepassing voor het afhandelen van alle bewerkingen (database query's, herstel bewerkingen, het configureren en uitvoeren van back-ups).

   U kunt controleren of de sleutel is gemaakt door de opdracht HDBSQL uit te voeren op de HANA-computer met SIDADM-referenties:

    ``` hdbsql
    hdbuserstore list
    ```

    De uitvoer van de opdracht moet de sleutel {SID} {DBNAME} bevatten, waarbij de gebruiker als AZUREWLBACKUPHANAUSER wordt weer gegeven.

> [!NOTE]
> Zorg ervoor dat u een unieke set SSFS-bestanden hebt onder **/usr/sap/{sid}/Home/.HDB/** . Dit pad mag slechts één map bevatten.

### <a name="setting-up-backint-parameters"></a>BackInt-para meters instellen

Nadat een Data Base is gekozen voor back-up, configureert de Azure Backup-Service backInt-para meters op DATABASE niveau:

- [catalog_backup_using_backint: True]
- [enable_accumulated_catalog_backup: False]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> Zorg ervoor dat deze para meters *niet* aanwezig zijn op het niveau van de host. Met para meters op hostniveau worden deze para meters overschreven en kan dit leiden tot onverwacht gedrag.

## <a name="restore-checks"></a>Controles herstellen

### <a name="single-container-database-sdc-restore"></a>Dit SDC-herstel (single container data base)

Voer de invoer uit tijdens het herstellen van één container database (dit SDC) voor HANA naar een andere dit SDC-computer. De naam van de data base moet worden opgegeven met kleine letters en ' dit SDC ' toegevoegd tussen haakjes. De HANA-instantie wordt weer gegeven in hoofd letters.

Stel dat er een back-up wordt gemaakt van een dit SDC HANA-exemplaar ' h21 '. Op de pagina Back-upitems wordt de naam van het back-upartikel weer gegeven als **' h21 (dit SDC) '** . Als u probeert om deze data base te herstellen naar een andere doel-dit SDC, zegt u H11. vervolgens moet u de volgende invoer invoeren.

![Dit SDC herstellen](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Houd rekening met de volgende punten:

- De naam van de herstelde DB wordt standaard gevuld met de naam van het back-upitem, bijvoorbeeld h21 (dit SDC)
- Als u het doel als H11 selecteert, wordt de herstelde database naam niet automatisch gewijzigd. **Deze moet worden bewerkt in H11 (dit SDC)** . In het geval van dit SDC is de herstelde database naam de doel exemplaar-ID met kleine letters en ' dit SDC ' toegevoegd tussen haakjes.
- Aangezien dit SDC slechts één data base kan hebben, moet u ook op het selectie vakje klikken om de bestaande database gegevens met de herstel punt gegevens te overschrijven.
- Linux is hoofdletter gevoelig en zorgt er daarom voor dat u de case behoudt.

### <a name="multiple-container-database-mdc-restore"></a>Multiple container data base (MDC) Restore

In meerdere container databases voor HANA is de standaard configuratie SYSTEMDB + 1 of meer Tenant Db's. Als u een volledig SAP HANA exemplaar herstelt, moet u zowel de SYSTEMDB als de Tenant Db's herstellen. Eén herstelt SYSTEMDB eerst en gaat vervolgens verder met de Tenant-data base. Systeem-DB betekent in wezen dat de systeem gegevens op het geselecteerde doel worden overschreven. Dit overschrijft ook de BackInt gerelateerde informatie in het doel exemplaar. Nadat de systeem database is hersteld naar een doel exemplaar, moet er daarom opnieuw een pre-registratie script worden uitgevoerd. Alleen dan zullen de volgende Tenant-DB-herstel bewerkingen slagen.

## <a name="common-user-errors"></a>Veelvoorkomende gebruikers fouten

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

data| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Kan geen verbinding maken met het HANA-systeem. Controleer of uw systeem actief is.| De Azure Backup-service kan geen verbinding maken met HANA omdat de HANA-data base niet beschikbaar is. Of HANA wordt uitgevoerd, maar staat niet toe dat de Azure Backup-service verbinding maakt. | Controleer of de HANA-data base of service niet beschikbaar is. Als de HANA-data base of-service actief is, controleert u of [alle machtigingen zijn ingesteld](#setting-up-permissions). Als de sleutel ontbreekt, voert u het script voor de voorafgaande registratie opnieuw uit om een nieuwe sleutel te maken. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Ongeldige Backint-configuratie gedetecteerd. Stop de beveiliging en configureer de data base opnieuw.| De backInt-para meters zijn onjuist opgegeven voor Azure Backup. | Controleer of [de para meters zijn ingesteld](#setting-up-backint-parameters). Als de backInt-para meters aanwezig zijn op de HOST, verwijdert u deze. Als de para meters niet aanwezig zijn op het niveau van de HOST, maar hand matig zijn gewijzigd op database niveau, moet u deze herstellen naar de juiste waarden zoals eerder beschreven. Of voer de **beveiliging stoppen uit en behoud back-upgegevens** van de Azure Portal en selecteer vervolgens **back-up hervatten**.|
