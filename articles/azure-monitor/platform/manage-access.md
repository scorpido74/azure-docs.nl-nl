---
title: Logboekanalysewerkruimten beheren in Azure-monitor | Microsoft Documenten
description: U de toegang tot gegevens die zijn opgeslagen in een werkruimte Log Analytics in Azure Monitor beheren met behulp van machtigingen op resource-, werkruimte- of tabelniveau. In dit artikel wordt beschreven hoe u dit invullen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/22/2019
ms.openlocfilehash: 1e559309b8e8d9768ca2f79dabfb01ec6086a961
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348725"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Toegang tot logboekgegevens en werkruimten beheren in Azure Monitor

Azure Monitor slaat [logboekgegevens](data-platform-logs.md) op in een Log Analytics-werkruimte. Een werkruimte is een container die gegevens- en configuratiegegevens bevat. Als u de toegang tot logboekgegevens wilt beheren, voert u verschillende administratieve taken uit met betrekking tot uw werkruimte.

In dit artikel wordt uitgelegd hoe u de toegang tot logboeken beheert en de werkruimten beheert die deze bevatten, inclusief hoe u toegang verleent tot: 

* De werkruimte met werkruimtemachtigingen.
* Gebruikers die toegang nodig hebben om gegevens uit specifieke bronnen te registreren met behulp van RBAC (Azure role-based access control).
* Gebruikers die toegang nodig hebben om gegevens in een specifieke tabel in de werkruimte te registreren met Azure RBAC.

## <a name="configure-access-control-mode"></a>Modus toegangsbeheer configureren

U de [toegangscontrolemodus](design-logs-deployment.md) bekijken die is geconfigureerd op een werkruimte vanuit de Azure-portal of met Azure PowerShell.  U deze instelling wijzigen met een van de volgende ondersteunde methoden:

* Azure Portal

* Azure PowerShell

* Azure Resource Manager-sjabloon

### <a name="from-the-azure-portal"></a>Vanuit Azure Portal

U de huidige modus voor toegangsbeheer voor werkruimteweering weergeven op de pagina **Overzicht** voor de werkruimte in het menu van de **werkruimte Log Analytics.**

![De modus voor toegangsbeheer voor werkruimteweering weergeven](media/manage-access/view-access-control-mode.png)

1. Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .
1. Selecteer in de Azure-portal Log Analytics-werkruimten > uw werkruimte.

U deze instelling wijzigen op de pagina **Eigenschappen** van de werkruimte. Als u de instelling wijzigt, wordt deze uitgeschakeld als u geen machtigingen hebt om de werkruimte te configureren.

