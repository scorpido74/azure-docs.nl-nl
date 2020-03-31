---
title: Diagnostische instelling maken om logboeken en statistieken in Azure te verzamelen
description: Diagnostische instellingen maken om Azure-platformlogboeken door te sturen naar Azure Monitor-logboeken, Azure-opslag of Azure Event Hubs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fb2f9ff5af68575d9f9d29e9a6aca83d603395b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672409"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Diagnostische instelling maken om platformlogboeken en -statistieken in Azure te verzamelen
[Platformlogboeken](platform-logs-overview.md) in Azure, inclusief Azure Activity log en resource logs, bieden gedetailleerde diagnostische en controle-informatie voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn. In dit artikel vindt u informatie over het maken en configureren van diagnostische instellingen om platformlogboeken naar verschillende bestemmingen te verzenden.

> [!IMPORTANT]
> Voordat u een diagnostische instelling maakt om het logboek Activiteit te verzamelen, moet u eerst een verouderde configuratie uitschakelen. Zie [Azure Activity-logboek verzamelen met verouderde instellingen](diagnostic-settings-legacy.md) voor meer informatie.

Elke Azure-bron vereist een eigen diagnostische instelling, die het volgende definieert:

- Categorieën van logboeken en metrische gegevens die worden verzonden naar de doelen die in de instelling zijn gedefinieerd. De beschikbare categorieën verschillen per resourcetype.
- Een of meer doelen voor het verzenden van de logboeken. Huidige doelen omvatten Log Analytics-werkruimte, Event Hubs en Azure Storage.
 
Eén diagnostische instelling kan niet meer dan één van de bestemmingen definiëren. Als u gegevens wilt verzenden naar meer dan één bepaald type doel (bijvoorbeeld twee verschillende Log Analytics-werkruimten), maakt u meerdere instellingen. Elke resource kan maximaal vijf diagnostische instellingen hebben.


