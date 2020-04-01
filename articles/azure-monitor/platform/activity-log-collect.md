---
title: Azure-activiteitenlogboek verzamelen en analyseren in Azure Monitor
description: Verzamel het Azure Activity Log in Azure Monitor Logs en gebruik de bewakingsoplossing om het Azure-activiteitenlogboek voor al uw Azure-abonnementen te analyseren en te doorzoeken.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/24/2020
ms.openlocfilehash: 4265f6050b237cb40afeddfc228ade9be06be039
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396790"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Azure Activity-logboek verzamelen en analyseren in Azure Monitor
Het [Azure Activity-logboek](platform-logs-overview.md) is een [platformlogboek](platform-logs-overview.md) dat inzicht biedt in gebeurtenissen op abonnementsniveau die zich in Azure hebben voorgedaan. Hoewel u het activiteitenlogboek in de Azure-portal bekijken, moet u het configureren om naar een werkruimte voor Log Analytics te verzenden om extra functies van Azure Monitor in te schakelen. In dit artikel wordt beschreven hoe u deze configuratie uitvoert en hoe u het activiteitenlogboek naar Azure-opslag- en gebeurtenishubs verzendt.

Het verzamelen van het activiteitenlogboek in een werkruimte loganalytics biedt de volgende voordelen:

- Er zijn geen kosten verbonden aan het innemen van gegevens of gegevensbewaarkosten voor activiteitslogboekgegevens die zijn opgeslagen in een loganalytics-werkruimte.
- Correleer gegevens over activiteitslogboeken met andere bewakingsgegevens die door Azure Monitor worden verzameld.
- Gebruik logboekquery's om complexe analyses uit te voeren en diepgaande inzichten te krijgen over items in het activiteitenlogboek.
- Gebruik logboekwaarschuwingen met activiteitsvermeldingen, zodat u complexere waarschuwingslogica instellen.
- Winkel activiteit logboekvermeldingen voor langer dan 90 dagen.
- Consolideer logboekitems van meerdere Azure-abonnementen en tenants in één locatie voor analyse samen.



## <a name="collecting-activity-log"></a>Logboek activiteit verzamelen
Het activiteitenlogboek wordt automatisch verzameld voor [weergave in de Azure-portal.](activity-log-view.md) Als u deze wilt verzamelen in een Log Analytics-werkruimte of azure-opslag- of gebeurtenishubs wilt verzenden, maakt u een [diagnostische instelling](diagnostic-settings.md). Dit is dezelfde methode die wordt gebruikt door resource logs waardoor het consistent is voor alle [platformlogs.](platform-logs-overview.md)  

