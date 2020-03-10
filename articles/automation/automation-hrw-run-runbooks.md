---
title: Runbooks uitvoeren op Azure Automation Hybrid Runbook Worker
description: Dit artikel bevat informatie over het uitvoeren van runbooks op computers in uw lokale Data Center of Cloud provider met de functie Hybrid Runbook Worker.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: c67fff32770446cac3adef8af50c9e5733077bc7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372614"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Runbooks uitvoeren op een Hybrid Runbook Worker

Runbooks die gericht zijn op een Hybrid Runbook Worker, beheren doorgaans resources op de lokale computer of op basis van resources in de lokale omgeving waar de werk nemer wordt geïmplementeerd. Runbooks in Azure Automation beheren resources doorgaans in de Azure-Cloud. Hoewel ze anders worden gebruikt, zijn runbooks die worden uitgevoerd in Azure Automation en runbooks die worden uitgevoerd op een Hybrid Runbook Worker, in de structuur identiek.

Wanneer u een runbook ontwerpt om uit te voeren op een Hybrid Runbook Worker, moet u het runbook bewerken en testen op de computer die als host fungeert voor de werk nemer. De hostcomputer heeft alle Power shell-modules en netwerk toegang die vereist zijn voor het beheren en openen van de lokale bronnen. Wanneer u het runbook op de Hybrid Runbook Worker machine hebt getest, kunt u het op de Azure Automation omgeving uploaden, waar het op de werk nemer kan worden uitgevoerd. 

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook-machtigingen voor een Hybrid Runbook Worker

Wanneer ze toegang krijgen tot niet-Azure-resources, kunnen runbooks die worden uitgevoerd op een Hybrid Runbook Worker het verificatie mechanisme niet gebruiken dat doorgaans wordt gebruikt door runbooks die verifiëren naar Azure-resources. Een runbook biedt een eigen verificatie voor lokale bronnen of configureert verificatie met behulp [van beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). U kunt ook een uitvoeren als-account opgeven om een gebruikers context te bieden voor alle runbooks.

### <a name="runbook-authentication"></a>Runbook-verificatie

Runbooks worden standaard uitgevoerd op de lokale computer. Voor Windows worden ze uitgevoerd in de context van het lokale systeem account. Voor Linux worden ze uitgevoerd in de context van het speciale gebruikers account **nxautomation**. In beide scenario's moet de runbooks hun eigen verificatie bieden voor de resources waartoe ze toegang hebben.

U kunt [referentie](automation-credentials.md) -en [certificaat](automation-certificates.md) assets in uw runbook gebruiken met cmdlets waarmee u referenties kunt opgeven zodat het runbook kan worden geverifieerd bij verschillende bronnen. In het volgende voor beeld ziet u een deel van een runbook waarmee een computer opnieuw wordt opgestart. De referenties worden opgehaald uit een referentie-element en de naam van de computer van een variabele Asset en vervolgens deze waarden gebruikt met de cmdlet **Restart-computer** .

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

