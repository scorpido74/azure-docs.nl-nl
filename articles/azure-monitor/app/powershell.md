---
title: Azure-toepassing Insights automatiseren met Power shell | Microsoft Docs
description: Het maken van resource-, waarschuwings-en beschikbaarheids tests in Power shell automatiseren met behulp van een Azure Resource Manager sjabloon.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: mbullwin
ms.openlocfilehash: b4f3d2eba70be39b23e86ebde3c71dfc7c19a374
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936705"
---
#  <a name="create-application-insights-resources-using-powershell"></a>Application Insights-resources maken met PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Dit artikel laat u zien hoe u het maken en bijwerken van [Application Insights](../../azure-monitor/app/app-insights-overview.md) resources automatisch kunt automatiseren met behulp van Azure resource management. Als onderdeel van een bouw proces kunt u bijvoorbeeld het volgende doen. Naast de basis Application Insights resource kunt u [Beschik baarheid-webtesten](../../azure-monitor/app/monitor-web-app-availability.md)maken, [waarschuwingen](../../azure-monitor/app/alerts.md)instellen, het [prijs schema](pricing.md)instellen en andere Azure-resources maken.

De sleutel voor het maken van deze resources is JSON-sjablonen voor [Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md). In een kort gezegd is de procedure: de JSON-definities van bestaande resources downloaden; para meters bepaalde waarden, zoals namen, en voer vervolgens de sjabloon uit wanneer u een nieuwe resource wilt maken. U kunt meerdere resources samenbundelen om ze allemaal in één keer te maken, bijvoorbeeld een app-monitor met beschikbaarheids tests, waarschuwingen en opslag voor continue export. Er zijn enkele finesses naar een aantal van de parameterizations, die hier wordt uitgelegd.

## <a name="one-time-setup"></a>Eenmalige installatie
Als u Power shell nog niet eerder hebt gebruikt met uw Azure-abonnement:

Installeer de Azure Power shell-module op de computer waarop u de scripts wilt uitvoeren:

1. Installeer het [installatie programma voor het micro soft-webplatform (V5 of hoger)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Gebruik het om Microsoft Azure Power shell te installeren.

## <a name="create-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon maken
Maak een nieuw. JSON-bestand: Bel het `template1.json` in dit voor beeld. Kopieer deze inhoud hierin:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "retentionInDays": "[variables('retentionInDays')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```



## <a name="create-application-insights-resources"></a>Application Insights-resources maken
1. Meld u in Power shell aan bij Azure:
   
    `Connect-AzAccount`
2. Voer een opdracht als volgt uit:
   
    ```PS
   
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` is de groep waar u de nieuwe resources wilt maken.
   * `-TemplateFile` moet vóór de aangepaste para meters worden uitgevoerd.
   * `-appName` de naam van de resource die u wilt maken.

U kunt andere para meters toevoegen: Hier vindt u de beschrijvingen in het gedeelte para meters van de sjabloon.

## <a name="to-get-the-instrumentation-key"></a>De instrumentatie sleutel ophalen
Nadat u een toepassings resource hebt gemaakt, wilt u de instrumentatie sleutel: 

```PS
    $resource = Find-AzResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Het prijs plan instellen

U kunt het [prijs plan](pricing.md)instellen.

Voor het maken van een app-resource met het ondernemings prijs schema, met behulp van de bovenstaande sjabloon:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|plan|
|---|---|
|1|Basic|
|2|Zakelijk|

* Als u alleen het standaard prijs plan voor Basic wilt gebruiken, kunt u de CurrentBillingFeatures-resource uit de sjabloon weglaten.
* Als u het prijs plan wilt wijzigen nadat de bron van het onderdeel is gemaakt, kunt u een sjabloon gebruiken die de resource ' micro soft. Insights/onderdelen ' weglaat. U kunt ook het knoop punt `dependsOn` van de facturerings resource weglaten. 

Als u het bijgewerkte prijs plan wilt controleren, bekijkt u de Blade **gebruik en geschatte kosten** in de browser. **Vernieuw de browser weergave** om er zeker van te zijn dat u de meest recente status ziet.



## <a name="add-a-metric-alert"></a>Een waarschuwing voor metrische gegevens toevoegen

Als u een metrische waarschuwing op hetzelfde moment als uw app-resource wilt instellen, voegt u code als volgt in het sjabloon bestand toe:

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Wanneer u de sjabloon aanroept, kunt u deze para meter eventueel toevoegen:

    `-responseTime 2`

U kunt natuurlijk para meters andere velden. 

Als u de type namen en configuratie details van andere waarschuwings regels wilt weten, maakt u een regel hand matig en inspecteert u deze in [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Een beschikbaarheids test toevoegen

Dit voor beeld is voor een ping-test (om één pagina te testen).  

**Er zijn twee onderdelen** in een beschikbaarheids test: de test zelf en de waarschuwing die u op de hoogte stelt van fouten.

Voeg de volgende code samen in het sjabloon bestand waarmee de app wordt gemaakt.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Als u de codes voor andere test locaties wilt detecteren, of als u het maken van complexere webtests wilt automatiseren, maakt u een voor beeld hand matig en vervolgens para meters u de code van [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Meer resources toevoegen

Voor het automatiseren van het maken van een andere bron, maakt u hand matig een voor beeld en kopieert en para meters de code vervolgens uit [Azure Resource Manager](https://resources.azure.com/). 

1. Open [Azure Resource Manager](https://resources.azure.com/). Ga omlaag door `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`, naar de resource van uw toepassing. 
   
    ![Navigatie in Azure Resource Explorer](./media/powershell/01.png)
   
    *Onderdelen* zijn de basis bronnen van Application Insights voor het weer geven van toepassingen. Er zijn afzonderlijke resources voor de bijbehorende waarschuwings regels en beschikbaarheids webtests.
2. Kopieer de JSON van het onderdeel naar de juiste plaats in `template1.json`.
3. Verwijder deze eigenschappen:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Open de secties webtests en alertrules en kopieer de JSON voor afzonderlijke items naar uw sjabloon. (Kopieer geen kopieën van de webtests of alertrules-knoop punten: Ga naar de items onder hen.)
   
    Elke webtest heeft een bijbehorende waarschuwings regel, zodat u beide kunt kopiëren.
   
    U kunt ook waarschuwingen over metrische gegevens toevoegen. [Metrische namen](powershell-alerts.md#metric-names).
5. Voeg deze regel in elke resource in:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>De sjabloon para meters
Nu moet u de specifieke namen vervangen door para meters. Als u [een sjabloon wilt para meters](../../azure-resource-manager/resource-group-authoring-templates.md), schrijft u expressies met behulp [van een set hulp functies](../../azure-resource-manager/resource-group-template-functions.md). 

U kunt niet slechts een deel van een teken reeks para meters. Gebruik daarom `concat()` om teken reeksen te bouwen.

Hier volgen enkele voor beelden van de vervangingen die u wilt maken. Er zijn verschillende exemplaren van elke vervanging. Mogelijk hebt u anderen nodig in uw sjabloon. In deze voor beelden worden de para meters en variabelen gebruikt die aan het begin van de sjabloon zijn gedefinieerd.

| find | Vervangen door |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (kleine letter) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>GUID en id verwijderen. |

### <a name="set-dependencies-between-the-resources"></a>Afhankelijkheden instellen tussen de resources
Azure moet de resources in strikte volg orde instellen. Als u er zeker van wilt zijn dat de installatie is voltooid voordat de volgende begint, voegt u afhankelijkheids lijnen toe:

* In de resource beschikbaarheids test:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* In de waarschuwings resource voor een beschikbaarheids test:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Volgende stappen
Andere automatiserings artikelen:

* [Maak een Application Insights resource](powershell-script-create-resource.md) snelle methode zonder een sjabloon te gebruiken.
* [Waarschuwingen instellen](powershell-alerts.md)
* [Webtests maken](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Diagnostische Azure-gegevens verzenden naar Application Insights](powershell-azure-diagnostics.md)
* [Implementeren in azure vanuit GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Release aantekeningen maken](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)