---
title: Invoerparameters voor runbook
description: Runbook-invoerparameters vergroten de flexibiliteit van runbooks doordat u gegevens doorgeven aan een runbook wanneer deze wordt gestart. In dit artikel worden verschillende scenario's beschreven waarin invoerparameters worden gebruikt in runbooks.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 17be351d4af3d277242af70ea96e8735a5f68bc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329082"
---
# <a name="runbook-input-parameters"></a>Invoerparameters voor runbook

Runbook-invoerparameters vergroten de flexibiliteit van een runbook doordat gegevens kunnen worden doorgegeven wanneer deze is gestart. Met deze parameters kunnen runbook-acties worden getarget voor specifieke scenario's en omgevingen. In dit artikel worden de configuratie en het gebruik van invoerparameters in uw runbooks beschreven.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="configuring-input-parameters"></a>Invoerparameters configureren

U invoerparameters configureren voor PowerShell-, PowerShell-werkstroom-, grafische en Python-runbooks. Een runbook kan meerdere parameters hebben met verschillende gegevenstypen of helemaal geen parameters. Invoerparameters kunnen verplicht of optioneel zijn en u standaardwaarden gebruiken voor optionele parameters.

U wijst waarden toe aan de invoerparameters voor een runbook wanneer u deze start. U een runbook starten vanuit de Azure-portal, een webservice of PowerShell. U er ook een starten als een onderliggend runbook dat inline wordt genoemd in een ander runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Invoerparameters configureren in PowerShell-runbooks

PowerShell- en PowerShell-werkstroomvoertboeken uit in azure automation-ondersteuningsinvoerparameters die zijn gedefinieerd via de volgende eigenschappen. 

| **Eigenschap** | **Beschrijving** |
|:--- |:--- |
| Type |Vereist. Het verwachte gegevenstype voor de parameterwaarde. Elk .NET-type is geldig. |
| Name |Vereist. De naam van de parameter. Deze naam moet uniek zijn in het runbook, moet beginnen met een letter en kan alleen letters, cijfers of tekens bevatten. |
| Verplicht |Optioneel. Booleaanse waarde die aangeeft of de parameter een waarde vereist. Als u dit **instelt op true,** moet een waarde worden opgegeven wanneer het runbook wordt gestart. Als u dit op **false**instelt, is een waarde optioneel. Als u geen waarde opgeeft voor de eigenschap **Verplicht,** beschouwt PowerShell de invoerparameter standaard als optioneel. |
| Standaardwaarde |Optioneel. Een waarde die voor de parameter wordt gebruikt als er geen invoerwaarde wordt doorgegeven wanneer het runbook wordt gestart. De runbook kan een standaardwaarde instellen voor elke parameter. |

Windows PowerShell ondersteunt meer kenmerken van invoerparameters dan de hierboven genoemde, zoals validatie, aliassen en parametersets. Azure Automation ondersteunt momenteel echter alleen de vermelde eigenschappen van de invoerparameter.

Laten we bijvoorbeeld een parameterdefinitie bekijken in een PowerShell-werkstroomrunboek. Deze definitie heeft de volgende algemene vorm, waarbij meerdere parameters worden gescheiden door komma's.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Laten we nu de invoerparameters configureren voor een PowerShell-werkstroomrunboek waarin details over virtuele machines worden uitgevoerd, één VM of alle VM's binnen een resourcegroep. Dit runbook heeft twee parameters, zoals weergegeven in de volgende schermafbeelding: de naam van de virtuele machine *(VMName*) en de naam van de resourcegroep *(resourceGroupName).*

