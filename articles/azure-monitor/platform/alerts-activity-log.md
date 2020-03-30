---
title: Waarschuwingen voor activiteitenlogboeken maken, weergeven en beheren in Azure Monitor
description: Maak waarschuwingen voor activiteitenlogboeken met behulp van de Azure-portal, een Azure Resource Manager-sjabloon en Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: bfbe2bc3ae3edf9285d3ec006ab0451f070cabd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132395"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Waarschuwingen voor activiteitenlogboeken maken, weergeven en beheren met Azure Monitor  

## <a name="overview"></a>Overzicht

Waarschuwingen voor activiteitenlogboeken zijn de waarschuwingen die worden geactiveerd wanneer een nieuwe activiteitslogboekgebeurtenis plaatsvindt die overeenkomt met de voorwaarden die in de waarschuwing zijn opgegeven.

Deze waarschuwingen zijn bedoeld voor Azure-resources en kunnen worden gemaakt met behulp van een azure resource manager-sjabloon. Ze kunnen ook worden gemaakt, bijgewerkt of verwijderd in de Azure-portal. Doorgaans maakt u meldingen voor activiteitenlogboeken om meldingen te ontvangen wanneer er specifieke wijzigingen optreden in resources in uw Azure-abonnement. Waarschuwingen worden vaak beperkt tot bepaalde resourcegroepen of resources. U wilt bijvoorbeeld een melding ontvangen wanneer een virtuele machine in de voorbeeldbrongroep **myProductionResourceGroup** wordt verwijderd. U ook een melding ontvangen als er nieuwe rollen zijn toegewezen aan een gebruiker in uw abonnement.

> [!IMPORTANT]
> Waarschuwingen voor servicestatusmeldingen kunnen niet worden gemaakt via de interface voor het maken van waarschuwingen voor activiteitenlogboeken. Zie Meldingen van [activiteitslogboeken ontvangen op meldingen van servicestatus](alerts-activity-log-service-notifications.md)voor meer informatie over het maken en gebruiken van servicestatusmeldingen.

Wanneer u waarschuwingsregels maakt, moet u het volgende controleren:

- Het abonnement in het bereik verschilt niet van het abonnement waarin de waarschuwing wordt gemaakt.
- De criteria moeten het niveau, de status, de beller, de resourcegroep, de resource-id of de categorie van het resourcetype zijn waarop de waarschuwing is geconfigureerd.
- Er is geen "anyOf" conditie of geneste omstandigheden in de waarschuwingsconfiguratie JSON. In principe is slechts één "allOf"-voorwaarde toegestaan zonder verdere "allOf" of "anyOf" voorwaarden.
- Wanneer de categorie 'administratief' is, moet u ten minste één van de voorgaande criteria in uw waarschuwing opgeven. U geen waarschuwing maken die elke gebeurtenis wordt geactiveerd in de activiteitslogboeken.

## <a name="azure-portal"></a>Azure Portal

U de Azure-portal gebruiken om waarschuwingsregels voor activiteitenlogboeken te maken en te wijzigen. De ervaring is geïntegreerd met een Azure-activiteitenlogboek om te zorgen voor een naadloze waarschuwing voor specifieke gebeurtenissen die van belang zijn.

### <a name="create-with-the-azure-portal"></a>Maken met de Azure-portal

Gebruik de volgende procedure.

