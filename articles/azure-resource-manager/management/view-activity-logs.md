---
title: Azure-activiteitslogboeken weergeven om resources te controleren
description: Gebruik de activiteitslogboeken om acties en fouten van gebruikers te controleren. Toont Azure-portal PowerShell, Azure CLI en REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 9511090099894fad6708843f106570ed029f0c1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478134"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Activiteitslogboeken weergeven om acties op resources te controleren

Met activiteitenlogboeken kunt u het volgende bepalen:

* welke bewerkingen zijn uitgevoerd op de resources in uw abonnement
* wie de bewerking is gestart
* wanneer de bewerking heeft plaatsgevonden
* de status van de operatie
* de waarden van andere eigenschappen die u kunnen helpen de bewerking te onderzoeken

Het activiteitenlogboek bevat alle schrijfbewerkingen (PUT, POST, DELETE) voor uw resources. Het bevat geen leesbewerkingen (GET). Zie Azure Resource Manager [Resource Provider-bewerkingen](../../role-based-access-control/resource-provider-operations.md)voor een lijst met resourceacties . U kunt de activiteitenlogboeken gebruiken om fouten te vinden bij foutoplossing of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

Activiteitenlogboek worden gedurende negentig dagen bewaard. U kunt een query uitvoeren voor een willekeurig datumbereik, zolang de begindatum niet meer dan negentig dagen in het verleden ligt.