> [!NOTE]
> [Platformstatistieken](metrics-supported.md) worden automatisch verzameld in [Azure Monitor Metrics](data-platform-metrics.md). Diagnostische instellingen kunnen worden gebruikt om metrische gegevens voor bepaalde Azure-services te verzamelen in Azure Monitor Logs voor analyse met andere bewakingsgegevens met behulp van [logboekquery's.](../log-query/log-query-overview.md)

## <a name="destinations"></a>Bestemmingen 
Platformlogboeken kunnen worden verzonden naar de bestemmingen in de volgende tabel. De configuratie voor elke bestemming wordt uitgevoerd met hetzelfde proces voor het maken van diagnostische instellingen die in dit artikel worden beschreven. Volg elke koppeling in de volgende tabel voor meer informatie over het verzenden van gegevens naar die bestemming.

| Doel | Beschrijving |
|:---|:---|
| [Log Analytics-werkruimte](resource-logs-collect-workspace.md) | Als u logboeken verzamelt in een Log Analytics-werkruimte, u deze analyseren met andere bewakingsgegevens die door Azure Monitor zijn verzameld met behulp van krachtige logboekquery's en ook andere Azure Monitor-functies zoals waarschuwingen en visualisaties gebruiken. |
| [Event hubs](resource-logs-stream-event-hubs.md) | Als u logboeken naar Event Hubs verzendt, u gegevens streamen naar externe systemen, zoals EXTERNE SM's en andere oplossingen voor logboekanalyse. |
| [Azure-opslagaccount](resource-logs-collect-storage.md) | Het archiveren van logboeken naar een Azure-opslagaccount is handig voor controle, statische analyse of back-up. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Diagnostische instellingen maken in Azure-portal
U diagnostische instellingen in de Azure-portal configureren in het menu Azure Monitor of in het menu voor de bron.

1. Waar u diagnostische instellingen configureert in de Azure-portal, is afhankelijk van de bron.

   - Klik voor één resource op **Diagnostische instellingen** onder **Monitor** in het menu van de bron.

        ![Diagnostische instellingen](media/diagnostic-settings/menu-resource.png)

    - Klik voor een of meer bronnen op **Diagnostische instellingen** onder **Instellingen** in het menu Azure Monitor en klik vervolgens op de bron.
    
        ![Diagnostische instellingen](media/diagnostic-settings/menu-monitor.png)

    - Klik voor het logboek Activiteit op **Activiteitslogboek** in het menu **Azure Monitor** en vervolgens op **Diagnostische instellingen**. Zorg ervoor dat u een verouderde configuratie uitschakelt voor het logboek Activiteit. Zie [Bestaande instellingen uitschakelen](diagnostic-settings-legacy.md#disable-existing-settings) voor meer informatie.

        ![Diagnostische instellingen](media/diagnostic-settings/menu-activity-log.png)

2. Als er geen instellingen bestaan op de bron die u hebt geselecteerd, wordt u gevraagd een instelling te maken. Klik **op Diagnostische instelling toevoegen**.

   ![Diagnostische instelling toevoegen - geen bestaande instellingen](media/diagnostic-settings/add-setting.png)

   Als er bestaande instellingen op de bron staan, ziet u een lijst met instellingen die al zijn geconfigureerd. Klik op **Diagnostische instelling toevoegen** om een nieuwe instelling toe te voegen of **Bewerk instelling** om een bestaande instelling te bewerken. Elke instelling mag niet meer dan één van elk van de doeltypen hebben.

   ![Diagnostische instelling toevoegen - bestaande instellingen](media/diagnostic-settings/edit-setting.png)

3. Geef uw instelling een naam als deze er nog niet is.
4. Schakel het selectievakje voor elke bestemming in om de logboeken te verzenden. Klik **op Configureren** om de instellingen op te geven zoals beschreven in de volgende tabel.

    | Instelling | Beschrijving |
    |:---|:---|
    | Log Analytics-werkruimte | Naam van werkruimte. |
    | Storage-account | Naam van het opslagaccount. |
    | Event hub-naamruimte | De naamruimte waar de gebeurtenishub wordt gemaakt (als dit de eerste keer streaming logs is) of gestreamd naar (als er al bronnen zijn die die logboekcategorie naar deze naamruimte streamen).
    | Naam van Event Hub | Geef optioneel een naam van de gebeurtenishub op om alle gegevens in de instelling te verzenden. Als u geen naam opgeeft, wordt voor elke logboekcategorie een gebeurtenishub gemaakt. Als u meerdere categorieën verzendt, u een naam opgeven om het aantal gemaakte gebeurtenishubs te beperken. Zie quota en limieten voor [Azure Event Hubs](../../event-hubs/event-hubs-quotas.md) voor meer informatie. |
    | Naam van het beleid voor gebeurtenishub | Hiermee definieert u de machtigingen die het streamingmechanisme heeft. |

    ![Diagnostische instelling toevoegen - bestaande instellingen](media/diagnostic-settings/setting-details.png)

5. Schakel het selectievakje in voor elk van de categorieën gegevens die u naar de opgegeven bestemmingen wilt verzenden. De lijst met categorieën is afhankelijk van elke Azure-service.

   > [!NOTE]
   > Het verzenden van multidimensionale metrische gegevens via diagnostische instellingen wordt momenteel niet ondersteund. Metrische gegevens met dimensies worden geëxporteerd als platte eendimensionale metrische gegevens, als totaal van alle dimensiewaarden.
   >
   > *Een voorbeeld*: de meetwaarde 'Binnenkomende berichten' voor een Event Hub kan worden verkend en uitgezet op wachtrijniveau. Wanneer de waarde wordt geëxporteerd via diagnostische instellingen, wordt deze echter voorgesteld als alle binnenkomende berichten voor alle wachtrijen in de Event Hub.

6. Klik op **Opslaan**.

Na enkele ogenblikken wordt de nieuwe instelling weergegeven in uw lijst met instellingen voor deze bron en worden logboeken naar de opgegeven bestemmingen gestreamd wanneer nieuwe gebeurtenisgegevens worden gegenereerd. Houd er rekening mee dat er maximaal vijftien minuten kunnen zitten tussen het moment waarop een gebeurtenis wordt uitgezonden en wanneer deze [wordt weergegeven in een werkruimte log analytics.](data-ingestion-time.md)



## <a name="create-diagnostic-settings-using-powershell"></a>Diagnostische instellingen maken met PowerShell
Gebruik de cmdlet [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) om een diagnostische instelling te maken met [Azure PowerShell](powershell-quickstart-samples.md). Zie de documentatie voor deze cmdlet voor beschrijvingen van de parameters.

> [!IMPORTANT]
> U deze methode niet gebruiken voor het Azure-activiteitenlogboek. Gebruik in plaats daarvan [Diagnostische instelling maken in Azure Monitor met behulp van een resourcemanagersjabloon](diagnostic-settings-template.md) om een Resource Manager-sjabloon te maken en deze te implementeren met PowerShell.

Hieronder volgt een voorbeeld PowerShell-cmdlet om een diagnostische instelling te maken met behulp van alle drie de bestemmingen.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Diagnostische instellingen maken met Azure CLI
Gebruik de [diagnostische instellingen voor az-monitor om](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) een diagnostische instelling te maken met Azure [CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Zie de documentatie voor deze opdracht voor beschrijvingen van de parameters.

> [!IMPORTANT]
> U deze methode niet gebruiken voor het Azure-activiteitenlogboek. Gebruik in plaats daarvan [Diagnostische instelling maken in Azure Monitor met behulp van een resourcemanagersjabloon](diagnostic-settings-template.md) om een Resource Manager-sjabloon te maken en deze te implementeren met CLI.

Hieronder volgt een voorbeeld van cli-opdracht om een diagnostische instelling te maken met behulp van alle drie de bestemmingen.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Diagnostische instellingen configureren met REST API
Zie [Diagnostische instellingen](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) voor het maken of bijwerken van diagnostische instellingen met behulp van de Azure Monitor REST [API](https://docs.microsoft.com/rest/api/monitor/).


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Diagnostische instellingen configureren met behulp van de sjabloon Resourcebeheer
Zie [Diagnostische instelling maken in Azure Monitor met behulp van een resourcemanagersjabloon](diagnostic-settings-template.md) om diagnostische instellingen te maken of bij te werken met een Resource Manager-sjabloon.

## <a name="next-steps"></a>Volgende stappen

* [Lees meer over Azure-platformlogboeken](platform-logs-overview.md)
