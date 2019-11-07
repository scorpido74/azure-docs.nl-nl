---
title: Azure-toepassing Insights automatiseren met Power shell | Microsoft Docs
description: Het maken en beheren van resources, waarschuwingen en beschikbaarheids tests in Power shell automatiseren met behulp van een Azure Resource Manager sjabloon.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/17/2019
ms.openlocfilehash: 5ae043c356559b2e675f05af3eb7eb61973eb170
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621941"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Application Insights-resources beheren met Power shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Dit artikel laat u zien hoe u het maken en bijwerken van [Application Insights](../../azure-monitor/app/app-insights-overview.md) resources automatisch kunt automatiseren met behulp van Azure resource management. Als onderdeel van een bouw proces kunt u bijvoorbeeld het volgende doen. Naast de basis Application Insights resource kunt u [Beschik baarheid-webtesten](../../azure-monitor/app/monitor-web-app-availability.md)maken, [waarschuwingen](../../azure-monitor/app/alerts.md)instellen, het [prijs schema](pricing.md)instellen en andere Azure-resources maken.

De sleutel voor het maken van deze resources is JSON-sjablonen voor [Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md). De basis procedure is: de JSON-definities van bestaande resources downloaden; para meters bepaalde waarden, zoals namen, en voer vervolgens de sjabloon uit wanneer u een nieuwe resource wilt maken. U kunt meerdere resources samenbundelen om ze allemaal in één keer te maken, bijvoorbeeld een app-monitor met beschikbaarheids tests, waarschuwingen en opslag voor continue export. Er zijn enkele finesses naar een aantal van de parameterizations, die hier wordt uitgelegd.

## <a name="one-time-setup"></a>Eenmalige installatie
Als u Power shell nog niet eerder hebt gebruikt met uw Azure-abonnement:

Installeer de Azure Power shell-module op de computer waarop u de scripts wilt uitvoeren:

