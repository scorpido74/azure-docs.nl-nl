---
title: Azure Automation State Configuration reporting data doorsturen naar Azure Monitor-logboeken
description: In dit artikel wordt uitgelegd hoe u DSC-rapportagegegevens (Desired State Configuration) van Azure Automation State Configuration naar Azure Monitor-logboeken verzendt om extra inzicht en beheer te bieden.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: badd8ba676ef25c33a5034bb04d616faeb4ef1b0
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392102"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Azure Automation State Configuration reporting data doorsturen naar Azure Monitor-logboeken

Azure Automation State Configuration bewaart knooppuntstatusgegevens gedurende 30 dagen. U knooppuntstatusgegevens naar uw Log Analytics-werkruimte verzenden als u deze gegevens liever voor een langere periode wilt bewaren. De nalevingsstatus is zichtbaar in de Azure-portal of met PowerShell, voor knooppunten en voor afzonderlijke DSC-resources in knooppuntconfiguraties. 

Azure Monitor-logboeken bieden een grotere operationele zichtbaarheid van uw configuratiegegevens van de automatiseringsstatus en kunnen incidenten sneller oplossen. Met Azure Monitor-logboeken u:

- Ontvang nalevingsgegevens voor beheerde knooppunten en afzonderlijke bronnen.
- Activeer een e-mail of waarschuwing op basis van de nalevingsstatus.
- Schrijf geavanceerde query's over uw beheerde knooppunten.
- Correleer de nalevingsstatus voor automatiseringsaccounts.
- Gebruik aangepaste weergaven en zoekopdrachten om uw runbook-resultaten, runbook-taakstatus en andere gerelateerde belangrijke indicatoren of statistieken te visualiseren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Vereisten

Als u wilt beginnen met het verzenden van uw configuratierapporten voor configuratie van automatiseringsstatus naar Azure Monitor-logboeken, moet u het gewenste aantal

- De release van [Azure PowerShell](/powershell/azure/overview) in november 2016 of later (v2.3.0).
- Een Azure Automation-account. Zie [Een inleiding tot Azure Automation](automation-intro.md)voor meer informatie.
- Een Log Analytics-werkruimte met een serviceaanbod voor &- &- en sturing. Zie [Aan de slag met Logboekanalyse in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)voor meer informatie.
- Ten minste één configuratieknooppunt azure-automatiseringsstatus. Zie [Onboarding-machines voor beheer door Azure Automation State Configuration voor](automation-dsc-onboarding.md)meer informatie.
- De [xDscDiagnostics-module,](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) versie 2.7.0.0 of hoger. Zie [Problemen met de gewenste statusconfiguratie azure automation oplossen](./troubleshoot/desired-state-configuration.md)voor installatiestappen .

## <a name="set-up-integration-with-azure-monitor-logs"></a>Integratie instellen met Azure Monitor-logboeken

Voer de volgende stappen uit om gegevens uit azure automatiseringsstatusconfiguratie te importeren in Azure Monitor-logboeken:

1. Meld u aan bij uw Azure-account in PowerShell. Zie [Aanmelden met Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
1. Haal de resource-id van uw Automatiseringsaccount op door de volgende PowerShell-cmdlet uit te voeren. Als u meer dan één automatiseringsaccount hebt, kiest u de resource-id voor het account dat u wilt configureren.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Haal de resource-id van uw Log Analytics-werkruimte op door de volgende PowerShell-cmdlet uit te voeren. Als u meer dan één werkruimte hebt, kiest u de resource-id voor de werkruimte die u wilt configureren.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Voer de volgende PowerShell-cmdlet `<WorkspaceResourceId>` uit, waarbij u de `ResourceId` waarden uit elk van de vorige stappen vervangt `<AutomationResourceId>` en met deze waarden plaatst.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Als u wilt stoppen met het importeren van gegevens uit Azure Automation State Configuration in Azure Monitor logs, voert u de volgende PowerShell-cmdlet uit.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>De statusconfiguratielogboeken weergeven

Nadat u de integratie met Azure Monitor-logboeken hebt ingesteld voor uw configuratiegegevens van de automatiseringsstatus, u deze weergeven door **logboeken** te selecteren in de sectie **Controle** in het linkerdeelvenster van de pagina Statusconfiguratie (DSC).

![Logboeken](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Het deelvenster Logboekzoeken wordt geopend met een querygebied dat is afgestemd op uw automatiseringsaccountbron. U zoeken in de statusconfiguratielogboeken voor DSC-bewerkingen door te zoeken in Azure Monitor-logboeken. De records voor DSC-bewerkingen worden opgeslagen in de `AzureDiagnostics` tabel. Als u bijvoorbeeld knooppunten wilt zoeken die niet voldoen, typt u de volgende query.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Filterdetails:

* Filter `DscNodeStatusData` op om bewerkingen voor elk statusconfiguratieknooppunt terug te sturen.
* Filter `DscResourceStatusData` op retourbewerkingen voor elke DSC-bron die wordt aangeroepen in de knooppuntconfiguratie die op die bron wordt toegepast. 
* Filter `DscResourceStatusData` op om foutgegevens terug te sturen voor alle DSC-bronnen die mislukken.

Zie [Overzicht van logboekquery's in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)voor meer informatie over het maken van logboekquery's om gegevens te vinden.

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Een e-mail verzenden wanneer een nalevingscontrole voor de configuratie van de status mislukt

Een van onze belangrijkste verzoeken van klanten is voor de mogelijkheid om een e-mail of een sms te sturen wanneer er iets misgaat met een DSC-configuratie.

Als u een waarschuwingsregel wilt maken, maakt u eerst een logboekzoekopdracht voor de rapporten van het configuratierapport status die de waarschuwing moeten aanroepen. Klik op de knop **Nieuwe waarschuwingsregel** om de waarschuwingsregel te maken en te configureren.

1. Klik op de pagina Overzicht van de werkruimte Log Analytics op **Logboeken**.
1. Maak een logboekzoekopdracht voor uw waarschuwing door de volgende zoekopdracht in het queryveld te typen:`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Als u logboeken hebt ingesteld van meer dan één Automatiseringsaccount of een abonnement op uw werkruimte, u uw waarschuwingen groeperen op abonnements- en automatiseringsaccount. De naam van het `Resource` automatiseringsaccount afleiden `DscNodeStatusData` van het veld bij het zoeken naar de records.
1. Als u het scherm **Regel maken wilt** openen, klikt u boven aan de pagina op Nieuwe **waarschuwingsregel.** 

Zie [Een waarschuwingsregel maken](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)voor meer informatie over de opties om de waarschuwing te configureren.

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Mislukte DSC-resources zoeken voor alle knooppunten

Een voordeel van het gebruik van Azure Monitor-logboeken is dat u zoeken naar mislukte controles tussen knooppunten. Ga als u op zoek naar alle exemplaren van DSC-resources die zijn mislukt:

1. Klik op de pagina Overzicht van de werkruimte Log Analytics op **Logboeken**.
1. Maak een logboekzoekopdracht voor uw waarschuwing door de volgende zoekopdracht in het queryveld te typen:`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Historische DSC-knooppuntstatus weergeven

Als u de statusgeschiedenis van uw DSC-knooppunt in de loop van de tijd wilt visualiseren, u deze query gebruiken:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Met deze query wordt een grafiek met de status van het knooppunt in de loop van de tijd weergegeven.

## <a name="azure-monitor-logs-records"></a>Azure Monitor registreert records

Azure Automation-diagnostiek maakt twee categorieën records in Azure Monitor-logboeken:

* Gegevens over de`DscNodeStatusData`status van knooppunt ( )
* Bronstatusgegevens`DscResourceStatusData`( )

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |Datum en tijd waarop de nalevingscontrole is uitgevoerd. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Waarde die aangeeft of het knooppunt voldoet. |
| NodeName_s |De naam van het beheerde knooppunt. |
| NodeComplianceStatus_s |Statuswaarde die aangeeft of het knooppunt voldoet. |
| DscReportStatus |Statuswaarde die aangeeft of de nalevingscontrole is uitgevoerd. |
| Configuratiemodus | De modus die wordt gebruikt om de configuratie op het knooppunt toe te passen. Mogelijke waarden zijn: <ul><li>`ApplyOnly`: DSC past de configuratie toe en doet niets verder tenzij een nieuwe configuratie naar het doelknooppunt wordt geduwd of wanneer een nieuwe configuratie van een server wordt getrokken. Na de eerste toepassing van een nieuwe configuratie controleert DSC niet op afwijking van een eerder geconfigureerde status. DSC probeert de configuratie toe te `ApplyOnly` passen totdat deze succesvol is voordat de waarde van kracht wordt. </li><li>`ApplyAndMonitor`: Dit is de standaardwaarde. De LCM past nieuwe configuraties toe. Na de eerste toepassing van een nieuwe configuratie, als het doelknooppunt afdrijft van de gewenste status, rapporteert DSC de discrepantie in logboeken. DSC probeert de configuratie toe te `ApplyAndMonitor` passen totdat deze succesvol is voordat de waarde van kracht wordt.</li><li>`ApplyAndAutoCorrect`: DSC past nieuwe configuraties toe. Na de eerste toepassing van een nieuwe configuratie, als het doelknooppunt afdrijft van de gewenste status, rapporteert DSC de discrepantie in logboeken en past de huidige configuratie opnieuw toe.</li></ul> |
| HostName_s | De naam van het beheerde knooppunt. |
| IPAddress | Het IPv4-adres van het beheerde knooppunt. |
| Categorie | `DscNodeStatus`. |
| Resource | De naam van het Azure Automation-account. |
| Tenant_g | GUID die de tenant voor de beller identificeert. |
| NodeId_g | GUID die het beheerde knooppunt identificeert. |
| DscReportId_g | GUID die het rapport identificeert. |
| LastSeenTime_t | Datum en tijd waarop het rapport voor het laatst is bekeken. |
| ReportStartTime_t | Datum en tijd waarop het rapport is gestart. |
| ReportEndTime_t | Datum en tijd waarop het rapport is voltooid. |
| NumberOfResources_d | Het aantal DSC-resources dat is aangeroepen in de configuratie die op het knooppunt wordt toegepast. |
| SourceSystem | Het bronsysteem dat identificeert hoe Azure Monitor de gegevens heeft verzameld. Altijd `Azure` voor Azure-diagnostiek. |
| ResourceId |De resource-id van het Azure Automation-account. |
| ResultaatBeschrijving | De resourcebeschrijving voor deze bewerking. |
| SubscriptionId | De Azure-abonnements-ID (GUID) voor het automatiseringsaccount. |
| ResourceGroup | De naam van de resourcegroep voor het account Automatisering. |
| ResourceProvider | Microsoft. Automatisering. |
| ResourceType | AUTOMATISERINGSREKENINGEN. |
| CorrelationId | Een GUID die de correlatie-id is van het nalevingsrapport. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |Datum en tijd waarop de nalevingscontrole is uitgevoerd. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Of de resource voldoet. |
| NodeName_s |De naam van het beheerde knooppunt. |
| Categorie | DscNodeStatus. |
| Resource | De naam van het Azure Automation-account. |
| Tenant_g | GUID die de tenant voor de beller identificeert. |
| NodeId_g |GUID die het beheerde knooppunt identificeert. |
| DscReportId_g |GUID die het rapport identificeert. |
| DscResourceId_s |De naam van de instantie DSC-resource. |
| DscResourceName_s |De naam van de DSC-bron. |
| DscResourceStatus_s |Of de DSC-bron in overeenstemming is. |
| DscModuleName_s |De naam van de PowerShell-module die de DSC-bron bevat. |
| DscModuleVersion_s |De versie van de PowerShell-module die de DSC-bron bevat. |
| DscConfigurationName_s |De naam van de configuratie die op het knooppunt wordt toegepast. |
| ErrorCode_s | De foutcode als de bron is mislukt. |
| ErrorMessage_s |Het foutbericht als de resource is mislukt. |
| DscResourceDuration_d |De tijd, in seconden, dat de DSC bron liep. |
| SourceSystem | Hoe Azure Monitor-logboeken de gegevens verzamelden. Altijd `Azure` voor Azure-diagnostiek. |
| ResourceId |De id van het Azure Automation-account. |
| ResultaatBeschrijving | De beschrijving voor deze bewerking. |
| SubscriptionId | De Azure-abonnements-ID (GUID) voor het automatiseringsaccount. |
| ResourceGroup | De naam van de resourcegroep voor het account Automatisering. |
| ResourceProvider | Microsoft. Automatisering. |
| ResourceType | AUTOMATISERINGSREKENINGEN. |
| CorrelationId |GUID dat is de correlatie-ID van het nalevingsrapport. |


## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Automation State Configuration](automation-dsc-overview.md)voor een overzicht.
- Zie [Aan de slag met Azure Automation State Configuration](automation-dsc-getting-started.md)om aan de slag te gaan.
- Zie [Configuraties compileren in Azure Automation State Configuration](automation-dsc-compile.md)voor meer informatie over het samenstellen van DSC-configuraties, zodat u ze toewijzen aan doelknooppunten.
- Zie [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)voor een PowerShell-cmdletreferentie.
- Zie Azure Automation State Configuration Pricing voor prijsinformatie voor [prijsinformatie.](https://azure.microsoft.com/pricing/details/automation/)
- Zie [Continue implementatie met Azure Automation State Configuration en Chocolatey](automation-dsc-cd-chocolatey.md)voor een voorbeeld van het gebruik van Azure Automation State Configuration in a continuous deployment pipeline.
- Zie [Logboekzoekopdrachten in Azure Monitor-logboeken](../log-analytics/log-analytics-log-searches.md)voor meer informatie over het maken van verschillende zoekopdrachten en het controleren van de configuratielogboeken van automatiseringsstatus met Azure Monitor-logboeken.
- Zie [Azure-opslaggegevens verzamelen in azure monitor-logboekenoverzicht](../azure-monitor/platform/collect-azure-metrics-logs.md)voor meer informatie over Azure Monitor-logboeken en gegevensverzamelingsbronnen.
