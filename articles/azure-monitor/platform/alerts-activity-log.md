---
title: Waarschuwingen voor activiteiten logboek in Azure Monitor maken, weer geven en beheren
description: Waarschuwingen voor activiteiten logboeken maken met behulp van de Azure Portal, een Azure Resource Manager sjabloon en Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: 9791ebaadeb1ee724692a9e1a0d61aff5cbae6a3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668482"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Waarschuwingen voor activiteiten logboek maken, weer geven en beheren met behulp van Azure Monitor  

## <a name="overview"></a>Overzicht

Waarschuwingen voor activiteiten logboeken zijn de waarschuwingen die worden geactiveerd wanneer een nieuwe activiteiten logboek gebeurtenis optreedt die overeenkomt met de voor waarden die zijn opgegeven in de waarschuwing.

Deze waarschuwingen zijn van Azure-resources en kunnen worden gemaakt met behulp van een Azure Resource Manager sjabloon. Ze kunnen ook worden gemaakt, bijgewerkt of verwijderd in de Azure Portal. Normaal gesp roken maakt u waarschuwingen voor activiteiten Logboeken om meldingen te ontvangen wanneer er specifieke wijzigingen worden aangebracht in resources in uw Azure-abonnement. Waarschuwingen zijn vaak van toepassing op bepaalde resource groepen of bronnen. Stel dat u een melding wilt ontvangen wanneer een virtuele machine in de voorbeeld resource groep **myProductionResourceGroup** wordt verwijderd. Of u wilt een melding ontvangen als nieuwe rollen zijn toegewezen aan een gebruiker in uw abonnement.

> [!IMPORTANT]
> Waarschuwingen voor service status meldingen kunnen niet worden gemaakt via de interface voor het maken van waarschuwingen voor activiteiten Logboeken. Zie [waarschuwingen voor activiteiten logboeken ontvangen in service status meldingen](alerts-activity-log-service-notifications.md)voor meer informatie over het maken en gebruiken van service status meldingen.

Wanneer u waarschuwings regels maakt, moet u het volgende controleren:

- Het abonnement in de scope wijkt niet af van het abonnement waarin de waarschuwing is gemaakt.
- De criteria moeten het niveau, de status, de beller, de resource groep, de resource-ID of de gebeurtenis categorie van het resource type zijn waarop de waarschuwing is geconfigureerd.
- Er is geen ' anyOf-voor waarde of geneste voor waarden in de JSON van de waarschuwings configuratie. In principe is slechts één ' overzet-voor waarde toegestaan zonder verdere ' overzet ' of ' anyOf-voor waarden.
- Wanneer de categorie ' beheer ' is, moet u ten minste één van de voor gaande criteria in uw waarschuwing opgeven. U kunt geen waarschuwing maken die telkens wanneer een gebeurtenis wordt gemaakt in de activiteiten Logboeken wordt geactiveerd.

## <a name="azure-portal"></a>Azure-portal

U kunt het Azure Portal gebruiken om waarschuwings regels voor het activiteiten logboek te maken en te wijzigen. De ervaring is geïntegreerd met een Azure-activiteiten logboek om ervoor te zorgen dat naadloze waarschuwingen worden gemaakt voor specifieke gebeurtenissen die van belang zijn.

### <a name="create-with-the-azure-portal"></a>Maken met de Azure Portal

Gebruik de volgende procedure.

