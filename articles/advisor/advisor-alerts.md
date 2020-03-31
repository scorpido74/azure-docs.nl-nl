---
title: Azure Advisor-waarschuwingen maken voor nieuwe aanbevelingen
description: Azure Advisor-waarschuwingen maken voor nieuwe aanbeveling
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 07cbc57ef718b6cac104d2b5238ff4e3196f197a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443157"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Azure Advisor-waarschuwingen maken voor nieuwe aanbevelingen 

In dit artikel ziet u hoe u een waarschuwing instelt voor nieuwe aanbevelingen van Azure Advisor met behulp van de Azure-portal- en Azure Resource Manager-sjablonen. 

Wanneer Azure Advisor een nieuwe aanbeveling voor een van uw resources detecteert, wordt een gebeurtenis opgeslagen in [het Azure Activity-logboek.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) U waarschuwingen voor deze gebeurtenissen instellen vanuit Azure Advisor met behulp van een ervaring voor het maken van aanbevelingsspecifieke waarschuwingen. U een abonnement selecteren en optioneel een resourcegroep om de bronnen op te geven waarop u waarschuwingen wilt ontvangen. 

U ook de typen aanbevelingen bepalen met behulp van de volgende eigenschappen:

* Categorie
* Impactniveau
* Type aanbeveling

U ook de actie configureren die plaatsvindt wanneer een waarschuwing wordt geactiveerd door:  

* Een bestaande actiegroep selecteren
* Een nieuwe actiegroep maken

Zie Actiegroepen maken en beheren voor meer informatie over [actiegroepen.](../azure-monitor/platform/action-groups.md)

> [!NOTE] 
> Advisor-waarschuwingen zijn momenteel alleen beschikbaar voor aanbevelingen voor hoge beschikbaarheid, prestaties en kosten. Beveiligingsaanbevelingen worden niet ondersteund. 

## <a name="in-the-azure-portal"></a>In de Azure Portal
1. Selecteer **Azure Advisor**in de **portal**.

    ![Azure Advisor in portal](./media/advisor-alerts/create1.png)

2. Selecteer **In** de sectie Controle van het linkermenu de optie **Waarschuwingen**. 

    ![Waarschuwingen in Adviseur](./media/advisor-alerts/create2.png)

3. Selecteer **Nieuwe Adviseur-waarschuwing**.

    ![Nieuwe Adviseur alert](./media/advisor-alerts/create3.png)

4. Selecteer **in** de sectie Bereik het abonnement en eventueel de resourcegroep waarop u wilt worden gewaarschuwd. 

    ![Waarschuwingsbereik van adviseur](./media/advisor-alerts/create4.png)

5. Selecteer **in de** sectie Voorwaarde de methode die u wilt gebruiken voor het configureren van uw waarschuwing. Als u wilt waarschuwen voor alle aanbevelingen voor een bepaalde categorie en/of impactniveau, selecteert u **Categorie en impactniveau**. Als u wilt waarschuwen voor alle aanbevelingen van een bepaald type, selecteert u **Aanbevelingstype**.

    ![Azure Advisor-waarschuwingsvoorwaarde](./media/advisor-alerts/create5.png)

6. Afhankelijk van de optie Configureren op optie die u selecteert, u de criteria opgeven. Als u alle aanbevelingen wilt, laat u de resterende velden leeg. 

    ![Advisor alert actiegroep](./media/advisor-alerts/create6.png)

7. Selecteer in de sectie **Actiegroepen** de optie **Bestaand toevoegen** om een actiegroep te gebruiken die u al hebt gemaakt of selecteer **Nieuw maken** om een nieuwe [actiegroep](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)in te stellen. 

    ![Advisor-waarschuwing voeg bestaande](./media/advisor-alerts/create7.png)

8. Geef uw waarschuwing in de sectie Waarschuwingsgegevens een naam en een korte beschrijving. Als u wilt dat uw waarschuwing is ingeschakeld, laat **u de regel Inschakelen staan bij het maken** van selectie ingesteld op **Ja**. Selecteer vervolgens de resourcegroep om uw waarschuwing op te slaan. Dit heeft geen invloed op de targeting van de aanbeveling. 

    ![Azure Advisor-banner](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>Met een Azure Resource Manager-sjabloon

Met deze sjabloon Resourcemanager wordt een aanbevelingswaarschuwing en een nieuwe actiegroep gemaakt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest2",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      },
      "dependsOn": []
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "name": "[parameters('activityLogAlerts_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
  ```

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Aanbevelingswaarschuwingen configureren om een webhook te gebruiken
In deze sectie ziet u hoe u Azure Advisor-waarschuwingen configureert om aanbevelingsgegevens via webhooks naar uw bestaande systemen te verzenden. 

U waarschuwingen instellen om op de hoogte te worden gesteld wanneer u een nieuwe aanbeveling van een adviseur hebt over een van uw bronnen. Deze waarschuwingen kunnen u via e-mail of sms op de hoogte brengen, maar ze kunnen ook worden gebruikt om via een webhook met uw bestaande systemen te integreren. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Het gebruik van de Advisor aanbeveling som payload
Als u Advisor-waarschuwingen met behulp van een webhook in uw eigen systemen wilt integreren, moet u de JSON-payload die vanuit de melding wordt verzonden, ontleden. 

Wanneer u uw actiegroep instelt voor deze waarschuwing, selecteert u of u het algemene waarschuwingsschema wilt gebruiken. Als u het algemene waarschuwingsschema selecteert, ziet uw laadvermogen eruit als volgt: 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

Als u het algemene schema niet gebruikt, ziet uw laadvermogen er als volgt uit: 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

In beide schema's u aanbevelingsgebeurtenissen `Recommendation` van Advisor identificeren door te zoeken naar **gebeurtenisBron** is en **operationName** is `Microsoft.Advisor/recommendations/available/action`.

Enkele andere belangrijke velden die u wilt gebruiken zijn: 

* *alertTargetIDs* (in het algemene schema) of *resourceId* (legacy-schema)
* *aanbevelingType*
* *aanbevelingNaam*
* *AanbevelingCategorie*
* *aanbevelingImpact*
* *aanbevelingResourceLink*


## <a name="manage-your-alerts"></a>De waarschuwingen beheren 

Vanuit Azure Advisor u waarschuwingen voor aanbevelingen bewerken, verwijderen of uitschakelen en inschakelen. 

1. Selecteer **Azure Advisor**in de **portal**.

    ![Azure Advisor-banner](./media/advisor-alerts/create1.png)

2. Selecteer **In** de sectie Controle van het linkermenu de optie **Waarschuwingen**.

    ![Azure Advisor-banner](./media/advisor-alerts/create2.png)

3. Als u een waarschuwing wilt bewerken, klikt u op de naam Waarschuwing om de waarschuwing te openen en de velden te bewerken die u wilt bewerken.

4. Als u een waarschuwing wilt verwijderen, inschakelen of uitschakelen, klikt u op de ellips aan het einde van de rij en selecteert u de actie die u wilt uitvoeren.
 

