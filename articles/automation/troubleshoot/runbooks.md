---
title: Fouten oplossen met Azure Automation Runbooks
description: Meer informatie over het oplossen en oplossen van problemen die u tegenkomen bij Azure Automation-runbooks.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b5d326d02587d6b5bd8fd73dcccfefdb13c47d57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500917"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Problemen in runbooks oplossen

Wanneer er fouten optreden bij het uitvoeren van runbooks in Azure Automation, u de volgende stappen gebruiken om de problemen te diagnosticeren.

1. **Zorg ervoor dat uw runbookscript succesvol wordt uitgevoerd op uw lokale machine.** 

    Raadpleeg de [PowerShell-documenten](/powershell/scripting/overview) of [Python-documenten](https://docs.python.org/3/) voor taalreferentie- en leermodules. Als u uw script lokaal uitvoert, kunnen veelvoorkomende fouten worden ontdekt en opgelost, zoals:

      * Ontbrekende modules
      * Syntaxisfouten
      * Logische fouten

2. **Onderzoek runbook [foutstreams](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output).**

    Kijk naar deze streams voor specifieke berichten en vergelijk ze met de fouten gedocumenteerd in dit artikel.

3. **Zorg ervoor dat uw knooppunten en automatiseringswerkruimte over de vereiste modules beschikken.** 

    Als uw runbook modules importeert, controleert u of ze beschikbaar zijn voor uw Automatiseringsaccount met behulp van de stappen die worden vermeld in [Importmodules.](../shared-resources/modules.md#import-modules) Werk uw modules bij naar de nieuwste versie door de instructies te volgen bij [Azure-modules bijwerken in Azure Automation.](..//automation-update-azure-modules.md) Zie Modules oplossen [voor](shared-resources.md#modules)meer informatie over probleemoplossing .

4. **Doen als uw runbook is opgeschort of onverwacht mislukt.**

    * [Controleer taakstatussen](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) definieert runbook statussen en een aantal mogelijke oorzaken.
    * [Voeg extra uitvoer toe](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) aan het runbook om te bepalen wat er gebeurt voordat het runbook wordt opgeschort.
    * [Eventuele uitzonderingen](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) verwerken die door uw taak worden gegenereerd.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scenario: Login-AzureRMAccount uitvoeren om in te loggen

### <a name="issue"></a>Probleem

U ontvangt de volgende fout bij het uitvoeren van een runbook:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Oorzaak

Deze fout kan optreden wanneer u geen Run As-account gebruikt of het Run As-account is verlopen. Zie [Azure Automation Run As-accounts beheren](https://docs.microsoft.com/azure/automation/manage-runas-account).

Deze fout heeft twee primaire oorzaken:

* Er zijn verschillende versies van de AzureRM- of Az-module.
* U probeert toegang te krijgen tot bronnen in een afzonderlijk abonnement.

### <a name="resolution"></a>Oplossing

Als u deze fout ontvangt na het bijwerken van één AzureRM- of Az-module, moet u al uw modules naar dezelfde versie bijwerken.

Als u toegang wilt tot bronnen in een ander abonnement, u de onderstaande stappen volgen om machtigingen te configureren.

1. Ga naar het automation run as-account en kopieer de toepassings-id en duimafdruk.
  ![Toepassings-id en duimafdruk kopiëren](../media/troubleshoot-runbooks/collect-app-id.png)
1. Ga naar het toegangsbeheer van het abonnement waar het automatiseringsaccount NIET wordt gehost en voeg een nieuwe roltoewijzing toe.
  ![Toegangsbeheer](../media/troubleshoot-runbooks/access-control.png)
1. Voeg de eerder verzamelde toepassings-id toe. Selecteer Machtigingen voor inzender.
   ![Roltoewijzing toevoegen](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Kopieer de naam van het abonnement.
1. U nu de volgende runbook-code gebruiken om de machtigingen van uw Automatiseringsaccount naar het andere abonnement te testen. Vervang `"\<CertificateThumbprint\>"` de waarde die u in stap 1 hebt gekopieerd. Vervang `"\<SubscriptionName\>"` de waarde die u in stap 4 hebt gekopieerd.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scenario: kan het Azure-abonnement niet vinden

### <a name="issue"></a>Probleem

U ontvangt de volgende fout `Select-AzureSubscription` `Select-AzureRmSubscription` bij het werken met de of cmdlet:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Fout

Deze fout kan optreden als:

* De naam van het abonnement is niet geldig.
* De Azure Active Directory-gebruiker die de abonnementsgegevens probeert te krijgen, is niet geconfigureerd als beheerder van het abonnement.

### <a name="resolution"></a>Oplossing

Volg de onderstaande stappen om te bepalen of u bent geverifieerd voor Azure en toegang hebt tot het abonnement dat u probeert te selecteren.

1. Test het buiten Azure Automation om ervoor te zorgen dat uw script zelfstandig werkt.
2. Zorg ervoor dat uw `Add-AzureAccount` script de cmdlet uitvoert voordat u de `Select-AzureSubscription` cmdlet uitvoert.
3. Toevoegen `Disable-AzureRmContextAutosave –Scope Process` aan het begin van uw runbook. Deze cmdlet-aanroep zorgt ervoor dat referenties alleen van toepassing zijn op de uitvoering van het huidige runbook.
4. Als u dit foutbericht nog steeds ziet, wijzigt u de code door de `AzureRmContext` parameter voor de `Add-AzureAccount` cmdlet toe te voegen en voert u de code uit.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scenario: Verificatie naar Azure is mislukt omdat meervoudige verificatie is ingeschakeld

### <a name="issue"></a>Probleem

U ontvangt de volgende fout wanneer u met uw Azure-gebruikersnaam en -wachtwoord naar Azure wordt geauthenticerd:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Oorzaak

Als u multi-factor-verificatie op uw Azure-account hebt, u geen Azure Active Directory-gebruiker gebruiken om te verifiëren aan Azure. In plaats daarvan moet u een certificaat of een serviceprincipal gebruiken om te verifiëren.

### <a name="resolution"></a>Oplossing

Als u een certificaat wilt gebruiken met azure-klassieke implementatiemodelcmdlets, raadpleegt u [Het maken en toevoegen van een certificaat om Azure-services te beheren.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Zie [Serviceprincipal maken met Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) en Een [serviceprincipal verifiëren met Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)als u een serviceprincipal wilt gebruiken met azure resource manager.

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scenario: U ziet een fout in uw taakstromen over de get_SerializationSettings-methode

### <a name="issue"></a>Probleem

U ziet de volgende fout in uw taakstromen voor een runbook:

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

### <a name="cause"></a>Oorzaak

Deze fout wordt veroorzaakt door het gebruik van zowel AzureRM- als Az-modulecmdlets in een runbook. Dit gebeurt wanneer u de Az-module importeert voordat u de AzureRM-module importeert.

### <a name="resolution"></a>Oplossing

Az- en AzureRM-cmdlets kunnen niet worden geïmporteerd en gebruikt in hetzelfde runbook. Zie [Az-moduleondersteuning in Azure Automation](../az-modules.md)voor meer informatie over Az-cmdlets in Azure Automation.

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Scenario: Het runbook mislukt met de fout: een taak is geannuleerd

### <a name="issue"></a>Probleem

Uw runbook mislukt met een fout die vergelijkbaar is met het volgende voorbeeld:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt door het gebruik van verouderde Azure-modules.

### <a name="resolution"></a>Oplossing

U deze fout oplossen door uw Azure-modules bij te werken naar de nieuwste versie. 

1. Klik in uw Automatiseringsaccount op **Modules**en **vervolgens Azure-modules bijwerken**. 
2. De update duurt ongeveer 15 minuten. Zodra het is voltooid, voert u het runbook opnieuw uit dat is mislukt.

Zie [Azure-modules bijwerken in Azure Automation](../automation-update-azure-modules.md)voor meer informatie over het bijwerken van uw modules.

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scenario: Runbooks mislukken bij het afhandelen van meerdere abonnementen

### <a name="issue"></a>Probleem

Bij het uitvoeren van runbooks kan de runbook geen Azure-bronnen beheren.

### <a name="cause"></a>Oorzaak

Het runbook gebruikt niet de juiste context tijdens het uitvoeren.

### <a name="resolution"></a>Oplossing

De abonnementscontext kan verloren gaan wanneer een runbook meerdere runbooks aanroept. Als u ervoor wilt zorgen dat de abonnementscontext wordt doorgegeven aan `Start-AzureRmAutomationRunbook` de runbooks, moet u de clientrunbook de context doorgeven aan de cmdlet in de `AzureRmContext` parameter. Gebruik `Disable-AzureRmContextAutosave` de cmdlet `Scope` met `Process` de parameterset om ervoor te zorgen dat de opgegeven referenties alleen worden gebruikt voor het huidige runbook. Zie [Werken met meerdere abonnementen](../automation-runbook-execution.md#working-with-multiple-subscriptions)voor meer informatie.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Scenario: Term niet herkend als de naam van een cmdlet, functie, script

### <a name="issue"></a>Probleem

Uw runbook mislukt met een fout die vergelijkbaar is met het volgende voorbeeld:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Oorzaak

Deze fout kan om de volgende redenen optreden:

* De module met de cmdlet wordt niet geïmporteerd in het account Automation.
* De module met de cmdlet wordt geïmporteerd, maar is verouderd.

### <a name="resolution"></a>Oplossing

Doe een van de volgende taken om deze fout op te lossen. 

* Zie [Azure PowerShell-modules bijwerken in Azure Automation](../automation-update-azure-modules.md) voor een Azure-module voor meer informatie over het bijwerken van uw modules in uw Automatiseringsaccount.

* Controleer bij een niet-Azure-module of de module in uw Automatiseringsaccount is geïmporteerd.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scenario: De start van de runbook-taak is drie keer geprobeerd, maar kon niet telkens worden gestart

### <a name="issue"></a>Probleem

Uw runbook mislukt met de volgende fout:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op als gevolg van een van de volgende problemen:

* Geheugenlimiet. Een taak kan mislukken als deze meer dan 400 MB geheugen gebruikt. De gedocumenteerde limieten voor geheugen die aan een sandbox zijn toegewezen, zijn te vinden op [de servicelimieten van Automatisering.](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) 

* Netwerksockets. Azure-sandboxes zijn beperkt tot 1000 gelijktijdige netwerksockets. Zie [Servicelimieten voor automatisering](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Module onverenigbaar. Afhankelijkheden van modules zijn mogelijk niet correct. In dit geval retourneert uw `Command not found` `Cannot bind parameter` runbook meestal een of bericht.

* Geen verificatie met Active Directory voor sandbox. Uw runbook heeft geprobeerd een uitvoerbaar of subproces aan te roepen dat wordt uitgevoerd in een Azure-sandbox. Het configureren van runbooks om te verifiëren met Azure AD met behulp van de Azure Active Directory Authentication Library (ADAL) wordt niet ondersteund.

* Te veel uitzonderingsgegevens. Uw runbook heeft geprobeerd te veel uitzonderingsgegevens naar de uitvoerstroom te schrijven.

### <a name="resolution"></a>Oplossing

* Geheugenlimiet, netwerksockets. Voorgestelde manieren om binnen de geheugenlimieten te werken, zijn om de werkbelasting te splitsen in meerdere runbooks, minder gegevens in het geheugen te verwerken, onnodige uitvoer uit uw runbooks te vermijden en na te gaan hoeveel controlepunten zijn geschreven in uw PowerShell-werkstroom runbooks. Gebruik de duidelijke methode, zoals `$myVar.clear`, om `[GC]::Collect` variabelen op te ruimen en te gebruiken om garbage collection onmiddellijk uit te voeren. Deze acties verminderen de geheugenvoetafdruk van uw runbook tijdens runtime.

* Module onverenigbaar. Werk uw Azure-modules bij door de stappen te volgen in [Azure PowerShell-modules bijwerken in Azure Automation.](../automation-update-azure-modules.md)

* Geen verificatie met Active Directory voor Sandbox. Wanneer u met een runbook naar Azure AD wordt gekoppeld, moet u ervoor zorgen dat de Azure AD-module beschikbaar is in uw Automatiseringsaccount. Zorg ervoor dat u het account Uitvoeren als de benodigde machtigingen verleent om de taken uit te voeren die het runbook automatiseert.

  Als uw runbook geen uitvoerbaar of subproces kan aanroepen dat wordt uitgevoerd in een Azure-sandbox, gebruikt u de runbook op een [hybride runbookworker](../automation-hrw-run-runbooks.md). Hybride werknemers worden niet beperkt door de geheugen- en netwerklimieten die Azure-sandboxes hebben.

* Te veel uitzonderingsgegevens. Er is een limiet van 1 MB op de taakuitvoerstroom. Zorg ervoor dat uw runbook oproepen omsluit `try` bij `catch` een uitvoerbaar of subproces met behulp en blokken. Als de bewerkingen een uitzondering maken, moet u de code het bericht van de uitzondering in een variabele Automatisering laten schrijven. Deze techniek voorkomt dat het bericht wordt geschreven in de taakuitvoerstream.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scenario: Aanmelden bij Azure-account is mislukt

### <a name="issue"></a>Probleem

U ontvangt een van de volgende `Add-AzureAccount` `Connect-AzureRmAccount` fouten bij het werken met de of cmdlet:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Oorzaak

Deze fouten treden op als de naam van de referentieasset niet geldig is. Ze kunnen ook optreden als de gebruikersnaam en het wachtwoord die u hebt gebruikt om het item Automatiseringsreferenties in te stellen, niet geldig zijn.

### <a name="resolution"></a>Oplossing

Ga als volgt te werk om te bepalen wat er mis is:

1. Zorg ervoor dat je geen speciale tekens hebt. Deze tekens `\@` bevatten het teken in de naam van de automatiseringsreferentieasset die u gebruikt om verbinding te maken met Azure.
2. Controleer of u de gebruikersnaam en het wachtwoord gebruiken die zijn opgeslagen in de Azure Automation-referenties in uw lokale PowerShell ISE-editor. Voer de volgende cmdlets uit in de PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Als uw verificatie lokaal mislukt, hebt u uw Azure Active Directory-referenties niet goed ingesteld. Raadpleeg het [Authenticeren naar Azure met Azure Active](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) Directory-blogbericht om het Azure Active Directory-account correct in te stellen.

4. Als de fout van voorbijgaande aard lijkt te zijn, probeert u logica opnieuw proberen toe aan uw verificatieroutine om het verifiëren robuuster te maken.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scenario: Objectverwijzing niet ingesteld op een instantie van een object

### <a name="issue"></a>Probleem

U ontvangt de volgende fout wanneer u `Wait` een onderliggende runbook met de parameter aanroept en de uitvoerstroom een object bevat:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Oorzaak

`Start-AzureRmAutomationRunbook`de uitvoerstroom niet correct verwerkt als de stream objecten bevat.

### <a name="resolution"></a>Oplossing

Het wordt aanbevolen om een polling-logica te implementeren en de cmdlet [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) te gebruiken om de uitvoer op te halen. Een voorbeeld van deze logica wordt hieronder gedefinieerd.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scenario: Runbook mislukt vanwege gedeserialiseerd object

### <a name="issue"></a>Probleem

Uw runbook mislukt met de fout:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Oorzaak

Als uw runbook een PowerShell-werkstroom is, worden complexe objecten in een gedeserialiseerde indeling opgeslagen om uw runbook-status voort te houden als de werkstroom wordt opgeschort.

### <a name="resolution"></a>Oplossing

Gebruik een van de volgende oplossingen om dit probleem op te lossen.

* Als u complexe objecten van de ene cmdlet naar de `InlineScript` andere begeeft, wikkelt u deze cmdlets in een activiteit.
* Geef de naam of waarde door die u van het complexe object nodig hebt in plaats van het hele object door te geven.
* Gebruik een PowerShell-runbook in plaats van een PowerShell-werkstroomwerkboek.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Scenario: Runbook-taak mislukt omdat toegewezen quotum is overschreden

### <a name="issue"></a>Probleem

Uw runbook-taak mislukt met de fout:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer de taakuitvoering het gratis quotum van 500 minuten voor uw account overschrijdt. Dit quotum is van toepassing op alle typen taakuitvoeringstaken. Sommige van deze taken zijn het testen van een taak, het starten van een taak vanuit de portal, het uitvoeren van een taak met behulp van webhooks of het plannen van een taak die moet worden uitgevoerd met behulp van de Azure-portal of uw datacenter. Zie [Automatiseringsprijzen](https://azure.microsoft.com/pricing/details/automation/)voor meer informatie over de prijzen voor automatisering.

### <a name="resolution"></a>Oplossing

Als u meer dan 500 minuten verwerking per maand wilt gebruiken, wijzigt u uw abonnement van de laag Gratis naar de laag Basis.

1. Meld u aan bij uw Azure-abonnement.
2. Selecteer het automatiseringsaccount om te upgraden.
3. Klik op **Instellingen**en vervolgens **op Prijzen**.
4. Klik op De pagina onderaan **inschakelen** om uw account te upgraden naar de basislaag.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scenario: Cmdlet niet herkend bij het uitvoeren van een runbook

### <a name="issue"></a>Probleem

Uw runbook-taak mislukt met de fout:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Oorzaak

Deze fout wordt veroorzaakt wanneer de PowerShell-engine de cmdlet die u gebruikt in uw runbook niet kan vinden. Het is mogelijk dat de module met de cmdlet ontbreekt in het account, er is een naamconflict met een runbooknaam of de cmdlet bestaat ook in een andere module en Automation kan de naam niet oplossen.

### <a name="resolution"></a>Oplossing

Gebruik een van de volgende oplossingen om het probleem op te lossen.

* Zorg ervoor dat u de naam van de cmdlet correct hebt ingevoerd.
* Zorg ervoor dat de cmdlet bestaat in uw Automatiseringsaccount en dat er geen conflicten zijn. Als u wilt controleren of de cmdlet aanwezig is, opent u een runbook in de bewerkingsmodus en zoekt u naar de cmdlet die u in de bibliotheek wilt vinden of voert u uit. `Get-Command <CommandName>` Zodra u hebt gevalideerd dat de cmdlet beschikbaar is voor het account en dat er geen naamconflicten zijn met andere cmdlets of runbooks, voegt u de cmdlet toe aan het canvas en controleert u of u een geldige parameterset in uw runbook gebruikt.
* Als u een naamconflict hebt en de cmdlet is beschikbaar in twee verschillende modules, los het probleem op door de volledig gekwalificeerde naam voor de cmdlet te gebruiken. U kunt bijvoorbeeld `ModuleName\CmdletName` gebruiken.
* Als u het runbook on-premises uitvoert in een hybride werknemersgroep, moet u ervoor zorgen dat de module en de cmdlet zijn geïnstalleerd op de machine die de hybride werknemer host.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scenario: Een langlopend e-running boek is niet voltooid

### <a name="issue"></a>Probleem

Je runbook wordt weergegeven in de status Gestopt nadat je 3 uur hebt uitgevoerd. Mogelijk ontvangt u deze fout ook:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Dit gedrag is ontworpen in Azure-sandboxes vanwege de [fair share-monitoring](../automation-runbook-execution.md#fair-share) van processen binnen Azure Automation. Als een proces langer dan drie uur duurt, stopt fair share automatisch een runbook. De status van een runbook dat voorbij de tijdslimiet voor een eerlijk aandeel gaat, verschilt per runbook-type. PowerShell- en Python-runbooks zijn ingesteld op de status Gestopt. PowerShell-werkstroomrunboeken zijn ingesteld op Mislukt.

### <a name="cause"></a>Oorzaak

Het runbook liep over de limiet van 3 uur toegestaan door fair share in een Azure sandbox.

### <a name="resolution"></a>Oplossing

Een aanbevolen oplossing is om het runbook uit te voeren op een [hybride runbook Worker.](../automation-hrw-run-runbooks.md) Hybride werknemers worden niet beperkt door de 3-uur fair share runbook limiet die Azure sandboxes hebben. Runbooks die worden uitgevoerd op Hybride Runbook Workers moeten worden ontwikkeld om opnieuw op startgedrag te ondersteunen als er onverwachte lokale infrastructuurproblemen zijn.

Een andere oplossing is om het runbook te optimaliseren door [onderliggende runbooks te](../automation-child-runbooks.md)maken. Als uw runbook dezelfde functie gebruikt op verschillende bronnen, bijvoorbeeld in een databasebewerking in verschillende databases, u de functie verplaatsen naar een onderliggende runbook. Elk onderliggend runbook wordt parallel uitgevoerd in een afzonderlijk proces. Dit gedrag vermindert de totale hoeveelheid tijd die het bovenliggende runbook moet voltooien.

De PowerShell-cmdlets waarmee het onderliggende runbook-scenario wordt ingeschakeld, zijn:

* [Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook). met deze cmdlet kunt u een runbook starten en parameters aan het runbook doorgeven.

* [Get-AzurermautomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob). Als er bewerkingen zijn die moeten worden uitgevoerd nadat het onderliggende runbook is voltooid, u met deze cmdlet de taakstatus voor elk kind controleren.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Scenario: Status: 400 Bad Request bij het aanroepen van een webhook

### <a name="issue"></a>Probleem

Wanneer u een webhook voor een Azure Automation-runbook probeert aan te roepen, ontvangt u de volgende fout:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Oorzaak

De webhook die u probeert te bellen is uitgeschakeld of is verlopen.

### <a name="resolution"></a>Oplossing

Als de webhook is uitgeschakeld, u de webhook opnieuw inschakelen via de Azure-portal. Als de webhook is verlopen, moet u deze verwijderen en opnieuw maken. U [een webhook](../automation-webhooks.md#renew-webhook) alleen vernieuwen als deze nog niet is verlopen.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scenario: 429: Het aanvraagpercentage is momenteel te groot...

### <a name="issue"></a>Probleem

U ontvangt het volgende foutbericht bij het uitvoeren van de `Get-AzureRmAutomationJobOutput` cmdlet:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Oorzaak

Deze fout kan optreden bij het ophalen van taakuitvoer uit een runbook met veel [Verbose-streams.](../automation-runbook-output-and-messages.md#verbose-stream)

### <a name="resolution"></a>Oplossing

Ga als een van de volgende handelingen te werk om deze fout op te lossen.

* Bewerk het runbook en verminder het aantal taakstromen dat wordt uitgezonden.

* Verminder het aantal streams dat moet worden opgehaald bij het uitvoeren van de cmdlet. Om dit te doen, `Stream` u de `Get-AzureRmAutomationJobOutput` waarde van de parameter instellen voor de cmdlet om alleen uitvoerstromen op te halen. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Scenario: PowerShell-taak mislukt met fout: Kan methode niet aanroepen

### <a name="issue"></a>Probleem

U ontvangt het volgende foutbericht bij het starten van een PowerShell-taak in een runbook die in Azure wordt uitgevoerd:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Oorzaak

Deze fout kan erop wijzen dat runbooks die in een Azure-sandbox worden uitgevoerd, niet kunnen worden uitgevoerd in de [modus Volledige taal.](/powershell/module/microsoft.powershell.core/about/about_language_modes)

### <a name="resolution"></a>Oplossing

Er zijn twee manieren om deze fout op te lossen.

* In plaats `Start-Job`van `Start-AzureRmAutomationRunbook` het gebruik, gebruiken om het runbook te starten.
* Probeer het runbook uit te voeren op een hybride runbook worker.

Zie [Runbook-gedrag voor](../automation-runbook-execution.md#runbook-behavior)meer informatie over dit gedrag en andere gedragingen van Azure Automation-runbooks.

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Scenario: Linux Hybrid Runbook Worker ontvangt een prompt voor een wachtwoord bij het ondertekenen van een runbook

### <a name="issue"></a>Probleem

Als `sudo` u de opdracht voor een Linux-hybride runbookworker uitvoert, wordt een onverwachte prompt voor een wachtwoord opgehaald.

### <a name="cause"></a>Oorzaak

Het **nxautomationuser** account voor de Log Analytics agent voor Linux is niet correct geconfigureerd in het **sudoers-bestand.** De Hybride Runbook Worker heeft de juiste configuratie van accountmachtigingen en andere gegevens nodig, zodat het runbooks kan ondertekenen op de Linux Runbook Worker.

### <a name="resolution"></a>Oplossing

* Zorg ervoor dat de Hybrid Runbook Worker de GnuPG (GPG) uitvoerbaar op de machine heeft.

* Controleer de configuratie van het **nxautomationuser-account** in het **sudoers-bestand.** Zie [Runbooks uitvoeren op een hybride runbookworker](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Scenario: Cmdlet faalt in PnP PowerShell-runbook op Azure Automation

### <a name="issue"></a>Probleem

Wanneer een runbook een door PnP PowerShell gegenereerd object rechtstreeks naar de Azure Automation-uitvoer schrijft, kan cmdlet-uitvoer niet worden gestreamd naar Automatisering.

### <a name="cause"></a>Oorzaak

Dit probleem treedt meestal op wanneer Azure Automation runbooks verwerkt die bijvoorbeeld `add-pnplistitem`PnP PowerShell-cmdlets aanroepen, zonder de retourobjecten te vangen.

### <a name="resolution"></a>Oplossing

Bewerk uw scripts om retourwaarden toe te wijzen aan variabelen, zodat de cmdlets niet proberen hele objecten naar de standaarduitvoer te schrijven. Een script kan de uitvoerstroom omleiden naar een cmdlet zoals hieronder wordt weergegeven.

```azurecli
  $null = add-pnplistitem
```

Als uw script cmdlet-uitvoer ontneemt, moet het script de uitvoer in een variabele opslaan en de variabele manipuleren in plaats van de uitvoer gewoon te streamen.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>Mijn probleem wordt hierboven niet vermeld

In de onderstaande secties worden andere veelvoorkomende fouten vermeld en worden ondersteunende documentatie weergegeven om u te helpen uw probleem op te lossen.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Er worden geen taken uitgevoerd met Hybrid Runbook Worker, of Hybrid Runbook Worker reageert niet

Als u taken uitvoert op een hybride runbookworker in plaats van in Azure Automation, moet u mogelijk [problemen oplossen met de hybride werknemer zelf.](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook mislukt zonder toestemming of enige variatie

Uitvoeren Als accounts mogelijk niet dezelfde machtigingen hebben voor Azure-bronnen als uw huidige account. Zorg ervoor dat uw Run As-account machtigingen heeft [om toegang te krijgen tot bronnen die](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) in uw script worden gebruikt.

### <a name="issues-passing-parameters-into-webhooks"></a>Problemen met het doorgeven van parameters in webhooks

Zie [Een runbook starten vanaf een webhook](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook)voor hulp bij het doorgeven van parameters in webhooks.

### <a name="issues-using-az-modules"></a>Problemen met Az-modules

Het gebruik van Az-modules en AzureRM-modules in hetzelfde Automatiseringsaccount wordt niet ondersteund. Zie [Az-modules in runbooks](https://docs.microsoft.com/azure/automation/az-modules) voor meer informatie.

### <a name="inconsistent-behavior-in-runbooks"></a>Inconsistent gedrag in runbooks

Volg de richtlijnen in [runbook-uitvoering](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) om problemen met gelijktijdige taken, resources die meerdere keren worden gemaakt of andere timinggevoelige logica in runbooks te voorkomen.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook mislukt met de fout Geen toestemming, Verboden (403), of een variatie

Uitvoeren Als accounts mogelijk niet dezelfde machtigingen hebben voor Azure-bronnen als uw huidige account. Zorg ervoor dat uw Run As-account machtigingen heeft [om toegang te krijgen tot bronnen die](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) in uw script worden gebruikt.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbooks werkten, maar zijn plotseling gestopt

* Controleer of het account Uitvoeren als niet is verlopen. Zie [verlenging van certificering](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Als u een [webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) gebruikt om een runbook te starten, moet u ervoor zorgen dat de webhook niet is verlopen.

### <a name="passing-parameters-into-webhooks"></a>Parameters doorgeven aan webhooks

Zie [Een runbook starten vanaf een webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook)voor hulp bij het doorgeven van parameters in webhooks.

### <a name="using-az-modules"></a>Az-modules gebruiken

Het gebruik van Az-modules en AzureRM-modules in hetzelfde Automatiseringsaccount wordt niet ondersteund. Zie [Az-modules in runbooks](https://docs.microsoft.com/azure/automation/az-modules).

### <a name="using-self-signed-certificates"></a>Zelfondertekende certificaten gebruiken

Zie Een nieuw certificaat [maken](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)als u zelfondertekende certificaten wilt gebruiken.

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Toegang geweigerd bij het gebruik van Azure sandbox voor een runbook

De Azure sandbox voorkomt toegang tot alle com-servers die niet worden verwerkt. Een sandbox-toepassing of runbook kan bijvoorbeeld geen aanroepen naar Windows Management Instrumentation (WMI) of in de Windows Installer-service (msiserver.exe). Zie [Runbook-uitvoering in Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution)voor meer informatie over het gebruik van de sandbox.

## <a name="recommended-documents"></a>Aanbevolen documenten

* [Een runbook starten in Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Runbook-uitvoering in Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met – het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, u een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
