---
title: Veelgestelde vragen over back-ups maken van SAP HANA-databases in virtuele Azure-machines
description: Ontdek in dit artikel antwoorden op veelgestelde vragen over het maken van back-ups van SAP HANA-databases met de Azure Backup-service.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155389"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Veelgestelde vragen – Back-ups maken van SAP HANA-databases op Azure VM's

In dit artikel worden veelgestelde vragen beantwoord over het maken van back-ups van SAP HANA-databases met behulp van de Azure Backup-service.

## <a name="backup"></a>Back-up

### <a name="how-many-full-backups-are-supported-per-day"></a>Hoeveel volledige back-ups worden er per dag ondersteund?

We ondersteunen slechts één volledige back-up per dag. U geen differentiële back-up en volledige back-up op dezelfde dag laten geactiveerd.

### <a name="do-successful-backup-jobs-create-alerts"></a>Maken succesvolle back-uptaken waarschuwingen?

Nee. Succesvolle back-uptaken maken geen waarschuwingen. Er worden alleen waarschuwingen verzonden voor mislukte back-uptaken. Gedetailleerd gedrag voor portalwaarschuwingen wordt [hier](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)gedocumenteerd. Als u echter geïnteresseerd bent in waarschuwingen, zelfs voor succesvolle taken, u [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)gebruiken.

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Kan ik geplande back-uptaken zien in het menu Back-uptaken?

In het menu Back-uptaak worden alleen ad-hoc back-uptaken weergegeven. Gebruik [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)voor geplande taken .

### <a name="are-future-databases-automatically-added-for-backup"></a>Worden toekomstige databases automatisch voor back-ups toegevoegd?

Nee, dit wordt momenteel niet ondersteund.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Als ik een database uit een instantie verwijder, wat gebeurt er dan met de back-ups?

Als een database uit een SAP HANA-exemplaar wordt verwijderd, worden de databaseback-ups nog steeds geprobeerd. Dit houdt in dat de verwijderde database begint te verschijnen als ongezond onder **Back-upitems** en is nog steeds beschermd.
De juiste manier om te stoppen met het beschermen van deze database is door Stop Backup uit te voeren **met verwijdergegevens** in deze database.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Wat is het gedrag als ik de naam van de database wijzig nadat deze is beveiligd?

Een nieuwe database wordt behandeld als een nieuwe database. Daarom zal de service deze situatie behandelen alsof de database niet is gevonden en met de back-ups mislukt. De herbenoemde database wordt weergegeven als een nieuwe database en moet zijn geconfigureerd voor bescherming.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Wat zijn de vereisten om een back-up te maken van SAP HANA-databases op een Azure VM?

Raadpleeg de [voorwaarden](tutorial-backup-sap-hana-db.md#prerequisites) en [wat het pre-registratiescript secties doet.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Welke machtigingen moeten worden ingesteld voor Azure om een back-up te kunnen maken van SAP HANA-databases?

Als u het preregistratiescript uitvoert, worden de vereiste machtigingen ingesteld om Azure in staat te stellen een back-up te maken van SAP HANA-databases. Meer informatie vindt u [hier](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)over wat het pre-registratiescript doet.

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Werken back-ups na het migreren van SAP HANA van 1,0 naar 2,0?

Raadpleeg [dit gedeelte](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) van de handleiding voor probleemoplossing.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Kan Azure HANA Backup worden ingesteld tegen een virtueel IP (load balancer) en niet tegen een virtuele machine?

Momenteel hebben we niet de mogelijkheid om de oplossing alleen tegen een virtueel IP-adres in te stellen. We hebben een virtuele machine nodig om de oplossing uit te voeren.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>Ik heb een SAP HANA System Replication (HSR), hoe moet ik back-up configureren voor deze setup?

De primaire en secundaire knooppunten van de HSR worden behandeld als twee afzonderlijke, niet-gerelateerde VM's. U moet een back-up configureren op het primaire knooppunt en wanneer de fail-over plaatsvindt, moet u een back-up configureren op het secundaire knooppunt (dat nu het primaire knooppunt wordt). Er is geen automatische 'fail-over' van back-up naar het andere knooppunt.

## <a name="restore"></a>Herstellen

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Waarom kan ik het HANA-systeem niet zien waarnaar ik mijn database wil herstellen?

Controleer of aan alle vereisten voor het herstel naar de SAP HANA-instantie is voldaan. Zie Voorwaarden voor meer informatie [: SAP HANA-databases herstellen in Azure VM](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Waarom is de Overwrite DB restore mislukt voor mijn database?

Controleer of de optie **Overschrijven van kracht** is geselecteerd tijdens het herstellen.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Waarom zie ik de fout 'Bron- en doelsystemen voor herstel zijn onverenigbaar'?

Raadpleeg de SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) om te zien welke hersteltypen momenteel worden ondersteund.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van back-ups van SAP HANA-databases](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) die worden uitgevoerd op Azure VM's.
