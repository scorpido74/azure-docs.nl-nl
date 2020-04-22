---
title: Diagnostische instellingen gebruiken om platformstatistieken en -logboeken te verzamelen en in Azure
description: Verzend Azure Monitor-platformstatistieken en -logboeken naar Azure Monitor-logboeken, Azure-opslag of Azure Event Hubs met behulp van een diagnostische instelling.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681161"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Diagnostische instelling maken om bronlogboeken en -statistieken in Azure te verzamelen

[Platformlogboeken](platform-logs-overview.md) in Azure, inclusief het Azure Activity-logboeken en resourcelogboeken, bieden gedetailleerde diagnostische en controle-informatie voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn. [Platformstatistieken](data-platform-metrics.md) worden standaard verzameld en meestal opgeslagen in de Azure Monitor-metrische gegevensdatabase.

In dit artikel vindt u informatie over het maken en configureren van diagnostische instellingen om platformstatistieken en platformlogboeken naar verschillende bestemmingen te verzenden.

> [!IMPORTANT]
> Voordat u een diagnostische instelling maakt om het logboek Activiteit te verzamelen, moet u eerst een verouderde configuratie uitschakelen. Zie [Azure Activity-logboek verzamelen met verouderde instellingen](diagnostic-settings-legacy.md) voor meer informatie.

Voor elke Azure-bron is een eigen diagnostische instelling vereist, die de volgende criteria definieert:

- Categorieën van logboeken en metrische gegevens die worden verzonden naar de doelen die in de instelling zijn gedefinieerd. De beschikbare categorieën verschillen per resourcetype.
- Een of meer doelen voor het verzenden van de logboeken. Huidige doelen omvatten Log Analytics-werkruimte, Event Hubs en Azure Storage.

Eén diagnostische instelling kan niet meer dan één van de bestemmingen definiëren. Als u gegevens wilt verzenden naar meer dan één bepaald type doel (bijvoorbeeld twee verschillende Log Analytics-werkruimten), maakt u meerdere instellingen. Elke resource kan maximaal vijf diagnostische instellingen hebben.

