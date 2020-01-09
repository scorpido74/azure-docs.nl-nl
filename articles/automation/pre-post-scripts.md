---
title: Pre-en post scripts configureren voor uw Updatebeheer-implementatie in azure
description: In dit artikel wordt beschreven hoe u vooraf-scripts en post scripts voor update-implementaties configureert en beheert.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 35fba966fcdb6d1c5cd7c531bb22c9c78ae16ff3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417790"
---
# <a name="manage-pre-and-post-scripts"></a>Pre en post-scripts beheren

Met pre-scripts en post scripts kunt u Power shell-runbooks uitvoeren in uw Azure Automation-account vóór (vóór taak) en na (na taak) een update-implementatie. Voorafgaande en post scripts worden uitgevoerd in de Azure-context, niet lokaal. Pre-scripts worden uitgevoerd aan het begin van de update-implementatie. Post-scripts worden aan het einde van de implementatie uitgevoerd en nadat opnieuw opstarten is geconfigureerd.

## <a name="runbook-requirements"></a>Runbook-vereisten

Het runbook moet worden geïmporteerd in uw Automation-account en gepubliceerd om een runbook te kunnen gebruiken als pre-of post script. Zie [een Runbook publiceren](manage-runbooks.md#publish-a-runbook)voor meer informatie over dit proces.

## <a name="using-a-pre-script-or-post-script"></a>Een pre-script of post script gebruiken

Als u een pre-script of post script wilt gebruiken in een update-implementatie, moet u beginnen met het maken van een update-implementatie. Selecteer **pre-scripts + post scripts**. Met deze actie opent **u de pagina pre-scripts + post-scripts selecteren** .

![Scripts selecteren](./media/pre-post-scripts/select-scripts.png)

Selecteer het script dat u wilt gebruiken. In dit voor beeld gebruiken we het runbook **UpdateManagement-TurnOnVms** . Wanneer u het runbook selecteert, wordt de pagina **script configureren** geopend. Selecteer **vooraf script**en selecteer vervolgens **OK**.

Herhaal dit proces voor het script **UpdateManagement-TurnOffVms** . Maar wanneer u het **script type**kiest, selecteert u **post-script**.

In het gedeelte **geselecteerde items** worden nu de geselecteerde scripts weer gegeven. Een is een pre-script en de andere is een post script:

![Geselecteerde items](./media/pre-post-scripts/selected-items.png)

De configuratie van de update-implementatie volt ooien.

Wanneer de update-implementatie is voltooid, kunt u naar **Update-implementaties** gaan om de resultaten weer te geven. Zoals u kunt zien, is de status van het pre-script en het post script:

![Resultaten bijwerken](./media/pre-post-scripts/update-results.png)

Als u de update-implementatie-uitvoering selecteert, worden er aanvullende details weer gegeven voor de pre-en post-scripts. Er wordt een koppeling naar de script bron op het moment van de uitvoering gegeven.

![Resultaten van implementatie-uitvoering](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Para meters door geven

Wanneer u vooraf en post-scripts configureert, kunt u de para meters door geven, net zoals bij het plannen van een runbook. Para meters worden gedefinieerd op het moment dat de update-implementatie wordt gemaakt. Voorafgaande en post scripts ondersteunen de volgende typen:

* char
* DBCS
* integer
* omvang
* komma
* afzonderlijke
* Dubbelklik
* DateTime
* tekenreeksexpressie

Als u een ander object type nodig hebt, kunt u dit naar een ander type converteren met uw eigen logica in het runbook.

Naast uw standaard runbook-para meters wordt een andere para meter opgegeven: **SoftwareUpdateConfigurationRunContext**

Deze para meter is een JSON-teken reeks en als u de para meter in uw pre-of post script definieert, wordt deze automatisch door gegeven door de update-implementatie. De para meter bevat informatie over de update-implementatie. Dit is een subset van informatie die wordt geretourneerd door de [SoftwareUpdateconfigurations-API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). 

In de volgende tabel ziet u de eigenschappen die in de variabele zijn opgenomen.

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext-eigenschappen

|Eigenschap  |Beschrijving  |
|---------|---------|
|SoftwareUpdateConfigurationName     | De naam van de configuratie van de software-update.        |
|SoftwareUpdateConfigurationRunId     | De unieke ID voor de uitvoering.        |
|SoftwareUpdateConfigurationSettings     | Een verzameling eigenschappen die betrekking hebben op de configuratie van de software-update.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | De besturings systemen die zijn gericht op de update-implementatie.         |
|SoftwareUpdateConfigurationSettings.duration     | De maximale duur van het uitvoeren van de update-implementatie als `PT[n]H[n]M[n]S` per ISO8601; wordt ook wel het *onderhouds venster*genoemd.          |
|SoftwareUpdateConfigurationSettings.Windows     | Een verzameling eigenschappen die betrekking hebben op Windows-computers.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Een lijst met Kb's die zijn uitgesloten van de update-implementatie.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Update classificaties geselecteerd voor de update-implementatie.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Instellingen voor de update-implementatie opnieuw opstarten.        |
|azureVirtualMachines     | Een lijst met resourceIds voor de virtuele Azure-machines in de update-implementatie.        |
|nonAzureComputerNames|Een lijst met de FQDN-namen van niet-Azure-computers in de update-implementatie.|

Het volgende voor beeld is een JSON-teken reeks die is door gegeven aan de para meter **SoftwareUpdateConfigurationRunContext** :

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

Een volledig voor beeld met alle eigenschappen vindt u op: [Software-update configuratie op naam ophalen](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> Het `SoftwareUpdateConfigurationRunContext`-object kan dubbele vermeldingen voor machines bevatten. Dit kan tot gevolg hebben dat voor-en post scripts meerdere keren op dezelfde computer worden uitgevoerd. U kunt dit probleem omzeilen door `Sort-Object -Unique` alleen unieke VM-namen in uw script te selecteren.


## <a name="stopping-a-deployment"></a>Een implementatie stoppen

Als u een implementatie op basis van een pre-script wilt stoppen, moet u een uitzonde ring [genereren](automation-runbook-execution.md#throw) . Als dat niet het geval is, worden de implementatie en het post script nog steeds uitgevoerd. Het volgende code fragment laat zien hoe u een uitzonde ring genereert.

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

## <a name="samples"></a>Voorbeelden

Voor beelden voor voorafgaande en post scripts vindt u in de [Script Center-galerie](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) en de [PowerShell Gallery](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), of u kunt ze importeren via de Azure Portal. Als u dit wilt doen, selecteert u in uw Automation-account, onder **proces automatisering**, **Runbooks galerie**. Gebruik **updatebeheer** voor het filter.

![Galerie lijst](./media/pre-post-scripts/runbook-gallery.png)

U kunt ook zoeken naar de naam van het script, zoals wordt weer gegeven in de volgende lijst:

* Updatebeheer-Vm's inschakelen
* Updatebeheer-Vm's uitschakelen
* Updatebeheer script lokaal uitvoeren
* Updatebeheer-sjabloon voor pre/post-scripts
* Updatebeheer script uitvoeren met de opdracht uitvoeren

> [!IMPORTANT]
> Nadat u de runbooks hebt geïmporteerd, moet u deze publiceren voordat ze kunnen worden gebruikt. Hiertoe gaat u naar het runbook in uw Automation-account, selecteert u **bewerken**en selecteert u vervolgens **publiceren**.

De voor beelden zijn gebaseerd op de basis sjabloon die in het volgende voor beeld is gedefinieerd. Deze sjabloon kan worden gebruikt om uw eigen runbook te maken voor gebruik met voorafgaande en post scripts. De benodigde logica voor verificatie met Azure en het afhandelen van de para meter `SoftwareUpdateConfigurationRunContext` is opgenomen.

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

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
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
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

## <a name="interacting-with-machines"></a>Interactie met computers

Voorafgaande en post-taken worden uitgevoerd als een runbook in uw Automation-account en niet rechtstreeks op de computers in uw implementatie. Pre en post-tasks worden ook uitgevoerd in de Azure-context en hebben geen toegang tot niet-Azure-machines. In de volgende secties ziet u hoe u rechtstreeks met de computers kunt werken, ongeacht of het nu virtuele machines van Azure of niet-Azure is.

### <a name="interacting-with-azure-machines"></a>Interactie met Azure-machines

Pre en post-tasks worden uitgevoerd als runbooks en worden niet systeem eigen uitgevoerd op uw virtuele Azure-machines in uw implementatie. Als u met uw Azure-Vm's wilt communiceren, hebt u de volgende items nodig:

* Een uitvoeren als-account
* Een runbook dat u wilt uitvoeren

Als u wilt communiceren met Azure-machines, moet u de cmdlet [invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) gebruiken om te communiceren met uw Azure-vm's. Zie voor een voor beeld van hoe u dit doet, het runbook [-voor beeld updatebeheer: script uitvoeren met de opdracht Run](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interactie met niet-Azure-machines

Voorafgaande en natasken worden uitgevoerd in de Azure-context en hebben geen toegang tot niet-Azure-machines. Als u wilt communiceren met de niet-Azure-computers, hebt u de volgende items nodig:

* Een uitvoeren als-account
* Hybrid Runbook Worker geïnstalleerd op de computer
* Een runbook dat u lokaal wilt uitvoeren
* Een bovenliggend runbook

Een bovenliggend runbook wordt uitgevoerd in de Azure-context om te communiceren met niet-Azure-machines. Met dit runbook wordt een onderliggend runbook aangeroepen met de cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) . U moet de para meter `-RunOn` opgeven en de naam opgeven van de Hybrid Runbook Worker voor het script dat moet worden uitgevoerd. Zie voor meer informatie het runbook-voor beeld [updatebeheer: script lokaal uitvoeren](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Patch implementatie afbreken

Als uw pre-script een fout retourneert, wilt u uw implementatie mogelijk afbreken. Hiervoor moet u een fout in het script [genereren](/powershell/module/microsoft.powershell.core/about/about_throw) voor eventuele logica die een fout zou vormen.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Bekende problemen

* U kunt geen Booleaanse waarden, objecten of matrices door geven aan para meters wanneer u vooraf en post scripts gebruikt. Als u dit wel doet, mislukt het runbook. Zie [para meters door geven](#passing-parameters)voor een volledige lijst met ondersteunde typen.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelf studie voor meer informatie over het beheren van updates voor uw virtuele Windows-machines:

> [!div class="nextstepaction"]
> [Updates en patches voor uw Azure Windows-Vm's beheren](automation-tutorial-update-management.md)

