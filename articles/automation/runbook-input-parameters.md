---
title: Invoerparameters van runbooks
description: Invoer parameters voor Runbook verg Roten de flexibiliteit van runbooks doordat u gegevens kunt door geven aan een runbook wanneer dit wordt gestart. In dit artikel worden verschillende scenario's beschreven waarbij invoer parameters worden gebruikt in runbooks.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be7d244f5aa422b2083d35fc56a52318a4379b79
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850224"
---
# <a name="runbook-input-parameters"></a>Invoerparameters van runbooks

Met de invoer parameters voor Runbook wordt de flexibiliteit van runbooks verhoogd, omdat u hiermee gegevens kunt door geven wanneer deze worden gestart. Met de para meters kunnen runbook-acties worden gericht op specifieke scenario's en omgevingen. In dit artikel doorloopt u verschillende scenario's waarin de invoer parameters worden gebruikt in runbooks.

## <a name="configure-input-parameters"></a>Invoer parameters configureren

Invoer parameters kunnen worden geconfigureerd in Power shell, Power shell workflow, python en grafische runbooks. Een runbook kan meerdere para meters met verschillende gegevens typen bevatten, of helemaal geen para meters. Invoer parameters kunnen verplicht of optioneel zijn, en u kunt een standaard waarde voor optionele para meters hebben. U wijst waarden toe aan de invoer parameters voor een runbook wanneer u deze via een van de beschik bare methoden start. Deze methoden omvatten het starten van een runbook vanuit de Azure Portal, een webservice of Power shell. U kunt ook een onderliggend runbook starten dat inline in een ander runbook wordt genoemd.

## <a name="configure-input-parameters-in-powershell-runbooks"></a>Invoer parameters configureren in Power shell-runbooks

Power shell-en Power shell-werk stroom-runbooks in Azure Automation ondersteunen invoer parameters die zijn gedefinieerd met de volgende kenmerken:  

| **Eigenschap** | **Beschrijving** |
|:--- |:--- |
| `Type` |Vereist. Het gegevens type wordt verwacht voor de parameter waarde. Elk .NET-type is geldig. |
| `Name` |Vereist. De naam van de para meter. Dit moet uniek zijn binnen het runbook en mag alleen letters, cijfers of onderstrepings tekens bevatten. De naam moet beginnen met een letter. |
| `Mandatory` |Optioneel. Hiermee geeft u op of er een waarde moet worden opgegeven voor de para meter. Als u dit instelt op **\$waar**, moet er een waarde worden opgegeven wanneer het runbook wordt gestart. Als u dit instelt op **\$False**, is een waarde optioneel. |
| `Default value` |Optioneel. Hiermee geeft u een waarde op die wordt gebruikt voor de para meter als een waarde niet wordt door gegeven wanneer het runbook wordt gestart. Een standaard waarde kan voor elke para meter worden ingesteld en de para meter wordt automatisch optioneel gemaakt, ongeacht de verplichte instelling. |

Windows Power shell ondersteunt meer kenmerken van invoer parameters dan de para meters die hier worden vermeld, zoals validatie, aliassen en parameter sets. Azure Automation ondersteunt momenteel echter alleen de voor gaande invoer parameters.

Een parameter definitie in Power shell workflow-runbooks heeft de volgende algemene vorm, waarbij meerdere para meters worden gescheiden door komma's.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

> [!NOTE]
> Als u para meters definieert en u het **verplichte** kenmerk niet opgeeft, wordt de para meter standaard als optioneel beschouwd. Als u een standaard waarde voor een para meter in Power shell workflow-runbooks instelt, wordt deze ook behandeld door Power shell als een optionele para meter, ongeacht de **verplichte** kenmerk waarde.

Als voor beeld configureren we de invoer parameters voor een Power shell workflow-runbook dat gegevens over virtuele machines, ofwel één virtuele machine of alle Vm's binnen een resource groep, uitvoert. Dit runbook heeft twee para meters, zoals wordt weer gegeven in de volgende scherm afbeelding: de naam van de virtuele machine en de naam van de resource groep.

