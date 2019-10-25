---
title: 'Azure Backup: Azure Backup met Azure Monitor controleren'
description: Bewaak Azure Backup werk belastingen en maak aangepaste waarschuwingen met behulp van Azure Monitor.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: Log Analytics; Azure Backup; Berichten Diagnostische instellingen; Actie groepen
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: dacurwin
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 2b951c6660143b1bd2f6502a5441aec3ba8d71e1
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792844"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Op schaal controleren met behulp van Azure Monitor

Azure Backup biedt [ingebouwde mogelijkheden voor bewaking en waarschuwingen](backup-azure-monitoring-built-in-monitor.md) in een Recovery Services kluis. Deze mogelijkheden zijn beschikbaar zonder enige extra beheer infrastructuur. Maar deze ingebouwde service is beperkt in de volgende scenario's:

- Als u gegevens van meerdere Recovery Services-kluizen in abonnementen bewaakt
- Als het voorkeurs meldings kanaal *geen* e-mail adres is
- Als gebruikers waarschuwingen voor meer scenario's willen
- Als u informatie wilt weer geven van een on-premises onderdeel, zoals System Center Data Protection Manager in azure, dat niet wordt weer gegeven in [**back-uptaken**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) of [**back-upwaarschuwingen**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) van de portal

## <a name="using-log-analytics-workspace"></a>Log Analytics werkruimte gebruiken

> [!NOTE]
> Gegevens van back-ups van virtuele Azure-machines, de Azure Backup-Agent, System Center Data Protection Manager, SQL-back-ups in azure Vm's en Azure Files share back-ups worden gepompt naar de Log Analytics-werk ruimte via Diagnostische instellingen. Ondersteuning voor Microsoft Azure Backup-Server (MABS) wordt binnenkort toegevoegd

Als u op schaal wilt bewaken/rapporteren, hebt u de mogelijkheden van twee Azure-Services nodig. *Diagnostische instellingen* verzenden gegevens van meerdere Azure Resource Manager resources naar een andere resource. *Log Analytics* genereert aangepaste waarschuwingen waarbij u actie groepen kunt gebruiken om andere meldings kanalen te definiëren.

In de volgende secties wordt beschreven hoe u Log Analytics kunt gebruiken om Azure Backup op schaal te bewaken.

### <a name="configure-diagnostic-settings"></a>Diagnostische instellingen configureren

Azure Resource Manager resources, zoals de Recovery Services kluis, registreert u informatie over geplande bewerkingen en door de gebruiker geactiveerde bewerkingen als diagnostische gegevens.

Selecteer **Diagnostische instellingen** in het gedeelte bewaking en geef het doel op voor de diagnostische gegevens van de Recovery Services kluis.