U kunt ook een [InlineScript](automation-powershell-workflow.md#inlinescript) -activiteit gebruiken. Met InlineScript kunt u code blokken uitvoeren op een andere computer met referenties die zijn opgegeven door de [gemeen schappelijke para meter PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="run-as-account"></a>Run as-account

In plaats van uw runbook zelf verificatie te bieden voor lokale resources, kunt u een uitvoeren als-account opgeven voor een Hybrid Runbook Worker groep. Hiervoor moet u een [referentie-element](automation-credentials.md) definiëren dat toegang heeft tot lokale bronnen. Deze resources omvatten certificaat archieven en alle runbooks worden onder deze referenties uitgevoerd op een Hybrid Runbook Worker in de groep.

De gebruikers naam voor de referentie moet een van de volgende indelingen hebben:

* vorm
* username@domain
* gebruikers naam (voor accounts lokaal op de on-premises computer)

Gebruik de volgende procedure om een uitvoeren als-account op te geven voor een Hybrid Runbook Worker groep.

1. Maak een [referentie-element](automation-credentials.md) met toegang tot lokale bronnen.
2. Open het Automation-account in de Azure Portal.
3. Selecteer de tegel **Hybrid worker groepen** en selecteer vervolgens de groep.
4. Selecteer **alle instellingen**, gevolgd door de instellingen van de **Hybrid worker-groep**.
5. Wijzig de waarde van **uitvoeren als** van **standaard** in **aangepast**.
6. Selecteer de referentie en klik op **Opslaan**.

### <a name="managed-identities-for-azure-resources"></a>Beheerde identiteiten voor Azure-resources

Hybrid Runbook Workers op virtuele machines van Azure kunnen beheerde identiteiten gebruiken voor Azure-resources om te verifiëren bij Azure-resources. Het gebruik van beheerde identiteiten voor Azure-resources in plaats van run as-accounts biedt voor delen, omdat u niet hoeft te doen:

* Exporteer het run as-certificaat en importeer het vervolgens in de Hybrid Runbook Worker
* Vernieuw het certificaat dat wordt gebruikt door het run as-account
* Het run as-verbindings object in uw runbook-code verwerken

Volg de volgende stappen om een beheerde identiteit voor Azure-resources te gebruiken op een Hybrid Runbook Worker.

1. Maak een Azure VM.
2. Configureer beheerde identiteiten voor Azure-resources op de VM. Zie [beheerde identiteiten voor Azure-resources configureren op een virtuele machine met behulp van de Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Geef de virtuele machine toegang tot een resource groep in Resource Manager. Raadpleeg [een door Windows-VM-systeem toegewezen beheerde identiteit gebruiken om toegang te krijgen tot Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Installeer de Hybrid Runbook worker op de VM. Zie [een Windows-Hybrid Runbook worker implementeren](automation-windows-hrw-install.md).
5. Werk het runbook bij om de cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) te gebruiken met de *id* -para meter voor verificatie bij Azure-resources. Deze configuratie vermindert de nood zaak om een uitvoeren als-account te gebruiken en het bijbehorende account beheer uit te voeren.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity` werkt voor een Hybrid Runbook Worker met behulp van een door het systeem toegewezen identiteit en één door de gebruiker toegewezen identiteit. Als u meerdere door de gebruiker toegewezen identiteiten gebruikt op de Hybrid Runbook Worker, moet uw Runbook de *accountid* -para meter opgeven voor **Connect-AzAccount** om een specifieke door de gebruiker toegewezen identiteit te selecteren.

### <a name="runas-script"></a>Uitvoeren als-account voor Automation

Als onderdeel van uw geautomatiseerde bouw proces voor het implementeren van resources in azure, hebt u mogelijk toegang tot on-premises systemen nodig ter ondersteuning van een taak of een reeks stappen in uw implementatie reeks. Als u verificatie wilt uitvoeren voor Azure met behulp van het uitvoeren als-account, moet u het certificaat van het uitvoeren als-account installeren.

Het volgende Power shell-runbook, met de naam **export-RunAsCertificateToHybridWorker**, exporteert het run as-certificaat uit uw Azure Automation-account. Het runbook wordt gedownload en geïmporteerd in het certificaat archief van de lokale computer op een Hybrid Runbook Worker dat is verbonden met hetzelfde account. Zodra deze stap is voltooid, controleert het runbook of de werk nemer kan worden geverifieerd bij Azure met behulp van het run as-account.

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
>Voor Power shell-runbooks zijn **add-AzAccount** en **add-AzureRMAccount** aliassen voor **Connect-AzAccount**. Als u de bibliotheek items zoekt en u geen **Connect-AzAccount**ziet, kunt u **add-AzAccount**gebruiken, maar u kunt ook uw modules bijwerken in uw Automation-account.

De voor bereiding van het uitvoeren als-account volt ooien:

1. Sla het runbook **export-RunAsCertificateToHybridWorker** op uw computer op met de extensie **. ps1** .
2. Importeer deze in uw Automation-account.
3. Bewerk het runbook en wijzig de waarde van de *wachtwoord* variabele o uw eigen wacht woord. 
4. Publiceer het runbook.
5. Voer het runbook uit om te richten op de Hybrid Runbook Worker groep die runbooks uitvoert en verifieert met behulp van het run as-account. 
6. Controleer de taak stroom om te zien dat de poging om het certificaat te importeren in het archief van de lokale computer, en wordt gevolgd door meerdere regels. Dit gedrag is afhankelijk van het aantal Automation-accounts dat u in uw abonnement hebt gedefinieerd en de mate van succes van de verificatie.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Taak gedrag voor Hybrid Runbook Workers

Azure Automation verwerkt taken van Hybrid Runbook Workers enigszins anders dan taken die worden uitgevoerd in azure-sandboxes. Een belang rijk verschil is dat er geen limiet is voor de taak duur van runbook Workers. Runbooks die worden uitgevoerd in azure-sandboxes zijn beperkt tot drie uur vanwege een [billijke share](automation-runbook-execution.md#fair-share).

Voor een langlopend runbook wilt u er zeker van zijn dat het apparaat kan worden opgestart, bijvoorbeeld als de computer die als host fungeert voor de werk nemer opnieuw wordt opgestart. Als de Hybrid Runbook Worker-hostcomputer opnieuw wordt opgestart, wordt elke actieve Runbook-taak opnieuw gestart vanaf het begin of van het laatste controle punt voor Power shell-werk stroom runbooks. Nadat een runbook-taak meer dan drie keer opnieuw is opgestart, wordt deze onderbroken.

Houd er rekening mee dat taken voor Hybrid Runbook Workers worden uitgevoerd onder het lokale systeem account op Windows of het **nxautomation** -account in Linux. Voor Linux moet u ervoor zorgen dat het **nxautomation** -account toegang heeft tot de locatie waar de runbook-modules zijn opgeslagen. Wanneer u de cmdlet [install-module](/powershell/module/powershellget/install-module) gebruikt, moet u **ALLUSERS** opgeven voor de *bereik* parameter om ervoor te zorgen dat het **nxautomation** -account toegang heeft. Zie voor meer informatie over Power shell op Linux [bekende problemen voor Power shell op niet-Windows-platforms](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Een runbook starten op een Hybrid Runbook Worker

[Bij het starten van een runbook in azure Automation](automation-starting-a-runbook.md) worden verschillende methoden beschreven voor het starten van een runbook. Voor het opstarten van een runbook op een Hybrid Runbook Worker gebruikt u de optie **uitvoeren op** waarmee u de naam van een Hybrid Runbook worker groep kunt opgeven. Wanneer een groep is opgegeven, haalt een van de werk nemers in die groep het runbook op en voert deze uit. Als uw runbook deze optie niet opgeeft, wordt het runbook op de gebruikelijke manier door Azure Automation uitgevoerd.

Wanneer u een runbook in de Azure Portal start, wordt de optie **uitvoeren op** weer gegeven waarvoor u **Azure** of **Hybrid worker**kunt selecteren. Als u **Hybrid worker**selecteert, kunt u de Hybrid Runbook worker groep kiezen in een vervolg keuzelijst.

Gebruik de para meter *RunOn* met de cmdlet **Start-AzureAutomationRunbook** . In het volgende voor beeld wordt Windows Power shell gebruikt om een runbook met de naam **test-runbook** te starten voor een Hybrid Runbook worker groep met de naam MyHybridGroup.

```azurepowershell-interactive
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> De para meter *RunOn* is toegevoegd aan **Start-AzureAutomationRunbook** in versie 0.9.1 tot en van Microsoft Azure PowerShell. Als u een eerder geïnstalleerd hebt, moet u [de nieuwste versie downloaden](https://azure.microsoft.com/downloads/) . Installeer deze versie alleen op het werk station waar u het runbook start vanuit Power shell. U hoeft deze niet te installeren op de Hybrid Runbook Worker computer, tenzij u van plan bent runbooks op deze computer te starten.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Werken met ondertekende runbooks op een Windows-Hybrid Runbook Worker

U kunt een Windows-Hybrid Runbook Worker zodanig configureren dat alleen ondertekende runbooks worden uitgevoerd.

> [!IMPORTANT]
> Zodra u een Hybrid Runbook Worker hebt geconfigureerd om alleen ondertekende runbooks uit te voeren, kunnen runbooks die niet zijn ondertekend, niet worden uitgevoerd op de werk nemer.

### <a name="create-signing-certificate"></a>Handtekening certificaat maken

In het volgende voor beeld wordt een zelfondertekend certificaat gemaakt dat kan worden gebruikt voor het ondertekenen van runbooks. Deze code maakt het certificaat en exporteert dit zodat de Hybrid Runbook Worker het later kan importeren. De vinger afdruk wordt ook geretourneerd voor later gebruik in de verwijzing naar het certificaat.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Certificaat importeren en werk nemers configureren voor handtekening validatie

Kopieer het certificaat dat u hebt gemaakt voor elke Hybrid Runbook Worker in een groep. Voer het volgende script uit om het certificaat te importeren en configureer de werk nemers om handtekening validatie op runbooks te gebruiken.

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

Met de Hybrid Runbook workers die zijn geconfigureerd voor het gebruik van alleen ondertekende runbooks, moet u runbooks ondertekenen die moeten worden gebruikt op de Hybrid Runbook Worker. Gebruik de volgende Power shell-voorbeeld code om deze runbooks te ondertekenen.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Wanneer een runbook is ondertekend, moet u deze importeren in uw Automation-account en dit publiceren met het handtekening blok. Zie [een Runbook importeren vanuit een bestand in azure Automation](manage-runbooks.md#import-a-runbook)voor meer informatie over het importeren van runbooks.

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Werken met ondertekende runbooks op een Linux-Hybrid Runbook Worker

Om te kunnen werken met ondertekende runbooks, moet een Linux-Hybrid Runbook Worker het uitvoer bare bestand [gpg](https://gnupg.org/index.html) op de lokale computer hebben.

> [!IMPORTANT]
> Zodra u een Hybrid Runbook Worker hebt geconfigureerd om alleen ondertekende runbooks uit te voeren, kunnen runbooks die niet zijn ondertekend, niet worden uitgevoerd op de werk nemer.

### <a name="create-a-gpg-keyring-and-keypair"></a>Een GPG-sleutel hanger en-sleutel paar maken

Als u de GPG sleutel hanger en het sleutel paar wilt maken, gebruikt u het account Hybrid Runbook Worker **nxautomation** .

1. Gebruik de toepassing sudo om u aan te melden als het **nxautomation** -account.

    ```bash
    sudo su – nxautomation
    ```

2. Wanneer u **nxautomation**gebruikt, genereert u het GPG-sleutel paar. GPG begeleidt u bij de stappen. U moet een naam, e-mail adres, verval tijd en wachtwoordzin opgeven. Vervolgens wacht u tot er voldoende entropie op de computer is om de sleutel te genereren.

    ```bash
    sudo gpg --generate-key
    ```

3. Omdat de GPG-map is gegenereerd met sudo, moet u de eigenaar ervan wijzigen in **nxautomation** met behulp van de volgende opdracht.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>De sleutel hanger beschikbaar maken voor de Hybrid Runbook Worker

Zodra de sleutel hanger is gemaakt, kunt u deze beschikbaar maken voor de Hybrid Runbook Worker. Wijzig het instellingen bestand `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` zodat de volgende voorbeeld code wordt vermeld in de sectie bestand **[worker-optioneel]** .

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Controleren of de handtekening validatie is ingeschakeld

Als handtekening validatie is uitgeschakeld op de computer, moet u deze inschakelen door de volgende sudo-opdracht uit te voeren. Vervang `<LogAnalyticsworkspaceId>` door de werk ruimte-ID.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Een runbook ondertekenen

Nadat u de handtekening validatie hebt geconfigureerd, gebruikt u de volgende GPG-opdracht om een runbook te ondertekenen.

```bash
gpg –-clear-sign <runbook name>
```

Het ondertekende runbook wordt `<runbook name>.asc`genoemd.

U kunt nu het ondertekende runbook uploaden naar Azure Automation en dit uitvoeren als een normaal runbook.

## <a name="next-steps"></a>Volgende stappen

* Zie [starten van een runbook in azure Automation](automation-starting-a-runbook.md)voor meer informatie over de methoden voor het starten van een runbook.
* Zie [een Runbook bewerken in azure Automation](automation-edit-textual-runbook.md)als u wilt weten hoe u de tekstuele editor gebruikt voor het werken met Power shell-runbooks in azure Automation.
* Als uw runbooks niet met succes worden voltooid, raadpleegt u de hand leiding voor het oplossen van de [runbook-uitvoerings fouten](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Raadpleeg de [Power shell-documenten](https://docs.microsoft.com/powershell/scripting/overview)voor meer informatie over Power shell, inclusief taal referentie-en leer modules.