![PowerShell-werkstroom voor automatisering](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

In deze parameterdefinitie zijn de invoerparameters eenvoudige parameters van typetekenreeks.

Houd er rekening mee dat PowerShell- en PowerShell-werkstroomlogboeken alle eenvoudige typen en complexe typen ondersteunen, zoals **object** of **PSCredential** voor invoerparameters. Als uw runbook een parameter voor objectinvoer heeft, moet u een PowerShell-hashtabel met naamwaardeparen gebruiken om een waarde door te geven. U hebt bijvoorbeeld de volgende parameter in een runbook.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

In dit geval u de volgende waarde doorgeven aan de parameter.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Wanneer u een waarde niet doorgeeft aan een optionele parameter String met een null-standaardwaarde, is de waarde van de parameter een lege tekenreeks in plaats van **Null**.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Invoerparameters configureren in grafische runbooks

Als u de configuratie van invoerparameters voor een grafisch runbook wilt illustreren, maken we een runbook dat details over virtuele machines uitvoert, één VM of alle VM's binnen een resourcegroep. Zie Mijn [eerste grafische runbook](automation-first-runbook-graphical.md)voor meer informatie.

Een grafisch runbook gebruikt deze belangrijke runbook-activiteiten:

* Configuratie van het Azure Run As-account om te verifiëren met Azure. 
* Definitie van een [Get-AzVM-cmdlet om VM-eigenschappen](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) te krijgen.
* Gebruik de activiteit Schrijfuitvoer om de [VM-namen](/powershell/module/microsoft.powershell.utility/write-output) uit te voeren. 

De **activiteit Get-AzVM** definieert twee ingangen, de VM-naam en de naam van de resourcegroep. Aangezien deze namen elke keer dat het runbook wordt gestart, kunnen verschillen, moet u invoerparameters aan uw runbook toevoegen om deze invoer te accepteren. Raadpleeg [grafische ontwerpen in Azure Automation](automation-graphical-authoring-intro.md).

Volg deze stappen om de invoerparameters te configureren.

1. Selecteer het grafische runbook op de pagina **Runbooks** en klik op **Bewerken**.
2. Klik in de grafische editor op de knop **Invoer en uitvoer en** voeg vervolgens invoer **toe** om het deelvenster Parameter van de invoer van runbook te openen.

   ![Dismuur grafisch runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Met het besturingselement Invoer en uitvoer wordt een lijst met invoerparameters weergegeven die zijn gedefinieerd voor het runbook. Hier u een nieuwe invoerparameter toevoegen of de configuratie van een bestaande invoerparameter bewerken. Als u een nieuwe parameter voor de runbook wilt toevoegen, klikt u op **Invoer toevoegen** om het **parameterblad runbook-invoer** te openen, waar u parameters configureren met behulp van de eigenschappen die zijn gedefinieerd in [Grafisch ontwerpen in Azure Automation](automation-graphical-authoring-intro.md).

    ![Nieuwe invoer toevoegen](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Maak twee parameters met de volgende eigenschappen die moeten worden gebruikt door de **activiteit Get-AzVM** en klik op **OK**.

   * Parameter 1:
        * **VMName** -- **naam**
        * **Tekst** -- Tekenreeks
        * **Verplicht** -- **nee**

   * Parameter 2:
        * **Name** -- **NaambronGroupName**
        * **Tekst** -- Tekenreeks
        * **Verplicht** -- **nee**
        * **Standaardwaarde** -- **Aangepast**
        * Aangepaste standaardwaarde : naam van de resourcegroep die de VM's bevat

5. Bekijk de parameters in het besturingselement Invoer en uitvoer. 
6. Klik nogmaals op **OK** en klik vervolgens op **Opslaan**.
7. Klik **op Publiceren** om uw runbook te publiceren.

### <a name="configure-input-parameters-in-python-runbooks"></a>Invoerparameters configureren in Python-runbooks

In tegenstelling tot PowerShell, PowerShell Workflow en grafische runbooks nemen Python runbooks geen benoemde parameters. De runbook-editor ontleden alle invoerparameters als een array met argumentwaarden. U hebt toegang tot de array door de **sys-module** in uw Python-script te importeren en vervolgens de **sys.argv-array** te gebruiken. Het is belangrijk op te merken dat `sys.argv[0]`het eerste element van de array, is de naam van het script. Daarom is de eerste werkelijke invoerparameter *sys.argv[1]*.

Zie [Mijn eerste Python-runbook in Azure Automation](automation-first-runbook-textual-python2.md)voor een voorbeeld van het gebruik van invoerparameters in een Python-runbook.

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Waarden toewijzen aan invoerparameters in runbooks

In deze sectie worden verschillende manieren beschreven om waarden door te geven aan invoerparameters in runbooks. U parameterwaarden toewijzen wanneer u:

* [Een runbook starten](#start-a-runbook-and-assign-parameters)
* [Een runbook testen](#test-a-runbook-and-assign-parameters)
* [Een planning voor het runbook koppelen](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Een webhook voor het runbook maken](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Een runbook starten en parameters toewijzen

Een runbook kan op vele manieren worden gestart: via de Azure-portal, met een webhook, met PowerShell-cmdlets, met de REST API of met de SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Een gepubliceerd runbook starten met de Azure-portal en parameters toewijzen

Wanneer u [de runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) start in de Azure-portal, wordt het **runbook-blad start** geopend en u waarden invoeren voor de parameters die u hebt gemaakt.

![De portal gebruiken](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

In het label onder het invoervak ziet u de eigenschappen die zijn ingesteld om parameterkenmerken te definiëren, bijvoorbeeld verplicht of optioneel, type, standaardwaarde. De helpballon naast de parameternaam definieert ook de belangrijkste informatie die nodig is om beslissingen te nemen over parameterinvoerwaarden. 

> [!NOTE]
> Tekenreeksparameters ondersteunen lege waarden van type Tekenreeks. Als **u [EmptyString]** invoert in het vak invoerparameter, wordt een lege tekenreeks doorgegeven aan de parameter. Ook bevatten tekenreeksparameters geen ondersteuning voor Null. Als u geen waarde doorgeeft aan een tekenreeksparameter, interpreteert PowerShell deze als Null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Een gepubliceerd runbook starten met PowerShell-cmdlets en parameters toewijzen

* **Cmdlets azure resource manager:** U een automatiseringsrunboek starten dat is gemaakt in een resourcegroep met [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
).

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Cmdlets voor het klassieke Azure-implementatiemodel:** U een automatiseringsrunboek starten dat is gemaakt in een standaardbrongroep met [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Wanneer u een runbook start met PowerShell-cmdlets wordt een standaardparameter, *MicrosoftApplicationManagementStartedBy,* gemaakt met de waarde **PowerShell**. U deze parameter weergeven in het deelvenster Taakgegevens.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Een runbook starten met een SDK en parameters toewijzen

* **Azure Resource Manager-methode:** U een runbook starten met de SDK van een programmeertaal. Hieronder vindt u een C#-codefragment voor het starten van een runbook in uw Automatiseringsaccount. U alle code bekijken op onze [GitHub repository.](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)  

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Azure-methode voor klassieke implementatiemodellen:** U een runbook starten met behulp van de SDK van een programmeertaal. Hieronder vindt u een C#-codefragment voor het starten van een runbook in uw Automatiseringsaccount. U alle code bekijken op onze [GitHub repository.](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   Als u deze methode wilt starten, maakt u een woordenboek om de runbookparameters *VMName* en *resourceGroupName* en hun waarden op te slaan. Start dan het loopboek. Hieronder vindt u het C#-codefragment voor het aanroepen van de hierboven gedefinieerde methode.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Een runbook starten met de REST API en parameters toewijzen

U een runbook-taak maken en starten met de Azure Automation REST API met behulp van de **PUT-methode** met de volgende aanvraag URI:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

Vervang in de aanvraag URI de volgende parameters:

* *abonnementId*: Uw Azure-abonnements-ID.  
* *resourceGroupName:* de naam van de resourcegroep voor het automatiseringsaccount.
* *automationAccountName:* de naam van het automatiseringsaccount dat wordt gehost binnen de opgegeven cloudservice.  
* *jobName:* De GUID voor de taak. GUIDs in PowerShell kunnen `[GUID]::NewGuid().ToString()*`worden gemaakt met behulp van .

Als u parameters wilt doorgeven aan de taak runbook, gebruikt u de aanvraaginstantie. Het neemt de volgende informatie, die in JSON-formaat:

* Runbook naam: Vereist. De naam van het runbook waar de taak moet worden gestart.  
* Runbook parameters: Optioneel. Een woordenboek van de parameterlijst in (naam, waarde) notatie, waarbij naam van het type String en waarde is, kan elke geldige JSON-waarde zijn.

Als u de **runbook Get-AzureVMTextual** wilt starten die eerder is gemaakt met *VMName* en *resourceGroupName* als parameters, gebruikt u de volgende JSON-indeling voor de aanvraaginstantie.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

Een HTTP-statuscode 201 wordt geretourneerd als de taak is gemaakt. Zie [Een runbook-taak maken met behulp van de REST API](/rest/api/automation/job/create)voor meer informatie over antwoordkoppen en de antwoordtekst.

### <a name="test-a-runbook-and-assign-parameters"></a>Een runbook testen en parameters toewijzen

Wanneer u [de conceptversie van uw runbook test](automation-testing-runbook.md) met de testoptie, wordt de **pagina Test** geopend. Gebruik deze pagina om waarden te configureren voor de parameters die u hebt gemaakt.

![Parameters testen en toewijzen](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Een planning koppelen aan een runbook en parameters toewijzen

U [een planning koppelen](automation-schedules.md) aan uw runbook, zodat het runbook op een bepaald tijdstip begint. U wijst invoerparameters toe wanneer u de planning maakt en de runbook gebruikt deze waarden wanneer deze door de planning wordt gestart. U de planning niet opslaan totdat alle verplichte parameterwaarden zijn opgegeven.

![Parameters plannen en toewijzen](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Een webhook maken voor een runbook en parameters toewijzen

U een [webhook](automation-webhooks.md) maken voor uw runbook en de invoerparameters van runbook configureren. U de webhook niet opslaan totdat alle verplichte parameterwaarden zijn opgegeven.

![Webhook maken en parameters toewijzen](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Wanneer u een runbook uitvoert met behulp van een webhook, wordt de vooraf gedefinieerde invoerparameter *[WebhookData](automation-webhooks.md)* verzonden, samen met de invoerparameters die u definieert. 

![WebhookData parameter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>Een JSON-object doorgeven aan een runbook

Het kan handig zijn om gegevens die u wilt doorgeven aan een runbook op te slaan in een JSON-bestand. U bijvoorbeeld een JSON-bestand maken dat alle parameters bevat die u aan een runbook wilt doorgeven. Om dit te doen, moet u de JSON-code converteren naar een tekenreeks en vervolgens de tekenreeks converteren naar een PowerShell-object voordat u deze doorgeeft aan het runbook.

In deze sectie wordt een voorbeeld gebruikt waarin een [PowerShell-script Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) aanroept om een PowerShell-runbook te starten, waarbij de inhoud van het JSON-bestand wordt doorgegeven aan het runbook. De PowerShell-runbook start een Azure VM door de parameters voor de VM uit het JSON-object te halen.

### <a name="create-the-json-file"></a>Het JSON-bestand maken

Typ de volgende code in een tekstbestand en sla deze op als `test.json` ergens op uw lokale computer.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Het runbook maken

Maak een nieuwe PowerShell-runbook met de naam **Test-Json** in Azure Automation. Zie [mijn eerste PowerShell-runbook](automation-first-runbook-textual-powershell.md).

Als u de JSON-gegevens wilt accepteren, moet het runbook een object als invoerparameter nemen. Het runbook kan vervolgens de eigenschappen gebruiken die zijn gedefinieerd in het JSON-bestand.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Sla dit runbook op en publiceer deze in uw Automatiseringsaccount.

### <a name="call-the-runbook-from-powershell"></a>Bel het runbook vanuit PowerShell

Nu u de runbook bellen vanaf uw lokale machine met Azure PowerShell. 

1. Meld u aan bij Azure zoals weergegeven. Daarna wordt u gevraagd uw Azure-referenties in te voeren.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Voor PowerShell-runbooks zijn **Add-AzAccount** en **Add-AzureRMAccount** aliassen voor **Connect-AzAccount**. Houd er rekening mee dat deze aliassen niet beschikbaar zijn voor grafische runbooks. Een grafisch runbook kan alleen **Connect-AzAccount** zelf gebruiken.

1. Haal de inhoud van het opgeslagen JSON-bestand op en converteer het naar een tekenreeks. `JsonPath`is het pad waar u het JSON-bestand hebt opgeslagen.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Converteer de `$json` tekenreeksinhoud van een PowerShell-object.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Maak een hashtable voor de parameters voor **Start-AzAutomationRunbook**. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   U stelt de waarde van *parameters* in op het PowerShell-object dat de waarden uit het JSON-bestand bevat.
1. Start het loopboek.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Volgende stappen

* Zie [Een runbook starten](automation-starting-a-runbook.md)voor meer informatie over verschillende manieren om een runbook te starten.
* Als u een tekstueel runbook wilt bewerken, raadpleegt u [Tekstuele runbooks bewerken](automation-edit-textual-runbook.md).
* Als u een grafisch runbook wilt bewerken, verwijst u naar [Grafische ontwerpen in Azure Automation](automation-graphical-authoring-intro.md).
