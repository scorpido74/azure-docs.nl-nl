---
title: Azure Automation status configuratie rapport gegevens door sturen naar Azure Monitor-logboeken
description: In dit artikel wordt beschreven hoe u de gewenste status configuratie (DSC)-rapport gegevens van Azure Automation status configuratie naar Azure Monitor Logboeken kunt verzenden om meer inzicht en beheer te bieden.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bbc9048452c5361306dd05e712090543bb1066ce
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2020
ms.locfileid: "77111525"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Azure Automation status configuratie rapport gegevens door sturen naar Azure Monitor-logboeken

Met de configuratie van Azure Automation status worden de knooppunt status gegevens gedurende 30 dagen bewaard.
U kunt knooppunt status gegevens verzenden naar uw Log Analytics-werk ruimte als u deze gegevens gedurende een langere periode wilt bewaren.
Nalevings status is zichtbaar in de Azure Portal of met Power shell, voor knoop punten en voor afzonderlijke DSC-resources in knooppunt configuraties.
Met Azure Monitor-Logboeken kunt u het volgende doen:

- Compatibiliteits informatie ophalen voor beheerde knoop punten en individuele resources
- Een e-mail of waarschuwing activeren op basis van de nalevings status
- Geavanceerde query's schrijven op uw beheerde knoop punten
- Nalevings status voor alle Automation-accounts correleren
- De nalevings geschiedenis van het knoop punt in de loop van de tijd visualiseren

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de configuratie Rapporten van de Automation-status te verzenden naar Azure Monitor-logboeken:

- De release van [Azure PowerShell](/powershell/azure/overview) november 2016 of hoger (v 2.3.0).
- Een Azure Automation-account. Zie [aan de slag met Azure Automation](automation-offering-get-started.md) voor meer informatie.
- Een Log Analytics-werk ruimte met een **automation & Control** service-aanbieding. Zie [aan de slag met Azure monitor-logboeken](../log-analytics/log-analytics-get-started.md)voor meer informatie.
- Ten minste één configuratie knooppunt voor Azure Automation status. Zie voor meer informatie [onboarding machines voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md)
- De [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) -module, versie 2.7.0.0 of hoger. Zie [DSC-logboeken weer geven op uw knoop punt](./troubleshoot/desired-state-configuration.md#steps-to-troubleshoot-desired-state-configuration-dsc)voor installatie stappen.

## <a name="set-up-integration-with-azure-monitor-logs"></a>Integratie met Azure Monitor-logboeken instellen

Voer de volgende stappen uit om te beginnen met het importeren van gegevens uit Azure Automation DSC in Azure Monitor logboeken:

1. Meld u aan bij uw Azure-account in Power shell. Zie [Aanmelden met Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
1. Haal de _ResourceID_ van uw Automation-account op door de volgende Power shell-opdracht uit te voeren: (als u meer dan één Automation-account hebt, kiest u het _ResourceID_ voor het account dat u wilt configureren).

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Haal de _ResourceID_ van uw log Analytics-werk ruimte op door de volgende Power shell-opdracht uit te voeren: (als u meer dan één werk ruimte hebt, kiest u de _ResourceID_ voor de werk ruimte die u wilt configureren).

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Voer de volgende Power shell-opdracht uit en vervang `<AutomationResourceId>` en `<WorkspaceResourceId>` door de _ResourceID_ -waarden uit elk van de vorige stappen:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

Voer de volgende Power shell-opdracht uit als u wilt stoppen met het importeren van gegevens van Azure Automation status configuratie in Azure Monitor logboeken:

```powershell
Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>De status configuratie logboeken weer geven

Nadat u de integratie met Azure Monitor Logboeken hebt ingesteld voor de configuratie gegevens van uw automatisering, kunnen ze worden weer gegeven door **Logboeken** te selecteren in het gedeelte **bewaking** in het linkerdeel venster van de pagina status configuratie (DSC).  

![Logboeken](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

De Blade **Zoeken in Logboeken** wordt geopend en u ziet een **DscNodeStatusData** -bewerking voor elk status configuratie knooppunt en een **DscResourceStatusData** -bewerking voor elke [DSC-resource](/powershell/scripting/dsc/resources/resources) met de naam in de knooppunt configuratie die op het knoop punt wordt toegepast.

De bewerking **DscResourceStatusData** bevat fout gegevens voor eventuele DSC-resources die zijn mislukt.

Klik op elke bewerking in de lijst om de gegevens voor die bewerking weer te geven.

U kunt de logboeken ook weer geven door in Azure Monitor logboeken te zoeken. Zie [gegevens zoeken met Zoek opdrachten in Logboeken](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview). Typ de volgende query om de logboeken voor de status configuratie te vinden.

```
AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Een e-mail verzenden wanneer een controle op naleving van de status configuratie mislukt

Een van de belangrijkste aanvragen van klanten is de mogelijkheid om een e-mail bericht of een tekst te verzenden wanneer er iets mis gaat met een DSC-configuratie.

Als u een waarschuwings regel wilt maken, moet u beginnen met het maken van een zoek opdracht in het logboek voor de status configuratie rapport records die de waarschuwing moeten aanroepen. Klik op de knop **+ nieuwe waarschuwings regel** om de waarschuwings regel te maken en te configureren.

1. Klik op de pagina overzicht van Log Analytics werk ruimte op **Logboeken**.
1. Maak een zoek opdracht in het logboek voor uw waarschuwing door de volgende zoek opdracht in het query veld in te voeren: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Als u logboeken van meer dan één Automation-account of abonnement op uw werk ruimte hebt ingesteld, kunt u uw waarschuwingen groeperen op abonnement en Automation-account.
   De naam van het Automation-account kan worden afgeleid van het veld Resource in de zoek opdracht van DscNodeStatusData.
1. Als u het scherm **regel maken** wilt openen, klikt u boven aan de pagina op **nieuwe waarschuwings regel** . Zie [een waarschuwings regel maken](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)voor meer informatie over de opties voor het configureren van de waarschuwing.

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Niet-werkende DSC-resources zoeken op alle knoop punten

Een voor deel van het gebruik van Azure Monitor-Logboeken is dat u kunt zoeken naar mislukte controles op verschillende knoop punten.
Om te zoeken naar alle exemplaren van DSC-resources die zijn mislukt.

1. Klik op de pagina overzicht van Log Analytics werk ruimte op **Logboeken**.
1. Maak een zoek opdracht in het logboek voor uw waarschuwing door de volgende zoek opdracht in het query veld in te voeren: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Historische DSC-knooppunt status weer geven

Ten slotte kunt u de geschiedenis van de DSC-knooppunt status gedurende een periode visualiseren.
U kunt deze query gebruiken om de status van de DSC-knooppunt status na verloop van tijd te zoeken.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Hiermee wordt een grafiek weer gegeven met de status van het knoop punt gedurende een bepaalde periode.

## <a name="azure-monitor-logs-records"></a>Azure Monitor registreert records

Diagnostische gegevens van Azure Automation twee categorieën records maken in Azure Monitor Logboeken.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |De datum en het tijdstip waarop de controle op naleving is uitgevoerd. |
| OperationName |DscNodeStatusData |
| ResultType |Hiermee wordt aangegeven of het knoop punt compatibel is. |
| NodeName_s |De naam van het beheerde knoop punt. |
| NodeComplianceStatus_s |Hiermee wordt aangegeven of het knoop punt compatibel is. |
| DscReportStatus |Of de nalevings controle is uitgevoerd. |
| ConfigurationMode | Hoe de configuratie wordt toegepast op het knoop punt. Mogelijke waarden zijn __"ApplyOnly"__ , __"ApplyandMonitior"__ en __"ApplyandAutoCorrect"__ . <ul><li>__ApplyOnly__: DSC past de configuratie toe en doet niets verder tenzij een nieuwe configuratie wordt gepusht naar het doel knooppunt of wanneer een nieuwe configuratie wordt opgehaald van een server. Na de eerste toepassing van een nieuwe configuratie controleert DSC niet op een eerder geconfigureerde status. DSC probeert de configuratie toe te passen totdat deze is voltooid voordat __ApplyOnly__ van kracht worden. </li><li> __ApplyAndMonitor__: dit is de standaard waarde. De LCM past nieuwe configuraties toe. Als er na de eerste toepassing van een nieuwe configuratie het doel knooppunt van de gewenste status is, wordt de discrepantie in de logboeken door DSC gerapporteerd. DSC probeert de configuratie toe te passen totdat deze is voltooid voordat __ApplyAndMonitor__ van kracht worden.</li><li>__ApplyAndAutoCorrect__: DSC past nieuwe configuraties toe. Als er na de eerste toepassing van een nieuwe configuratie het doel knooppunt van de gewenste status is, wordt de discrepantie in de logboeken door DSC gerapporteerd en wordt de huidige configuratie opnieuw toegepast.</li></ul> |
| HostName_s | De naam van het beheerde knoop punt. |
| IP-adres | Het IPv4-adres van het beheerde knoop punt. |
| Category | DscNodeStatus |
| Resource | De naam van het Azure Automation-account. |
| Tenant_g | GUID waarmee de Tenant voor de oproepende functie wordt geïdentificeerd. |
| NodeId_g |GUID waarmee het beheerde knoop punt wordt aangeduid. |
| DscReportId_g |GUID waarmee het rapport wordt geïdentificeerd. |
| LastSeenTime_t |De datum en het tijdstip waarop het rapport het laatst is bekeken. |
| ReportStartTime_t |De datum en tijd waarop het rapport is gestart. |
| ReportEndTime_t |De datum en tijd waarop het rapport is voltooid. |
| NumberOfResources_d |Het aantal DSC-resources dat wordt aangeroepen in de configuratie die wordt toegepast op het knoop punt. |
| SourceSystem | Hoe Azure Monitor Logboeken de gegevens verzamelen. Altijd *Azure* voor Azure Diagnostics. |
| ResourceId |Hiermee geeft u het Azure Automation-account. |
| ResultDescription | De beschrijving voor deze bewerking. |
| SubscriptionId | De ID (GUID) van het Azure-abonnement voor het Automation-account. |
| ResourceGroup | De naam van de resource groep voor het Automation-account. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID die de correlatie-ID van het nalevings rapport is. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |De datum en het tijdstip waarop de controle op naleving is uitgevoerd. |
| OperationName |DscResourceStatusData|
| ResultType |Hiermee wordt aangegeven of de resource compatibel is. |
| NodeName_s |De naam van het beheerde knoop punt. |
| Category | DscNodeStatus |
| Resource | De naam van het Azure Automation-account. |
| Tenant_g | GUID waarmee de Tenant voor de oproepende functie wordt geïdentificeerd. |
| NodeId_g |GUID waarmee het beheerde knoop punt wordt aangeduid. |
| DscReportId_g |GUID waarmee het rapport wordt geïdentificeerd. |
| DscResourceId_s |De naam van het DSC-resource-exemplaar. |
| DscResourceName_s |De naam van de DSC-resource. |
| DscResourceStatus_s |Hiermee wordt aangegeven of de DSC-resource compatibel is. |
| DscModuleName_s |De naam van de Power shell-module die de DSC-resource bevat. |
| DscModuleVersion_s |De versie van de Power shell-module die de DSC-resource bevat. |
| DscConfigurationName_s |De naam van de configuratie die op het knoop punt wordt toegepast. |
| ErrorCode_s | De fout code als de resource is mislukt. |
| ErrorMessage_s |Het fout bericht als de bron is mislukt. |
| DscResourceDuration_d |De tijd, in seconden, die de DSC-resource heeft uitgevoerd. |
| SourceSystem | Hoe Azure Monitor Logboeken de gegevens verzamelen. Altijd *Azure* voor Azure Diagnostics. |
| ResourceId |Hiermee geeft u het Azure Automation-account. |
| ResultDescription | De beschrijving voor deze bewerking. |
| SubscriptionId | De ID (GUID) van het Azure-abonnement voor het Automation-account. |
| ResourceGroup | De naam van de resource groep voor het Automation-account. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID die de correlatie-ID van het nalevings rapport is. |

## <a name="summary"></a>Samenvatting

Als u de configuratie gegevens voor de automatiserings status naar Azure Monitor-logboeken verzendt, kunt u beter inzicht krijgen in de status van uw configuratie knooppunten voor de automatiserings status.

- Waarschuwingen instellen om u op de hoogte te stellen wanneer er een probleem is
- Met aangepaste weer gaven en zoek query's kunt u de runbook-resultaten, de status van de runbook-taak en andere gerelateerde sleutel indicatoren of metrische gegevens visualiseren.

Azure Monitor-Logboeken biedt meer operationele zicht baarheid van de configuratie gegevens van de automatiserings status en kunnen incidenten sneller aanpakken.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Automation State Configuration](automation-dsc-overview.md) (Engelstalig) voor een overzicht
- Zie aan de slag [met de configuratie van de Azure Automation-status](automation-dsc-getting-started.md) om aan de slag te gaan.
- Zie [configuraties compileren in azure Automation status configuratie](automation-dsc-compile.md) voor meer informatie over het compileren van DSC-configuraties zodat u ze aan doel knooppunten kunt toewijzen.
- Zie [Azure Automation status configuratie-cmdlets](/powershell/module/azurerm.automation/#automation) voor informatie over de Power shell-cmdlet.
- Zie [prijzen voor Azure Automation status configuratie](https://azure.microsoft.com/pricing/details/automation/) voor prijs informatie.
- Voor een voor beeld van het gebruik van Azure Automation status configuratie in een pijp lijn voor continue implementatie gaat u naar [continue implementatie met behulp van Azure Automation-status configuratie en chocolade](automation-dsc-cd-chocolatey.md)
- Zie [Zoek opdrachten in Logboeken in azure monitor logboeken](../log-analytics/log-analytics-log-searches.md) voor meer informatie over het maken van verschillende Zoek query's en het controleren van de configuratie logboeken voor de automatiserings status met Azure monitor Logboeken.
- Zie [Azure Storage-gegevens verzamelen in azure monitor logs Overview](../azure-monitor/platform/collect-azure-metrics-logs.md) voor meer informatie over Azure monitor logboeken en bronnen voor gegevens verzameling.
