---
title: Log Analytics-werk ruimten beheren in Azure Monitor | Microsoft Docs
description: U kunt de toegang beheren tot gegevens die zijn opgeslagen in een Log Analytics werk ruimte in Azure Monitor met behulp van resource, werk ruimte of machtiging op tabel niveau. In dit artikel wordt beschreven hoe u deze kunt volt ooien.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: magoedte
ms.openlocfilehash: 920e470a8bc06050219d0f603ab842cfc267e6ce
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695010"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Toegang tot logboek gegevens en-werk ruimten in Azure Monitor beheren

Azure Monitor worden [logboek](data-platform-logs.md) gegevens opgeslagen in een log Analytics-werk ruimte, wat in feite een container is die gegevens-en configuratie gegevens bevat. Als u de toegang tot logboek gegevens wilt beheren, voert u verschillende beheer taken uit die betrekking hebben op uw werk ruimte.

In dit artikel wordt uitgelegd hoe u de toegang tot logboeken beheert en hoe u de werk ruimten die ze bevatten, kunt beheren, waaronder:

* Toegang verlenen aan gebruikers die toegang nodig hebben tot logboek gegevens van specifieke bronnen met behulp van Azure op rollen gebaseerd toegangs beheer (RBAC).

* Toegang tot de werk ruimte verlenen met behulp van werkruimte machtigingen.

* Toegang verlenen aan gebruikers die toegang nodig hebben tot logboek gegevens in een specifieke tabel in de werk ruimte met behulp van Azure RBAC.

## <a name="configure-access-control-mode"></a>Toegangs beheer modus configureren

U kunt de toegangs beheer modus die is geconfigureerd op een werk ruimte weer geven vanuit de Azure Portal of met Azure PowerShell.  U kunt deze instelling wijzigen met een van de volgende ondersteunde methoden:

* Azure Portal

* Azure PowerShell

* Azure Resource Manager-sjabloon

### <a name="from-the-azure-portal"></a>van de Azure Portal

U kunt de huidige toegangs beheer modus voor de werk ruimte weer geven op de pagina **overzicht** van de werk ruimte in het menu van de **log Analytics werk ruimte** .

![Toegangs beheer modus voor werk ruimte weer geven](media/manage-access/view-access-control-mode.png)

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
1. Selecteer in de Azure Portal Log Analytics werk ruimten > uw werk ruimte.

U kunt deze instelling wijzigen op de pagina **Eigenschappen** van de werk ruimte. Het wijzigen van de instelling wordt uitgeschakeld als u geen machtigingen hebt om de werk ruimte te configureren.

