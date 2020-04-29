---
title: Azure-activiteiten logboek in Azure Monitor verzamelen en analyseren
description: Verzamel het Azure-activiteiten logboek in Azure Monitor logboeken en gebruik de bewakings oplossing om het Azure-activiteiten logboek te analyseren en doorzoeken op al uw Azure-abonnementen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382858"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Azure-activiteiten logboek in Azure Monitor verzamelen en analyseren
Het [Azure-activiteiten logboek](platform-logs-overview.md) is een [platform logboek](platform-logs-overview.md) dat inzicht biedt in gebeurtenissen op abonnements niveau die zich in azure hebben voorgedaan. U kunt het activiteiten logboek weer geven in de Azure Portal door het te configureren voor verzen ding naar een Log Analytics-werk ruimte om aanvullende functies van Azure Monitor in te scha kelen. In dit artikel wordt beschreven hoe u deze configuratie uitvoert en hoe u het activiteiten logboek verzendt naar Azure Storage en Event hubs.

Het verzamelen van het activiteiten logboek in een Log Analytics werk ruimte biedt de volgende voor delen:

- Geen gegevens opname of gegevens retentie voor activiteiten logboek gegevens die zijn opgeslagen in een Log Analytics-werk ruimte.
- Correleer activiteiten logboek gegevens met andere bewakings gegevens die zijn verzameld door Azure Monitor.
- Gebruik logboek query's om complexe analyses uit te voeren en uitgebreide inzichten te verkrijgen over activiteiten logboek vermeldingen.
- Gebruik logboek waarschuwingen met activiteiten vermeldingen die meer complexe logica voor waarschuwingen toestaan.
- Vermeldingen in het activiteiten logboek worden langer dan 90 dagen bewaard.
- Consolideer logboek vermeldingen van meerdere Azure-abonnementen en-tenants naar één locatie voor analyse tegelijk.

> [!IMPORTANT]
> Voor het verzamelen van Logboeken tussen tenants is [Azure Lighthouse](/azure/lighthouse)vereist.

## <a name="collecting-activity-log"></a>Activiteiten logboek verzamelen
Het activiteiten logboek wordt automatisch verzameld voor [weer gave in de Azure Portal](activity-log-view.md). Als u deze wilt verzamelen in een Log Analytics-werk ruimte of als u Azure Storage of event hubs wilt verzenden, maakt u een [Diagnostische instelling](diagnostic-settings.md). Dit is dezelfde methode die wordt gebruikt door bron logboeken, zodat deze consistent is voor alle [platform logboeken](platform-logs-overview.md).  

