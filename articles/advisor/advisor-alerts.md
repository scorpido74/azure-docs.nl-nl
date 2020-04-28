---
title: Azure Advisor waarschuwingen maken voor nieuwe aanbevelingen
description: Azure Advisor waarschuwingen voor nieuwe aanbeveling maken
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 07cbc57ef718b6cac104d2b5238ff4e3196f197a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75443157"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Azure Advisor-waarschuwingen maken voor nieuwe aanbevelingen 

In dit artikel wordt beschreven hoe u een waarschuwing instelt voor nieuwe aanbevelingen van Azure Advisor met behulp van de Azure Portal en Azure Resource Manager sjablonen. 

Wanneer Azure Advisor een nieuwe aanbeveling voor een van uw resources detecteert, wordt een gebeurtenis opgeslagen in het [activiteiten logboek van Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview). U kunt waarschuwingen instellen voor deze gebeurtenissen van Azure Advisor met behulp van een aanbevelings gerichte waarschuwing voor het maken van waarschuwingen. U kunt een abonnement en optioneel een resource groep selecteren om de resources op te geven waarvoor u waarschuwingen wilt ontvangen. 

U kunt ook de typen aanbevelingen vaststellen met behulp van de volgende eigenschappen:

* Categorie
* Impact niveau
* Type aanbeveling

U kunt ook de actie configureren die wordt uitgevoerd wanneer een waarschuwing wordt geactiveerd door:  

* Een bestaande actie groep selecteren
* Een nieuwe actie groep maken

Zie [actie groepen maken en beheren](../azure-monitor/platform/action-groups.md)voor meer informatie over actie groepen.

> [!NOTE] 
> Advisor-waarschuwingen zijn momenteel alleen beschikbaar voor aanbevelingen voor hoge Beschik baarheid, prestaties en kosten. Beveiligings aanbevelingen worden niet ondersteund. 

## <a name="in-the-azure-portal"></a>In de Azure Portal
1. Selecteer in de **portal** **Azure Advisor**.

    ![Azure Advisor in Portal](./media/advisor-alerts/create1.png)

2. Selecteer in de sectie **bewaking** van het linkermenu **waarschuwingen**. 

    ![Waarschuwingen in Advisor](./media/advisor-alerts/create2.png)

3. Selecteer **nieuwe Advisor-waarschuwing**.

    ![Nieuwe Advisor-waarschuwing](./media/advisor-alerts/create3.png)

4. Selecteer in de sectie **bereik** het abonnement en optioneel de resource groep waarop u wilt worden gewaarschuwd. 

    ![Advisor-waarschuwings bereik](./media/advisor-alerts/create4.png)

5. Selecteer in de sectie **voor waarde** de methode die u wilt gebruiken voor het configureren van de waarschuwing. Als u een waarschuwing wilt ontvangen voor alle aanbevelingen voor een bepaalde categorie en/of een impact niveau, selecteert u **categorie en impact niveau**. Als u een waarschuwing wilt ontvangen voor alle aanbevelingen van een bepaald type, selecteert u **aanbevelings type**.

    ![Azure Advisor waarschuwings voorwaarde](./media/advisor-alerts/create5.png)

6. Afhankelijk van de optie configureren op die u selecteert, kunt u de criteria opgeven. Als u alle aanbevelingen wilt, laat u de resterende velden leeg. 

    ![Actie groep voor Advisor-waarschuwingen](./media/advisor-alerts/create6.png)

7. Selecteer in de sectie **actie groepen** de optie **bestaande toevoegen** om een actie groep te gebruiken die u al hebt gemaakt of selecteer **nieuwe maken** om een nieuwe [actie groep](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)in te stellen. 

    ![Advisor-waarschuwing bestaande toevoegen](./media/advisor-alerts/create7.png)

8. Geef in de sectie waarschuwings Details een naam en een korte beschrijving voor de waarschuwing op. Als u wilt dat uw waarschuwing wordt ingeschakeld, verlaat u **regel inschakelen bij** selectie instellen op **Ja**. Selecteer vervolgens de resource groep waarin u de waarschuwing wilt opslaan. Dit heeft geen invloed op het doel bereik van de aanbeveling. 

    ![Azure Advisor banner](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>Met een Azure Resource Manager sjabloon

Met deze resource manager-sjabloon maakt u een aanbevelings waarschuwing en een nieuwe actie groep.

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

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Aanbevelings waarschuwingen configureren voor het gebruik van een webhook
In deze sectie wordt beschreven hoe u Azure Advisor waarschuwingen configureert om aanbevelings gegevens via webhooks naar uw bestaande systemen te verzenden. 

U kunt waarschuwingen instellen om een melding te ontvangen wanneer u een nieuwe adviseur-aanbeveling hebt voor een van uw resources. Deze waarschuwingen kunnen u verwittigen via e-mail of SMS, maar ze kunnen ook worden gebruikt om te integreren met uw bestaande systemen via een webhook. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>De nettolading aanbeveling van Advisor-waarschuwing gebruiken
Als u Advisor-waarschuwingen wilt integreren in uw eigen systemen met een webhook, moet u de JSON-nettolading parseren die vanuit de melding wordt verzonden. 

Wanneer u uw actie groep voor deze waarschuwing instelt, selecteert u of u het algemene waarschuwings schema wilt gebruiken. Als u het algemene schema voor waarschuwingen selecteert, ziet uw nettolading er als volgt uit: 

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

Als u het algemene schema niet gebruikt, ziet uw nettolading er als volgt uit: 

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

In een van beide schema's kunt u aanbevelingen voor Advisor identificeren door te kijken naar `Recommendation` **Event source** is en `Microsoft.Advisor/recommendations/available/action` **operationname** .

Enkele van de andere belang rijke velden die u wilt gebruiken, zijn: 

* *alertTargetIDs* (in het algemene schema) of *resourceId* (verouderd schema)
* *recommendationType*
* *aanbeveling*
* *recommendationCategory*
* *recommendationImpact*
* *recommendationResourceLink*


## <a name="manage-your-alerts"></a>De waarschuwingen beheren 

Vanuit Azure Advisor kunt u waarschuwingen voor uw aanbevelingen bewerken, verwijderen of uitschakelen en inschakelen. 

1. Selecteer in de **portal** **Azure Advisor**.

    ![Azure Advisor banner](./media/advisor-alerts/create1.png)

2. Selecteer in de sectie **bewaking** van het linkermenu **waarschuwingen**.

    ![Azure Advisor banner](./media/advisor-alerts/create2.png)

3. Als u een waarschuwing wilt bewerken, klikt u op de naam van de waarschuwing om de waarschuwing te openen en bewerkt u de velden die u wilt bewerken.

4. Als u een waarschuwing wilt verwijderen, inschakelen of uitschakelen, klikt u op de ovaal aan het einde van de rij en selecteert u vervolgens de actie die u wilt uitvoeren.
 

