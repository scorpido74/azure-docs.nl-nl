---
title: Diagnostische gegevens inschakelen in Azure Cloud Services met PowerShell | Microsoft Documenten
description: Meer informatie over het inschakelen van diagnoses voor cloudservices met PowerShell
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: tagore
ms.openlocfilehash: 76cdffed813fd182980b36f848e0ae42f3226539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386541"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Diagnostische gegevens inschakelen in Azure Cloud Services met PowerShell
U diagnostische gegevens zoals toepassingslogboeken, prestatiemeteritems etc. verzamelen van een Cloud Service met behulp van de Azure Diagnostics-extensie. In dit artikel wordt beschreven hoe u de Azure Diagnostics-extensie voor een cloudservice inschakelt met PowerShell.  Zie [Hoe u Azure PowerShell installeert en configureert](/powershell/azure/overview) voor de vereisten die nodig zijn voor dit artikel.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>De extensie voor diagnostische gegevens inschakelen als onderdeel van het implementeren van een cloudservice
Deze aanpak is van toepassing op het type scenario's voor continue integratie, waarbij de diagnostische extensie kan worden ingeschakeld als onderdeel van het implementeren van de cloudservice. Wanneer u een nieuwe Cloud Service-implementatie maakt, u de diagnostische extensie inschakelen door de parameter *ExtensionConfiguration* door te geven aan de cmdlet [Nieuw-AzureDeployment.](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) De parameter *ExtensionConfiguration* neemt een reeks diagnostische configuraties die kunnen worden gemaakt met behulp van de cmdlet [Nieuw-AzureServiceDiagnosticsExtensionConfig.](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0)

In het volgende voorbeeld ziet u hoe u diagnostiek voor een cloudservice inschakelen met een WebRole en WorkerRole, elk met een andere diagnostische configuratie.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Als het configuratiebestand `StorageAccount` een element met een opslagaccountnaam opgeeft, gebruikt de `New-AzureServiceDiagnosticsExtensionConfig` cmdlet automatisch dat opslagaccount. Om dit te laten werken, moet het opslagaccount in hetzelfde abonnement zitten als de Cloud Service die wordt geïmplementeerd.

Vanaf Azure SDK 2.6 worden de configuratiebestanden voor extensie die door de MSBuild-publicatiedoeluitvoer worden gegenereerd, de naam van het opslagaccount opgenomen op basis van de configuratietekenreeks voor diagnostische gegevens die is opgegeven in het serviceconfiguratiebestand (.cscfg). In het onderstaande script ziet u hoe u de configuratiebestanden van extensie ontleeden uit de publicatiedoeluitvoer en hoe u de diagnostische extensie voor elke rol configureert bij het implementeren van de cloudservice.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online hanteert een vergelijkbare aanpak voor geautomatiseerde implementaties van Cloud Services met de diagnostische extensie. Zie [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) voor een compleet voorbeeld.

Als `StorageAccount` er geen is opgegeven in de diagnoseconfiguratie, moet u de parameter *StorageAccountName* doorgeven aan de cmdlet. Als de parameter *StorageAccountName* is opgegeven, gebruikt de cmdlet altijd het opslagaccount dat is opgegeven in de parameter en niet het account dat is opgegeven in het configuratiebestand voor diagnose.

Als het diagnostischopslagaccount zich in een ander abonnement bevindt dan de Cloud Service, moet u de parameters *StorageAccountName* en *StorageAccountKey* expliciet doorgeven aan de cmdlet. De parameter *StorageAccountKey* is niet nodig wanneer het diagnostischopslagaccount zich in hetzelfde abonnement bevindt, omdat de cmdlet automatisch de sleutelwaarde kan opvragen en instellen wanneer de diagnostische extensie wordt ingeschakeld. Als het opslagaccount voor diagnostische gegevens echter een ander abonnement heeft, kan de cmdlet de sleutel mogelijk niet automatisch krijgen en moet u de sleutel expliciet opgeven via de parameter *StorageAccountKey.*

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>De extensie voor diagnostische gegevens inschakelen voor een bestaande cloudservice
U de cmdlet [Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) gebruiken om de configuratie van diagnostische gegevens in te schakelen of bij te werken op een cloudservice die al wordt uitgevoerd.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>De huidige configuratie van de extensie voor diagnostische gegevens ophalen
Gebruik de cmdlet [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) om de huidige diagnostische configuratie voor een cloudservice op te halen.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>De extensie voor diagnostische gegevens verwijderen
Als u diagnostische gegevens voor een cloudservice wilt uitschakelen, u de cmdlet [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) gebruiken.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Als u de diagnostische extensie hebt ingeschakeld met *set-AzureServiceDiagnosticsExtension* of de *Nieuw-AzureServiceDiagnosticsExtensionConfig* zonder de *parameter Role,* u de extensie verwijderen met *Remove-AzureServiceDiagnosticsExtension* zonder de parameter *Role.* Als de parameter *Role* is gebruikt bij het inschakelen van de extensie, moet deze ook worden gebruikt bij het verwijderen van de extensie.

De extensie voor diagnostische gegevens verwijderen voor elke afzonderlijke rol:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Volgende stappen
* Zie Diagnostische gegevens inschakelen in Azure Cloud Services [en Virtuele Machines](cloud-services-dotnet-diagnostics.md)voor aanvullende richtlijnen voor het gebruik van Azure-diagnoses en andere technieken om problemen op te lossen.
* In [het diagnoseconfiguratieschema](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot3) worden de verschillende xml-configuratiesopties voor de diagnostische extensie uitgelegd.
* Zie [Een virtuele Windows-machine maken met bewaking en diagnose met Azure Resource Manager-sjabloon](../virtual-machines/windows/extensions-diagnostics-template.md) voor meer informatie over het inschakelen van de diagnostische extensie voor virtuele machines.