1. Installeer het [installatie programma voor het micro soft-webplatform (V5 of hoger)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Gebruik het om Microsoft Azure Power shell te installeren.

Naast het gebruik van Resource Manager-sjablonen beschikt u over een uitgebreide set [Application Insights Power shell-cmdlets](https://docs.microsoft.com/powershell/module/az.applicationinsights), waarmee u eenvoudig Application Insights resources kunt configureren. De mogelijkheden die zijn ingeschakeld door de cmdlets zijn onder andere:

* Application Insights-resources maken en verwijderen
* Een lijst met Application Insights resources en de bijbehorende eigenschappen ophalen
* Continue export maken en beheren
* Toepassings sleutels maken en beheren
* Het dagelijks kapje instellen
* Het prijs plan instellen

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Application Insights-resources maken met een Power shell-cmdlet

U kunt als volgt een nieuwe Application Insights-resource maken in het Azure-Oost-Data Center met behulp van de cmdlet [New-AzApplicationInsights](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights) :

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Application Insights resources maken met een resource manager-sjabloon

U kunt als volgt een nieuwe Application Insights resource maken met behulp van een resource manager-sjabloon.

### <a name="create-the-azure-resource-manager-template"></a>De Azure Resource Manager sjabloon maken

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
                    "retentionInDays": "[parameters('retentionInDays')]",
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

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>De Resource Manager-sjabloon gebruiken om een nieuwe Application Insights resource te maken

1. Meld u in Power shell aan bij Azure met `$Connect-AzAccount`
2. Stel uw context in op een abonnement met `Set-AzContext "<subscription ID>"`
2. Voer een nieuwe implementatie uit om een nieuwe Application Insights-resource te maken:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` is de groep waar u de nieuwe resources wilt maken.
   * `-TemplateFile` moet plaatsvinden vóór de aangepaste para meters.
   * `-appName` de naam van de resource die u wilt maken.

U kunt andere para meters toevoegen: Hier vindt u de beschrijvingen in het gedeelte para meters van de sjabloon.

## <a name="get-the-instrumentation-key"></a>De instrumentatie sleutel ophalen

Nadat u een toepassings resource hebt gemaakt, wilt u de instrumentatie sleutel: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Als u een lijst met veel andere eigenschappen van uw Application Insights resource wilt weer geven, gebruikt u:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Aanvullende eigenschappen zijn beschikbaar via de-cmdlets:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Raadpleeg de [gedetailleerde documentatie](https://docs.microsoft.com/powershell/module/az.applicationinsights) voor de para meters voor deze cmdlets.  

## <a name="set-the-data-retention"></a>De Bewaar periode voor gegevens instellen 

Als u de huidige gegevens retentie voor uw Application Insights resource wilt ophalen, kunt u het OSS-hulp programma [ARMClient](https://github.com/projectkudu/ARMClient)gebruiken.  (Meer informatie over ARMClient van artikelen op [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) en de [Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Hier volgt een voor beeld van het gebruik van `ARMClient`om de huidige retentie te verkrijgen:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Als u de retentie wilt instellen, is de opdracht een soort gelijke PUT:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Voer de volgende handelingen uit om de gegevens retentie tot 365 dagen in te stellen met behulp van de bovenstaande sjabloon:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

Het volgende script kan ook worden gebruikt voor het wijzigen van de Bewaar periode. Kopieer dit script om op te slaan als `Set-ApplicationInsightsRetention.ps1`.

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Dit script kan vervolgens worden gebruikt als:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Het dagelijks kapje instellen

Als u de eigenschappen van het dagelijks kapje wilt ophalen, gebruikt u de cmdlet [set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) : 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Gebruik dezelfde cmdlet om de eigenschappen van het dagelijks kapje in te stellen. Om bijvoorbeeld de Cap in te stellen op 300 GB/dag, 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Het prijs plan instellen 

Gebruik de cmdlet [set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) om het huidige prijs plan op te halen: 

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Als u het prijs plan wilt instellen, gebruikt u dezelfde cmdlet met de `-PricingPlan` opgegeven:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

U kunt ook het prijs plan op een bestaande Application Insights resource instellen met behulp van de Resource Manager-sjabloon hierboven, waarbij de resource micro soft. Insights/onderdelen en het `dependsOn`-knoop punt van de facturerings bron worden wegge laten. Als u dit bijvoorbeeld wilt instellen op het abonnement per GB (voorheen het basis abonnement genoemd), voert u de volgende handelingen uit:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

|priceCode|Fonds|
|---|---|
|1|Per GB (voorheen de naam van het Basic-abonnement)|
|2|Per knoop punt (voorheen de naam van het bedrijfs plan)|

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

1. Open [Azure Resource Manager](https://resources.azure.com/). Navigeer door `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`naar de resource van uw toepassing. 
   
    ![Navigatie in Azure Resource Explorer](./media/powershell/01.png)
   
    *Onderdelen* zijn de basis bronnen van Application Insights voor het weer geven van toepassingen. Er zijn afzonderlijke resources voor de bijbehorende waarschuwings regels en beschikbaarheids webtests.
2. Kopieer de JSON van het onderdeel naar de juiste plaats in `template1.json`.
3. Verwijder deze eigenschappen:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Open de secties `webtests` en `alertrules` en kopieer de JSON voor afzonderlijke items naar uw sjabloon. (Kopieer niet van de `webtests`-of `alertrules` knooppunten: Ga naar de items onder deze knoop punten.)
   
    Elke webtest heeft een bijbehorende waarschuwings regel, zodat u beide kunt kopiëren.
   
    U kunt ook waarschuwingen over metrische gegevens toevoegen. [Metrische namen](powershell-alerts.md#metric-names).
5. Voeg deze regel in elke resource in:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>De sjabloon para meters
Nu moet u de specifieke namen vervangen door para meters. Als u [een sjabloon wilt para meters](../../azure-resource-manager/resource-group-authoring-templates.md), schrijft u expressies met behulp [van een set hulp functies](../../azure-resource-manager/resource-group-template-functions.md). 

U kunt niet slechts een deel van een teken reeks para meters, dus gebruik `concat()` om teken reeksen te bouwen.

Hier volgen enkele voor beelden van de vervangingen die u wilt maken. Er zijn verschillende exemplaren van elke vervanging. Mogelijk hebt u anderen nodig in uw sjabloon. In deze voor beelden worden de para meters en variabelen gebruikt die aan het begin van de sjabloon zijn gedefinieerd.

| find | vervangen door |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (kleine letter) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

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
