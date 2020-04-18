---
title: Diagnostische instellingen gebruiken voor vaults van Recovery Services
description: In dit artikel wordt beschreven hoe u de oude en nieuwe diagnostische gebeurtenissen voor Azure Backup gebruiken.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: f11c9d2a5b48b9cd27ac6e6f8a00eb5ac0ac7a9d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617311"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Diagnostische instellingen gebruiken voor vaults van Recovery Services

Azure Backup verzendt diagnostische gebeurtenissen die kunnen worden verzameld en gebruikt voor analyse, waarschuwing en rapportage.

U diagnostische instellingen voor een Vault recovery services configureren via de Azure-portal door naar de kluis te gaan en **Diagnostische instellingen**te selecteren. Als u diagnostische instelling selecteren + **Toevoegen,** u een of meer diagnostische gebeurtenissen verzenden naar een opslagaccount, een gebeurtenishub of een werkruimte voor Logboekanalyse.

![Het deelvenster Diagnostische instellingen](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Diagnostische gebeurtenissen beschikbaar voor Azure Backup-gebruikers

Azure Backup biedt de volgende diagnostische gebeurtenissen. Elke gebeurtenis biedt gedetailleerde gegevens over een specifieke set back-upgerelateerde artefacten:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

Zie [Gegevensmodel voor Azure Backup-diagnostische gebeurtenissen voor](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)meer informatie .

Gegevens voor deze gebeurtenissen kunnen worden verzonden naar een opslagaccount, een Log Analytics-werkruimte of een gebeurtenishub. Als u deze gegevens naar een werkruimte Log Analytics verzendt, selecteert u de **functie Resourcespecifiek** in het scherm **Diagnostische instellingen.** Zie de volgende secties voor meer informatie.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Diagnostische instellingen gebruiken met Log Analytics

U Azure Backup nu gebruiken om gegevens over kluisdiagnoses te verzenden naar speciale Log Analytics-tabellen voor back-ups. Deze tabellen worden [resourcespecifieke tabellen](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)genoemd.

Ga als uw vault diagnostics-gegevens naar Log Analytics:

1. Ga naar uw kluis en selecteer **Diagnostische instellingen**. Selecteer **+ Diagnostische instelling toevoegen**.
1. Geef een naam aan de diagnose-instelling.
1. Schakel het selectievakje **Verzenden naar logboekanalyse** in en schakel een werkruimte Log Analytics in.
1. Selecteer **Resourcespecifiek** in de schakelknop en selecteer de volgende zes gebeurtenissen: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage**en **AddonAzureBackupProtectedInstance**.
1. Selecteer **Opslaan**.

   ![Resourcespecifieke modus](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Nadat gegevens zijn binnengekomen in de werkruimte Log Analytics, worden er speciale tabellen voor elk van deze gebeurtenissen gemaakt in uw werkruimte. U een van deze tabellen rechtstreeks opvragen. U indien nodig ook joins of vakbonden tussen deze tabellen uitvoeren.

> [!IMPORTANT]
> De zes gebeurtenissen, namelijk CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage en AddonAzureBackupProtectedInstance, worden *alleen* ondersteund in de resourcespecifieke modus in [Back-uprapporten.](https://docs.microsoft.com/azure/backup/configure-reports) *Als u gegevens probeert te verzenden voor deze zes gebeurtenissen in de azure-diagnostische modus, zijn er geen gegevens zichtbaar in back-uprapporten.*

## <a name="legacy-event"></a>Legacy-gebeurtenis

Traditioneel werden alle back-upgerelateerde diagnostische gegevens voor een kluis opgenomen in één gebeurtenis, AzureBackupReport genaamd. De zes gebeurtenissen die hier worden beschreven, zijn in wezen een ontleding van alle gegevens in AzureBackupReport. 

Momenteel blijven we de gebeurtenis AzureBackupReport ondersteunen voor achterwaartse compatibiliteit in gevallen waarin gebruikers bestaande aangepaste query's op deze gebeurtenis hebben. Voorbeelden zijn aangepaste logboekwaarschuwingen en aangepaste visualisaties. *We raden u aan zo vroeg mogelijk naar de [nieuwe evenementen](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) te gaan.* De nieuwe evenementen:

- Maak de gegevens veel gemakkelijker om mee te werken in logquery's.
- Zorg voor een betere vindbaarheid van schema's en hun structuur.
- Verbeter de prestaties voor zowel de innamelatentie als de querytijden. 

*De verouderde gebeurtenis in de Azure-diagnostische modus wordt uiteindelijk afgeschaft. Als u de nieuwe gebeurtenissen kiest, u complexe migraties op een later tijdstip voorkomen.* Onze [rapportageoplossing](https://docs.microsoft.com/azure/backup/configure-reports) die Gebruikmaakt van Log Analytics, zal ook stoppen met het ondersteunen van gegevens van de legacy-gebeurtenis.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Stappen om over te gaan naar nieuwe diagnostische instellingen voor een Log Analytics-werkruimte

1. Bepaal welke kluizen gegevens verzenden naar de Log Analytics-werkruimten met behulp van de legacy-gebeurtenis en de abonnementen waartoe ze behoren. Voer de volgende werkruimten uit om deze kluizen en abonnementen te identificeren.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId    
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault" 
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, hence you need to use isFuzzy
    let CombinedVaultTable = (){
        CombinedTable | union isfuzzy = true 
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti ( 
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

1. Gebruik het [ingebouwde Azure-beleid](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) in Azure Backup om een nieuwe diagnostische instelling toe te voegen voor alle kluizen in een opgegeven bereik. Met dit beleid wordt een nieuwe diagnostische instelling toegevoegd aan kluizen die geen diagnostische instelling hebben of alleen een verouderde diagnostische instelling hebben. Dit beleid kan worden toegewezen aan een volledig abonnement of resourcegroep tegelijk. U moet eigenaar toegang hebben tot elk abonnement waarvoor het beleid is toegewezen.

U ervoor kiezen om afzonderlijke diagnostische instellingen voor AzureBackupReport en de zes nieuwe gebeurtenissen te hebben totdat u al uw aangepaste query's hebt gemigreerd om gegevens uit de nieuwe tabellen te gebruiken. In de volgende afbeelding ziet u een voorbeeld van een kluis met twee diagnostische instellingen. De eerste instelling, met de naam **Instelling1,** verzendt gegevens van een Gebeurtenis AzureBackupReport naar een Log Analytics-werkruimte in de diagnosemodus Azure. De tweede instelling, **setting2**genaamd, verzendt gegevens van de zes nieuwe Azure Backup-gebeurtenissen naar een log-analysewerkruimte in de resourcespecifieke modus.

![Twee instellingen](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> De gebeurtenis AzureBackupReport wordt *alleen* ondersteund in de diagnosemodus azure. *Als u gegevens voor deze gebeurtenis probeert te verzenden in de resourcespecifieke modus, worden er geen gegevens naar de werkruimte Log Analytics verzonden.*

> [!NOTE]
> De optie voor **Azure-diagnose** of **Resourcespecifiek** wordt alleen weergegeven als de gebruiker **Verzenden naar logboekanalyse**selecteert. Als u gegevens naar een opslagaccount of een gebeurtenishub wilt verzenden, selecteert een gebruiker de vereiste bestemming en selecteert hij de selectievakjes voor een van de gewenste gebeurtenissen, zonder extra invoer. Nogmaals, we raden u aan niet de verouderde gebeurtenis AzureBackupReport in de toekomst te kiezen.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Azure Site Recovery-gebeurtenissen verzenden naar Logboekanalyse

Azure Backup- en Azure Site Recovery-gebeurtenissen worden verzonden vanuit dezelfde vault van Recovery Services. Azure Site Recovery is momenteel niet beschikbaar voor resourcespecifieke tabellen. Gebruikers die Azure Site Recovery-gebeurtenissen naar Log Analytics willen *verzenden,* worden alleen door de diagnostische modus van Azure gebruikt, zoals in de afbeelding wordt weergegeven. *Als u de resourcespecifieke modus voor Azure Site Recovery-gebeurtenissen kiest, worden de vereiste gegevens niet naar de werkruimte Log Analytics verzonden.*

![Gebeurtenissen voor siteherstel](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Samenvattend:

* Als u al logboekanalysediagnostiek hebt ingesteld met Azure Diagnostics en er aangepaste query's op hebt geschreven, houdt u die instelling *intact* totdat u uw query's migreert om gegevens uit de nieuwe gebeurtenissen te gebruiken.
* Als u ook aan boord wilt gaan naar nieuwe tabellen, maakt u, maakt u, maakt u een **nieuwe** diagnose-instelling, selecteert **u Resourcespecifiek**en selecteert u de zes nieuwe gebeurtenissen.
* Als u momenteel Azure Site Recovery-gebeurtenissen verzendt naar Log Analytics, kiest *u niet* de resourcespecifieke modus voor deze gebeurtenissen. Anders worden gegevens voor deze gebeurtenissen niet naar uw werkruimte Log Analytics verzonden. Maak in plaats daarvan een aanvullende diagnostische instelling, selecteer **Azure-diagnose**en selecteer de relevante Azure Site Recovery-gebeurtenissen.

In de volgende afbeelding ziet u een voorbeeld van een gebruiker die drie diagnose-instellingen voor een kluis heeft. De eerste instelling, met de naam **Instelling1,** verzendt gegevens van een gebeurtenis AzureBackupReport naar een Log Analytics-werkruimte in de diagnosemodus Azure. De tweede instelling, **setting2**genaamd, verzendt gegevens van de zes nieuwe Azure Backup-gebeurtenissen naar een log-analysewerkruimte in de resourcespecifieke modus. De derde instelling, **setting3**genaamd, verzendt gegevens van de Azure Site Recovery-gebeurtenissen naar een Log Analytics-werkruimte in de diagnosemodus Azure.

![Drie instellingen](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het gegevensmodel Log Analytics voor de diagnostische gebeurtenissen](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