Als u een diagnostische instelling voor het activiteiten logboek wilt maken, selecteert u **Diagnostische instellingen** in het menu **activiteiten logboek** in azure monitor. Zie [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](diagnostic-settings.md) voor meer informatie over het maken van de instelling. Zie [Categorieën in het activiteiten logboek](activity-log-view.md#categories-in-the-activity-log) voor een beschrijving van de categorieën die u kunt filteren. Als u verouderde instellingen hebt, moet u deze uitschakelen voordat u een diagnostische instelling maakt. Als beide zijn ingeschakeld, kan dit leiden tot dubbele gegevens.

![Diagnostische instellingen](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> Op dit moment kunt u alleen een diagnostische instelling op abonnements niveau maken met behulp van de Azure Portal en een resource manager-sjabloon. 


## <a name="legacy-settings"></a>Oude instellingen 
Diagnostische instellingen zijn de voorkeurs methode voor het verzenden van het activiteiten logboek naar verschillende bestemmingen. verouderde methoden blijven werken als u niet wilt vervangen door een diagnostische instelling. Diagnostische instellingen hebben de volgende voor delen ten opzichte van oudere methoden en het wordt aanbevolen om uw configuratie bij te werken:

- Consistente methode voor het verzamelen van alle platform Logboeken.
- Verzamel activiteiten logboek over meerdere abonnementen en tenants.
- Filter verzameling om alleen logboeken voor bepaalde categorieën te verzamelen.
- Verzamel alle activiteiten logboek categorieën. Sommige categorieën worden niet verzameld met een verouderde methode.
- Snellere latentie voor opname van Logboeken. De vorige methode heeft ongeveer 15 minuten latentie terwijl Diagnostische instellingen meer dan 1 minuut toevoegen.



### <a name="log-profiles"></a>Logboek profielen
Logboek profielen zijn de verouderde methode voor het verzenden van het activiteiten logboek naar Azure Storage of event hubs. Gebruik de volgende procedure om met een logboek profiel te blijven werken of om het uit te scha kelen als voor bereiding op het migreren naar een diagnostische instelling.

1. Selecteer in het menu **Azure monitor** van de Azure Portal het **activiteiten logboek**.
3. Klik op **Diagnostische instellingen**.

   ![Diagnostische instellingen](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klik op de banner paars voor de oude ervaring.

    ![Verouderde ervaring](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Log Analytics-werkruimte
De verouderde methode voor het verzamelen van het activiteiten logboek in een Log Analytics-werk ruimte is het koppelen van het logboek in de werkruimte configuratie. 

1. Selecteer in het menu **log Analytics werk ruimten** in de Azure Portal de werk ruimte om het activiteiten logboek te verzamelen.
1. Selecteer in de sectie **werkruimte gegevens bronnen** van het menu van de werk ruimte **Azure-activiteiten logboek**.
1. Klik op het abonnement dat u wilt verbinden.

    ![Workspaces](media/activity-log-collect/workspaces.png)

1. Klik op **verbinden** om het activiteiten logboek in het abonnement te verbinden met de geselecteerde werk ruimte. Als het abonnement al is verbonden met een andere werk ruimte, klikt u eerst op **verbinding verbreken** om de verbinding te verbreken.

    ![Werk ruimten verbinden](media/activity-log-collect/connect-workspace.png)


Als u de instelling wilt uitschakelen, voert u dezelfde procedure uit en klikt u op **verbinding verbreken** om het abonnement uit de werk ruimte te verwijderen.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Activiteiten logboek in Log Analytics werk ruimte analyseren
Wanneer u een activiteiten logboek verbindt met een Log Analytics-werk ruimte, worden er items naar de werk ruimte geschreven in een tabel met de naam *AzureActivity* die u met een [logboek query](../log-query/log-query-overview.md)kunt ophalen. De structuur van deze tabel is afhankelijk van de [categorie van de logboek vermelding](activity-log-view.md#categories-in-the-activity-log). Zie [Azure activiteiten logboek gebeurtenis schema](activity-log-schema.md) voor een beschrijving van elke categorie.


### <a name="data-structure-changes"></a>Wijzigingen in de gegevens structuur
Diagnostische instellingen verzamelen dezelfde gegevens als de oude methode die wordt gebruikt voor het verzamelen van het activiteiten logboek met enkele wijzigingen in de structuur van de tabel *AzureActivity* .

De kolommen in de volgende tabel zijn afgeschaft in het bijgewerkte schema. Ze zijn nog steeds aanwezig in *AzureActivity* , maar ze hebben geen gegevens. De vervanging voor deze kolommen is niet nieuw, maar bevat dezelfde gegevens als de afgeschafte kolom. Ze hebben een andere indeling, dus u moet mogelijk logboek query's wijzigen die deze gebruiken. 

| Afgeschafte kolom | Vervangende kolom |
|:---|:---|
| Activity status    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> In sommige gevallen kunnen de waarden in deze kolommen in hoofd letters worden getypt. Als u een query hebt die deze kolommen bevat, moet u de [operator = ~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) gebruiken om een niet-hoofdletter gevoelige vergelijking te maken.

De volgende kolom zijn toegevoegd aan *AzureActivity* in het bijgewerkte schema:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Bewakings oplossing voor activiteiten logboeken
De bewakings oplossing voor Azure Log Analytics wordt binnenkort afgeschaft en vervangen door een werkmap met het bijgewerkte schema in de Log Analytics-werk ruimte. U kunt de oplossing blijven gebruiken als u deze al hebt ingeschakeld, maar deze kan alleen worden gebruikt als u het activiteiten logboek verzamelt met behulp van verouderde instellingen. 



### <a name="use-the-solution"></a>De oplossing gebruiken
Bewakings oplossingen zijn toegankelijk via het menu **monitor** in de Azure Portal. Selecteer **meer** in het gedeelte **inzichten** om de **overzichts** pagina te openen met de oplossings tegels. De tegel **activiteiten logboeken van Azure** bevat een telling van het aantal **AzureActivity** -records in uw werk ruimte.

![Tegel Azure-activiteiten logboeken](media/collect-activity-logs/azure-activity-logs-tile.png)


Klik op de tegel **Azure-activiteiten logboeken** om de weer gave **Azure-activiteiten logboeken** te openen. De weer gave bevat de visualisatie onderdelen in de volgende tabel. Elk onderdeel bevat Maxi maal 10 items die overeenkomen met de criteria van die onderdelen voor het opgegeven tijds bereik. U kunt een logboek query uitvoeren waarmee alle overeenkomende records worden geretourneerd door te klikken op **Alles bekijken** onder aan het onderdeel.

![Dash board Azure-activiteiten logboeken](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>De oplossing voor nieuwe abonnementen inschakelen
U kunt de activiteiten logboek analyse niet meer aan uw abonnement toevoegen met behulp van de Azure Portal. U kunt deze toevoegen aan de hand van de volgende procedure met een resource manager-sjabloon. 

1. Kopieer de volgende JSON naar een bestand met de naam *ActivityLogTemplate*. json.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Implementeer de sjabloon met behulp van de volgende Power shell-opdrachten:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [activiteiten logboek](platform-logs-overview.md).
- Meer informatie over het [Azure monitor-gegevens platform](data-platform.md).
- Gebruik [logboek query's](../log-query/log-query-overview.md) om gedetailleerde informatie uit uw activiteiten logboek weer te geven.
