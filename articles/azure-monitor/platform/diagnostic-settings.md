---
title: Diagnostische instellingen gebruiken voor het verzamelen van metrische gegevens over het platform en logboeken en in azure
description: Verzend Azure Monitor platform metrische gegevens en logboeken naar Azure Monitor-logboeken, Azure-opslag of Azure Event Hubs met een diagnostische instelling.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2020
ms.subservice: logs
ms.openlocfilehash: 46dd7949dde1890035053a7a985f2f1d921e141e
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266658"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Diagnostische instelling maken voor het verzamelen van bron logboeken en metrische gegevens in azure

[Platform logboeken](platform-logs-overview.md) in azure, met inbegrip van het Azure-activiteiten logboek en de resource logboeken, bieden gedetailleerde diagnostische en controle-informatie voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn. [Metrische platform gegevens](data-platform-metrics.md) worden standaard verzameld en worden meestal opgeslagen in de data base met Azure monitor gegevens.

In dit artikel vindt u informatie over het maken en configureren van diagnostische instellingen voor het verzenden van platform metrieken en platform logboeken naar verschillende bestemmingen.

> [!IMPORTANT]
> Voordat u een diagnostische instelling maakt om het activiteiten logboek te verzamelen, moet u eerst alle verouderde configuratie uitschakelen. Zie [Azure-activiteiten logboek verzamelen met verouderde instellingen](diagnostic-settings-legacy.md) voor meer informatie.

Elke Azure-resource vereist een eigen diagnostische instelling, waarmee de volgende criteria worden gedefinieerd:

- Categorieën van logboeken en metrische gegevens die worden verzonden naar de doelen die in de instelling zijn gedefinieerd. De beschikbare categorieën verschillen per resourcetype.
- Een of meer doelen voor het verzenden van de logboeken. Huidige doelen omvatten Log Analytics-werkruimte, Event Hubs en Azure Storage.

Een enkele diagnostische instelling kan niet meer dan een van de doelen definiëren. Als u gegevens wilt verzenden naar meer dan één bepaald type doel (bijvoorbeeld twee verschillende Log Analytics-werkruimten), maakt u meerdere instellingen. Elke resource kan maximaal vijf diagnostische instellingen hebben.