> [!NOTE]
> [Platformstatistieken](metrics-supported.md) worden automatisch verzameld in [Azure Monitor Metrics](data-platform-metrics.md). Diagnostische instellingen kunnen worden gebruikt om metrische gegevens voor bepaalde Azure-services te verzamelen in Azure Monitor Logs voor analyse met andere bewakingsgegevens met behulp van [logboekquery's.](../log-query/log-query-overview.md)

## <a name="destinations"></a>Bestemmingen

Platformlogboeken en statistieken kunnen worden verzonden naar de bestemmingen in de volgende tabel. Volg elke koppeling in de volgende tabel voor meer informatie over het verzenden van gegevens naar die bestemming.

| Doel | Beschrijving |
|:---|:---|
| [Log Analytics-werkruimte](resource-logs-collect-workspace.md) | Door logboeken en statistieken te verzamelen in een Log Analytics-werkruimte u deze analyseren met andere bewakingsgegevens die door Azure Monitor zijn verzameld met behulp van krachtige logboekquery's en ook andere Azure Monitor-functies zoals waarschuwingen en visualisaties gebruiken. |
| [Gebeurtenishubs](resource-logs-stream-event-hubs.md) | Door logboeken en statistieken naar Event Hubs te verzenden, u gegevens streamen naar externe systemen, zoals EXTERNE SM's en andere oplossingen voor logboekanalyse. |
| [Azure-opslagaccount](resource-logs-collect-storage.md) | Het archiveren van logboeken en metrische gegevens naar een Azure-opslagaccount is handig voor controle, statische analyse of back-up. In vergelijking met Azure Monitor Logs en een Log Analytics-werkruimte is Azure-opslag goedkoper en kunnen logboeken daar voor onbepaalde tijd worden bewaard. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Diagnostische instellingen maken in Azure-portal

U diagnostische instellingen in de Azure-portal configureren in het menu Azure Monitor of in het menu voor de bron.

1. Waar u diagnostische instellingen configureert in de Azure-portal, is afhankelijk van de bron.

   - Klik voor één resource op **Diagnostische instellingen** onder **Monitor** in het menu van de bron.

        ![Diagnostische instellingen](media/diagnostic-settings/menu-resource.png)

   - Klik voor een of meer bronnen op **Diagnostische instellingen** onder **Instellingen** in het menu Azure Monitor en klik vervolgens op de bron.

      ![Diagnostische instellingen](media/diagnostic-settings/menu-monitor.png)

   - Klik voor het logboek Activiteit op **Activiteitslogboek** in het menu **Azure Monitor** en vervolgens op **Diagnostische instellingen**. Zorg ervoor dat u een verouderde configuratie uitschakelt voor het logboek Activiteit. Zie [Bestaande instellingen uitschakelen](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) voor meer informatie.

        ![Diagnostische instellingen](media/diagnostic-settings/menu-activity-log.png)

2. Als er geen instellingen bestaan op de bron die u hebt geselecteerd, wordt u gevraagd een instelling te maken. Klik **op Diagnostische instelling toevoegen**.

   ![Diagnostische instelling toevoegen - geen bestaande instellingen](media/diagnostic-settings/add-setting.png)

   Als er bestaande instellingen op de bron staan, ziet u een lijst met instellingen die al zijn geconfigureerd. Klik op **Diagnostische instelling toevoegen** om een nieuwe instelling toe te voegen of **Bewerk instelling** om een bestaande instelling te bewerken. Elke instelling mag niet meer dan één van elk van de doeltypen hebben.

   ![Diagnostische instelling toevoegen - bestaande instellingen](media/diagnostic-settings/edit-setting.png)

3. Geef uw instelling een naam als deze er nog niet is.

    ![Diagnostische instelling toevoegen](media/diagnostic-settings/setting-new-blank.png)

4. **Categoriegegevens (wat moet worden gerouteerd)** - Schakel het selectievakje in voor elke categorie gegevens die u later naar bestemmingen wilt verzenden. De lijst met categorieën varieert voor elke Azure-service.

     - **AllMetrics** leidt de platformstatistieken van een resource naar de Azure Logs Store, maar in logboekvorm. Deze statistieken worden meestal alleen verzonden naar de database met Azure Monitor-statistieken. Als u ze naar de Azure Monitor Logs Store verzendt (die doorzoekbaar is via Log Analytics) u ze integreren in query's die in andere logboeken zoeken. Deze optie is mogelijk niet beschikbaar voor alle resourcetypen. Wanneer de ondersteuning wordt ondersteund, worden [door Azure Monitor ondersteunde statistieken](metrics-supported.md) weergegeven welke statistieken worden verzameld voor welke resourcetypen.

       > [!NOTE]
       > Het verzenden van multidimensionale metrische gegevens via diagnostische instellingen wordt momenteel niet ondersteund. Metrische gegevens met dimensies worden geëxporteerd als platte eendimensionale metrische gegevens, als totaal van alle dimensiewaarden.
       >
       > *Bijvoorbeeld:* De statistiek 'IOReadBytes' op een Blockchain kan worden verkend en in kaart gebracht op een niveau per knooppunt. Wanneer de geëxporteerde metrische instellingen echter via diagnostische instellingen worden geëxporteerd, wordt deze weergegeven als alle gelezen bytes voor alle knooppunten.

     - **Logboeken** bevatten de verschillende categorieën die beschikbaar zijn, afhankelijk van het resourcetype. Controleer alle categorieën die u naar een bestemming wilt leiden.

5. **Bestemmingsgegevens** - Schakel het selectievakje voor elke bestemming in. Wanneer u elk vakje inschakelt, worden er opties weergegeven waarmee u extra informatie toevoegen.

      ![Verzenden naar Log Analytics of Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** - Voer het abonnement en de werkruimte in.  Als u geen werkruimte hebt, moet u [er een maken voordat u verdergaat.](../learn/quick-create-workspace.md)

    1. **Gebeurtenishubs** - Geef de volgende criteria op:
       - Het abonnement waar de event hub deel van uitmaakt
       - De naamruimte van de gebeurtenishub - Als u er nog geen hebt, moet u [er een maken](../../event-hubs/event-hubs-create.md)
       - Een gebeurtenishubnaam (optioneel) om alle gegevens naar te verzenden. Als u geen naam opgeeft, wordt voor elke logboekcategorie een gebeurtenishub gemaakt. Als u meerdere categorieën verzendt, u een naam opgeven om het aantal gemaakte gebeurtenishubs te beperken. Zie quota en limieten voor [Azure Event Hubs](../../event-hubs/event-hubs-quotas.md) voor meer informatie.
       - Een eventhubbeleid (optioneel) Een beleid definieert de machtigingen die het streamingmechanisme heeft. Zie [Event-hubs-functies](../../event-hubs/event-hubs-features.md#publisher-policy)voor meer informatie.

    1. **Opslag** - Kies het abonnements-, opslagaccount en bewaarbeleid.

        ![Verzenden naar opslag](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > U het bewaarbeleid instellen op 0 en uw gegevens handmatig verwijderen uit de opslag met behulp van een geplande taak om mogelijke verwarring in de toekomst te voorkomen.
        >
        > Ten eerste, als u opslag gebruikt voor archivering, wilt u uw gegevens over het algemeen langer dan 365 dagen in de buurt hebben. Ten tweede, als u een bewaarbeleid kiest dat groter is dan 0, is de vervaldatum gekoppeld aan de logboeken op het moment van opslag. U de datum voor die logboeken niet wijzigen nadat deze zijn opgeslagen.
        >
        > Als u bijvoorbeeld het bewaarbeleid voor *WorkflowRuntime* instelt op 180 dagen en vervolgens 24 uur later instelt op 365 dagen, worden de logboeken die tijdens die eerste 24 uur zijn opgeslagen, na 180 dagen automatisch verwijderd, terwijl alle volgende logboeken van dat type na 365 dagen automatisch worden verwijderd. Als u het bewaarbeleid later wijzigt, blijven de eerste 24 uur logboeken niet 365 dagen rond.

6. Klik op **Opslaan**.

Na enkele ogenblikken wordt de nieuwe instelling weergegeven in uw lijst met instellingen voor deze bron en worden logboeken naar de opgegeven bestemmingen gestreamd wanneer nieuwe gebeurtenisgegevens worden gegenereerd. Het kan tot 15 minuten duren tussen het moment waarop een gebeurtenis wordt uitgezonden en wanneer deze [wordt weergegeven in een werkruimte van Log Analytics.](data-ingestion-time.md)

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

- [Lees meer over Azure-platformlogboeken](platform-logs-overview.md)