1. Selecteer > **waarschuwingen** **bewaken** in het Azure Portal.
2. Selecteer **nieuwe waarschuwings regel** in de linkerbovenhoek van het venster **waarschuwingen** .

     ![Nieuwe waarschuwings regel](media/alerts-activity-log/AlertsPreviewOption.png)

     Het venster **regel maken** wordt weer gegeven.

      ![Opties voor nieuwe waarschuwings regel](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Geef onder **waarschuwings voorwaarde definiëren**de volgende informatie op en selecteer **gereed**:

   - **Waarschuwings doel:** Als u het doel voor de nieuwe waarschuwing wilt weer geven en selecteren, gebruikt u **filteren op abonnement** / **filteren op resource type**. Selecteer de resource of resource groep in de lijst die wordt weer gegeven.

     > [!NOTE]
     > 
     > U kunt alleen [Azure Resource Manager](../../azure-resource-manager/management/overview.md) bijgehouden resource, resource groep of een volledig abonnement voor een activiteiten logboek selecteren. 

     **Voorbeeld weergave van waarschuwings doel**

     ![Doel selecteren](media/alerts-activity-log/select-target.png)

   - Selecteer **criteria toevoegen**onder **doel criteria**. Alle beschik bare signalen voor het doel worden weer gegeven, die uit verschillende categorieën **activiteiten logboek**zijn opgenomen. De categorie naam wordt toegevoegd aan de service naam van de **monitor** .

   - Selecteer in de lijst het signaal dat wordt weer gegeven van verschillende bewerkingen die mogelijk zijn voor het type **activiteiten logboek**.

     U kunt de tijd lijn van de logboek geschiedenis en de bijbehorende waarschuwings logica voor dit doel signaal selecteren:

     **Scherm criteria toevoegen**

     ![Criteria toevoegen](media/alerts-activity-log/add-criteria.png)

     - **Geschiedenis tijd**: gebeurtenissen die beschikbaar zijn voor de geselecteerde bewerking kunnen worden weer gegeven in de afgelopen 6, 12 of 24 uur of in de afgelopen week.

     - **Waarschuwings logica**:

       - **Gebeurtenis niveau**: het Ernst niveau van de gebeurtenis: _uitgebreid_, _informatief_, _waarschuwing_, _fout_of _kritiek_.
       - **Status**: de status van de gebeurtenis: _gestart_, _mislukt_of _geslaagd_.
       - **Gebeurtenis gestart door**: ook wel bekend als de aanroeper. Het e-mail adres of de Azure Active Directory id van de gebruiker die de bewerking heeft uitgevoerd.

       In deze grafiek met voorbeeld signalen wordt de waarschuwings logica toegepast:

       ![Geselecteerde criteria](media/alerts-activity-log/criteria-selected.png)

4. Geef onder **waarschuwings Details definiëren**de volgende details op:

    - **Naam van waarschuwings regel**: de naam voor de nieuwe waarschuwings regel.
    - **Beschrijving**: de beschrijving van de nieuwe waarschuwings regel.
    - **Waarschuwing opslaan in resource groep**: Selecteer de resource groep waar u deze nieuwe regel wilt opslaan.

5. Geef onder **actie groep**in de vervolg keuzelijst de actie groep op die u wilt toewijzen aan deze nieuwe waarschuwings regel. Of [Maak een nieuwe actie groep](../../azure-monitor/platform/action-groups.md) en wijs deze toe aan de nieuwe regel. Als u een nieuwe groep wilt maken, selecteert u **+ nieuwe groep**.

6. Als u de regels wilt inschakelen nadat u ze hebt gemaakt, selecteert u **Ja** voor de optie **regel inschakelen bij het maken** .
7. Selecteer **waarschuwings regel maken**.

    De nieuwe waarschuwings regel voor het activiteiten logboek wordt gemaakt en er wordt een bevestigings bericht weer gegeven in de rechter bovenhoek van het venster.

    U kunt een regel inschakelen, uitschakelen, bewerken of verwijderen. Meer informatie over het beheren van regels voor het activiteiten logboek.


Een eenvoudige analoge waarde voor de voor waarden waarop waarschuwings regels kunnen worden gemaakt in een activiteiten logboek is het verkennen of filteren van gebeurtenissen via het [activiteiten logboek in de Azure Portal](activity-log-view.md#azure-portal). In het venster **Azure monitor-activiteiten logboek** kunt u de benodigde gebeurtenis filteren of zoeken en vervolgens een waarschuwing maken met behulp van de knop waarschuwing voor het **activiteiten logboek toevoegen** . Volg vervolgens de stappen 4 tot en met 7, zoals eerder is weer gegeven.
    
 ![Waarschuwing toevoegen vanuit het activiteiten logboek](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Weer geven en beheren in de Azure Portal

1. Selecteer > **waarschuwingen** **bewaken** in het Azure Portal. Selecteer **waarschuwings regels beheren** in de linkerbovenhoek van het venster.

    ![Waarschuwings regels beheren](media/alerts-activity-log/manage-alert-rules.png)

    De lijst met beschik bare regels wordt weer gegeven.

2. Zoek de logboek regel voor activiteiten die u wilt wijzigen.

    ![Waarschuwings regels voor het activiteiten logboek zoeken](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    U kunt de beschik bare filters, het _abonnement_, de _resource groep_, de _resource_, het _signaal type_of de _status_gebruiken om de activiteit regel te vinden die u wilt bewerken.

   > [!NOTE]
   > 
   > U kunt alleen een **Beschrijving**, **doel criteria**en **actie groepen**bewerken.

3. Selecteer de regel en dubbel klik om de regel opties te bewerken. Breng de gewenste wijzigingen aan en selecteer vervolgens **Opslaan**.

   ![Waarschuwings regels beheren](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. U kunt een regel inschakelen, uitschakelen of verwijderen. Selecteer de relevante optie boven aan het venster nadat u de regel hebt geselecteerd, zoals beschreven in stap 2.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon
Als u een waarschuwing voor een activiteiten logboek wilt maken met behulp van een Azure Resource Manager-sjabloon, maakt u een resource van het type `microsoft.insights/activityLogAlerts`. Vervolgens vult u alle gerelateerde eigenschappen in. Hier volgt een sjabloon voor het maken van een waarschuwing voor een activiteiten logboek:

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
De voor gaande voor beeld-JSON kan worden opgeslagen als bijvoorbeeld sampleActivityLogAlert. json voor het doel van deze procedure en kan worden geïmplementeerd met behulp van [Azure Resource Manager in de Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

> [!NOTE]
> Het kan tot vijf minuten duren voordat de nieuwe waarschuwings regel voor het activiteiten logboek actief wordt.

## <a name="rest-api"></a>REST-API 
De [API voor waarschuwingen in het Azure monitor-activiteiten logboek](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) is een rest API. Het is volledig compatibel met de Azure Resource Manager REST API. Het kan worden gebruikt via Power shell met behulp van de Resource Manager-cmdlet of de Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>De Resource Manager-sjabloon implementeren met Power shell
Als u Power shell wilt gebruiken om de voorbeeld sjabloon voor een resource manager te implementeren die wordt weer gegeven in de vorige [Azure Resource Manager sjabloon](#azure-resource-manager-template) sectie, gebruikt u de volgende opdracht:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

waarbij sampleActivityLogAlert. para meters. json de waarden bevat die zijn opgegeven voor de para meters die nodig zijn voor het maken van de waarschuwings regel.

### <a name="use-activity-log-powershell-cmdlets"></a>Power shell-cmdlets voor het activiteiten logboek gebruiken

Er zijn speciale Power shell-cmdlets beschikbaar voor waarschuwingen voor activiteiten logboeken:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Hiermee wordt een waarschuwing voor een nieuwe activiteiten logboek gemaakt of wordt een bestaande waarschuwing voor een activiteiten logboek bijgewerkt.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Hiermee worden een of meer resources voor een activiteiten logboek waarschuwing opgehaald.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Hiermee wordt een waarschuwing voor een bestaande activiteiten logboek ingeschakeld en worden de bijbehorende tags ingesteld.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Hiermee wordt een waarschuwing voor een bestaande activiteiten logboek uitgeschakeld en worden labels ingesteld.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Hiermee verwijdert u een waarschuwing voor een activiteiten logboek.

## <a name="azure-cli"></a>Azure CLI

Toegewezen Azure CLI-opdrachten onder de de set [AZ monitor-activiteit-logboek waarschuwing](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) zijn beschikbaar voor het beheren van de waarschuwings regels voor het activiteiten logboek.

Als u een nieuwe waarschuwings regel voor het activiteiten logboek wilt maken, gebruikt u de volgende opdrachten in deze volg orde:

1. [AZ monitor-activiteit-logboek waarschuwing maken](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): een nieuwe waarschuwing regel resource in het activiteiten logboek maken.
1. [AZ monitor activiteit-log-waarschuwings bereik](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Voeg een bereik toe voor de waarschuwings regel voor het gemaakte activiteiten logboek.
1. [AZ monitor-activiteit-logboek waarschuwing actie-groep](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): een actie groep toevoegen aan de waarschuwings regel voor het activiteiten logboek.

Als u één waarschuwings regel voor een activiteiten logboek wilt ophalen, gebruikt u de Azure CLI [-opdracht AZ monitor Activity-Log alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Als u alle resources van de waarschuwings regel voor het activiteiten logboek in een resource groep wilt weer geven, gebruikt u de [waarschuwings lijst AZ monitor Activity-Log](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Regel resources voor waarschuwings logboeken kunnen worden verwijderd met behulp van de Azure CLI [-opdracht AZ monitor Activity-Log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [webhook-schema voor activiteiten logboeken](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Lees een [overzicht van activiteiten logboeken](../../azure-monitor/platform/activity-log-alerts.md).
- Meer informatie over [actie groepen](../../azure-monitor/platform/action-groups.md).  
- Meer informatie over [service status meldingen](../../azure-monitor/platform/service-notifications.md).