> [!NOTE]
> De [metrische gegevens](metrics-supported.md) van het platform worden automatisch verzameld om [Azure monitor metrische gegevens](data-platform-metrics.md). Diagnostische instellingen kunnen worden gebruikt voor het verzamelen van metrische gegevens voor bepaalde Azure-Services in Azure Monitor-logboeken voor analyse met andere bewakings informatie met behulp van [logboek query's](../log-query/log-query-overview.md) met bepaalde beperkingen. 
>  
>  
> Het verzenden van multidimensionale metrische gegevens via diagnostische instellingen wordt momenteel niet ondersteund. Metrische gegevens met dimensies worden geëxporteerd als platte eendimensionale metrische gegevens, als totaal van alle dimensiewaarden. *Bijvoorbeeld*: de metrische waarde ' IOReadBytes ' op een Block Chain kan worden verkend en gediagrameerd op basis van een niveau per knoop punt. Wanneer de metrische gegevens echter worden geëxporteerd via Diagnostische instellingen, worden alle bytes gelezen voor alle knoop punten. Als gevolg van interne beperkingen zijn niet alle metrische gegevens exporteerbaar voor Azure Monitor logboeken/Log Analytics. Zie de [lijst met Exporteer bare metrische](metrics-supported-export-diagnostic-settings.md)gegevens voor meer informatie. 
>  
>  
> Om deze beperkingen voor specifieke metrische gegevens te omzeilen, wordt u aangeraden deze hand matig te extra heren met behulp van de [metrische gegevens rest API](https://docs.microsoft.com/rest/api/monitor/metrics/list) en deze te importeren in azure monitor logboeken met behulp van de [Azure Monitor Data Collector-API](data-collector-api.md).  

## <a name="destinations"></a>Bestemmingen

Platform-logboeken en-metrische gegevens kunnen worden verzonden naar de doelen in de volgende tabel. Volg elke koppeling in de volgende tabel voor meer informatie over het verzenden van gegevens naar deze bestemming.

| Doel | Beschrijving |
|:---|:---|
| [Log Analytics-werkruimte](resource-logs-collect-workspace.md) | Door Logboeken en metrische gegevens in een Log Analytics-werk ruimte te verzamelen, kunt u ze analyseren met andere bewakings informatie die door Azure Monitor wordt verzameld met behulp van krachtige logboek query's en ook om gebruik te maken van andere Azure Monitor functies, zoals waarschuwingen en visualisaties. |
| [Event hubs](resource-logs-stream-event-hubs.md) | Door Logboeken en metrische gegevens naar Event Hubs te verzenden, kunt u met externe systemen, zoals Siem's van derden en andere log Analytics-oplossingen. |
| [Azure-opslag account](resource-logs-collect-storage.md) | Het archiveren van Logboeken en metrische gegevens naar een Azure-opslag account is handig voor controle, statische analyses of back-ups. Vergeleken met Azure Monitor-logboeken en een Log Analytics-werk ruimte is Azure Storage minder kostbaar en kunnen de logboeken voor onbepaalde tijd worden bewaard. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Diagnostische instellingen maken in Azure-portal

U kunt Diagnostische instellingen configureren in de Azure Portal in het menu Azure Monitor of in het menu voor de resource.

1. Waar u de diagnostische instellingen in de Azure Portal configureert, is afhankelijk van de resource.

   - Voor één resource klikt u op **Diagnostische instellingen** onder **monitor** in het resource menu.

        ![Diagnostische instellingen](media/diagnostic-settings/menu-resource.png)

   - Klik voor een of meer resources op **Diagnostische instellingen** onder **instellingen** in het menu Azure monitor en klik vervolgens op de resource.

      ![Diagnostische instellingen](media/diagnostic-settings/menu-monitor.png)

   - Klik voor het activiteiten logboek op **activiteiten logboek** in het menu **Azure monitor** en vervolgens op **Diagnostische instellingen**. Zorg ervoor dat u alle verouderde configuraties voor het activiteiten logboek uitschakelt. Zie [bestaande instellingen uitschakelen](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) voor meer informatie.

        ![Diagnostische instellingen](media/diagnostic-settings/menu-activity-log.png)

2. Als er geen instellingen bestaan op de resource die u hebt geselecteerd, wordt u gevraagd een instelling te maken. Klik op **Diagnostische instelling toevoegen**.

   ![Diagnostische instelling toevoegen-geen bestaande instellingen](media/diagnostic-settings/add-setting.png)

   Als er bestaande instellingen zijn voor de resource, ziet u een lijst met instellingen die al zijn geconfigureerd. Klik op **Diagnostische instelling toevoegen** om een nieuwe instelling of **Bewerk instelling** toe te voegen die u kunt bewerken. Elke instelling kan niet meer dan één van de doel typen hebben.

   ![Diagnostische instelling toevoegen-bestaande instellingen](media/diagnostic-settings/edit-setting.png)

3. Geef een naam op voor uw instelling als deze nog niet aanwezig is.

    ![Diagnostische instelling toevoegen](media/diagnostic-settings/setting-new-blank.png)

4. **Categorie Details (wat u moet omleiden)** : Schakel het selectie vakje in voor elke gegevens categorie die u later wilt verzenden naar de opgegeven locaties. De lijst met categorieën varieert voor elke Azure-service.

     - **AllMetrics** routeert de platform metrieken van een resource in de Azure logs Store, maar in de logboek vorm. Deze metrische gegevens worden doorgaans alleen verzonden naar de data base van de time-series van Azure Monitor metrische gegevens. Deze te verzenden naar de Azure Monitor logboeken Store (die kan worden doorzocht via Log Analytics) waarmee u ze kunt integreren in query's die in andere logboeken zoeken. Deze optie is mogelijk niet beschikbaar voor alle resource typen. Als dit wordt ondersteund, wordt door [Azure monitor ondersteunde metrische gegevens](metrics-supported.md) weer gegeven welke metrische gegevens worden verzameld voor welke resource typen.

       > [!NOTE]
       > Zie limitatation voor routerings metrieken voor Azure Monitor logboeken eerder in dit artikel.  


     - In **Logboeken** worden de verschillende beschik bare categorieën weer gegeven, afhankelijk van het resource type. Controleer alle categorieën die u wilt omleiden naar een bestemming.

5. **Doel Details** : Schakel het selectie vakje voor elke bestemming in. Wanneer u elk selectie vakje inschakelt, worden er opties weer gegeven om extra informatie toe te voegen.

      ![Verzenden naar Log Analytics of Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** : Voer het abonnement en de werk ruimte in.  Als u geen werk ruimte hebt, moet u [er een maken voordat u doorgaat](../learn/quick-create-workspace.md).

    1. **Event hubs** -Geef de volgende criteria op:
       - Het abonnement waarvan de Event Hub deel uitmaakt
       - De Event hub-naam ruimte: als u deze nog niet hebt, moet u [er een maken](../../event-hubs/event-hubs-create.md)
       - Een event hub-naam (optioneel) voor het verzenden van alle gegevens naar. Als u geen naam opgeeft, wordt er een Event Hub voor elke logboek categorie gemaakt. Als u meerdere categorieën verzendt, wilt u mogelijk een naam opgeven om het aantal gemaakte Event hubs te beperken. Zie [Azure Event hubs quota's en limieten](../../event-hubs/event-hubs-quotas.md) voor meer informatie.
       - Een event hub-beleid (optioneel) een beleid definieert de machtigingen die het streaming-mechanisme heeft. Zie [Event-hubs-features (](../../event-hubs/event-hubs-features.md#publisher-policy)Engelstalig) voor meer informatie.

    1. **Opslag** : Kies het abonnement, het opslag account en het Bewaar beleid.

        ![Verzenden naar opslag](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Overweeg het Bewaar beleid in te stellen op 0 en hand matig de gegevens uit de opslag te verwijderen met behulp van een geplande taak om mogelijke Verwar ring in de toekomst te voor komen.
        >
        > Als u opslag gebruikt voor archivering, wilt u in het algemeen uw gegevens over meer dan 365 dagen gebruiken. Ten tweede, als u een Bewaar beleid kiest dat groter is dan 0, wordt de verval datum gekoppeld aan de logboeken op het moment van opslag. U kunt de datum voor deze logboeken niet wijzigen nadat deze is opgeslagen.
        >
        > Als u bijvoorbeeld het Bewaar beleid voor de *WorkflowRuntime* instelt op 180 dagen en vervolgens 24 uur later instelt op 365 dagen, worden de logboeken die zijn opgeslagen tijdens de eerste 24 uur automatisch verwijderd na 180 dagen, terwijl alle volgende logboeken van dat type automatisch worden verwijderd na 365 dagen. Als u het retentie beleid later wijzigt, blijven de eerste 24 uur aan logboeken gedurende 365 dagen bewaard.

6. Klik op **Opslaan**.

Na enkele ogen blikken wordt de nieuwe instelling weer gegeven in de lijst met instellingen voor deze resource en worden logboeken naar de opgegeven doelen gestreamd wanneer er nieuwe gebeurtenis gegevens worden gegenereerd. Het kan tot vijf tien minuten duren voordat een gebeurtenis wordt verzonden en wanneer deze [in een log Analytics-werk ruimte wordt weer gegeven](data-ingestion-time.md).

## <a name="create-diagnostic-settings-using-powershell"></a>Diagnostische instellingen maken met behulp van Powershell

Gebruik de cmdlet [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) om een diagnostische instelling met [Azure PowerShell](powershell-quickstart-samples.md)te maken. Raadpleeg de documentatie voor deze cmdlet voor beschrijvingen van de para meters.

> [!IMPORTANT]
> U kunt deze methode niet gebruiken voor het Azure-activiteiten logboek. Gebruik in plaats daarvan [Diagnostische instelling maken in azure monitor met behulp van een resource manager-sjabloon](diagnostic-settings-template.md) om een resource manager-sjabloon te maken en te implementeren met Power shell.

Hieronder volgt een voor beeld van een Power shell-cmdlet om een diagnostische instelling te maken met behulp van alle drie de bestemmingen.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-diagnostic-settings-using-azure-cli"></a>Diagnostische instellingen maken met behulp van Azure CLI

Gebruik de opdracht [AZ monitor Diagnostic-settings Create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) om een diagnostische instelling te maken met [Azure cli](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Raadpleeg de documentatie voor deze opdracht voor beschrijvingen van de para meters.

> [!IMPORTANT]
> U kunt deze methode niet gebruiken voor het Azure-activiteiten logboek. Gebruik in plaats daarvan [Diagnostische instelling maken in azure monitor met behulp van een resource manager-sjabloon](diagnostic-settings-template.md) om een resource manager-sjabloon te maken en te implementeren met cli.

Hier volgt een voor beeld van een CLI-opdracht om een diagnostische instelling te maken met behulp van alle drie de bestemmingen.

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

## <a name="configure-diagnostic-settings-using-rest-api"></a>Diagnostische instellingen configureren met behulp van REST API

Zie [Diagnostische instellingen](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) voor het maken of bijwerken van diagnostische instellingen met behulp van de [Azure monitor rest API](https://docs.microsoft.com/rest/api/monitor/).

## <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Diagnostische instellingen configureren met Resource Manager-sjabloon

Zie [Diagnostische instelling maken in azure monitor met behulp van een resource manager-sjabloon](diagnostic-settings-template.md) voor het maken of bijwerken van diagnostische instellingen met een resource manager-sjabloon.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Azure platform-logboeken](platform-logs-overview.md)