![De toegangsmodus voor werkruimte wijzigen](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>PowerShell gebruiken

Gebruik de volgende opdracht om de toegangscontrolemodus voor alle werkruimten in het abonnement te onderzoeken:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

De uitvoer moet als volgt lijken:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Een waarde `False` van middelen die de werkruimte is geconfigureerd met de toegangsmodus werkruimte-context.  Een waarde `True` van middelen die de werkruimte is geconfigureerd met de toegangsmodus resourcecontext.

> [!NOTE]
> Als een werkruimte wordt geretourneerd zonder booleaanse waarde en leeg `False` is, komt dit ook overeen met de resultaten van een waarde.
>

Gebruik het volgende script om de toegangscontrolemodus voor een specifieke werkruimte in te stellen op de machtiging resourcecontext:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Gebruik het volgende script om de toegangscontrolemodus in te stellen voor alle werkruimten in het abonnement op de machtiging resourcecontext:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>Een resourcemanagersjabloon gebruiken

Als u de toegangsmodus wilt configureren in een Azure Resource Manager-sjabloon, stelt u de functievlag **van EnableLogAccessUsingOnlyResourcePermissions** in op de werkruimte op een van de volgende waarden.

* **false:** stel de werkruimte in op machtigingen voor werkruimte-context. Dit is de standaardinstelling als de vlag niet is ingesteld.
* **true:** Stel de werkruimte in op machtigingen voor resourcecontexten.

## <a name="manage-access-using-workspace-permissions"></a>Toegang beheren met behulp van machtigingen voor werkruimtes

Aan elke werkruimte kunnen meerdere accounts zijn gekoppeld en elk account kan toegang hebben tot meerdere werkruimten. Toegang wordt beheerd met [Azure-toegang op basis van azure-rollen.](../../role-based-access-control/role-assignments-portal.md)

Voor de volgende activiteiten zijn ook Azure-machtigingen vereist:

|Actie |Azure-machtigingen nodig |Opmerkingen |
|-------|-------------------------|------|
| Bewakingsoplossingen toevoegen en verwijderen | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Deze machtigingen moeten worden toegekend op het niveau van de resourcegroep of het abonnement. |
| De prijscategorie wijzigen | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Gegevens weergeven op de tegels *Back-up* en *Site Recovery* | Beheerder/medebeheerder | Heeft toegang tot resources die zijn geïmplementeerd met behulp van het klassieke implementatiemodel |
| Een werkruimte maken in Azure Portal | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Basiseigenschappen voor werkruimte weergeven en het werkblad in de portal invoeren | `Microsoft.OperationalInsights/workspaces/read` ||
| Querylogboeken met behulp van elke interface | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Alle logboektypen openen met query's | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Toegang tot een specifieke logboektabel | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Lees de werkruimtetoetsen om logboeken naar deze werkruimte te verzenden | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Toegang beheren met Azure-machtigingen

Volg de stappen in [Roltoewijzingen gebruiken voor het beheer van de toegang tot de resources van uw Azure-abonnement](../../role-based-access-control/role-assignments-portal.md) om toegang te verlenen tot de Log Analytics-werkruimte met behulp van Azure-machtigingen. Zie Aangepaste rollen [voorbeeld,](#custom-role-examples) zie Aangepaste rollen voorbeeld

Azure heeft twee ingebouwde gebruikersrollen voor Log Analytics-werkruimten:

* Lezer van Log Analytics
* Inzender van Log Analytics

Leden van de rol *Lezer van Log Analytics* kunnen:

* Alle controlegegevens weergeven en doorzoeken
* Controlegegevens weergeven, inclusief de configuratie van Azure Diagnostics voor alle Azure-resources.

De rol Logboekanalyselezer bevat de volgende Azure-acties:

| Type    | Machtiging | Beschrijving |
| ------- | ---------- | ----------- |
| Actie | `*/read`   | Mogelijkheid om alle Azure-bronnen en resourceconfiguratie weer te geven. Omvat: <br> Status van de VM-extensie <br> Configuratie van Azure Diagnostics voor resources <br> Alle eigenschappen en instellingen van alle resources. <br> Voor werkruimten kunnen volledige onbeperkte machtigingen worden gelezen om de werkruimte-instellingen te lezen en query's op de gegevens uit te voeren. Zie hierboven meer gedetailleerde opties. |
| Actie | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Afgeschaft, geen noodzaak om ze toe te wijzen aan gebruikers. |
| Actie | `Microsoft.OperationalInsights/workspaces/search/action` | Afgeschaft, geen noodzaak om ze toe te wijzen aan gebruikers. |
| Actie | `Microsoft.Support/*` | Mogelijkheid ondersteuningsaanvragen te openen |
|Geen bewerking | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Hiermee voorkomt u dat de werkruimtesleutel wordt gelezen die nodig is om de API voor gegevensverzameling te gebruiken en agents te installeren. Dit voorkomt dat de gebruiker nieuwe bronnen toevoegt aan de werkruimte |

Leden van de rol *Inzender van Log Analytics* kunnen:

* Bevat alle bevoegdheden van de *rol Log Analytics Reader,* zodat de gebruiker alle bewakingsgegevens kan lezen
* Automatiseringsaccounts maken en configureren
* Beheeroplossingen toevoegen en verwijderen

    > [!NOTE]
    > Om de laatste twee acties succesvol uit te voeren, moet deze toestemming worden verleend op resourcegroep- of abonnementsniveau.

* Opslagaccountsleutels lezen
* De verzameling logboeken configureren vanuit Azure Storage
* De controle-instellingen voor Azure-resources bewerken, inclusief
  * De VM-extensie toevoegen aan virtuele machines
  * Azure Diagnostics configureren op alle Azure-resources

> [!NOTE]
> U kunt de mogelijkheid om een VM-extensie toe te voegen aan een virtuele machine, gebruiken om volledige controle over een virtuele machine te krijgen.

De rol Log Analytics-inzender bevat de volgende Azure-acties:

| Machtiging | Beschrijving |
| ---------- | ----------- |
| `*/read`     | De mogelijkheid om alle resources en de resourceconfiguratie weer te geven. Omvat: <br> Status van de VM-extensie <br> Configuratie van Azure Diagnostics voor resources <br> Alle eigenschappen en instellingen van alle resources. <br> Voor werkruimten kunnen volledige onbeperkte machtigingen de instelling van de werkruimte lezen en query's op de gegevens uitvoeren. Zie hierboven meer gedetailleerde opties. |
| `Microsoft.Automation/automationAccounts/*` | De mogelijkheid om Azure Automation-accounts te maken en te configureren, inclusief het toevoegen en bewerken van runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | VM-extensies toevoegen, bijwerken en verwijderen, met inbegrip van de Microsoft Monitoring Agent-extensie en de OMS Agent for Linux-extensie |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Geef de opslagaccountsleutel weer. Vereist om Log Analytics te configureren om logboeken uit Azure-opslagaccounts te lezen |
| `Microsoft.Insights/alertRules/*` | Waarschuwingsregels toevoegen, bijwerken en verwijderen |
| `Microsoft.Insights/diagnosticSettings/*` | Diagnostische instellingen bij Azure-resources toevoegen, bijwerken en verwijderen |
| `Microsoft.OperationalInsights/*` | Configuratie voor Log Analytics-werkruimten toevoegen, bijwerken en verwijderen. Als u geavanceerde instellingen voor `Microsoft.OperationalInsights/workspaces/write`de werkruimte wilt bewerken, heeft de gebruiker deze nodig. |
| `Microsoft.OperationsManagement/*` | Beheeroplossingen toevoegen en verwijderen |
| `Microsoft.Resources/deployments/*` | Maak en verwijder implementaties. Vereist voor het toevoegen en verwijderen van oplossingen, werkruimten en Automation-accounts |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Maak en verwijder implementaties. Vereist voor het toevoegen en verwijderen van oplossingen, werkruimten en Automation-accounts |

Als u gebruikers wilt toevoegen aan en verwijderen uit een gebruikersrol, moet u beschikken over machtigingen voor `Microsoft.Authorization/*/Delete` en `Microsoft.Authorization/*/Write`.

Gebruik deze rollen om gebruikers toegang te geven op verschillende niveaus:

* Abonnement: toegang tot alle werkruimten in het abonnement
* Resourcegroep: toegang tot alle werkruimten in de resourcegroep
* Resource: alleen toegang tot de opgegeven werkruimte

We raden u aan opdrachten uit te voeren op resourceniveau (werkruimte) om nauwkeurige toegangscontrole te garanderen. Gebruik [aangepaste rollen](../../role-based-access-control/custom-roles.md) om rollen te maken met de specifieke machtigingen die nodig zijn.

### <a name="resource-permissions"></a>Bronmachtigingen

Wanneer gebruikers logboeken vanuit een werkruimte opvragen met toegang tot resourcecontext, hebben ze de volgende machtigingen voor de bron:

| Machtiging | Beschrijving |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Voorbeelden:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Mogelijkheid om alle logboekgegevens voor de bron te bekijken.  |
| `Microsoft.Insights/diagnosticSettings/write` | Mogelijkheid om diagnostische instellingen te configureren om logboeken voor deze bron in te stellen. |

`/read`toestemming wordt meestal verleend vanuit een rol die _ \*/lezen of_ _\*_ machtigingen bevat, zoals de ingebouwde [lezers-](../../role-based-access-control/built-in-roles.md#reader) en [inzenderrollen.](../../role-based-access-control/built-in-roles.md#contributor) Aangepaste rollen die specifieke acties of speciale ingebouwde rollen bevatten, bevatten mogelijk deze toestemming niet.

Zie [Hieronder toegangsbeheer per tabel definiëren](#table-level-rbac) als u ander toegangscontrole voor verschillende tabellen wilt maken.

## <a name="custom-role-examples"></a>Aangepaste rolvoorbeelden

1. Voer het volgende uit om een gebruiker toegang te verlenen tot logboekgegevens uit zijn bronnen:

    * De modus voor toegangsbeheer voor werkruimte configureren om **werkruimte- of resourcemachtigingen** te gebruiken

    * Gebruikers `*/read` of `Microsoft.Insights/logs/*/read` machtigingen verlenen aan hun bronnen. Als ze al de rol [Log Analytics Reader](../../role-based-access-control/built-in-roles.md#reader) op de werkruimte toegewezen hebben, is deze voldoende.

2. Voer het volgende uit om een gebruiker toegang te verlenen tot logboekgegevens uit zijn bronnen en hun bronnen te configureren om logboeken naar de werkruimte te verzenden:

    * De modus voor toegangsbeheer voor werkruimte configureren om **werkruimte- of resourcemachtigingen** te gebruiken

    * Geef gebruikers de volgende machtigingen `Microsoft.OperationalInsights/workspaces/read` voor `Microsoft.OperationalInsights/workspaces/sharedKeys/action`de werkruimte: en . Met deze machtigingen kunnen gebruikers geen query's op werkruimteniveau uitvoeren. Ze kunnen alleen de werkruimte opsommen en gebruiken als bestemming voor diagnostische instellingen of agentconfiguratie.

    * Geef gebruikers de volgende machtigingen `Microsoft.Insights/logs/*/read` voor `Microsoft.Insights/diagnosticSettings/write`hun resources: en . Als de rol [Log-Inzender van Analytics](../../role-based-access-control/built-in-roles.md#contributor) al is `*/read` toegewezen, de leesrol is toegewezen of machtigingen voor deze bron zijn verleend, volstaat deze.

3. Voer het volgende uit om een gebruiker toegang te verlenen tot logboekgegevens uit zijn bronnen zonder beveiligingsgebeurtenissen te kunnen lezen en gegevens te verzenden:

    * De modus voor toegangsbeheer voor werkruimte configureren om **werkruimte- of resourcemachtigingen** te gebruiken

    * Geef gebruikers de volgende machtigingen `Microsoft.Insights/logs/*/read`voor hun resources: .

    * Voeg de volgende non-action toe om te `Microsoft.Insights/logs/SecurityEvent/read`voorkomen dat gebruikers het type SecurityEvent lezen: . De NonAction heeft dezelfde aangepaste rol als de actie`Microsoft.Insights/logs/*/read`die de leestoestemming biedt ( ). Als de gebruiker de leesactie van een andere rol die is toegewezen aan deze resource of aan de abonnements- of resourcegroep, inherent is aan de leesactie, kan hij alle logboektypen lezen. Dit geldt ook als `*/read`ze erven, die bijvoorbeeld bestaan met de rol Reader of Contributor.

4. Voer het volgende uit om een gebruiker toegang te verlenen tot logboekgegevens uit zijn bronnen en alle aanmeldende en lees-updatebeheeroplossingslogboekgegevens uit de werkruimte te lezen:

    * De modus voor toegangsbeheer voor werkruimte configureren om **werkruimte- of resourcemachtigingen** te gebruiken

    * Geef gebruikers de volgende machtigingen voor de werkruimte: 

        * `Microsoft.OperationalInsights/workspaces/read`– vereist zodat het gebruik de werkruimte kan opsommen en het werkruimteblad in de Azure-portal kan openen
        * `Microsoft.OperationalInsights/workspaces/query/read`– vereist voor elke gebruiker die query's kan uitvoeren
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read`– azure AD-aanmeldingslogboeken kunnen lezen
        * `Microsoft.OperationalInsights/workspaces/query/Update/read`– om logboeken van updatebeheeroplossingen te kunnen lezen
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read`– om logboeken van updatebeheeroplossingen te kunnen lezen
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read`– om logboeken van het updatebeheer te kunnen lezen
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read`– vereist om de Update Management-oplossing te kunnen gebruiken
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read`– vereist om de Update Management-oplossing te kunnen gebruiken

    * Geef gebruikers de volgende machtigingen `*/read`aan hun resources: , `Microsoft.Insights/logs/*/read`toegewezen aan de rol Reader, of . 

## <a name="table-level-rbac"></a>Tabelniveau RBAC

**Met RBAC op tabelniveau** u naast de andere machtigingen ook gedetailleerdere besturingselementen definiëren in gegevens in een Log Analytics-werkruimte. Met dit besturingselement u specifieke gegevenstypen definiëren die alleen toegankelijk zijn voor een specifieke groep gebruikers.

U implementeert tabeltoegangsbeheer met [aangepaste Azure-rollen](../../role-based-access-control/custom-roles.md) om toegang te verlenen tot specifieke [tabellen](../log-query/logs-structure.md) in de werkruimte. Deze rollen worden toegepast op werkruimten met werkruimte-context- of [resourcecontexttoegangsbeheermodi,](design-logs-deployment.md#access-control-mode) ongeacht de [toegangsmodus](design-logs-deployment.md#access-mode)van de gebruiker.

Maak een [aangepaste rol](../../role-based-access-control/custom-roles.md) met de volgende acties om toegang tot tabeltoegangsbeheer te definiëren.

* Als u toegang wilt verlenen tot een tabel, neemt u deze op in de sectie **Acties** van de roldefinitie. Als u de toegang wilt aftrekken van de toegestane **acties,** neemt u deze op in de sectie **NotActions.**
* Gebruik Microsoft.OperationalInsights/workspaces/query/* om alle tabellen op te geven.

Als u bijvoorbeeld een rol wilt maken met toegang tot de _heartbeat-_ en _AzureActivity-tabellen,_ maakt u een aangepaste rol met de volgende acties:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Als u een rol wilt maken met alleen toegang tot de tabel _SecurityBaseline,_ maakt u een aangepaste rol met de volgende acties:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```

### <a name="custom-logs"></a>Aangepaste logboeken

 Aangepaste logboeken worden gemaakt op basis van gegevensbronnen zoals aangepaste logboeken en HTTP Data Collector API. De eenvoudigste manier om het type logboek te identificeren, is door de tabellen te controleren die worden vermeld onder [Aangepaste logboeken in het logboekschema](../log-query/get-started-portal.md#understand-the-schema).

 U momenteel geen toegang verlenen tot afzonderlijke aangepaste logboeken, maar u wel toegang verlenen tot alle aangepaste logboeken. Als u een rol wilt maken met toegang tot alle aangepaste logboeken, maakt u een aangepaste rol met de volgende acties:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```

### <a name="considerations"></a>Overwegingen

* Als een gebruiker globale leestoestemming krijgt met de standaardlees- of inzenderrollen die de _ \*/read-actie_ bevatten, wordt het toegangsbeheer per tabel overschreven en krijgt hij toegang tot alle logboekgegevens.
* Als een gebruiker toegang per tabel krijgt, maar geen andere machtigingen, heeft deze toegang tot logboekgegevens uit de API, maar niet vanuit de Azure-portal. Als u toegang wilt bieden vanuit de Azure-portal, gebruikt u Log Analytics Reader als basisrol.
* Beheerders van het abonnement hebben toegang tot alle gegevenstypen, ongeacht andere machtigingsinstellingen.
* Eigenaren van werkruimtes worden behandeld als elke andere gebruiker voor toegangsbeheer per tabel.
* We raden u aan rollen toe te wijzen aan beveiligingsgroepen in plaats van aan individuele gebruikers om het aantal toewijzingen te verminderen. Dit helpt u ook bij het gebruik van bestaande groepsbeheertools om toegang te configureren en te verifiëren.

## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van loganalytics-agentom](../../azure-monitor/platform/log-analytics-agent.md) gegevens te verzamelen van computers in uw datacenter of andere cloudomgeving.

* Zie [Gegevens verzamelen over virtuele Azure-machines](../../azure-monitor/learn/quick-collect-azurevm.md) om gegevensverzameling vanuit Azure VM's te configureren.
