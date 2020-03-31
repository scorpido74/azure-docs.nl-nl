---
title: Runbooks uitvoeren op Azure Automation Hybrid Runbook Worker
description: In dit artikel vindt u informatie over het uitvoeren van runbooks op machines in uw lokale datacenter of cloudprovider met de rol Hybride Runbook Worker.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: 9b9196cde45686e42d1baf7faedf94bdb73acccc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367054"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Runbooks uitvoeren op een hybride runbookworker

Runbooks die zich richten op een hybride runbook worker beheren doorgaans resources op de lokale computer of tegen resources in de lokale omgeving waar de werknemer wordt geïmplementeerd. Beheer boeken in Azure Automation doorgaans resources in de Azure-cloud. Hoewel ze anders worden gebruikt, zijn runbooks die worden uitgevoerd in Azure Automation en runbooks die worden uitgevoerd op een hybride runbook worker identiek in structuur.

Wanneer u een runbook maakt om op een hybride runbookworker uit te voeren, moet u het runbook bewerken en testen op de machine die de werknemer host. De hostmachine beschikt over alle PowerShell-modules en netwerktoegang die nodig zijn om de lokale bronnen te beheren en te openen. Zodra u de runbook op de hybride runbook worker-machine hebt getest, u deze uploaden naar de Azure Automation-omgeving, waar het op de werknemer kan worden uitgevoerd. 

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook-machtigingen voor een hybride runbookworker

Omdat ze toegang hebben tot niet-Azure-bronnen, kunnen runbooks die worden uitgevoerd op een hybride runbook worker het verificatiemechanisme dat doorgaans wordt gebruikt door boeken uitvoeren die naar Azure-bronnen worden geauthenticeren, niet gebruiken. Een runbook biedt zijn eigen verificatie voor lokale bronnen of configureert verificatie met [beheerde identiteiten voor Azure-bronnen.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) U ook een Run As-account opgeven om een gebruikerscontext voor alle runbooks te bieden.

### <a name="runbook-authentication"></a>Runbook-verificatie

Standaard worden runbooks uitgevoerd op de lokale computer. Voor Windows worden ze uitgevoerd in de context van het lokale systeemaccount. Voor Linux, ze draaien in de context van de speciale gebruikersaccount **nxautomation**. In beide scenario's moeten de runbooks hun eigen verificatie bieden aan bronnen die ze openen.

U [referentie-](automation-credentials.md) en [certificaatelementen](automation-certificates.md) in uw runbook gebruiken met cmdlets waarmee u referenties opgeven, zodat de runbook kan verifiëren naar verschillende bronnen. In het volgende voorbeeld wordt een gedeelte van een runbook weergegeven dat een computer opnieuw start. Het haalt referenties op van een referentie-asset en de naam van de `Restart-Computer` computer uit een variabel actief en gebruikt deze waarden vervolgens met de cmdlet.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