![Toegangs modus voor werk ruimte wijzigen](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>PowerShell gebruiken

Gebruik de volgende opdracht om de toegangs beheer modus voor alle werk ruimten in het abonnement te controleren:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

De uitvoer moet er als volgt uitzien:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Een waarde van `False` betekent dat de werk ruimte is geconfigureerd met de toegangs modus voor de werk ruimte-context.  Een waarde van `True` betekent dat de werk ruimte is geconfigureerd met de toegangs modus voor de resource context.

> [!NOTE]
> Als een werk ruimte wordt geretourneerd zonder een Booleaanse waarde en deze leeg is, komt dit ook overeen met de resultaten van een `False`-waarde.
>

Gebruik het volgende script om de toegangs beheer modus voor een specifieke werk ruimte in te stellen op de machtiging resource-context:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Gebruik het volgende script om de toegangs beheer modus voor alle werk ruimten in het abonnement in te stellen op de resource-context machtiging:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="using-a-resource-manager-template"></a>Een resource manager-sjabloon gebruiken

Als u de toegangs modus in een Azure Resource Manager sjabloon wilt configureren, stelt u de **enableLogAccessUsingOnlyResourcePermissions** -functie vlag in de werk ruimte in op een van de volgende waarden.

* **Onwaar**: Stel de werk ruimte in op de werk ruimte-context machtigingen. Dit is de standaard instelling als de vlag niet is ingesteld.
* **waar**: Stel de werk ruimte in op resource-context machtigingen.

## <a name="manage-access-using-workspace-permissions"></a>Toegang beheren met werkruimte machtigingen

Elke werkruimte kunnen meerdere accounts worden gekoppeld, en elk account kan toegang hebben tot meerdere werkruimten. Toegang wordt beheerd met behulp [van Azure op rollen gebaseerde toegang](../../role-based-access-control/role-assignments-portal.md).

Voor de volgende activiteiten zijn ook Azure-machtigingen vereist:

|Bewerking |Azure-machtigingen nodig |Opmerkingen |
|-------|-------------------------|------|
| Bewakings oplossingen toevoegen en verwijderen | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Deze machtigingen moeten worden toegekend op het niveau van de resourcegroep of het abonnement. |
| De prijscategorie wijzigen | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Gegevens weergeven op de tegels *Back-up* en *Site Recovery* | Beheerder/medebeheerder | Heeft toegang tot resources die zijn geïmplementeerd met behulp van het klassieke implementatiemodel |
| Een werkruimte maken in Azure Portal | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Basis eigenschappen van werk ruimte weer geven en de Blade werk ruimte in de portal invoeren | `Microsoft.OperationalInsights/workspaces/read` ||
| Query logboeken met een wille keurige interface | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Toegang tot alle logboek typen met behulp van query's | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Toegang tot een specifieke logboek tabel | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| De werkruimte sleutels voor het verzenden van logboeken naar deze werk ruimte lezen | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Toegang beheren met Azure-machtigingen

Volg de stappen in [Roltoewijzingen gebruiken voor het beheer van de toegang tot de resources van uw Azure-abonnement](../../role-based-access-control/role-assignments-portal.md) om toegang te verlenen tot de Log Analytics-werkruimte met behulp van Azure-machtigingen. Voor beeld van aangepaste rollen, Zie [voor beelden van aangepaste rollen](#custom-role-examples)

Azure heeft twee ingebouwde gebruikers rollen voor Log Analytics-werk ruimten:

* Lezer van Log Analytics
* Inzender van Log Analytics

Leden van de rol *Lezer van Log Analytics* kunnen:

* Alle controlegegevens weergeven en doorzoeken
* Controlegegevens weergeven, inclusief de configuratie van Azure Diagnostics voor alle Azure-resources.

De rol Lezer van Log Analytics bevat de volgende Azure acties:

| Type    | Machtiging | Description |
| ------- | ---------- | ----------- |
| Bewerking | `*/read`   | De mogelijkheid om alle Azure-resources en resourceconfiguratie weer te geven. Omvat: <br> Status van de VM-extensie <br> Configuratie van Azure Diagnostics voor resources <br> Alle eigenschappen en instellingen van alle resources. <br> Voor werk ruimten kunnen volledige onbeperkte machtigingen de werk ruimte-instellingen lezen en query's uitvoeren op de gegevens. Bekijk meer gedetailleerde opties hierboven. |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Afgeschaft, u hoeft ze niet aan gebruikers toe te wijzen. |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | Afgeschaft, u hoeft ze niet aan gebruikers toe te wijzen. |
| Action | `Microsoft.Support/*` | Mogelijkheid ondersteuningsaanvragen te openen |
|Geen bewerking | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Hiermee voorkomt u dat het lezen van de werkruimte sleutel die nodig is om te gebruiken van de gegevensverzameling-API en om agents te installeren. Hiermee voorkomt u dat de gebruiker van het toevoegen van nieuwe resources in de werkruimte |

Leden van de rol *Inzender van Log Analytics* kunnen:

* Alle controlegegevens lezen als lezer van Log Analytics
* Automation-accounts maken en configureren
* Beheeroplossingen toevoegen en verwijderen

    > [!NOTE]
    > Wilt u is de laatste twee acties uitvoeren, moet deze machtiging worden verleend op het niveau van de resource-groep of abonnement.

* Opslagaccountsleutels lezen
* Verzameling met logboeken uit Azure Storage configureren
* De controle-instellingen voor Azure-resources bewerken, inclusief
  * De VM-extensie toevoegen aan virtuele machines
  * Azure Diagnostics configureren op alle Azure-resources

> [!NOTE]
> U kunt de mogelijkheid om een VM-extensie toe te voegen aan een virtuele machine, gebruiken om volledige controle over een virtuele machine te krijgen.

De rol Inzender van Log Analytics bevat de volgende Azure acties:

| Machtiging | Description |
| ---------- | ----------- |
| `*/read`     | De mogelijkheid om alle resources en de resourceconfiguratie weer te geven. Omvat: <br> Status van de VM-extensie <br> Configuratie van Azure Diagnostics voor resources <br> Alle eigenschappen en instellingen van alle resources. <br> Voor werk ruimten kunnen volledige onbeperkte machtigingen de werk ruimte-instelling lezen en query's uitvoeren op de gegevens. Bekijk meer gedetailleerde opties hierboven. |
| `Microsoft.Automation/automationAccounts/*` | De mogelijkheid om Azure Automation-accounts te maken en te configureren, inclusief het toevoegen en bewerken van runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | VM-extensies toevoegen, bijwerken en verwijderen, met inbegrip van de Microsoft Monitoring Agent-extensie en de OMS Agent for Linux-extensie |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Geef de opslagaccountsleutel weer. Vereist om Log Analytics te configureren om logboeken uit Azure-opslagaccounts te lezen |
| `Microsoft.Insights/alertRules/*` | Waarschuwingsregels toevoegen, bijwerken en verwijderen |
| `Microsoft.Insights/diagnosticSettings/*` | Diagnostische instellingen bij Azure-resources toevoegen, bijwerken en verwijderen |
| `Microsoft.OperationalInsights/*` | De configuratie voor Log Analytics-werk ruimten toevoegen, bijwerken en verwijderen. Voor het bewerken van geavanceerde instellingen voor de werk ruimte moet de gebruiker `Microsoft.OperationalInsights/workspaces/write` hebben. |
| `Microsoft.OperationsManagement/*` | Beheeroplossingen toevoegen en verwijderen |
| `Microsoft.Resources/deployments/*` | Maak en verwijder implementaties. Vereist voor het toevoegen en verwijderen van oplossingen, werkruimten en Automation-accounts |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Maak en verwijder implementaties. Vereist voor het toevoegen en verwijderen van oplossingen, werkruimten en Automation-accounts |

Als u gebruikers wilt toevoegen aan en verwijderen uit een gebruikersrol, moet u beschikken over machtigingen voor `Microsoft.Authorization/*/Delete` en `Microsoft.Authorization/*/Write`.

Gebruik deze rollen om gebruikers toegang te geven op verschillende niveaus:

* Abonnement: toegang tot alle werkruimten in het abonnement
* Resourcegroep: toegang tot alle werkruimten in de resourcegroep
* Resource: alleen toegang tot de opgegeven werkruimte

U moet toewijzingen op het niveau van de resource (werk ruimte) uitvoeren om nauw keurig toegangs beheer te garanderen.  Gebruik [aangepaste rollen](../../role-based-access-control/custom-roles.md) om rollen te maken met de specifieke machtigingen die nodig zijn.

### <a name="resource-permissions"></a>Resource machtigingen

Wanneer gebruikers een query uitvoeren op Logboeken vanuit een werk ruimte met behulp van resource-context toegang, hebben ze de volgende machtigingen voor de resource:

| Machtiging | Description |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Voorbeelden:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | De mogelijkheid om alle logboek gegevens voor de resource weer te geven.  |
| `Microsoft.Insights/diagnosticSettings/write` | De mogelijkheid om Diagnostische instellingen te configureren om Logboeken in te stellen voor deze bron. |

de machtiging `/read` wordt doorgaans verleend vanuit een rol met _\*/Lees-of_ _\*-_ machtigingen, zoals de ingebouwde functie voor [lezer](../../role-based-access-control/built-in-roles.md#reader) en [Inzender](../../role-based-access-control/built-in-roles.md#contributor) . Houd er rekening mee dat aangepaste rollen die specifieke acties of speciale ingebouwde rollen bevatten, deze machtiging mogelijk niet bevatten.

Zie [definiëren per-tabel toegangs beheer](#table-level-rbac) hieronder als u een ander toegangs beheer voor verschillende tabellen wilt maken.

## <a name="custom-role-examples"></a>Voor beelden van aangepaste rollen

1. Als u een gebruiker toegang wilt geven tot logboek gegevens vanuit hun resources, voert u de volgende handelingen uit:

    * De toegangs beheer modus voor de werk ruimte configureren voor het **gebruik van werk ruimte-of resource machtigingen**

    * Gebruikers `*/read` of `Microsoft.Insights/logs/*/read`-machtigingen verlenen aan hun resources. Als ze al de rol van [log Analytics lezer](../../role-based-access-control/built-in-roles.md#reader) in de werk ruimte zijn toegewezen, is het voldoende.

2. Als u een gebruiker toegang wilt geven tot logboek gegevens van hun resources en de resources wilt configureren voor het verzenden van logboeken naar de werk ruimte, voert u de volgende handelingen uit:

    * De toegangs beheer modus voor de werk ruimte configureren voor het **gebruik van werk ruimte-of resource machtigingen**

    * Gebruikers de volgende machtigingen verlenen voor de werk ruimte: `Microsoft.OperationalInsights/workspaces/read` en `Microsoft.OperationalInsights/workspaces/sharedKeys/action`. Met deze machtigingen kunnen gebruikers geen query's op werkruimte niveau uitvoeren. Ze kunnen de werk ruimte alleen inventariseren en gebruiken als doel voor Diagnostische instellingen of de configuratie van de agent.

    * Ken gebruikers de volgende machtigingen toe aan hun resources: `Microsoft.Insights/logs/*/read` en `Microsoft.Insights/diagnosticSettings/write`. Als aan de gebruiker [log Analytics](../../role-based-access-control/built-in-roles.md#contributor) de rol van rol van lezer is toegewezen, of `*/read` machtigingen aan deze resource is verleend, is deze voldoende.

3. Als u een gebruiker toegang wilt geven tot logboek gegevens vanuit hun resources zonder dat er beveiligings gebeurtenissen kunnen worden gelezen en gegevens worden verzonden, voert u de volgende handelingen uit:

    * De toegangs beheer modus voor de werk ruimte configureren voor het **gebruik van werk ruimte-of resource machtigingen**

    * Ken gebruikers de volgende machtigingen toe aan hun resources: `Microsoft.Insights/logs/*/read`.

    * Voeg de volgende niet-actie toe om te voor komen dat gebruikers het type SecurityEvent lezen: `Microsoft.Insights/logs/SecurityEvent/read`. De niet-actie moet in dezelfde aangepaste rol worden uitgevoerd als de actie die de Lees machtiging biedt (`Microsoft.Insights/logs/*/read`). Als de gebruiker de actie lezen inherent van een andere rol die is toegewezen aan deze resource of aan het abonnement of de resource groep, kunnen ze alle logboek typen lezen. Dit geldt ook als ze `*/read` overnemen, bijvoorbeeld met de rol lezer of Inzender.

4. Als u een gebruiker toegang wilt geven tot logboek gegevens van hun resources en alle Azure AD-aanmelding wilt lezen en Updatebeheer gegevens van het oplossings logboek van de werk ruimte wilt lezen, voert u de volgende handelingen uit:

    * De toegangs beheer modus voor de werk ruimte configureren voor het **gebruik van werk ruimte-of resource machtigingen**

    * Gebruikers de volgende machtigingen verlenen voor de werk ruimte: 

        * `Microsoft.OperationalInsights/workspaces/read`: vereist zodat het gebruik de werk ruimte kan opsommen en de Blade werk ruimte kan openen in de Azure Portal
        * `Microsoft.OperationalInsights/workspaces/query/read`: vereist voor elke gebruiker die query's kan uitvoeren
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read`: als u logboeken van Azure AD-aanmelding wilt kunnen lezen
        * `Microsoft.OperationalInsights/workspaces/query/Update/read`: als u de logboeken van Updatebeheer oplossingen wilt kunnen lezen
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read`: als u de logboeken van Updatebeheer oplossingen wilt kunnen lezen
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read`: om logboeken voor update beheer te kunnen lezen
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read`: vereist om Updatebeheer oplossing te kunnen gebruiken
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read`: vereist om Updatebeheer oplossing te kunnen gebruiken

    * Ken gebruikers de volgende machtigingen toe aan hun resources: `*/read`, toegewezen aan de rol van lezer of `Microsoft.Insights/logs/*/read`. 

## <a name="table-level-rbac"></a>RBAC op tabel niveau

Met **RBAC op tabel niveau** kunt u naast de andere machtigingen nauw keurigere controle definiëren voor gegevens in een log Analytics-werk ruimte. Met dit besturings element kunt u specifieke gegevens typen definiëren die alleen toegankelijk zijn voor een specifieke groep gebruikers.

U implementeert Table Access Control met [aangepaste Azure-rollen](../../role-based-access-control/custom-roles.md) voor het verlenen of weigeren van toegang tot specifieke [tabellen](../log-query/logs-structure.md) in de werk ruimte. Deze rollen worden toegepast op werk ruimten met [toegangs beheer modi](design-logs-deployment.md#access-control-mode) werk ruimte-context of resource-context, ongeacht de [toegangs modus](design-logs-deployment.md#access-mode)van de gebruiker.

Maak een [aangepaste rol](../../role-based-access-control/custom-roles.md) met de volgende acties om toegang te definiëren tot Table Access Control.

* Als u toegang wilt verlenen aan een tabel, neemt u deze op in de sectie **acties** van de roldefinitie.
* Als u de toegang tot een tabel wilt weigeren, neemt u deze op in de sectie **intact** van de roldefinitie.
* Gebruik * om alle tabellen op te geven.

Als u bijvoorbeeld een rol wilt maken met toegang tot de tabellen _heartbeat_ en _AzureActivity_ , maakt u een aangepaste rol met behulp van de volgende acties:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Als u een rol wilt maken die alleen toegang heeft tot _Security Baseline Baseline_ en geen andere tabellen, maakt u een aangepaste rol met behulp van de volgende acties:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Aangepaste logboeken

 Aangepaste logboeken worden gemaakt op basis van gegevens bronnen, zoals aangepaste logboeken en HTTP-gegevens verzamelaar-API. De eenvoudigste manier om het type logboek te identificeren, is door de tabellen die worden vermeld onder [aangepaste Logboeken in het logboek schema](../log-query/get-started-portal.md#understand-the-schema)te controleren.

 U kunt momenteel geen toegang verlenen of weigeren voor afzonderlijke aangepaste logboeken, maar u hebt toegang tot alle aangepaste Logboeken. Als u een rol wilt maken met toegang tot alle aangepaste logboeken, maakt u een aangepaste rol met behulp van de volgende acties:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Overwegingen

* Als een gebruiker algemene Lees machtigingen heeft met de Standard Reader-of Inzender rollen die de  _\*/Read_ -actie bevatten, wordt het toegangs beheer per tabel overschreven en hebben ze toegang tot alle logboek gegevens.
* Als een gebruiker toegang verleent per tabel, maar geen andere machtigingen heeft, zouden ze toegang kunnen krijgen tot logboek gegevens vanuit de API, maar niet van de Azure Portal. Als u toegang wilt bieden vanaf de Azure Portal, gebruikt u Log Analytics Reader als basis functie.
* Beheerders van het abonnement hebben toegang tot alle gegevens typen, ongeacht andere machtigings instellingen.
* Werkruimte eigenaren worden beschouwd als elke andere gebruiker voor toegangs beheer per tabel.
* U moet rollen toewijzen aan beveiligings groepen in plaats van afzonderlijke gebruikers om het aantal toewijzingen te verminderen. Hiermee kunt u ook bestaande hulpprogram ma's voor groeps beheer gebruiken om de toegang te configureren en te controleren.

## <a name="next-steps"></a>Volgende stappen

* Zie [Log Analytics-agent overzicht](../../azure-monitor/platform/log-analytics-agent.md) voor het verzamelen van gegevens van computers in uw datacenter of andere cloudomgeving.

* Zie [gegevens verzamelen over Azure virtual machines](../../azure-monitor/learn/quick-collect-azurevm.md) voor het configureren van gegevens verzameling vanuit Azure vm's.