![De diagnostische instelling van de Recovery Services kluis, gericht op Log Analytics](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

U kunt een Log Analytics-werk ruimte richten op een ander abonnement. Als u de kluizen op één locatie wilt bewaken, selecteert u dezelfde Log Analytics werk ruimte voor meerdere Recovery Services kluizen. Als u alle informatie die betrekking heeft op Azure Backup in de Log Analytics-werk ruimte, wilt kanaal, selecteert u **AzureBackupReport** als het logboek.

> [!IMPORTANT]
> Nadat u de configuratie hebt voltooid, moet u 24 uur wachten totdat de eerste gegevens push is voltooid. Na deze eerste gegevens push worden alle gebeurtenissen gepusht, zoals verderop in dit artikel wordt beschreven, in de [sectie frequentie](#diagnostic-data-update-frequency).

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Een oplossing implementeren voor de Log Analytics-werk ruimte

> [!IMPORTANT]
> We hebben een bijgewerkte [sjabloon](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) voor meerdere weer gaven uitgebracht voor bewaking en rapportage op basis van LA in azure backup. Gebruikers die de [eerdere oplossing](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) gebruiken, blijven deze in hun werk ruimten zien, zelfs na het implementeren van de nieuwe oplossing. De oude oplossing kan echter onnauwkeurige resultaten opleveren als gevolg van een aantal kleine wijzigingen in het schema. Gebruikers zijn daarom vereist voor het implementeren van de nieuwe sjabloon.

Nadat de gegevens zich in de Log Analytics-werk ruimte bevinden, [implementeert u een github-sjabloon](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) voor log Analytics om de gegevens te visualiseren. Zorg ervoor dat u dezelfde resource groep, werkruimte naam en werkruimte locatie geeft om de werk ruimte goed te kunnen identificeren. Installeer vervolgens deze sjabloon in de werk ruimte.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Azure Backup gegevens weer geven met behulp van Log Analytics

Nadat de sjabloon is geïmplementeerd, wordt de oplossing voor bewaking en rapportage in Azure Backup weer gegeven in de samenvattings regio van de werk ruimte. Als u naar de samen vatting wilt gaan, volgt u een van deze paden:

- **Azure monitor**: Selecteer in de sectie **inzichten** **meer** en kies vervolgens de relevante werk ruimte.
- **Log Analytics werk ruimten**: Selecteer de relevante werk ruimte en selecteer vervolgens **samen vatting van werk ruimte**onder **Algemeen**.

![De tegels Log Analytics bewaking en rapportage](media/backup-azure-monitoring-laworkspace/la-azurebackup-overview-dashboard.png)

Wanneer u een van de overzichts tegels selecteert, kunt u meer informatie weer geven. Hier volgen enkele van de rapporten die u ziet:

- Niet-logboek back-uptaken

   ![Grafieken Log Analytics voor back-uptaken](media/backup-azure-monitoring-laworkspace/la-azurebackup-backupjobsnonlog.png)

- Waarschuwingen van back-ups van Azure-resources

   ![Log Analytics Graph voor het herstellen van taken](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertsazure.png)

Op dezelfde manier kunt u op de andere tegels rapporten bekijken over herstel taken, Cloud opslag, back-upitems, waarschuwingen van on-premises bronnen en back-uptaken.

Deze grafieken worden bij de sjabloon meegeleverd. Als dat nodig is, kunt u de grafieken bewerken of meer grafieken toevoegen.

### <a name="create-alerts-by-using-log-analytics"></a>Waarschuwingen maken met behulp van Log Analytics

In Azure Monitor kunt u uw eigen waarschuwingen maken in een Log Analytics-werk ruimte. In de werk ruimte gebruikt u *Azure-actie groepen* om uw voorkeurs meldings mechanisme te selecteren.

> [!IMPORTANT]
> Zie voor meer informatie over de kosten voor het maken van deze query [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/).

Selecteer een van de grafieken om de sectie **Logboeken** van de log Analytics-werk ruimte te openen. Bewerk de query's in de sectie **logs** en maak waarschuwingen.

![Een waarschuwing maken in een Log Analytics-werk ruimte](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Wanneer u **nieuwe waarschuwings regel**selecteert, wordt de pagina Azure monitor waarschuwings-maken geopend, zoals wordt weer gegeven in de volgende afbeelding. Hier is de resource al gemarkeerd als de Log Analytics-werk ruimte en de integratie van de actie groep.

![De pagina Log Analytics waarschuwing: maken](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Waarschuwings voorwaarde

Het definiëren van het kenmerk van een waarschuwing is de trigger voorwaarde. Selecteer **voor waarde** om de Kusto-query automatisch te laden op de pagina **Logboeken** , zoals wordt weer gegeven in de volgende afbeelding. Hier kunt u de voor waarde aan uw behoeften aanpassen. Zie voor [beelden van Kusto-query's](#sample-kusto-queries)voor meer informatie.

![Een waarschuwings voorwaarde instellen](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Indien nodig kunt u de Kusto-query bewerken. Kies een drempel waarde, punt en frequentie. De drempel waarde bepaalt wanneer de waarschuwing wordt gegenereerd. De periode is het tijd venster waarin de query wordt uitgevoerd. Als de drempel waarde bijvoorbeeld groter is dan 0, de periode 5 minuten is en de frequentie 5 minuten is, voert de regel de query elke vijf minuten uit, waarna de vorige 5 minuten wordt gecontroleerd. Als het aantal resultaten groter is dan 0, ontvangt u een melding via de geselecteerde actie groep.

#### <a name="alert-action-groups"></a>Waarschuwings actie groepen

Een actie groep gebruiken om een meldings kanaal op te geven. Voor een overzicht van de beschik bare meldings mechanismen onder **actie groepen**, selecteert u **nieuwe maken**.

![Beschik bare meldings mechanismen in het venster actie groep toevoegen](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

U kunt aan alle vereisten voor waarschuwingen en bewaking voldoen via alleen Log Analytics, of u kunt Log Analytics gebruiken om ingebouwde meldingen aan te vullen.

Zie [logboek waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) en [actie groepen maken en beheren in de Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)voor meer informatie.

### <a name="sample-kusto-queries"></a>Voor beeld van Kusto-query's

De standaard grafieken bieden u Kusto query's voor basis scenario's waarop u waarschuwingen kunt bouwen. U kunt ook de query's wijzigen om de gegevens op te halen waarvoor u waarschuwingen wilt ontvangen. Plak de volgende voor beelden van Kusto-query's op de **Logboeken** pagina en maak waarschuwingen op de query's:

- Alle geslaagde back-uptaken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Alle mislukte back-uptaken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Alle voltooide Azure VM-back-uptaken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Alle voltooide SQL-logboek back-uptaken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Alle geslaagde Azure Backup Agent taken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

### <a name="diagnostic-data-update-frequency"></a>Update frequentie van diagnostische gegevens

De diagnostische gegevens van de kluis worden naar de Log Analytics-werk ruimte gepompt met enige vertraging. Elke gebeurtenis arriveert in de Log Analytics werk ruimte van *20 tot 30 minuten* nadat deze is gepusht vanuit de Recovery Services kluis. Hier vindt u meer informatie over de vertraging:

- In alle oplossingen worden de ingebouwde waarschuwingen van de back-upservice gepusht zodra ze zijn gemaakt. Deze worden doorgaans weer gegeven in de Log Analytics-werk ruimte na 20 tot 30 minuten.
- In alle oplossingen worden ad hoc-back-uptaken en herstel taken gepusht zodra deze zijn *voltooid*.
- Voor alle oplossingen behalve SQL backup worden geplande back-uptaken gepusht zodra deze zijn *voltooid*.
- Voor SQL backup, omdat logboek back-ups om de 15 minuten kunnen worden uitgevoerd, worden de gegevens voor alle voltooide geplande back-uptaken, inclusief logboeken, batches en elke 6 uur gepusht.
- Voor alle oplossingen worden andere gegevens, zoals het back-upitem, het beleid, de herstel punten, de opslag, enzovoort, ten minste *één keer per dag* gepusht.
- Een wijziging in de back-upconfiguratie (zoals het wijzigen van beleid of het bewerken van beleid) activeert een push van alle gerelateerde back-upgegevens.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>De activiteiten logboeken van Recovery Services kluis gebruiken

> [!CAUTION]
> De volgende stappen zijn alleen van toepassing op *back-ups van Azure-vm's.* U kunt deze stappen niet gebruiken voor oplossingen zoals de Azure Backup-Agent, SQL-back-ups in azure of Azure Files.

U kunt ook activiteiten Logboeken gebruiken om een melding te ontvangen voor gebeurtenissen zoals het maken van een back-up. Voer de volgende stappen uit om te beginnen:

1. Meld u aan bij de Azure Portal.
1. Open de relevante Recovery Services kluis.
1. Open in de eigenschappen van de kluis de sectie **activiteiten logboek** .

Het juiste logboek identificeren en een waarschuwing maken:

1. Controleer of u activiteiten logboeken ontvangt voor geslaagde back-ups door de filters toe te passen die in de volgende afbeelding worden weer gegeven. Wijzig indien nodig de **time span** -waarde om records weer te geven.

   ![Filteren om activiteiten logboeken te zoeken voor back-ups van Azure-VM'S](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Selecteer de naam van de bewerking om de relevante gegevens weer te geven.
1. Selecteer **nieuwe waarschuwings regel** om de pagina **regel maken** te openen.
1. Maak een waarschuwing door de stappen te volgen in [waarschuwingen voor activiteiten logboek maken, weer geven en beheren met behulp van Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Nieuwe waarschuwings regel](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Hier is de resource de Recovery Services kluis zelf. Herhaal dezelfde stappen voor alle kluizen waarin u wilt worden gewaarschuwd via activiteiten Logboeken. De voor waarde heeft geen drempel waarde, punt of frequentie omdat deze waarschuwing is gebaseerd op gebeurtenissen. Zodra het relevante activiteiten logboek wordt gegenereerd, wordt de waarschuwing geactiveerd.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Bewaken met behulp van Log Analytics op schaal

U kunt alle waarschuwingen weer geven die zijn gemaakt op basis van activiteiten logboeken en Log Analytics werk ruimten in Azure Monitor. Open het deel venster **waarschuwingen** aan de linkerkant.

Hoewel u meldingen kunt ontvangen via activiteiten logboeken, raden we u ten zeerste aan gebruik te maken van Log Analytics in plaats van activiteiten logboeken voor bewaking op schaal. Dit is de reden:

- **Beperkte scenario's**: meldingen via activiteiten logboeken zijn alleen van toepassing op back-ups van Azure-vm's. De meldingen moeten worden ingesteld voor elke Recovery Services kluis.
- **Passende definitie**: de geplande back-upactiviteit past niet op de nieuwste definitie van activiteiten Logboeken. In plaats daarvan wordt de [resource-logboeken](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-resource-logs-in-a-workspace)uitgelijnd. Deze uitlijning veroorzaakt onverwachte effecten wanneer de gegevens die door het activiteiten logboek kanaal worden gewijzigd.
- **Problemen met het activiteiten logboek kanaal**: in Recovery Services kluizen worden activiteiten logboeken die zijn gepompt van Azure Backup een nieuw model volgen. Helaas heeft deze wijziging gevolgen voor het genereren van activiteiten Logboeken in Azure Government, Azure Duitsland en Azure China 21Vianet. Als gebruikers van deze Cloud Services waarschuwingen van activiteiten Logboeken in Azure Monitor maken of configureren, worden de waarschuwingen niet geactiveerd. Als een gebruiker in alle open bare Azure-regio's ook [Recovery Services-activiteiten Logboeken in een log Analytics werkruimte verzamelt](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), worden deze logboeken niet weer gegeven.

Gebruik een Log Analytics-werk ruimte voor bewaking en waarschuwingen op schaal voor al uw workloads die worden beveiligd door Azure Backup.

## <a name="next-steps"></a>Volgende stappen

Zie [log Analytics gegevens model](backup-azure-log-analytics-data-model.md)voor het maken van aangepaste query's.