U informatie uit de activiteitenlogboeken ophalen via de portal, PowerShell, Azure CLI, Insights REST API of [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Azure Portal

Voer de volgende stappen uit om de activiteitslogboeken via de portal te bekijken:

1. Selecteer in het menu van de Azure-portal de optie **Controleren**of zoeken naar en selecteer **Monitor** op een pagina.

    ![Monitor selecteren](./media/view-activity-logs/select-monitor-from-menu.png)

1. Selecteer **Activiteitenlogboek**.

    ![Activiteitslogboek selecteren](./media/view-activity-logs/select-activity-log.png)

1. U ziet een overzicht van recente bewerkingen. Er wordt een standaardset filters toegepast op de bewerkingen. Let op de informatie op de samenvatting bevat wie de actie is gestart en wanneer deze is gebeurd.

    ![Overzicht van recente bewerkingen weergeven](./media/view-activity-logs/audit-summary.png)

1. Als u snel een vooraf gedefinieerde set filters wilt uitvoeren, selecteert u **Quick Insights**.

    ![Snelle inzichten selecteren](./media/view-activity-logs/select-quick-insights.png)

1. Selecteer een van de opties. Selecteer bijvoorbeeld **Mislukte implementaties** om fouten van implementaties te zien.

    ![Mislukte implementaties selecteren](./media/view-activity-logs/select-failed-deployments.png)

1. Let op: de filters zijn gewijzigd om zich te concentreren op implementatiefouten in de afgelopen 24 uur. Alleen bewerkingen die overeenkomen met de filters worden weergegeven.

    ![Weergavefilters](./media/view-activity-logs/view-filters.png)

1. Als u zich wilt concentreren op specifieke bewerkingen, wijzigt u de filters of past u nieuwe toe. In de volgende afbeelding wordt bijvoorbeeld een nieuwe waarde weergegeven voor het **type Tijdspanne** en **Resource** is ingesteld op opslagaccounts.

    ![Filteropties instellen](./media/view-activity-logs/set-filter.png)

1. Als u de query later opnieuw wilt uitvoeren, selecteert u **Huidige filters vastmaken**.

    ![Filters vastmaken](./media/view-activity-logs/pin-filters.png)

1. Geef het filter een naam.

    ![Naamfilters](./media/view-activity-logs/name-filters.png)

1. Het filter is beschikbaar in het dashboard. Selecteer **Dashboard** in het menu van Azure Portal.

    ![Filter weergeven op dashboard](./media/view-activity-logs/activity-log-on-dashboard.png)

1. Vanuit de portal u wijzigingen in een resource weergeven. Ga terug naar de standaardweergave in Monitor en selecteer een bewerking waarbij een resource moet worden gewijzigd.

    ![Bewerking selecteren](./media/view-activity-logs/select-operation.png)

1. Selecteer **Geschiedenis wijzigen (Voorbeeld)** en kies een van de beschikbare bewerkingen.

    ![Wijzigingsgeschiedenis selecteren](./media/view-activity-logs/select-change-history.png)

1. De wijzigingen in de resource worden weergegeven.

    ![Wijzigingen weergeven](./media/view-activity-logs/show-changes.png)

Zie [Resourcewijzigingen opbrengen](../../governance/resource-graph/how-to/get-resource-changes.md)voor meer informatie over de geschiedenis van wijzigingen in de wijziging van resources .

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u logboekvermeldingen wilt ophalen, voert u de opdracht **Get-AzLog uit.** U geeft extra parameters om de lijst met vermeldingen te filteren. Als u geen begin- en eindtijd opgeeft, worden de vermeldingen van de afgelopen zeven dagen geretourneerd.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

In het volgende voorbeeld ziet u hoe u het activiteitenlogboek gebruiken voor onderzoeksbewerkingen die gedurende een bepaalde tijd zijn uitgevoerd. De begin- en einddatumworden opgegeven in een datumnotatie.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

U ook datumfuncties gebruiken om het datumbereik op te geven, zoals de afgelopen 14 dagen.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

U de acties opzoeken die door een bepaalde gebruiker zijn uitgevoerd.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

U filteren op mislukte bewerkingen.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

U zich op één fout concentreren door te kijken naar het statusbericht voor dat item.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

U specifieke waarden selecteren om de geretourneerde gegevens te beperken.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

Afhankelijk van de begintijd die u opgeeft, kunnen de vorige opdrachten een lange lijst met bewerkingen voor de resourcegroep retourneren. U de resultaten filteren op wat u zoekt door zoekcriteria op te geven. U bijvoorbeeld filteren op het type bewerking.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

U ResourceGraph gebruiken om de wijzigingsgeschiedenis voor een resource weer te geven. Zie [Resourcewijzigingen opvragen voor](../../governance/resource-graph/how-to/get-resource-changes.md)meer informatie .

## <a name="azure-cli"></a>Azure-CLI

Als u logboekvermeldingen wilt ophalen, voert u de opdracht [activiteitslogboeklijst van AZ-monitor](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) uit met een verschuiving om de tijdspanne aan te geven.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

In het volgende voorbeeld ziet u hoe u het activiteitenlogboek gebruiken voor onderzoeksbewerkingen die gedurende een bepaalde tijd zijn uitgevoerd. De begin- en einddatumworden opgegeven in een datumnotatie.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

U de acties opzoeken die door een bepaalde gebruiker zijn uitgevoerd, zelfs voor een resourcegroep die niet meer bestaat.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

U filteren op mislukte bewerkingen.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

U zich op één fout concentreren door te kijken naar het statusbericht voor dat item.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

U specifieke waarden selecteren om de geretourneerde gegevens te beperken.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

Afhankelijk van de begintijd die u opgeeft, kunnen de vorige opdrachten een lange lijst met bewerkingen voor de resourcegroep retourneren. U de resultaten filteren op wat u zoekt door zoekcriteria op te geven. U bijvoorbeeld filteren op het type bewerking.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

U ResourceGraph gebruiken om de wijzigingsgeschiedenis voor een resource weer te geven. Zie [Resourcewijzigingen opvragen voor](../../governance/resource-graph/how-to/get-resource-changes.md)meer informatie .

## <a name="rest-api"></a>REST API

De REST-bewerkingen voor het werken met het activiteitenlogboek maken deel uit van de [Insights REST API.](/rest/api/monitor/) Zie [De beheergebeurtenissen in een abonnement](/rest/api/monitor/activitylogs)weergeven om activiteitenlogboekgebeurtenissen op te halen.

## <a name="next-steps"></a>Volgende stappen

* Azure Activity-logboeken kunnen worden gebruikt met Power BI om meer inzicht te krijgen in de acties in uw abonnement. Zie [Azure Activity Logs weergeven en analyseren in Power BI en meer](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Zie [Azure Role-based Access Control](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het instellen van beveiligingsbeleid.
* Zie Analyse van [toepassingswijziging gebruiken in Azure Monitor](../../azure-monitor/app/change-analysis.md)voor meer informatie over de wijzigingen in uw toepassingen vanaf de infrastructuurlaag tot de implementatie van toepassingen.
* Zie [Implementatiebewerkingen weergeven](../templates/deployment-history.md)voor meer informatie over de opdrachten voor het weergeven van implementatiebewerkingen.
* Zie [Resources vergrendelen met Azure Resource Manager](lock-resources.md)voor meer informatie over het voorkomen van verwijderingen op een bron voor alle gebruikers.
* Zie [Azure Resource Manager Resource Provider-bewerkingen](../../role-based-access-control/resource-provider-operations.md) voor elke Microsoft Azure Resource Manager-provider voor de lijst met bewerkingen die beschikbaar zijn voor elke Microsoft Azure Resource Manager-provider.
