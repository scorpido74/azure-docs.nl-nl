---
title: Azure-toepassingsinzichten automatiseren met PowerShell | Microsoft Documenten
description: Automatiseer het maken en beheren van resources, waarschuwingen en beschikbaarheidstests in PowerShell met behulp van een Azure Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 9494b659b5b4357f3190c45d8cc72c4e130f0ecc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275878"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Resources voor toepassingsinzichten beheren met PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In dit artikel ziet u hoe u de creatie en update van [application insights-resources](../../azure-monitor/app/app-insights-overview.md) automatisch automatiseren met Azure Resource Management. U dit bijvoorbeeld doen als onderdeel van een bouwproces. Samen met de basisbron Application Insights u [webtests voor beschikbaarheid](../../azure-monitor/app/monitor-web-app-availability.md)maken, [waarschuwingen](../../azure-monitor/app/alerts.md)instellen, het [prijsschema](pricing.md)instellen en andere Azure-bronnen maken.

De sleutel tot het maken van deze resources zijn JSON-sjablonen voor [Azure Resource Manager.](../../azure-resource-manager/management/manage-resources-powershell.md) De basisprocedure is: download de JSON definities van bestaande bronnen; bepaalde waarden zoals namen te parameteriseren; en voer de sjabloon uit wanneer u een nieuwe bron wilt maken. U meerdere resources samenverpakken om ze allemaal in één keer te maken, bijvoorbeeld een app-monitor met beschikbaarheidstests, waarschuwingen en opslag voor continue export. Er zijn enkele subtiliteiten aan sommige van de parameterisaties, die we hier zullen uitleggen.

## <a name="one-time-setup"></a>Eenmalige installatie
Als u PowerShell nog niet eerder met uw Azure-abonnement hebt gebruikt:

Installeer de Azure Powershell-module op de machine waar u de scripts wilt uitvoeren:

