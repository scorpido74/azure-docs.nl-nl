---
title: Azure Firewall-logboeken en metrische gegevens bewaken
description: In dit artikel leert u hoe u Azure Firewall logboeken en metrische gegevens kunt inschakelen en beheren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/17/2020
ms.author: victorh
ms.openlocfilehash: 784459282007edab599d54edff0d2b38eed07b34
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320639"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Azure Firewall-logboeken en metrische gegevens bewaken

U kunt Azure Firewall bewaken met behulp van firewall-logboeken. U kunt ook activiteitenlogboeken gebruiken om bewerkingen in Azure Firewall-resources te controleren. Met metrische gegevens kunt u prestatiemeteritems in de portal zien.

Via de portal kunt u toegang verkrijgen tot sommige van deze logboeken. Logboeken kunnen worden verzonden naar [Azure monitor logboeken](../azure-monitor/insights/azure-networking-analytics.md), opslag en Event hubs en worden geanalyseerd in azure monitor Logboeken of door verschillende hulpprogram Ma's zoals Excel en Power bi.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Lees voordat u begint [Azure firewall logboeken en metrische gegevens](logs-and-metrics.md) voor een overzicht van de diagnostische logboeken en metrische gegevens die beschikbaar zijn voor Azure firewall.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Diagnostische logboekregistratie inschakelen via de Azure-portal

Nadat u deze procedure voor het inschakelen van diagnostische logboekregistratie hebt voltooid, kan het enkele minuten duren voordat de gegevens in uw logboeken verschijnen. Als u aanvankelijk niets ziet, controleert u het een paar minuten later opnieuw.

1. Open in de Azure Portal de resource groep firewall en selecteer de firewall.
2. Selecteer **Diagnostische instellingen** onder **Controle**.

   Voor Azure Firewall zijn vier servicespecifieke logboeken beschikbaar:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallThreatIntelLog
   * AzureFirewallDnsProxy


3. Selecteer **Diagnostische instelling toevoegen**. Op de pagina **Diagnostische instellingen** staan de instellingen voor de diagnostische logboeken.
5. In dit voorbeeld worden de logboeken in Azure Monitor-logboeken opgeslagen, dus typ **Firewall Log Analytics** als naam.
6. Selecteer **onder logboek**de optie **AzureFirewallApplicationRule**, **AzureFirewallNetworkRule**, **AzureFirewallThreatIntelLog**en **AzureFirewallDnsProxy** om de logboeken te verzamelen.
7. Selecteer **verzenden naar log Analytics** om uw werk ruimte te configureren.
8. Selecteer uw abonnement.
9. Selecteer **Opslaan**.

## <a name="enable-logging-with-powershell"></a>Logboekregistratie inschakelen met PowerShell

Activiteitenlogboekregistratie is automatisch ingeschakeld voor elke Resource Manager-resource. Diagnostische logboekregistratie moet worden ingeschakeld om te beginnen met het verzamelen van de gegevens die beschikbaar zijn via die logboeken.

Volg de onderstaande stappen om diagnostische logboekregistratie in te schakelen:

1. Noteer de resource-ID van uw opslagaccount waar de logboekgegevens worden opgeslagen. Deze waarde is van het formulier: */Subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Storage/storageAccounts/ \<storage account name\> *.

   U kunt elk opslagaccount in uw abonnement gebruiken. U kunt de Azure-portal gebruiken om deze informatie te vinden. De informatie staat op de pagina **Eigenschap** van de resource.

2. Noteer de resource-ID van uw firewall waarvoor logboekregistratie is ingeschakeld. Deze waarde is van het formulier: */Subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Network/azureFirewalls/ \<Firewall name\> *.

   U kunt de portal gebruiken om deze informatie te vinden.

3. Schakel diagnostische logboekregistratie in door de volgende PowerShell-cmdlet te gebruiken:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```

> [!TIP]
>Voor diagnostische logboeken is geen apart opslagaccount nodig. Voor het gebruik van opslag voor toegangs- en prestatielogboeken worden servicekosten in rekening gebracht.

## <a name="view-and-analyze-the-activity-log"></a>Het activiteitenlogboek bekijken en analyseren

U kunt activiteitenlogboekgegevens bekijken en analyseren via een van de volgende methoden:

* **Azure-hulpprogramma’s**: Haal informatie uit het activiteitenlogboek op via Azure PowerShell, de Azure CLI, de Azure REST-API of de Azure-portal. In het artikel [Activiteitsbewerkingen met Resource Manager](../azure-resource-manager/management/view-activity-logs.md) staan stapsgewijze instructies voor elke methode.
* **Power BI**: Als u nog geen [Power BI](https://powerbi.microsoft.com/pricing)-account hebt, kunt u het gratis uitproberen. Door het [inhoudspakket voor Azure-activiteitenlogboeken voor Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) te gebruiken, kunt u uw gegevens analyseren met vooraf geconfigureerde dashboards die u kunt gebruiken zoals geleverd of kunt aanpassen.
* **Azure-Sentinel**: u kunt Azure firewall-logboeken verbinden met Azure-Sentinel, zodat u logboek gegevens in werkmappen weer geven, ze kunt gebruiken om aangepaste waarschuwingen te maken en deze op te nemen om uw onderzoek te verbeteren. De Azure Firewall gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview. Zie [verbinding maken tussen gegevens van Azure firewall](../sentinel/connect-azure-firewall.md)voor meer informatie.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>De logboeken voor netwerk- en toepassingsregels bekijken en analyseren

[Azure Monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md) verzamelt de teller- en gebeurtenislogboekbestanden. Het omvat visualisaties en krachtige zoekmogelijkheden om uw logboeken te analyseren.

Zie [Azure Firewall log Analytics](log-analytics-samples.md)-voor beelden voor Azure Firewall log Analytics-voorbeeld query's.

U kunt ook verbinding maken met uw opslagaccount en de JSON-logboekitems voor toegangs- en prestatielogboeken ophalen. Nadat u de JSON-bestanden hebt gedownload, kunt u ze naar de CSV-indeling converteren en in Excel, Power BI of een ander hulpprogramma voor gegevensvisualisatie bekijken.

> [!TIP]
> Als u bekend bent met Visual Studio en de basisconcepten van het wijzigen van waarden voor constanten en variabelen in C#, kunt u de [logboekconversieprogramma’s](https://github.com/Azure-Samples/networking-dotnet-log-converter) gebruiken die beschikbaar zijn in GitHub.

## <a name="view-metrics"></a>Metrische gegevens bekijken
Blader naar een Azure Firewall en selecteer **metrische gegevens**onder **bewaking** . Om de beschikbare waarden te zien, selecteert u de vervolgkeuzelijst **METRISCH**.

## <a name="next-steps"></a>Volgende stappen

Nu u uw firewall hebt geconfigureerd om logboeken te verzamelen, kunt u Azure Monitor-logboeken verkennen om uw gegevens te bekijken.

[Netwerkbewakingsoplossingen in Azure Monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md)
