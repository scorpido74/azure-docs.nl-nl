---
title: Azure Backup bewaken met Azure Monitor
description: Controleer Azure Backup-workloads en maak aangepaste waarschuwingen met Azure Monitor.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 547cef66be9902468f4e2755c31e5f586eccad5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459511"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Op schaal controleren met Azure Monitor

Azure Backup biedt [ingebouwde bewakings- en waarschuwingsmogelijkheden](backup-azure-monitoring-built-in-monitor.md) in een vault van Recovery Services. Deze mogelijkheden zijn beschikbaar zonder extra beheerinfrastructuur. Maar deze ingebouwde service is beperkt in de volgende scenario's:

- Als u gegevens van meerdere Vaults voor Herstelservices controleert op verschillende abonnementen
- Als het voorkeursmeldingskanaal *geen* e-mail is
- Als gebruikers waarschuwingen willen voor meer scenario's
- Als u informatie wilt weergeven van een on-premises component, zoals System Center Data Protection Manager in Azure, die de portal niet weergeeft in [**Back-uptaken**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) of [**back-upwaarschuwingen**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Werkruimte Log Analytics gebruiken

### <a name="create-alerts-by-using-log-analytics"></a>Waarschuwingen maken met Log Analytics

In Azure Monitor u uw eigen waarschuwingen maken in een werkruimte log Analytics. In de werkruimte gebruikt u *Azure-actiegroepen* om het gewenste meldingsmechanisme te selecteren.

> [!IMPORTANT]
> Zie [Azure Monitor-prijzen](https://azure.microsoft.com/pricing/details/monitor/)voor informatie over de kosten voor het maken van deze query.

Open de sectie **Logboeken** van de werkruimte Log Analytics en maak een query voor uw eigen logboeken. Wanneer u **Nieuwe waarschuwingsregel**selecteert, wordt de pagina voor het maken van waarschuwingen voor Azure Monitor geopend, zoals in de volgende afbeelding wordt weergegeven.

![Een waarschuwing maken in een werkruimte Log Analytics](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Hier is de bron al gemarkeerd als de werkruimte Log Analytics en wordt de integratie van de actiegroep verstrekt.

![De pagina Logboekanalyse-waarschuwingen](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Waarschuwingsvoorwaarde

Het bepalende kenmerk van een waarschuwing is de triggering conditie. Selecteer **Voorwaarde** om de Kusto-query automatisch te laden op de pagina **Logboeken** zoals weergegeven in de volgende afbeelding. Hier u de voorwaarde aanpassen aan uw behoeften. Zie [Voorbeeld van Kusto-query's voor](#sample-kusto-queries)meer informatie.

![Een waarschuwingsvoorwaarde instellen](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Indien nodig u de Kusto-query bewerken. Kies een drempelwaarde, periode en frequentie. De drempelwaarde bepaalt wanneer de waarschuwing wordt verhoogd. De periode is het tijdsvenster waarin de query wordt uitgevoerd. Als de drempelwaarde bijvoorbeeld groter is dan 0, de periode 5 minuten is en de frequentie 5 minuten, wordt de query elke 5 minuten uitgevoerd, waarbij de vorige 5 minuten worden gecontroleerd. Als het aantal resultaten groter is dan 0, wordt u hiervan op de hoogte gesteld via de geselecteerde actiegroep.

#### <a name="alert-action-groups"></a>Actiegroepen waarschuwen

Gebruik een actiegroep om een meldingskanaal op te geven. Als u de beschikbare meldingsmechanismen wilt bekijken, selecteert u onder **Actiegroepen**De optie **Nieuw maken**.

![Beschikbare meldingsmechanismen in het venster Actiegroep toevoegen](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

U voldoen aan alle vereisten voor waarschuwingen en monitoring van Log Analytics alleen, of u Log Analytics gebruiken om ingebouwde meldingen aan te vullen.

Zie [Logboekwaarschuwingen maken, weergeven en beheren met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) en [Actiegroepen maken en beheren in de Azure-portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)voor meer informatie.

### <a name="sample-kusto-queries"></a>Voorbeeld van Kusto-query's

De standaardgrafieken geven u Kusto-query's voor basisscenario's waarop u waarschuwingen maken. U ook de query's wijzigen om de gegevens te krijgen waarop u wilt worden gewaarschuwd. Plak het volgende voorbeeld Kusto-query's op de pagina **Logboeken** en maak vervolgens waarschuwingen voor de query's:

- Alle succesvolle back-uptaken

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    ````

- Alle mislukte back-uptaken

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Failed"
    ````

- Alle succesvolle Azure VM-back-uptaken

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Alle succesvolle SQL-logboekback-uptaken

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Alle succesvolle Azure Backup-agenttaken

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Back-upopslag verbruikt per back-upitem

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId 
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs 
    | sort by StorageConsumedInMBs desc
    ````


### <a name="diagnostic-data-update-frequency"></a>Frequentie voor het bijwerken van diagnostische gegevens

De diagnostische gegevens uit de kluis worden met enige vertraging naar de werkruimte Log Analytics gepompt. Elk evenement arriveert in de Log Analytics-werkruimte *20 tot 30 minuten* nadat het uit de kluis van Recovery Services is geschoven. Hier zijn meer details over de vertraging:

- Voor alle oplossingen worden de ingebouwde waarschuwingen van de back-upservice gepusht zodra ze zijn gemaakt. Ze worden dus meestal na 20 tot 30 minuten weergegeven in de werkruimte Log Analytics.
- Over alle oplossingen worden on-demand back-uptaken en hersteltaken gepusht zodra ze *klaar zijn.*
- Voor alle oplossingen behalve SQL-back-up worden geplande back-uptaken gepusht zodra ze *zijn voltooid.*
- Voor SQL-back-ups, omdat logboekbackups elke 15 minuten kunnen optreden, wordt informatie voor alle voltooide geplande back-uptaken, inclusief logboeken, elke 6 uur gebatcht en gepusht.
- Over alle oplossingen wordt andere informatie, zoals het back-upitem, het beleid, herstelpunten, opslag, enzovoort, ten minste *eenmaal per dag* gepusht.
- Een wijziging in de back-upconfiguratie (zoals het wijzigen van beleid of bewerkingsbeleid) activeert een push van alle gerelateerde back-upgegevens.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>De activiteitslogboeken van de vault Recovery Services gebruiken

> [!CAUTION]
> De volgende stappen zijn alleen van toepassing op *Azure VM-back-ups.* U deze stappen niet gebruiken voor oplossingen zoals de Azure Backup-agent, SQL-back-ups binnen Azure of Azure Files.

U ook activiteitslogboeken gebruiken om meldingen te krijgen voor gebeurtenissen zoals back-upsucces. Voer om te beginnen de volgende stappen uit:

1. Meld u aan bij de Azure-portal.
1. Open de kluis van de desbetreffende Recovery Services.
1. Open de sectie **Activiteitslogboek** in de eigenschappen van de kluis.

Ga als het gaat om het juiste logboek te identificeren en een waarschuwing te maken:

1. Controleer of u activiteitslogboeken ontvangt voor succesvolle back-ups door de filters toe te passen die in de volgende afbeelding worden weergegeven. Wijzig indien nodig de **tijdspannewaarde** om records weer te geven.

   ![Filteren om activiteitslogboeken voor Azure VM-back-ups te zoeken](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Selecteer de bedrijfsnaam om de relevante details te bekijken.
1. Selecteer **Nieuwe waarschuwingsregel** om de pagina **Regel maken** te openen.
1. Maak een waarschuwing door de stappen te volgen in [Waarschuwingen voor activiteitenlogboeken maken, weergeven en beheren met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Nieuwe waarschuwingsregel](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Hier is de bron de Vault Recovery Services zelf. Herhaal dezelfde stappen voor alle kluizen waarin u via activiteitslogboeken op de hoogte wilt worden gesteld. De voorwaarde heeft geen drempelwaarde, periode of frequentie omdat deze waarschuwing is gebaseerd op gebeurtenissen. Zodra het relevante activiteitenlogboek wordt gegenereerd, wordt de waarschuwing verhoogd.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Log Analytics gebruiken om op schaal te controleren

U alle waarschuwingen bekijken die zijn gemaakt van activiteitslogboeken en Logboekanalysewerkruimten in Azure Monitor. Open het deelvenster **Waarschuwingen** aan de linkerkant.

Hoewel u meldingen ontvangen via activiteitslogboeken, raden we u ten zeerste aan log-analyses te gebruiken in plaats van activiteitslogboeken voor monitoring op schaal. Waarom?

- **Beperkte scenario's:** Meldingen via activiteitslogboeken zijn alleen van toepassing op Azure VM-back-ups. De meldingen moeten worden ingesteld voor elke vault van Recovery Services.
- **Definitiefit:** de geplande back-upactiviteit past niet bij de nieuwste definitie van activiteitslogboeken. In plaats daarvan wordt het uitgelijnd met [resourcelogboeken.](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace) Deze uitlijning veroorzaakt onverwachte effecten wanneer de gegevens die door het activiteitslogboekkanaal stromen, worden gewijzigd.
- **Problemen met het activiteitenlogboekkanaal**: In vaults van Recovery Services volgen activiteitslogboeken die vanuit Azure Backup worden gepompt een nieuw model. Helaas is deze wijziging van invloed op het genereren van activiteitslogboeken in Azure Government, Azure Germany en Azure China 21Vianet. Als gebruikers van deze cloudservices waarschuwingen maken of configureren op activiteitslogboeken in Azure Monitor, worden de waarschuwingen niet geactiveerd. Als een gebruiker in alle openbare Azure-regio's [logboeken verzamelt in een werkruimte Voor Log Analytics,](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)worden deze logboeken niet weergegeven.

Gebruik een Log Analytics-werkruimte voor het bewaken en waarschuwen op schaal voor al uw workloads die zijn beschermd door Azure Backup.

## <a name="next-steps"></a>Volgende stappen

Zie [Gegevensmodel Log Analytics](backup-azure-reports-data-model.md)voor het maken van aangepaste query's.