1. [Microsoft Web Platform Installer (v5 of hoger) installeren.](https://www.microsoft.com/web/downloads/platform.aspx)
2. Gebruik het om Microsoft Azure Powershell te installeren.

Naast het gebruik van Resource Manager-sjablonen, is er een uitgebreide set [Application Insights PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/az.applicationinsights), waardoor het eenvoudig is om toepassingsinzichten-bronnen programmatisch te configureren. De mogelijkheden die door de cmdlets worden ingeschakeld, zijn:

* Bronnen voor Application Insights maken en verwijderen
* Lijsten met resources voor Application Insights en hun eigenschappen opmaken
* Continue export maken en beheren
* Toepassingssleutels maken en beheren
* Stel de dagelijkse limiet in
* Het prijsplan instellen

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Application Insights-bronnen maken met een PowerShell-cmdlet

U als u als volgende een nieuwe Application Insights-bron maken in het Azure East US-datacenter met de cmdlet [Nieuw-AzApplicationInsights:](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights)

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Resources voor toepassingsinzichten maken met een sjabloon Resourcemanager

U als u als voorbeeld een nieuwe Application Insights-bron maken met behulp van een resourcemanagersjabloon.

### <a name="create-the-azure-resource-manager-template"></a>De sjabloon Azure Resource Manager maken

Maak een nieuw .json-bestand - `template1.json` laten we het in dit voorbeeld noemen. Kopieer deze inhoud erin:

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
                "defaultValue": 0,
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
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
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

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>De sjabloon Resourcemanager gebruiken om een nieuwe application insights-bron te maken

1. Log in PowerShell in bij Azure met`$Connect-AzAccount`
2. Uw context instellen op een abonnement met`Set-AzContext "<subscription ID>"`
2. Voer een nieuwe implementatie uit om een nieuwe Application Insights-bron te maken:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName`is de groep waar u de nieuwe resources wilt maken.
   * `-TemplateFile`moeten plaatsvinden vóór de aangepaste parameters.
   * `-appName`De naam van de te maken bron.

U andere parameters toevoegen - u vindt hun beschrijvingen in het gedeelte parameters van de sjabloon.

## <a name="get-the-instrumentation-key"></a>Haal de instrumentatiesleutel op

Nadat u een toepassingsbron hebt hebt gemaakt, wilt u de instrumentatietoets: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Gebruik het als u een lijst met vele andere eigenschappen van uw toepassingsstatistiekenbron wilt weergeven:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Extra eigenschappen zijn beschikbaar via de cmdlets:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Raadpleeg de [gedetailleerde documentatie](https://docs.microsoft.com/powershell/module/az.applicationinsights) voor de parameters voor deze cmdlets.  

## <a name="set-the-data-retention"></a>De gegevensbewaring instellen 

Als u de huidige gegevensbewaring voor uw Application Insights-bron wilt verkrijgen, u de OSS-tool [ARMClient gebruiken.](https://github.com/projectkudu/ARMClient)  (Meer informatie over ARMClient uit artikelen van [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) en [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Hier is een `ARMClient`voorbeeld met behulp van , om de huidige retentie te krijgen:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Als u de bewaarfunctie wilt instellen, is de opdracht een vergelijkbare PUT:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Voer het opslaan van gegevens in op 365 dagen met de bovenstaande sjabloon:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

Het volgende script kan ook worden gebruikt om retentie te wijzigen. Kopieer dit script `Set-ApplicationInsightsRetention.ps1`om op te slaan als .

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

## <a name="set-the-daily-cap"></a>Stel de dagelijkse dop in

Gebruik de cmdlet [Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) om de dagelijkse dopeigenschappen te krijgen: 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Als u de dagelijkse dopeigenschappen wilt instellen, gebruikt u dezelfde cmdlet. Bijvoorbeeld om de limiet in te stellen op 300 GB/dag,

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

U [ARMClient](https://github.com/projectkudu/ARMClient) ook gebruiken om dagelijkse limietparameters te krijgen en in te stellen.  Gebruik het als u de huidige waarden wilt krijgen:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>De tijd voor het opnieuw instellen van de dagelijkse dop instellen

Als u de tijd voor het instellen van de dagelijkse dop instellen, u [ARMClient](https://github.com/projectkudu/ARMClient)gebruiken. Hier volgt een `ARMClient`voorbeeld met , om de resettijd in te stellen op een nieuw uur (in dit voorbeeld 12:00 GMT):

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Het prijsplan instellen 

Gebruik de cmdlet [Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) om het huidige prijsplan op te halen:

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Als u het prijsplan wilt instellen, `-PricingPlan` gebruikt u dezelfde cmdlet met de opgegeven:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

U ook het prijsplan instellen voor een bestaande Application Insights-bron met de bovenstaande sjabloon Resourcebeheer, door de bron 'microsoft.insights/components' en het knooppunt van de `dependsOn` factureringsbron weg te laten. Als u het bijvoorbeeld wilt instellen op het plan Per GB (voorheen het Basisplan), voert u het volgende uit:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

De `priceCode` wordt gedefinieerd als:

|prijsCode|plannen|
|---|---|
|1|Per GB (voorheen het Basisplan genoemd)|
|2|Per knooppunt (voorheen het Enterprise-plan genoemd)|

Ten slotte u [ARMClient](https://github.com/projectkudu/ARMClient) gebruiken om prijsplannen en dagelijkse limietparameters te krijgen en in te stellen.  Gebruik het als u de huidige waarden wilt krijgen:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

En u al deze parameters instellen met behulp van:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

Hiermee wordt de dagelijkse limiet ingesteld op 200 GB/dag, configureert u de dagelijkse cap resettijd op 12:00 UTC, stuurt u e-mails wanneer de dop wordt geraakt en wordt het waarschuwingsniveau gehaald en stelt u de waarschuwingsdrempel in op 90% van de dop.  

## <a name="add-a-metric-alert"></a>Een metrische waarschuwing toevoegen

Als u het maken van metrische waarschuwingen wilt automatiseren, raadpleegt u het [sjabloonartikel met metrische waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>Een beschikbaarheidstest toevoegen

Raadpleeg het [sjabloonartikel met metrische waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-an-availability-test-along-with-a-metric-alert)om beschikbaarheidstests te automatiseren.

## <a name="add-more-resources"></a>Meer resources toevoegen

Als u het maken van een andere resource van welke aard dan ook wilt automatiseren, maakt u handmatig een voorbeeld en kopieert en parameteriseert u de code van [Azure Resource Manager.](https://resources.azure.com/) 

1. [Azure Resource Manager openen](https://resources.azure.com/). Navigeer omlaag `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`door, naar uw toepassingsbron. 
   
    ![Navigatie in Azure Resource Explorer](./media/powershell/01.png)
   
    *Componenten* zijn de basisbronnen voor Application Insights voor het weergeven van toepassingen. Er zijn afzonderlijke bronnen voor de bijbehorende waarschuwingsregels en beschikbaarheidswebtests.
2. Kopieer de JSON van de component `template1.json`naar de juiste plaats in .
3. Verwijder deze eigenschappen:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Open `webtests` de `alertrules` secties en kopieer de JSON voor afzonderlijke items naar uw sjabloon. (Kopieer niet vanuit `webtests` de `alertrules` of knooppunten: ga naar de items eronder.)
   
    Elke webtest heeft een bijbehorende waarschuwingsregel, dus u moet ze allebei kopiëren.
   
    U ook waarschuwingen opnemen over statistieken. [Metrische namen](powershell-alerts.md#metric-names).
5. Voeg deze regel in elke resource in:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>De sjabloon parameteriseren
Nu moet je de specifieke namen vervangen door parameters. Als [u een sjabloon wilt parameteriseren,](../../azure-resource-manager/templates/template-syntax.md)schrijft u expressies met behulp van een set [helperfuncties](../../azure-resource-manager/templates/template-functions.md). 

U niet alleen een deel van een `concat()` tekenreeks parameteriseren, dus gebruik om tekenreeksen te bouwen.

Hier volgen voorbeelden van de vervangingen die u wilt maken. Er zijn verschillende gevallen van elke substitutie. Mogelijk hebt u anderen in uw sjabloon nodig. Deze voorbeelden maken gebruik van de parameters en variabelen die we boven aan de sjabloon hebben gedefinieerd.

| find | vervangen door |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"`(kleine letters) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Afhankelijkheden tussen de resources instellen
Azure moet de resources in strikte volgorde instellen. Voeg afhankelijkheidslijnen toe om ervoor te zorgen dat de ene installatie is voltooid voordat de volgende begint:

* In de bron voor beschikbaarheidstests:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* In de waarschuwingsbron voor een beschikbaarheidstest:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Volgende stappen
Andere automatiseringsartikelen:

* [Maak een Application Insights-bron](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) - snelle methode zonder een sjabloon te gebruiken.
* [Waarschuwingen instellen](powershell-alerts.md)
* [Webtests maken](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Diagnostische Azure-gegevens verzenden naar Application Insights](powershell-azure-diagnostics.md)
* [Implementeren naar Azure vanuit GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Release-annotaties maken](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)