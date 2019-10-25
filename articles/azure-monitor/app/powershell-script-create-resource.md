---
title: Power shell-script voor het maken van een Application Insights resource | Microsoft Docs
description: Automatisch maken van Application Insights-resources.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/19/2016
ms.openlocfilehash: 11245d0f9d6e6b86a5d0249df65b33f851bee9d7
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820682"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>PowerShell-script om een Application Insights-resource te maken

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wanneer u een nieuwe toepassing of een nieuwe versie van een toepassing wilt bewaken, met [Azure-toepassing Insights](https://azure.microsoft.com/services/application-insights/), stelt u een nieuwe resource in Microsoft Azure in. Deze resource is de locatie waar de telemetriegegevens van uw app worden geanalyseerd en weer gegeven. 

U kunt het maken van een nieuwe resource automatiseren met behulp van Power shell.

Als u bijvoorbeeld een app voor mobiele apparaten ontwikkelt, is het waarschijnlijk dat er op elk moment verschillende gepubliceerde versies van uw app worden gebruikt door uw klanten. Het is niet nodig om de telemetrie-resultaten van verschillende versies te verkrijgen. U krijgt dan uw bouw proces voor het maken van een nieuwe resource voor elke build.

> [!NOTE]
> Als u een set resources tegelijk wilt maken, kunt u overwegen om [de resources te maken met behulp van een Azure-sjabloon](powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Script voor het maken van een Application Insights bron
Zie de relevante cmdlet-specificaties:

* [New-AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*Power shell-script*  

```powershell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzAccount / Connect-AzAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Wat u kunt doen met de iKey
Elke resource wordt geïdentificeerd aan de hand van de instrumentatie sleutel (iKey). De iKey is een uitvoer van het script voor het maken van resources. Uw build-script moet de iKey leveren aan de Application Insights SDK die in uw app is inge sloten.

Er zijn twee manieren om de iKey beschikbaar te maken voor de SDK:

* In [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Of in de [initialisatie code](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Zie ook
* [Application Insights-en web-test resources maken op basis van sjablonen](powershell.md)
* [Controle van Azure Diagnostics instellen met Power shell](powershell-azure-diagnostics.md) 
* [Waarschuwingen instellen met behulp van Power shell](powershell-alerts.md)