U ook een [InlineScript-activiteit](automation-powershell-workflow.md#inlinescript) gebruiken. `InlineScript`hiermee u codeblokken uitvoeren op een andere computer met referenties die zijn opgegeven door de [algemene parameter PSCredential.](/powershell/module/psworkflow/about/about_workflowcommonparameters)

### <a name="run-as-account"></a>Run As-account

In plaats van dat uw runbook zijn eigen verificatie aan lokale bronnen biedt, u een Run As-account opgeven voor een groep hybride runbookworker. Om dit te doen, moet u een [referentie-asset](automation-credentials.md) definiëren die toegang heeft tot lokale bronnen. Deze bronnen omvatten certificaatopslag en alle runbooks worden uitgevoerd onder deze referenties op een hybride runbookworker in de groep.

De gebruikersnaam voor de referentie moet zich in een van de volgende indelingen bevindt:

* Domein\gebruikersnaam
* username@domain
* gebruikersnaam (voor accounts lokaal naar de on-premises computer)

Gebruik de volgende procedure om een Run As-account op te geven voor een groep hybride runbookwerker.

1. Maak een [referentie-asset](automation-credentials.md) met toegang tot lokale bronnen.
2. Open het automatiseringsaccount in de Azure-portal.
3. Selecteer de tegel **Hybride werknemersgroepen** en selecteer de groep.
4. Selecteer **Alle instellingen**, gevolgd door instellingen voor hybride **werknemersgroep**.
5. Wijzig de waarde van **Uitvoeren als** van **standaard** naar **aangepast**.
6. Selecteer de referentie en klik op **Opslaan**.

### <a name="managed-identities-for-azure-resources"></a><a name="managed-identities-for-azure-resources"></a>Beheerde identiteiten voor Azure-bronnen

Hybride runbook-werknemers op virtuele Azure-machines kunnen beheerde identiteiten voor Azure-resources gebruiken om te verifiëren voor Azure-resources. Beheerde identiteiten gebruiken voor Azure-resources in plaats van Run As-accounts biedt voordelen omdat u dat niet hoeft te doen:

* Exporteer het certificaat Uitvoeren als en importeer het vervolgens in de hybride runbookworker.
* Verleng het certificaat dat wordt gebruikt door het run as-account.
* Het object Uitvoeren als verbinding verwerken in uw runbook-code.

Volg de volgende stappen om een beheerde identiteit voor Azure-resources te gebruiken op een hybride runbookworker.

1. Maak een Azure VM.
2. Beheerde identiteiten configureren voor Azure-resources op de VM. Zie [Beheerde identiteiten configureren voor Azure-resources op een vm met behulp van de Azure-portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Geef de vm toegang tot een resourcegroep in Resource Manager. Raadpleeg [Een beheerde identiteit van het Windows VM-systeem gebruiken om toegang te krijgen tot Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Installeer de hybride runbook-werkmedewerker op de VM. Zie [Een Windows Hybrid Runbook Worker implementeren](automation-windows-hrw-install.md).
5. Werk het runbook bij om de cmdlet `Identity` [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) met de parameter te gebruiken om te verifiëren naar Azure-resources. Deze configuratie vermindert de noodzaak om een Run As-account te gebruiken en het bijbehorende accountbeheer uit te voeren.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`werkt voor een hybride runbookworker met behulp van een door het systeem toegewezen identiteit en één door de gebruiker toegewezen identiteit. Als u meerdere door de gebruiker toegewezen identiteiten gebruikt in de Hybride Runbook Worker, moet uw runbook de parameter *AccountId* opgeven om `Connect-AzAccount` een specifieke door de gebruiker toegewezen identiteit te selecteren.

### <a name="automation-run-as-account"></a><a name="runas-script"></a>Automatisering als account uitvoeren

Als onderdeel van uw geautomatiseerde bouwproces voor het implementeren van resources in Azure, hebt u mogelijk toegang nodig tot on-premises systemen om een taak of een reeks stappen in uw implementatiereeks te ondersteunen. Als u verificatie wilt bieden ten opzichte van Azure met het Run As-account, moet u het certificaat Run As installeren.

In de volgende PowerShell-runbook, **exportrunascertificatetohybridworker**genaamd, wordt het run As-certificaat geëxporteerd vanuit uw Azure Automation-account. De runbook downloadt en importeert het certificaat in het lokale machinecertificaatarchief op een hybride runbookworker die is verbonden met hetzelfde account. Zodra deze stap is voltooid, controleert de runbook of de werknemer met behulp van het Run As-account kan verifiëren aan Azure.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>Voor `Add-AzAccount` PowerShell-runbooks `Add-AzureRMAccount` en zijn `Connect-AzAccount`aliassen voor . Wanneer u in uw bibliotheekitems `Connect-AzAccount`zoekt, als `Add-AzAccount`u deze niet ziet, u uw modules gebruiken of u uw modules bijwerken in uw Automatiseringsaccount.

Ga als u klaar bent met het voorbereiden van het run as-account:

1. Sla het **runboek Export-RunAsCertificateToHybridWorker** op uw computer op met een **.ps1-extensie.**
2. Importeer het in uw Automatiseringsaccount.
3. Bewerk het runbook en wijzig `Password` de waarde van de variabele met uw eigen wachtwoord. 
4. Publiceer het runbook.
5. Voer de runbook uit en richt u op de groep Hybride runbookworker die runbooks uitvoert en verifieert met het run As-account. 
6. Controleer de taakstroom om te zien dat het certificaat wordt geïmporteerd in het lokale machinearchief en volgt met meerdere regels. Dit gedrag is afhankelijk van het aantal automatiseringsaccounts dat u in uw abonnement definieert en de mate van succes van de verificatie.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Taakgedrag op hybride runbook-werknemers

Azure Automation verwerkt taken op Hybride Runbook Workers iets anders dan taken die worden uitgevoerd in Azure-sandboxes. Een belangrijk verschil is dat er geen limiet is voor de duur van de taak op de runbook-werknemers. Runbooks die in Azure-sandboxes worden uitgevoerd, zijn beperkt tot drie uur vanwege [een eerlijk aandeel.](automation-runbook-execution.md#fair-share)

Voor een langlopend e-running runbook wilt u ervoor zorgen dat het bestand is tegen een eventuele herstart, bijvoorbeeld als de machine die de werknemer host opnieuw wordt opgestart. Als de hostmachine hybride runbookworker opnieuw wordt opgestart, wordt elke runbook-taak opnieuw gestart vanaf het begin of vanaf het laatste controlepunt voor PowerShell-werkstroomwerkboeken. Nadat een runbook-taak meer dan drie keer opnieuw is gestart, wordt deze opgeschort.

Vergeet niet dat taken voor hybride runbook-werknemers worden uitgevoerd onder het lokale systeemaccount op Windows of het **nxautomation-account** op Linux. Voor Linux moet u ervoor zorgen dat het **nxautomation-account** toegang heeft tot de locatie waar de runbook-modules worden opgeslagen. Wanneer u de cmdlet Installatiemodule gebruikt, moet u `Scope` AllUsers opgeven voor de parameter om ervoor te zorgen dat het **nxautomation-account** toegang heeft. [Install-Module](/powershell/module/powershellget/install-module) Zie [Bekende problemen voor PowerShell op niet-Windows-platforms voor](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms)meer informatie over PowerShell op Linux.

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Een runbook starten op een hybride runbookworker

[Het starten van een runbook in Azure Automation](automation-starting-a-runbook.md) beschrijft verschillende methoden voor het starten van een runbook. Het opstarten voor een runbook op een hybride runbookworker maakt gebruik van een optie **Run on** waarmee u de naam van een hybride runbookworker-groep opgeven. Wanneer een groep is opgegeven, haalt een van de werknemers in die groep het runbook op en voert deze uit. Als in uw runbook deze optie niet wordt opgegeven, wordt de runbook zoals gewoonlijk uitgevoerd in Azure Automation.

Wanneer u een runbook start in de Azure-portal, krijgt u de optie **Uitvoeren op** waarmee u **Azure** of **Hybrid Worker**selecteren. Als u **Hybride werker selecteert,** u de groep Hybride runbookworker kiezen in een vervolgkeuzelijst.

Gebruik `RunOn` de parameter `Start-AzureAutomationRunbook` met de cmdlet. In het volgende voorbeeld wordt Windows PowerShell gebruikt om een runbook met de naam **Test-Runbook** te starten op een hybride runbookworkergroep met de naam MyHybridGroup.

```azurepowershell-interactive
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> De `RunOn` parameter is `Start-AzureAutomationRunbook` toegevoegd aan versie 0.9.1 van Microsoft Azure PowerShell. U moet [de nieuwste versie downloaden](https://azure.microsoft.com/downloads/) als u een eerdere versie hebt geïnstalleerd. Installeer deze versie alleen op het werkstation waar u de runbook van PowerShell start. U hoeft deze niet te installeren op de hybride runbook worker-computer, tenzij u van plan bent om runbooks vanaf deze computer te starten.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Werken met ondertekende runbooks op een Windows Hybrid Runbook Worker

U een Windows Hybrid Runbook Worker configureren om alleen ondertekende runbooks uit te voeren.

> [!IMPORTANT]
> Zodra u een hybride runbookworker hebt geconfigureerd om alleen ondertekende runbooks uit te voeren, worden runbooks die niet zijn ondertekend, niet meer uitgevoerd op de werknemer.

### <a name="create-signing-certificate"></a>Ondertekeningscertificaat maken

In het volgende voorbeeld wordt een zelfondertekend certificaat gemaakt dat kan worden gebruikt voor het ondertekenen van runbooks. Met deze code wordt het certificaat gewaarittsitert en wordt het geëxporteerd, zodat de hybride runbookworker het later kan importeren. De duimafdruk wordt ook geretourneerd voor later gebruik bij het verwijzen naar het certificaat.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Certificaat importeren en werknemers configureren voor handtekeningvalidatie

Kopieer het certificaat dat u hebt gemaakt naar elke hybride runbookworker in een groep. Voer het volgende script uit om het certificaat te importeren en configureer de werknemers om handtekeningvalidatie op runbooks te gebruiken.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Uw runbooks ondertekenen met het certificaat

Als de hybride runbook-werknemers zijn geconfigureerd om alleen ondertekende runbooks te gebruiken, moet u runbooks ondertekenen die moeten worden gebruikt op de hybride runbookworker. Gebruik het volgende voorbeeld PowerShell-code om deze runbooks te ondertekenen.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Wanneer een runbook is ondertekend, moet u het importeren in uw Automatiseringsaccount en het publiceren met het handtekeningblok. Zie [Een runbook importeren uit een bestand importeren in Azure Automation](manage-runbooks.md#import-a-runbook)voor meer informatie over het importeren van runbooks.

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Werken met ondertekende runbooks op een Linux Hybrid Runbook Worker

Om met ondertekende runbooks te kunnen werken, moet een Linux Hybrid Runbook Worker de [GPG](https://gnupg.org/index.html) uitvoerbaar hebben op de lokale machine.

> [!IMPORTANT]
> Zodra u een hybride runbookworker hebt geconfigureerd om alleen ondertekende runbooks uit te voeren, worden runbooks die niet zijn ondertekend, niet meer uitgevoerd op de werknemer.

### <a name="create-a-gpg-keyring-and-keypair"></a>Een GPG-sleutelhanger en sleutelpaar maken

Als u de GPG-sleutelhanger en keypair wilt maken, gebruikt u het **nxautomation-account** hybrid Runbook Worker.

1. Gebruik de sudo-toepassing om in te loggen als het **nxautomation-account.**

    ```bash
    sudo su – nxautomation
    ```

2. Zodra u **nxautomation**gebruikt, genereert u het GPG-sleutelpaar. GPG begeleidt u door de stappen. U moet naam, e-mailadres, vervaldatum en wachtwoordzin opgeven. Dan wacht u tot er genoeg entropie op de machine is om de sleutel te kunnen genereren.

    ```bash
    sudo gpg --generate-key
    ```

3. Omdat de GPG-map is gegenereerd met sudo, moet u de eigenaar wijzigen in **nxautomation** met behulp van de volgende opdracht.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>De sleutelhanger beschikbaar maken voor de hybride runbookworker

Zodra de sleutelhanger is gemaakt, maakt u deze beschikbaar voor de hybride runbookworker. Wijzig het instellingenbestand **/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf** om de volgende `[worker-optional]`voorbeeldcode onder de bestandssectie op te nemen.

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Controleren of de handtekeningvalidatie is ingeschakeld

Als de validatie van handtekeningen is uitgeschakeld op de machine, moet u deze inschakelen door de volgende opdracht sudo uit te voeren. Vervang `<LogAnalyticsworkspaceId>` door uw werkruimte-id.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Een runbook ondertekenen

Zodra u handtekeningvalidatie hebt geconfigureerd, gebruikt u de volgende GPG-opdracht om een runbook te ondertekenen.

```bash
gpg –-clear-sign <runbook name>
```

Het ondertekende runbook `<runbook name>.asc`heet .

U het ondertekende runbook nu uploaden naar Azure Automation en het uitvoeren als een gewone runbook.

## <a name="next-steps"></a>Volgende stappen

* Zie [Een runbook starten in Azure Automation](automation-starting-a-runbook.md)voor meer informatie over de methoden voor het starten van een runbook.
* Zie [Een runbook](automation-edit-textual-runbook.md)bewerken in Azure Automation voor meer informatie over het gebruik van de tekstuele editor om met PowerShell-runbooks te werken in Azure Automation.
* Als uw runbooks niet zijn voltooid, controleert u de handleiding voor probleemoplossing voor uitvoeringsfouten voor [runbook.](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)
* Zie voor meer informatie over PowerShell, inclusief taalverwijzingen en leermodules, de [PowerShell-documenten](https://docs.microsoft.com/powershell/scripting/overview).