Als u een diagnostische instelling voor het logboek Activiteit wilt maken, selecteert u **Diagnostische instellingen** in het menu **Activiteitslogboek** in Azure Monitor. Zie [Diagnostische instelling maken om platformlogboeken en -statistieken in Azure te verzamelen](diagnostic-settings.md) voor meer informatie over het maken van de instelling. Zie [Rubrieken in het logboek Activiteit](activity-log-view.md#categories-in-the-activity-log) voor een beschrijving van de categorieën die u filteren. Als u oudere instellingen hebt, moet u deze uitschakelen voordat u een diagnostische instelling maakt. Als beide zijn ingeschakeld, kunnen dubbele gegevens worden ingevoerd.

![Diagnostische instellingen](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> Momenteel u alleen een diagnostische instelling op abonnementsniveau maken met behulp van de Azure-portal en een resourcemanagersjabloon. 


## <a name="legacy-settings"></a>Verouderde instellingen 
Hoewel diagnostische instellingen de voorkeur hebben om het activiteitslogboek naar verschillende bestemmingen te verzenden, blijven oudere methoden werken als u niet kiest voor vervanging door een diagnostische instelling. Diagnostische instellingen hebben de volgende voordelen ten opzichte van oudere methoden en het wordt aanbevolen dat u uw configuratie bijwerkt:

- Consistente methode voor het verzamelen van alle platformlogs.
- Het logboek activiteit verzamelen over meerdere abonnementen en tenants.
- Filterverzameling om alleen logboeken voor bepaalde categorieën te verzamelen.
- Alle categorieën van activiteitenlogboeken verzamelen. Sommige categorieën worden niet verzameld met behulp van een verouderde methode.
- Snellere latentie voor het innemen van logboeken. De vorige methode heeft ongeveer 15 minuten latentie, terwijl diagnostische instellingen voegt slechts ongeveer 1 minuut.



### <a name="log-profiles"></a>Logboekprofielen
Logboekprofielen zijn de verouderde methode voor het verzenden van het activiteitslogboek naar Azure-opslag- of gebeurtenishubs. Gebruik de volgende procedure om te blijven werken met een logboekprofiel of om het uit te schakelen ter voorbereiding op het migreren naar een diagnostische instelling.

1. Selecteer **Activiteitslogboek**in het menu **Azure Monitor** in de Azure-portal .
3. Klik op **Diagnostische instellingen**.

   ![Diagnostische instellingen](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klik op de paarse banner voor de legacy-ervaring.

    ![Ervaring met legacy](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Log Analytics-werkruimte
De verouderde methode voor het verzamelen van het logboek activiteit in een werkruimte log Analytics verbindt het logboek in de werkruimteconfiguratie. 

1. Selecteer in het menu **Logboekanalyse-werkruimten** in de Azure-portal de werkruimte om het activiteitenlogboek te verzamelen.
1. Selecteer **azure-activiteitenlogboek**in de sectie **Werkruimtegegevensbronnen in** het menu van de werkruimte .
1. Klik op het abonnement dat u wilt aansluiten.

    ![Workspaces](media/activity-log-collect/workspaces.png)

1. Klik **op Verbinding maken** om het activiteitslogboek in het abonnement te verbinden met de geselecteerde werkruimte. Als het abonnement al is verbonden met een andere werkruimte, klikt u eerst op **Verbinding verbreken** om de verbinding te verbreken.

    ![Werkruimten verbinden](media/activity-log-collect/connect-workspace.png)


Als u de instelling wilt uitschakelen, voert u dezelfde procedure uit en klikt u op **Verbinding verbreken** om het abonnement uit de werkruimte te verwijderen.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Activiteitslogboek analyseren in de werkruimte Log Analytics
Wanneer u een activiteitenlogboek koppelt aan een werkruimte loganalytics, worden items naar de werkruimte geschreven in een tabel met de naam *AzureActivity* die u ophalen met een [logboekquery](../log-query/log-query-overview.md). De structuur van deze tabel is afhankelijk van de [categorie van de logvermelding.](activity-log-view.md#categories-in-the-activity-log) Zie [gebeurtenisschema azure activity log](activity-log-schema.md) voor een beschrijving van elke categorie.


### <a name="data-structure-changes"></a>Wijzigingen in gegevensstructuur
Diagnostische instellingen verzamelen dezelfde gegevens als de verouderde methode die wordt gebruikt om het activiteitslogboek te verzamelen met enkele wijzigingen in de structuur van de *AzureActivity-tabel.*

De kolommen in de volgende tabel zijn afgeschaft in het bijgewerkte schema. Ze bestaan nog steeds in *AzureActivity,* maar ze hebben geen gegevens. De vervanging voor deze kolommen zijn niet nieuw, maar ze bevatten dezelfde gegevens als de afgeschafte kolom. Ze zijn in een andere indeling, dus het kan nodig zijn om logboekquery's die ze gebruiken te wijzigen. 

| Afgeschafte kolom | Vervangende kolom |
|:---|:---|
| ActiviteitStatus    | ActivityStatusValue    |
| ActiviteitSubstatus | ActivitySubstatusWaarde |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderWaarde  |

> [!IMPORTANT]
> In sommige gevallen kunnen de waarden in deze kolommen in alle hoofdletters zijn. Als u een query hebt die deze kolommen bevat, moet u de [operator =~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) gebruiken om een ongevoelige vergelijking uit te voeren.

De volgende kolom is toegevoegd aan *AzureActivity* in het bijgewerkte schema:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Monitoringoplossing activity logs Analytics
De azure log analytics-bewakingsoplossing wordt binnenkort afgeschaft en vervangen door een werkmap met behulp van het bijgewerkte schema in de werkruimte Log Analytics. U de oplossing nog steeds gebruiken als u deze al hebt ingeschakeld, maar deze kan alleen worden gebruikt als u het activiteitenlogboek verzamelt met oudere instellingen. 



### <a name="use-the-solution"></a>Gebruik de oplossing
Bewakingsoplossingen zijn toegankelijk via het menu **Monitor** in de Azure-portal. Selecteer **Meer** in de sectie **Inzichten** om de **pagina Overzicht** met de oplossingstegels te openen. Op de tegel **Azure Activity Logs** wordt een aantal **AzureActivity-records** in uw werkruimte weergegeven.

![Tegel Azure Activity Logs](media/collect-activity-logs/azure-activity-logs-tile.png)


Klik op de tegel **Azure Activity Logs** om de weergave Azure Activity **Logs** te openen. De weergave bevat de visualisatie-onderdelen in de volgende tabel. Elk onderdeel bevat maximaal 10 items die overeenkomen met de criteria van die onderdelen voor het opgegeven tijdsbereik. U een logboekquery uitvoeren die alle overeenkomende records retourneert door onder aan het onderdeel op **Alles weergeven** te klikken.

![Azure-activiteitslogboekendashboard](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>De oplossing inschakelen voor nieuwe abonnementen
Binnenkort u de Activity Logs Analytics-oplossing niet meer aan uw abonnement toevoegen via de Azure-portal. U het toevoegen met de volgende procedure met een resourcemanagersjabloon. 

1. Kopieer de volgende json naar een bestand genaamd *ActivityLogTemplate*.json.

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

2. Implementeer de sjabloon met de volgende PowerShell-opdrachten:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [activiteitenlogboek](platform-logs-overview.md).
- Meer informatie over het [Azure Monitor-gegevensplatform](data-platform.md).
- Gebruik [logboekquery's](../log-query/log-query-overview.md) om gedetailleerde informatie uit uw activiteitenlogboek weer te geven.