![Werk stroom Automation Power shell](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

In deze parameter definitie zijn de para meters **\$VMName** en **\$resourceGroupName** eenvoudige para meters van het type teken reeks. Power shell-en Power shell-werk stroom-runbooks ondersteunen echter alle eenvoudige typen en complexe typen, zoals **object** -of **PSCredential** voor invoer parameters.

Als uw runbook een invoer parameter voor het object type heeft, gebruikt u een Power shell-hashtabel met (naam, waarde) paren om een waarde door te geven. Als u bijvoorbeeld de volgende para meter in een runbook hebt:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Vervolgens kunt u de volgende waarde door geven aan de para meter:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Wanneer u geen waarde doorgeeft aan een optionele `[String]` type para meter met de _standaard waarde_ `\$null`, is de waarde van de para meter een _lege teken reeks_, **niet** `\$null`.

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Invoer parameters configureren in grafische runbooks

Als u [een grafisch runbook](automation-first-runbook-graphical.md) met invoer parameters wilt configureren, gaan we een grafisch runbook maken dat gegevens over virtuele machines, ofwel één virtuele machine, ofwel alle vm's binnen een resource groep, uitvoert. Het configureren van een runbook bestaat uit twee belang rijke activiteiten, zoals hieronder wordt beschreven.

[**Runbooks verifiëren met een uitvoeren als-account van Azure**](automation-sec-configure-azure-runas-account.md) om te verifiëren met Azure.

[**Get-AzureRmVm**](/powershell/module/azurerm.compute/get-azurermvm) om de eigenschappen van een virtuele machine op te halen.

U kunt de activiteit [**Write-output**](/powershell/module/microsoft.powershell.utility/write-output) gebruiken om de namen van virtuele machines uit te voeren. De activiteit **Get-AzureRmVm** accepteert twee para meters, de naam van de **virtuele machine** en de naam van de **resource groep**. Omdat deze para meters elke keer dat u het runbook start, andere waarden moeten opgeven, kunt u invoer parameters toevoegen aan uw runbook. Hier volgen de stappen voor het toevoegen van invoer parameters:

1. Selecteer het grafische runbook in de Blade **Runbooks** en klik vervolgens op [**bewerken**](automation-graphical-authoring-intro.md) .
2. Klik in de runbook-editor op **invoer en uitvoer** om de Blade **invoer en uitvoer** te openen.

   ![Grafisch runbook voor automatisering](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. De Blade **invoer en uitvoer** bevat een lijst met invoer parameters die zijn gedefinieerd voor het runbook. Op deze Blade kunt u een nieuwe invoer parameter toevoegen of de configuratie van een bestaande invoer parameter bewerken. Als u een nieuwe para meter voor het runbook wilt toevoegen, klikt u op **invoer toevoegen** om de Blade **invoer parameter van runbook** te openen. Hier kunt u de volgende para meters configureren:

   | **Eigenschap** | **Beschrijving** |
   |:--- |:--- |
   | `Name` |Vereist. De naam van de para meter. Dit moet uniek zijn binnen het runbook en mag alleen letters, cijfers of onderstrepings tekens bevatten. De naam moet beginnen met een letter. |
   | `Description` |Optioneel. Beschrijving van het doel van de invoer parameter. |
   | `Type` |Optioneel. Het gegevens type dat wordt verwacht voor de parameter waarde. Ondersteunde parameter typen zijn **String**, **Int32**, **Int64**, **Decimal**, **Boolean**, **DateTime**en **object**. Als er geen gegevens type is geselecteerd, wordt standaard de **teken reeks**gebruikt. |
   | `Mandatory` |Optioneel. Hiermee geeft u op of er een waarde moet worden opgegeven voor de para meter. Als u **Ja**kiest, moet u een waarde opgeven wanneer het runbook wordt gestart. Als u **Nee**kiest, is een waarde niet vereist wanneer het runbook wordt gestart en kan er een standaard waarde worden ingesteld. |
   | `Default Value` |Optioneel. Hiermee geeft u een waarde op die wordt gebruikt voor de para meter als een waarde niet wordt door gegeven wanneer het runbook wordt gestart. Een standaard waarde kan worden ingesteld voor een para meter die niet verplicht is. Als u een standaard waarde wilt instellen, kiest u **aangepast**. Deze waarde wordt gebruikt tenzij er een andere waarde wordt gegeven wanneer het runbook wordt gestart. Kies **geen** als u geen standaard waarde wilt opgeven. |

    ![Nieuwe invoer toevoegen](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Maak twee para meters met de volgende eigenschappen die worden gebruikt door de activiteit **Get-AzureRmVm** :

   * **Parameter1:**
     * Naam-VMName
     * Type teken reeks
     * Verplicht-Nee
   * **Parameter2:**
     * Naam-resourceGroupName
     * Type teken reeks
     * Verplicht-Nee
     * Standaard waarde-aangepast
     * Aangepaste standaard waarde: \<naam van de resource groep die de virtuele machines bevat\>

5. Klik op **OK**als u de para meters hebt toegevoegd. U kunt deze nu bekijken op de **pagina invoer en uitvoer**. Klik nogmaals op **OK** en klik vervolgens op **Opslaan** en **publiceren** van uw runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Invoer parameters configureren in python-runbooks

In tegens telling tot Power shell, Power shell-werk stroom en grafische runbooks, hebben python-runbooks geen benoemde para meters.
Alle invoer parameters worden geparseerd als een matrix van argument waarden.
U opent de matrix door de `sys` module te importeren in uw python-script en vervolgens de `sys.argv`-matrix te gebruiken.
Het is belang rijk te weten dat het eerste element van de matrix, `sys.argv[0]`, de naam van het script is, zodat de eerste werkelijke invoer parameter `sys.argv[1]`is.

Zie [mijn eerste python-runbook in azure Automation](automation-first-runbook-textual-python2.md)voor een voor beeld van het gebruik van invoer parameters in een python-runbook.

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Waarden toewijzen aan invoer parameters in runbooks

U kunt waarden door geven aan invoer parameters in runbooks in de volgende scenario's:

### <a name="start-a-runbook-and-assign-parameters"></a>Een runbook starten en para meters toewijzen

Een runbook kan op diverse manieren worden gestart: via de Azure Portal, met een webhook, met Power shell-cmdlets, met de REST API of met de SDK. Hieronder worden verschillende methoden besproken voor het starten van een runbook en het toewijzen van para meters.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Een gepubliceerd runbook starten met behulp van de Azure Portal en para meters toewijzen

Wanneer u [het runbook start](start-runbooks.md#start-a-runbook-with-the-azure-portal), wordt de Blade **runbook starten** geopend en kunt u waarden opgeven voor de para meters die u hebt gemaakt.

![Beginnen met het gebruik van de portal](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

In het label onder het invoervak ziet u de kenmerken die zijn ingesteld voor de para meter. Kenmerken bevatten verplicht of optioneel, type en standaard waarde. In de Help-ballon naast de naam van de para meter ziet u alle belang rijke informatie die u nodig hebt voor het nemen van beslissingen over de invoer waarden van para meters. Deze informatie omvat of een para meter verplicht of optioneel is. Het bevat ook het type en de standaard waarde (indien van toepassing) en andere handige opmerkingen.

> [!NOTE]
> Teken reeks type parameters bieden ondersteuning voor **lege** teken reeks waarden.  Als u **[EmptyString]** opgeeft in het vak invoer parameter, wordt een lege teken reeks door gegeven aan de para meter. Daarnaast ondersteunen teken reeks type-para meters geen **Null** -waarden die worden door gegeven. Als u geen waarde doorgeeft aan de teken reeks parameter, wordt deze door Power shell geïnterpreteerd als null.

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Een gepubliceerd runbook starten met Power shell-cmdlets en para meters toewijzen

* **Azure Resource Manager-cmdlets:** U kunt een Automation-runbook dat is gemaakt in een resource groep starten met behulp van [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook).
  
  **Voorbeeld:**

  ```powershell
  $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
  ```

* **Cmdlets voor het klassieke Azure-implementatie model:** U kunt een Automation-runbook dat is gemaakt in een standaard resource groep starten met behulp van [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
  **Voorbeeld:**

  ```powershell
  $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
  Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
  ```

> [!NOTE]
> Wanneer u een runbook start met behulp van Power shell-cmdlets, wordt een standaard parameter **MicrosoftApplicationManagementStartedBy** gemaakt met de waarde **Power shell**. U kunt deze para meter weer geven op de pagina met **taak Details** .  

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Een runbook starten met behulp van een SDK en para meters toewijzen

* **Azure Resource Manager methode:** U kunt een runbook starten met behulp van de SDK van een programmeer taal. Hieronder vindt u C# een code fragment voor het starten van een runbook in uw Automation-account. U kunt alle code in onze github- [opslag plaats](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)bekijken.  

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

* **Methode voor het klassieke Azure-implementatie model:** U kunt een runbook starten met behulp van de SDK van een programmeer taal. Hieronder vindt u C# een code fragment voor het starten van een runbook in uw Automation-account. U kunt alle code in onze github- [opslag plaats](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)bekijken.

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

  U kunt deze methode starten door een woorden boek te maken voor het opslaan van de runbook-para meters, **VMName** en **resourceGroupName**, en hun waarden. Start vervolgens het runbook. Hieronder ziet u C# het code fragment voor het aanroepen van de hierboven gedefinieerde methode.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Een runbook starten met behulp van de para meters REST API en Assign

Een runbook-taak kan worden gemaakt en gestart met de Azure Automation REST API met behulp van de **put** -methode met de volgende aanvraag-URI: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`


Vervang de volgende para meters in de aanvraag-URI:

* **subscriptionId:** Uw Azure-abonnements-ID.  
* **resourceGroupName:** De naam van de resource groep voor het Automation-account.
* **automationAccountName:** De naam van uw Automation-account dat in de opgegeven Cloud service wordt gehost.  
* **jobName:** De GUID voor de taak. GUID'S in Power shell kunnen worden gemaakt met behulp van **[GUID]:: NewGuid (). Opdracht ToString ()** .

Als u para meters wilt door geven aan de runbook-taak, gebruikt u de hoofd tekst van de aanvraag. De volgende twee eigenschappen van de JSON-indeling worden gebruikt:

* **Runbook-naam:** Vereist. De naam van het runbook waarmee de taak moet worden gestart.  
* **Runbook-para meters:** Beschrijving. Een woorden lijst van de parameter lijst in (naam-, waarde-) indeling waarbij de naam van het teken reeks type is en de waarde een geldige JSON-waarde kan zijn.

Als u het ' **Get-AzureVMTextual-** runbook wilt starten dat eerder is gemaakt met **VMName** en **resourceGroupName** als para meters, gebruikt u de volgende JSON-indeling voor de hoofd tekst van de aanvraag.

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

Er wordt een HTTP-status code 201 geretourneerd als de taak is gemaakt. Zie het artikel over het [maken van een runbook-taak met behulp van de rest API](/rest/api/automation/job/create) voor meer informatie over antwoord headers en de antwoord tekst.

### <a name="test-a-runbook-and-assign-parameters"></a>Een runbook testen en para meters toewijzen

Wanneer u de [concept versie van uw runbook test](automation-testing-runbook.md) met behulp van de test optie, wordt de **test** pagina geopend en kunt u waarden configureren voor de para meters die u hebt gemaakt.

![Para meters testen en toewijzen](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Een planning aan een runbook koppelen en para meters toewijzen

U kunt [een planning](automation-schedules.md) aan uw runbook koppelen zodat het runbook op een specifiek tijdstip wordt gestart. U wijst invoer parameters toe wanneer u de planning maakt en het runbook gebruikt deze waarden wanneer het wordt gestart door de planning. U kunt de planning pas opslaan als alle verplichte parameter waarden zijn opgegeven.

![Para meters plannen en toewijzen](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Een webhook voor een runbook maken en para meters toewijzen

U kunt een [webhook](automation-webhooks.md) voor uw runbook maken en invoer parameters voor runbook configureren. U kunt de webhook pas opslaan als alle verplichte parameter waarden zijn opgegeven.

![Webhook maken en para meters toewijzen](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Wanneer u een runbook uitvoert met behulp van een webhook, wordt de vooraf gedefinieerde invoer parameter **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** verzonden, samen met de invoer parameters die u hebt gedefinieerd. U kunt op klikken om de para meter **WebhookData** uit te vouwen voor meer informatie.

![WebhookData-para meter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Een JSON-object door geven aan een runbook

Het kan handig zijn om gegevens op te slaan die u wilt door geven aan een runbook in een JSON-bestand.
U kunt bijvoorbeeld een JSON-bestand maken dat alle para meters bevat die u wilt door geven aan een runbook. Hiervoor moet u de JSON naar een teken reeks converteren en vervolgens de teken reeks converteren naar een Power shell-object voordat deze wordt door gegeven aan het runbook.

In dit voor beeld hebt u een Power shell-script waarmee [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) wordt aangeroepen om een Power shell-runbook te starten, waarbij de inhoud van de JSON wordt door gegeven aan het runbook.
Het Power shell-runbook start een virtuele Azure-machine, waarbij de para meters voor de virtuele machine worden opgehaald uit de JSON die is door gegeven in.

### <a name="create-the-json-file"></a>Het JSON-bestand maken

Typ de volgende test in een tekst bestand en sla deze op als `test.json` ergens op uw lokale computer.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Het runbook maken

Maak een nieuw Power shell-runbook met de naam ' test-JSON ' in Azure Automation.
Zie [mijn eerste Power shell-runbook](automation-first-runbook-textual-powershell.md)voor meer informatie over het maken van een nieuw Power shell-runbook.

Als u de JSON-gegevens wilt accepteren, moet het runbook een object als invoer parameter hebben.

Het runbook kan vervolgens de eigenschappen gebruiken die zijn gedefinieerd in de JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Sla dit runbook op en publiceer het in uw Automation-account.

### <a name="call-the-runbook-from-powershell"></a>Het runbook aanroepen vanuit Power shell

Nu kunt u het runbook aanroepen vanaf uw lokale computer met behulp van Azure PowerShell.
Voer de volgende PowerShell-opdrachten uit:

1. Meld u aan bij Azure:

   ```powershell
   Connect-AzureRmAccount
   ```

   U wordt gevraagd om uw Azure-referenties in te voeren.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** is nu een alias voor **Connect-AzureRmAccount**. Als u de bibliotheek items zoekt en u geen **Connect-AzureRMAccount**ziet, kunt u **add-AzureRMAccount**gebruiken, maar u kunt ook uw modules bijwerken in uw Automation-account.

1. De inhoud van het JSON-bestand ophalen en converteren naar een teken reeks:

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

   `JsonPath` is het pad waar u het JSON-bestand hebt opgeslagen.

1. De teken reeks inhoud van `$json` converteren naar een Power shell-object:

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Een hashtabel maken voor de para meters voor `Start-AzureRmAutomationRunbook`:

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   U ziet dat u de waarde van `Parameters` instelt op het Power shell-object dat de waarden uit het JSON-bestand bevat.
1. Het runbook starten

   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Volgende stappen

* Zie [een Runbook starten](automation-starting-a-runbook.md)voor meer informatie over de verschillende manieren om een runbook te starten.
* Zie [tekst Runbooks bewerken](automation-edit-textual-runbook.md)voor het bewerken van een tekst runbook.
* Als u een grafisch runbook wilt bewerken, raadpleegt u [grafisch ontwerpen in azure Automation](automation-graphical-authoring-intro.md).
