---
title: Veelgestelde vragen - Back-ups maken van SQL Server-databases in Azure VM's
description: Antwoorden vinden op veelgestelde vragen over het maken van back-ups van SQL Server-databases in Azure VM's met Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: a973761bf16e2d271d718e4a8b29e08624276987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247707"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Veelgestelde vragen over SQL Server-databases die worden uitgevoerd op een Azure VM-back-up

In dit artikel worden veelgestelde vragen beantwoord over het maken van back-ups van SQL Server-databases die worden uitgevoerd op virtuele Azure-machines (VM's) en de [Azure Backup-service](backup-overview.md) gebruiken.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Kan ik Azure-back-up gebruiken voor IaaS VM en SQL Server op dezelfde machine?

Ja, u zowel VM-back-up als SQL-back-up op dezelfde VM hebben. In dit geval activeren we intern volledige back-up van alleen kopiëren op de VM om de logboeken niet af te leiden.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Is de oplossing opnieuw proberen of auto-genezen van de back-ups?

Onder bepaalde omstandigheden activeert de Azure Backup-service herstelback-ups. Auto-heal kan gebeuren voor een van de zes onderstaande voorwaarden:

- Als logboek- of differentiële back-up mislukt als gevolg van LSN-validatiefout, wordt de volgende logboek- of differentiële back-up in plaats daarvan geconverteerd naar een volledige back-up.
- Als er geen volledige back-up is gebeurd voordat een logboek of differentiële back-up is, wordt die logboek- of differentiële back-up in plaats daarvan geconverteerd naar een volledige back-up.
- Als de point-in-time van de laatste volledige back-up ouder is dan 15 dagen, wordt de volgende logboek- of differentiële back-up in plaats daarvan geconverteerd naar een volledige back-up.
- Alle back-uptaken die worden geannuleerd als gevolg van een upgrade van de extensie, worden opnieuw geactiveerd nadat de upgrade is voltooid en de extensie is gestart.
- Als u ervoor kiest om de database te overschrijven tijdens Restore, mislukt de volgende logboek/differentiële back-up en wordt in plaats daarvan een volledige back-up geactiveerd.
- In gevallen waarin een volledige back-up nodig is om de logboekketens te resetten als gevolg van wijzigingen in het databaseherstelmodel, wordt een volledige automatisch geactiveerd in het volgende schema.

Automatisch genezen als een mogelijkheid is standaard ingeschakeld voor alle gebruikers; Maar in het geval u ervoor kiest om zich af te melden, voert u het onderstaand uit:

- Maak of bewerk het bestand **ExtensionSettingsOverrides.json** in de map *C:\Program Files\Azure Workload Backup\bin* in de SQL Server-instantie.
- Stel in de **extensieinstellingenOverrides.json** *{"EnableAutoHealer": false}* in.
- Sla uw wijzigingen op en sluit het bestand.
- Open **taakbeheer** in de SQL Server-instantie en start de **AzureWLBackupCoordinatorSvc-service** opnieuw.

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>Kan ik bepalen hoeveel gelijktijdige back-ups op de SQL-server worden uitgevoerd?

Ja. U de snelheid waarmee het back-upbeleid wordt uitgevoerd beperken om de impact op een SQL Server-exemplaar te minimaliseren. Ga als volgt te werk om de instelling te wijzigen:

1. Maak in de sql server-instantie in de map *C:\Program Files\Azure Workload Backup\bin* het bestand *ExtensionSettingsOverrides.json.*
2. Wijzig in het bestand *ExtensionSettingsOverrides.json* de instelling **DefaultBackupTasksThreshold** in een lagere waarde (bijvoorbeeld 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
De standaardwaarde van DefaultBackupTasksThreshold is **20**.

3. Sla uw wijzigingen op en sluit het bestand.
4. Open **Taakbeheer** op het SQL Server-exemplaar. Start de service **AzureWLBackupCoordinatorSvc** opnieuw.<br/> <br/>
 Hoewel deze methode helpt als de back-uptoepassing een grote hoeveelheid resources verbruikt, is SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) een meer algemene manier om limieten op te geven voor de hoeveelheid CPU, fysieke IO en geheugen die inkomende toepassingsaanvragen kunnen gebruiken.

> [!NOTE]
> In de UX u nog steeds doorgaan en zo veel back-ups op een bepaald moment plannen, maar ze worden verwerkt in een schuifvenster van bijvoorbeeld 5, volgens het bovenstaande voorbeeld.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Kan ik een volledige back-up van een secundaire replica uitvoeren?

Volgens SQL-beperkingen u alleen kopiëren naar volledige back-up uitvoeren op secundaire replica. Echter volledige back-up is niet toegestaan.

## <a name="can-i-protect-availability-groups-on-premises"></a>Kan ik beschikbaarheidsgroepen on-premises beveiligen?

Nee. Azure Backup beschermt SQL Server-databases die in Azure worden uitgevoerd. Als een beschikbaarheidsgroep (AG) is verspreid over Azure- en on-premises machines, kan de AG alleen worden beveiligd als de primaire replica wordt uitgevoerd in Azure. Azure Backup beschermt ook alleen de knooppunten die worden uitgevoerd in dezelfde Azure-regio als de kluis Recovery Services.

## <a name="can-i-protect-availability-groups-across-regions"></a>Kan ik beschikbaarheidsgroepen in meerdere regio's beschermen?

De kluis Azure Backup Recovery Services kan alle knooppunten detecteren en beveiligen die zich in dezelfde regio als de kluis bevinden. Als de groep SQL Server Always On meerdere Azure-regio's omvat, stelt u de back-up in vanuit de regio met het primaire knooppunt. Azure Backup kan alle databases in de beschikbaarheidsgroep detecteren en beveiligen op basis van uw back-upvoorkeur. Wanneer niet aan uw back-upvoorkeur is voldaan, mislukken back-ups en krijgt u de melding van de fout.

## <a name="do-successful-backup-jobs-create-alerts"></a>Maken succesvolle back-uptaken waarschuwingen?

Nee. Succesvolle back-uptaken maken geen waarschuwingen. Er worden alleen waarschuwingen verzonden voor mislukte back-uptaken. Gedetailleerd gedrag voor portalwaarschuwingen wordt [hier](backup-azure-monitoring-built-in-monitor.md)gedocumenteerd. Mocht u echter geïnteresseerd zijn, maar zelfs waarschuwingen voor succesvolle taken, u Monitoring gebruiken [met Azure Monitor.](backup-azure-monitoring-use-azuremonitor.md)

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Kan ik geplande back-uptaken zien in het menu Back-uptaken?

In het menu **Back-uptaak** worden alleen back-uptaken op aanvraag weergegeven. Voor gepland [taakgebruikcontrole met Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Worden toekomstige databases automatisch voor back-ups toegevoegd?

Ja, u deze mogelijkheid bereiken met [automatische beveiliging.](backup-sql-server-database-azure-vms.md#enable-auto-protection)  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Als ik een database verwijder uit een automatisch beveiligde instantie, wat gebeurt er dan met de back-ups?

Als een database wordt verwijderd uit een automatisch beveiligde instantie, worden de databaseback-ups nog steeds geprobeerd. Dit houdt in dat de verwijderde database begint te verschijnen als ongezond onder **Back-upitems** en is nog steeds beschermd.

De juiste manier om te stoppen met het beschermen van deze database is door **Stop Backup** te doen met verwijderen **van gegevens** in deze database.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Als ik stop met de back-upwerking van een automatisch beveiligde database, wat is dan het gedrag ervan?

Als u **back-ups met bewarende gegevens stopt,** vinden er geen toekomstige back-ups plaats en blijven de bestaande herstelpunten intact. De database wordt nog steeds als beschermd beschouwd en wordt weergegeven onder de **back-upitems**.

Als u **back-ups met verwijderenvan gegevens stopt,** vinden er geen toekomstige back-ups plaats en worden ook de bestaande herstelpunten verwijderd. De database wordt als niet-beveiligd beschouwd en wordt weergegeven onder de instantie in de configureerback-up. In tegenstelling tot andere up-protected databases die handmatig kunnen worden geselecteerd of die automatisch beveiligd kunnen worden, wordt deze database echter grijs weergegeven en kan deze niet worden geselecteerd. De enige manier om deze database opnieuw te beschermen, is door automatische beveiliging op de instantie uit te schakelen. U deze database nu selecteren en de beveiliging erop configureren of automatische beveiliging opnieuw inschakelen op de instantie.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Als ik de naam van de database wijzig nadat deze is beveiligd, wat is dan het gedrag?

Een nieuwe database wordt behandeld als een nieuwe database. Vandaar dat de dienst deze situatie zal behandelen alsof de database niet is gevonden en met de back-ups mislukt.

U de database selecteren, die nu een andere naam heeft gekregen en de beveiliging erop configureren. In het geval dat de automatische beveiliging is ingeschakeld op de instantie, wordt de hernoemde database automatisch gedetecteerd en beschermd.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Waarom kan ik geen toegevoegde database zien voor een automatisch beveiligd exemplaar?

Een database die u [toevoegt aan een automatisch beveiligde instantie,](backup-sql-server-database-azure-vms.md#enable-auto-protection) wordt mogelijk niet onmiddellijk weergegeven onder beveiligde items. Dat komt doordat de detectie doorgaans om de 8 uur wordt uitgevoerd. U echter nieuwe databases onmiddellijk ontdekken en beveiligen als u handmatig een detectie uitvoert door **DB's opnieuw te zoeken**, zoals in de volgende afbeelding wordt weergegeven:

  ![Een nieuw toegevoegde database handmatig ontdekken](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een back-up van een SQL Server-database](backup-azure-sql-database.md) die wordt uitgevoerd op een Azure-vm.