1. Selecteer in de Azure-portal > **Monitorwaarschuwingen**. **Monitor**
2. Selecteer **Nieuwe waarschuwingsregel** in de linkerbovenhoek van het **venster Waarschuwingen.**

     ![Nieuwe waarschuwingsregel](media/alerts-activity-log/AlertsPreviewOption.png)

     Het **venster Regel maken** wordt weergegeven.

      ![Nieuwe opties voor waarschuwingsregels](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Geef **onder Waarschuwingsvoorwaarde definiëren**de volgende informatie op en selecteer **Gereed:**

   - **Waarschuwingsdoel:** Als u het doel voor de nieuwe waarschuwing wilt weergeven en selecteren, gebruikt u **Filter op abonnementsfilter** / **op resourcetype**. Selecteer de resource- of resourcegroep in de weergegeven lijst.

     > [!NOTE]
     > 
     > U alleen [Azure Resource Manager-bijgehouden](../../azure-resource-manager/management/overview.md) resources, resourcegroep of een volledig abonnement selecteren voor een activiteitslogboeksignaal. 

     **Voorbeeldweergave waarschuwingsdoel**

     ![Doel selecteren](media/alerts-activity-log/select-target.png)

   - Selecteer Criteria **toevoegen**onder **Doelcriteria**. Alle beschikbare signalen voor het doel worden weergegeven, waaronder die uit verschillende categorieën van **Activiteitlog**. De categorienaam wordt toegevoegd aan de naam van de **monitorservice.**

   - Selecteer het signaal in de lijst die wordt weergegeven met verschillende bewerkingen die mogelijk zijn voor het type **Activiteitslogboek**.

     U de tijdlijn van de logboekgeschiedenis en de bijbehorende waarschuwingslogica voor dit doelsignaal selecteren:

     **Criteriascherm toevoegen**

     ![Criteria toevoegen](media/alerts-activity-log/add-criteria.png)

     - **Geschiedenistijd:** Gebeurtenissen die beschikbaar zijn voor de geselecteerde bewerking kunnen worden uitgezet in de afgelopen 6, 12 of 24 uur of in de afgelopen week.

     - **Waarschuwingslogica:**

       - **Gebeurtenisniveau**: Het ernstniveau van de gebeurtenis: _Verbose_, _Informatief_, _Waarschuwing_, _Fout_of _Kritiek_.
       - **Status**: De status van de gebeurtenis: _Gestart,_ _Mislukt_of _Geslaagd_.
       - **Gebeurtenis geïnitieerd door**: Ook wel bekend als de beller. Het e-mailadres of de Azure Active Directory-id van de gebruiker die de bewerking heeft uitgevoerd.

       In deze voorbeeldsignaalgrafiek wordt de waarschuwingslogica toegepast:

       ![Geselecteerde criteria](media/alerts-activity-log/criteria-selected.png)

4. Geef **onder Waarschuwingsgegevens definiëren**de volgende details op:

    - **Naam van de waarschuwingsregel:** de naam voor de nieuwe waarschuwingsregel.
    - **Beschrijving**: De beschrijving voor de nieuwe waarschuwingsregel.
    - **Waarschuwing opslaan in resourcegroep:** selecteer de resourcegroep waar u deze nieuwe regel wilt opslaan.

5. Geef **onder Actiegroep**in het vervolgkeuzemenu de actiegroep op die u aan deze nieuwe waarschuwingsregel wilt toewijzen. Of [maak een nieuwe actiegroep](../../azure-monitor/platform/action-groups.md) en wijs deze toe aan de nieuwe regel. Als u een nieuwe groep wilt maken, selecteert u **+ Nieuwe groep**.

6. Als u de regels wilt inschakelen nadat u ze hebt gemaakt, selecteert u **Ja** voor de **regel Inschakelen bij het maken.**
7. Selecteer **Waarschuwingsregel maken**.

    De nieuwe waarschuwingsregel voor het activiteitenlogboek wordt gemaakt en er verschijnt een bevestigingsbericht in de rechterbovenhoek van het venster.

    U een regel in- of uitschakelen, bewerken of verwijderen. Meer informatie over het beheren van regels voor activiteitenlogboeken.


Een eenvoudige analogie voor het begrijpen van voorwaarden waarop waarschuwingsregels kunnen worden gemaakt in een activiteitenlogboek, is het verkennen of filteren van gebeurtenissen via het [activiteitenlogboek in de Azure-portal.](activity-log-view.md#azure-portal) In het scherm **Azure Monitor - Activity Log** u de benodigde gebeurtenis filteren of zoeken en vervolgens een waarschuwing maken met de knop Waarschuwing voor **activiteitslogboek toevoegen.** Volg vervolgens de stappen 4 tot en met 7 zoals eerder getoond.
    
 ![Waarschuwing toevoegen vanuit activiteitenlogboek](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>De Azure-portal weergeven en beheren

1. Selecteer in de Azure-portal > **Monitorwaarschuwingen**. **Monitor** Selecteer **Waarschuwingsregels beheren** in de linkerbovenhoek van het venster.

    ![Waarschuwingsregels beheren](media/alerts-activity-log/manage-alert-rules.png)

    De lijst met beschikbare regels wordt weergegeven.

2. Zoek naar de regel voor het activiteitenlogboek die u wilt wijzigen.

    ![Waarschuwingsregels voor zoekactiviteitenlogboeken](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    U de beschikbare filters, _Abonnement_, _Resourcegroep,_ _Resource,_ _Signaaltype_of _Status_gebruiken om de activiteitsregel te vinden die u wilt bewerken.

   > [!NOTE]
   > 
   > U alleen **beschrijvings-,** **doelcriteria-** en **actiegroepen**bewerken.

3. Selecteer de regel en dubbelklik om de regelopties te bewerken. Breng de vereiste wijzigingen aan en selecteer **Opslaan**.

   ![Waarschuwingsregels beheren](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. U een regel in- of uitschakelen of verwijderen. Selecteer de juiste optie boven aan het venster nadat u de regel hebt geselecteerd zoals beschreven in stap 2.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon
Als u een waarschuwingsregel voor activiteitenlogboeken wilt maken met behulp `microsoft.insights/activityLogAlerts`van een azure resource manager-sjabloon, maakt u een bron van het type . Vervolgens vult u alle gerelateerde eigenschappen in. Hier is een sjabloon waarmee een waarschuwingsregel voor activiteitenlogboeken wordt gemaakt:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
Het vorige voorbeeld JSON kan worden opgeslagen als bijvoorbeeld sampleActivityLogAlert.json met het oog op deze doorloop en kan worden geïmplementeerd met [Azure Resource Manager in de Azure-portal.](../../azure-resource-manager/templates/deploy-portal.md)

De volgende velden zijn de opties die u gebruiken in de sjabloon Azure Resource Manager voor de velden Voorwaarden: Resourcestatus, 'Adviseur' en 'Servicestatus' hebben extra eigenschappenvelden voor hun speciale velden. 
1. resourceId: de resource-id van de impactgevende resource in de gebeurtenis activiteitslogboek waarop de waarschuwing moet worden gegenereerd.
2. categorie: de categorie van de gebeurtenis activiteitslogboek. Bijvoorbeeld: Administratief, ServiceHealth, ResourceHealth, Autoscale, Beveiliging, Aanbeveling, Beleid.
3. beller: het e-mailadres of de Azure Active Directory-id van de gebruiker die de bewerking van de gebeurtenis activiteitslogboek heeft uitgevoerd.
4. niveau: niveau van de activiteit in de gebeurtenis activiteitslogboek waarop de waarschuwing moet worden gegenereerd. Bijvoorbeeld: Kritisch, Fout, Waarschuwing, Informatief, Verbose.
5. operationName: de naam van de bewerking in de gebeurtenis activiteitslogboek. Bijvoorbeeld: Microsoft.Resources/implementaties/schrijven
6. resourceGroep: naam van de resourcegroep voor de impactgevende resource in de gebeurtenis activiteitslogboek.
7. resourceProvider: [uitleg azure resource providers en typen uitleg](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Zie [Resourceproviders voor Azure-services voor](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)een lijst die resourceproviders aan Azure-services toeinziet.
8. status: tekenreeks die de status van de bewerking in de activiteitsgebeurtenis beschrijft. Bijvoorbeeld: Gestart, In uitvoering, Geslaagd, Mislukt, Actief, Opgelost
9. subStatus: Meestal de HTTP-statuscode van de bijbehorende REST-aanroep, maar kan ook andere tekenreeksen bevatten die een substatus beschrijven.   Bijvoorbeeld: OK (HTTP-statuscode: 200), Gemaakt (HTTP-statuscode: 201), Geaccepteerd (HTTP-statuscode: 202), Geen inhoud (HTTP-statuscode: 204), Bad Request (HTTP-statuscode: 400), Niet gevonden (HTTP-statuscode: 404), Conflict (HTTP-statuscode: 409), Interne server Fout (HTTP-statuscode: 500), Service niet beschikbaar (HTTP-statuscode: 503), gatewaytime-out (HTTP-statuscode: 504).
10. resourceType: het type resource dat door de gebeurtenis is beïnvloed. Bijvoorbeeld: Microsoft.Resources/implementaties

Bijvoorbeeld:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
Meer informatie over de velden van het activiteitenlogboek vindt u [hier.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)



> [!NOTE]
> Het kan tot 5 minuten duren voordat de nieuwe waarschuwingsregel voor activiteitenlogboeken actief is.

## <a name="rest-api"></a>REST API 
De [API voor azure monitoractiviteitslogboekwaarschuwingen](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) is een REST-API. Het is volledig compatibel met de Azure Resource Manager REST API. Het kan worden gebruikt via PowerShell met behulp van de Resource Manager-cmdlet of de Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>De sjabloon Resourcebeheer implementeren met PowerShell
Als u PowerShell wilt gebruiken om de voorbeeldsjabloon Resourcebeheer te implementeren die wordt weergegeven in de vorige sjabloonsectie [Azure Resource Manager,](#azure-resource-manager-template) gebruikt u de volgende opdracht:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

waar de sampleActivityLogAlert.parameters.json de waarden bevat die zijn opgegeven voor de parameters die nodig zijn voor het maken van waarschuwingsregels.

### <a name="use-activity-log-powershell-cmdlets"></a>PowerShell-cmdlets van activiteitslogboek gebruiken

Waarschuwingen voor activiteitenlogboeken hebben speciale PowerShell-cmdlets beschikbaar:

- [Set-AzActivityLogAlert:](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert)hiermee maakt u een waarschuwing voor een nieuw activiteitenlogboek of wordt een waarschuwing voor een bestaand activiteitenlogboek bijgewerkt.
- [Get-AzActivityLogAlert:](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert)Krijgt een of meer waarschuwingsbronnen voor activiteitenlogboeken.
- [Enable-AzActivityLogAlert:](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert)hiermee wordt een waarschuwing voor een bestaand activiteitenlogboek ingeschakeld en worden de tags ingesteld.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): schakelt een bestaande activiteitslogboekwaarschuwing uit en stelt de tags in.
- [Remove-AzActivityLogAlert:](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert)Verwijdert een waarschuwing voor het activiteitenlogboek.

## <a name="azure-cli"></a>Azure-CLI

Speciale Azure CLI-opdrachten onder de ingestelde waarschuwing voor [het AZ-monitorlogboek](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) zijn beschikbaar voor het beheren van waarschuwingsregels voor activiteitenlogboeken.

Als u een nieuwe waarschuwingsregel voor activiteitenlogboeken wilt maken, gebruikt u de volgende opdrachten in deze volgorde:

1. [waarschuwing voor activiteitenlogboek van AZ-monitor:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)Maak een nieuwe waarschuwingsregelbron voor activiteitenlogboeken.
1. [waarschuwingsbereik voor activiteiten-logboek van AZ-monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Voeg ruimte toe voor de gemaakte waarschuwingsregel voor activiteitenlogboeken.
1. [actiegroep voor waarschuwingsacties voor activiteitenlogboek van AZ-monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): Voeg een actiegroep toe aan de waarschuwingsregel voor activiteitenlogboeken.

Als u één waarschuwingsregelbron voor activiteitenlogboeken wilt ophalen, gebruikt u de waarschuwing voor de waarschuwing voor [activiteitslogboeken van de](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)opdracht Azure CLI . Als u alle waarschuwingsregelbronnen voor activiteitenlogboeken in een resourcegroep wilt weergeven, gebruikt u [de waarschuwingslijst activiteitslogboekwaarschuwingvan AZ-monitor](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Statusmeldingsregelbronnen voor activiteitenlogboeken kunnen worden verwijderd met behulp van de waarschuwing voor de statusmelding van de opdracht Azure [CLI- monitorstatus.](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [webhook-schema voor activiteitslogboeken](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Lees een [overzicht van activiteitenlogboeken.](../../azure-monitor/platform/activity-log-alerts.md)
- Meer informatie over [actiegroepen](../../azure-monitor/platform/action-groups.md).  
- Meer informatie over [meldingen over servicestatus](../../azure-monitor/platform/service-notifications.md).
