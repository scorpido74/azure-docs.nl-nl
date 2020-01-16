---
title: Zelfstudie - Azure Firewall-logboeken en metrische gegevens bewaken
description: In deze zelfstudie leert u hoe u Azure Firewall-logboeken en metrische gegevens inschakelt en beheert.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/24/2018
ms.author: victorh
ms.openlocfilehash: da46cf826da40658883d22692e5038b09d222907
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974532"
---
# <a name="tutorial-monitor-azure-firewall-logs-and-metrics"></a>Zelfstudie: Azure Firewall-logboeken en metrische gegevens bewaken

U kunt Azure Firewall bewaken met behulp van firewall-logboeken. U kunt ook activiteitenlogboeken gebruiken om bewerkingen in Azure Firewall-resources te controleren. Met metrische gegevens kunt u prestatiemeteritems in de portal zien.

Via de portal kunt u toegang verkrijgen tot sommige van deze logboeken. Logboeken kunnen worden verzonden naar [Azure Monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md), Storage en Event Hubs en kunnen worden geanalyseerd in Azure Monitor-logboeken of door verschillende hulpprogramma's zoals Excel en Power BI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Logboekregistratie inschakelen via de Azure-portal
> * Logboekregistratie inschakelen met PowerShell
> * Het activiteitenlogboek bekijken en analyseren
> * De logboeken voor netwerk- en toepassingsregels bekijken en analyseren
> * Metrische gegevens bekijken


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, leest u de [Azure Firewall-logboeken en metrische gegevens](logs-and-metrics.md) voor een overzicht van de diagnostische logboek en metrische gegevens voor Azure Firewall.


## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Diagnostische logboekregistratie inschakelen via de Azure-portal

Nadat u deze procedure voor het inschakelen van diagnostische logboekregistratie hebt voltooid, kan het enkele minuten duren voordat de gegevens in uw logboeken verschijnen. Als u aanvankelijk niets ziet, controleert u het een paar minuten later opnieuw.

1. Open in de Azure-portal uw firewall-resourcegroep en klik op de firewall.
2. Klik onder **bewaking**op **Diagnostische instellingen**.

   Voor Azure Firewall zijn er twee servicespecifieke logboeken beschikbaar:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. Klik op **Diagnostische gegevens inschakelen** om te beginnen met het verzamelen van gegevens.
4. Op de pagina **Diagnostische instellingen** staan de instellingen voor de diagnostische logboeken.
5. In dit voorbeeld worden de logboeken in Azure Monitor-logboeken opgeslagen, dus typ **Firewall Log Analytics** als naam.
6. Klik op **Verzenden naar Log Analytics** om uw werkruimte te configureren. U kunt ook Event Hubs en een opslagaccount gebruiken om de diagnostische logboeken op te slaan.
7. Klik onder **Log Analytics** op **Configureren**.
8. Klik op de pagina Log Analytics-werkruimten op **Nieuwe werkruimte maken**.
9. Op de pagina **Log Analytics-werkruimte** typt u **firewall-oms** als naam voor de nieuwe **Log Analytics-werkruimte**.
10. Selecteer uw abonnement, gebruik de bestaande firewall-resourcegroep (**Test-FW-RG**), selecteer **VS - oost** als locatie en selecteer de prijscategorie **Gratis**.
11. Klik op **OK**.
   ![Het configuratieproces starten][1] OMS-werkruimten worden nu aangeduid als Log Analytics-werkruimten.  
12. Klik onder **Logboek** op **AzureFirewallApplicationRule** en **AzureFirewallNetworkRule** om logboeken voor toepassings- en netwerkregels te verzamelen.
   ![Diagnostische instellingen opslaan][2]
13. Klik op **Opslaan**.

## <a name="enable-logging-with-powershell"></a>Logboekregistratie inschakelen met PowerShell

Activiteitenlogboekregistratie is automatisch ingeschakeld voor elke Resource Manager-resource. Diagnostische logboekregistratie moet worden ingeschakeld om te beginnen met het verzamelen van de gegevens die beschikbaar zijn via die logboeken.

Volg de onderstaande stappen om diagnostische logboekregistratie in te schakelen:

1. Noteer de resource-ID van uw opslagaccount waar de logboekgegevens worden opgeslagen. Deze waarde heeft de volgende indeling: */abonnementen/\<subscriptionId\>/resourceGroups/\<resourcegroepnaam\>/providers/Microsoft.Storage/storageAccounts/\<opslagaccountnaam\>* .

   U kunt elk opslagaccount in uw abonnement gebruiken. U kunt de Azure-portal gebruiken om deze informatie te vinden. De informatie staat op de pagina **Eigenschap** van de resource.

2. Noteer de resource-ID van uw firewall waarvoor logboekregistratie is ingeschakeld. Deze waarde heeft de volgende indeling: */abonnementen/\<subscriptionId\>/resourceGroups/\<resourcegroepnaam\>/providers/Microsoft.Network/azureFirewalls/\<firewallnaam\>* .

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

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>De logboeken voor netwerk- en toepassingsregels bekijken en analyseren

[Azure Monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md) verzamelt de teller- en gebeurtenislogboekbestanden. Het omvat visualisaties en krachtige zoekmogelijkheden om uw logboeken te analyseren.

Zie [Voorbeelden van Azure Firewall Log Analytics](log-analytics-samples.md) voor voorbeeldquery’s van Azure Firewall Log Analytics.

U kunt ook verbinding maken met uw opslagaccount en de JSON-logboekitems voor toegangs- en prestatielogboeken ophalen. Nadat u de JSON-bestanden hebt gedownload, kunt u ze naar de CSV-indeling converteren en in Excel, Power BI of een ander hulpprogramma voor gegevensvisualisatie bekijken.

> [!TIP]
> Als u bekend bent met Visual Studio en de basisconcepten van het wijzigen van waarden voor constanten en variabelen in C#, kunt u de [logboekconversieprogramma’s](https://github.com/Azure-Samples/networking-dotnet-log-converter) gebruiken die beschikbaar zijn in GitHub.

## <a name="view-metrics"></a>Metrische gegevens bekijken
Ga naar een Azure Firewall via **Bewaking** en klik op **Metrische gegevens**. Om de beschikbare waarden te zien, selecteert u de vervolgkeuzelijst **METRISCH**.

## <a name="next-steps"></a>Volgende stappen

Nu u uw firewall hebt geconfigureerd om logboeken te verzamelen, kunt u Azure Monitor-logboeken verkennen om uw gegevens te bekijken.

> [!div class="nextstepaction"]
> [Netwerkbewakingsoplossingen in Azure Monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
