---
title: Pre-scripts en postscripts beheren in uw implementatie van UpdateBeheer in Azure
description: In dit artikel wordt beschreven hoe u prescripts en postscripts voor update-implementaties configureert en beheert.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 00cde5255f9c9a2baa7c7042ae2a8f73448da0ae
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679994"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Pre-scripts en postscripts beheren

Prescripts en postscripts worden uitgevoerd in uw Azure Automation-account voor (pre-task) en na (na taak) een update-implementatie. Prescripts en postscripts worden uitgevoerd in de Azure-context, niet lokaal. Prescripts worden uitgevoerd aan het begin van de update-implementatie. Postscripts worden uitgevoerd aan het einde van de implementatie en na eventuele reboots die zijn geconfigureerd.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="pre-script-and-post-script-requirements"></a>Vereisten voor voor script en postscript

Als u een runbook wilt gebruiken als een pre-script of post-script, moet u het importeren in uw Automatiseringsaccount en [het runbook publiceren.](manage-runbooks.md#publishing-a-runbook)

## <a name="pre-script-and-post-script-parameters"></a>Parameters voor voor script en postscript

Wanneer u prescripts en postscripts configureert, u parameters doorgeven, net als het plannen van een runbook. Parameters worden gedefinieerd op het moment van het maken van de implementatie van de update. Prescripts en postscripts ondersteunen de volgende typen:

* [char]
* [byte]
* [int]
* [lang]
* [decimaal]
* [single]
* [dubbel]
* [DateTime]
* [tekenreeks]

Pre-script- en postscriptrunbookparameters ondersteunen geen booleaanse, object- of arraytypen. Deze waarden zorgen ervoor dat de runbooks mislukken. 

Als u een ander objecttype nodig hebt, u het naar een ander type casten met uw eigen logica in het runbook.

Naast de standaard runbook parameters `SoftwareUpdateConfigurationRunContext` wordt de parameter (type JSON-tekenreeks) meegeleverd. Als u de parameter definieert in uw pre-script- of postscript-runbook, wordt deze automatisch doorgegeven door de update-implementatie. De parameter bevat informatie over de update-implementatie, een subset van informatie die wordt geretourneerd door de [API voor SoftwareUpdateconfiguraties.](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) Onderstaande secties definiëren de bijbehorende eigenschappen.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Eigenschappen van SoftwareUpdateConfigurationRunContext

|Eigenschap  |Beschrijving  |
|---------|---------|
|Naam softwareupdateconfiguratie     | De naam van de configuratie van de software-update.        |
|SoftwareUpdateConfigurationRunId     | De unieke ID voor de run.        |
|Instellingen voor softwareupdateconfiguratie     | Een verzameling eigenschappen met betrekking tot de configuratie van de software-update.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | De besturingssystemen die zijn gericht op de implementatie van de update.         |
|SoftwareUpdateConfigurationSettings.duration     | De maximale duur van `PT[n]H[n]M[n]S` de update-implementatie wordt uitgevoerd volgens ISO8601; ook wel het onderhoudsvenster genoemd.          |
|SoftwareUpdateConfigurationSettings.Windows     | Een verzameling eigenschappen met betrekking tot Windows-computers.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Een lijst met KB's die zijn uitgesloten van de update-implementatie.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassificaties     | Classificaties bijwerken die zijn geselecteerd voor de update-implementatie.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootInstelling     | Instellingen voor het opnieuw opstarten voor de implementatie van de update.        |
|azureVirtualMachines     | Een lijst met resourceId's voor de Azure VM's in de update-implementatie.        |
|nietAzureComputerNames|Een lijst met de fqdn-computers van niet-Azure-computers in de update-implementatie.|

Het volgende voorbeeld is een JSON-tekenreeks die is doorgegeven aan de parameter **SoftwareUpdateConfigurationRunContext:**

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ],
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Een volledig voorbeeld met alle eigenschappen is te vinden op: [Download software-update configuratie op naam](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> Het `SoftwareUpdateConfigurationRunContext` object kan dubbele vermeldingen voor machines bevatten. Dit kan ertoe leiden dat pre-scripts en postscripts meerdere keren op dezelfde machine worden uitgevoerd. Als u dit gedrag `Sort-Object -Unique` wilt omzeilen, gebruikt u om alleen unieke VM-namen te selecteren.

## <a name="using-a-pre-script-or-post-script-in-a-deployment"></a>Een prescript of postscript gebruiken in een implementatie

Als u een prescript of postscript wilt gebruiken in een update-implementatie, maakt u eerst een update-implementatie. Selecteer **Pre-scripts + Post-Scripts**. Met deze actie wordt de pagina **Prescripts selecteren + Postscripts** geopend.

![Scripts selecteren](./media/pre-post-scripts/select-scripts.png)

Selecteer het script dat u wilt gebruiken. In dit voorbeeld gebruiken we het **runbook UpdateManagement-TurnOnVms.** Wanneer u de runbook selecteert, wordt de pagina **Script configureren** geopend. Selecteer **Pre-Script**en selecteer **OK**.

Herhaal dit proces voor het **script UpdateManagement-TurnOffVms.** Maar wanneer u het **Script-type**kiest, selecteert u **Post-Script**.

In de sectie **Geselecteerde items** worden nu beide geselecteerde scripts weergegeven. Een daarvan is een pre-script en de andere is een post-script:

![Geselecteerde objecten](./media/pre-post-scripts/selected-items.png)

Het configureren van uw update-implementatie voltooien.

Wanneer uw update-implementatie is voltooid, u naar **Implementaties bijwerken** om de resultaten te bekijken. Zoals u zien, is de status beschikbaar voor het pre-script en post-script:

![Resultaten bijwerken](./media/pre-post-scripts/update-results.png)

Als u de implementatierun voor updates selecteert, krijgt u aanvullende details te zien van prescripts en postscripts. Een link naar de scriptbron op het moment van de run is aanwezig.

![Resultaten van implementatieuitvoeren](./media/pre-post-scripts/deployment-run.png)

es in uw script.

## <a name="stopping-a-deployment"></a>Een implementatie stoppen

Als u een implementatie wilt stoppen op basis van een voorscript, moet u een uitzondering [maken.](automation-runbook-execution.md#throw) Als u dit niet doet, worden de implementatie en het naschrift nog steeds uitgevoerd. In het volgende codefragment ziet u hoe u een uitzondering maken.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```



## <a name="interacting-with-machines"></a>Interactie met machines

Prescripts en posttaken worden uitgevoerd als runbooks in uw Automatiseringsaccount en niet rechtstreeks op de machines in uw implementatie. Pre-taken en posttaken worden ook uitgevoerd in de Azure-context en hebben geen toegang tot niet-Azure-machines. In de volgende secties ziet u hoe u rechtstreeks met de machines communiceren, of het nu Azure VM's of niet-Azure-machines zijn.

### <a name="interact-with-azure-machines"></a>Interactie met Azure-machines

Pre-taken en posttaken worden uitgevoerd als runbooks en worden niet in eigen land uitgevoerd op uw Azure VM's in uw implementatie. Als u wilt communiceren met uw Azure VM's, moet u de volgende items hebben:

* Een Run As-account
* Een runbook dat u wilt uitvoeren

Als u wilt communiceren met Azure-machines, moet u de cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) gebruiken om te communiceren met uw Azure VM's. Zie het voorbeeld Updatebeheer voor runbook [: voer script uit met opdracht Uitvoeren](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)voor een voorbeeld van hoe u dit doen.

### <a name="interact-with-non-azure-machines"></a>Interactie met niet-Azure-machines

Pre-taken en posttaken worden uitgevoerd in de Azure-context en hebben geen toegang tot niet-Azure-machines. Als u wilt communiceren met de niet-Azure-machines, moet u de volgende items hebben:

* Een Run As-account
* Hybride runbookworker geïnstalleerd op de machine
* Een runbook dat u lokaal wilt uitvoeren
* Een bovenliggend runbook

Als u wilt communiceren met niet-Azure-machines, wordt een bovenliggende runbook uitgevoerd in de Azure-context. Deze runbook roept een onderliggend runbook aan met de [Cmdlet Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) U moet `RunOn` de parameter opgeven en de naam opgeven van de hybride runbookworker waarop het script moet worden uitgevoerd. Zie het voorbeeld Updatebeheer van het runbook [– voer het script lokaal uit.](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44)

## <a name="aborting-patch-deployment"></a>Patchimplementatie afbreken

Als uw prescript een fout retourneert, u uw implementatie afbreken. Om dat te doen, moet u een fout in uw script [gooien](/powershell/module/microsoft.powershell.core/about/about_throw) voor elke logica die een fout zou vormen.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>Voorbeelden

Voorbeelden voor prescripts en postscripts zijn te vinden in de [Script Center Gallery](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) en de [PowerShell Gallery,](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)of u ze importeren via de Azure-portal. Selecteer hiervoor in uw automatiseringsaccount onder **Procesautomatisering**de optie **Runbooks Gallery**. **Updatebeheer** gebruiken voor het filter.

![Galerielijst](./media/pre-post-scripts/runbook-gallery.png)

U ze ook zoeken op hun scriptnaam, zoals in de volgende lijst wordt weergegeven:

* Beheer bijwerken - VM's inschakelen
* Updatebeheer - VM's uitschakelen
* Beheer bijwerken - Script lokaal uitvoeren
* Beheer bijwerken - Sjabloon voor pre/post-scripts
* Beheer bijwerken - Script uitvoeren met opdracht Uitvoeren

> [!IMPORTANT]
> Nadat u de runbooks hebt geïmporteerd, moet u ze publiceren voordat ze kunnen worden gebruikt. Zoek hiervoor het runbook in uw Automatiseringsaccount, selecteer **Bewerken**en selecteer **Publiceren**.

De voorbeelden zijn allemaal gebaseerd op de basissjabloon die in het volgende voorbeeld is gedefinieerd. Deze sjabloon kan worden gebruikt om uw eigen runbook te maken om te gebruiken met pre-scripts en post-scripts. De benodigde logica voor het verifiëren `SoftwareUpdateConfigurationRunContext` met Azure en het afhandelen van de parameter is opgenomen.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> Voor niet-grafische PowerShell-runbooks `Add-AzAccount` en `Add-AzureRMAccount` zijn aliassen voor [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). U deze cmdlets gebruiken of u [uw modules](automation-update-azure-modules.md) in uw Automation-account updaten naar de nieuwste versies. Mogelijk moet u uw modules bijwerken, zelfs als u zojuist een nieuw Automation-account hebt gemaakt.

## <a name="next-steps"></a>Volgende stappen

Ga verder met de volgende zelfstudie voor meer informatie over het beheren van updates voor uw virtuele Windows-machines:

> [!div class="nextstepaction"]
> [Updates en patches voor uw Windows Azure-VM's beheren](automation-tutorial-update-management.md)