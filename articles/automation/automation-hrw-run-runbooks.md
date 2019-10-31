---
title: Runbooks uitvoeren op Azure Automation Hybrid Runbook Worker
description: Dit artikel bevat informatie over het uitvoeren van runbooks op computers in uw lokale Data Center of Cloud provider met de functie Hybrid Runbook Worker.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 83c185a6ba8f1c5e6edf095db5baf575f750fa3b
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176464"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Runbooks uitvoeren op een Hybrid Runbook Worker

Er is geen verschil in de structuur van runbooks die worden uitgevoerd in Azure Automation en runbooks die worden uitgevoerd op een Hybrid Runbook Worker. Runbooks die u gebruikt met elke waarschijnlijke verschillen aanzienlijk. Dit verschil is omdat runbooks die gericht zijn op een Hybrid Runbook Worker meestal resources op de lokale computer zelf of op basis van resources in de lokale omgeving beheert waar deze worden geïmplementeerd. Runbooks in Azure Automation beheren resources doorgaans in de Azure-Cloud.

Wanneer u runbooks ontwerpt om uit te voeren op een Hybrid Runbook Worker, moet u de runbooks bewerken en testen op de computer die als host fungeert voor de Hybrid Worker. De hostcomputer heeft alle Power shell-modules en netwerk toegang die u nodig hebt voor het beheren en openen van de lokale resources. Zodra een runbook is getest op de Hybrid worker-machine, kunt u deze uploaden naar de Azure Automation omgeving waar deze beschikbaar is voor uitvoering in de Hybrid Worker. Het is belang rijk om te weten dat taken die worden uitgevoerd onder het lokale systeem account voor Windows of een speciaal gebruikers account `nxautomation` op Linux. In Linux betekent dit dat u moet controleren of het `nxautomation`-account toegang heeft tot de locatie waar u de modules opslaat. Wanneer u de cmdlet [install-module](/powershell/module/powershellget/install-module) gebruikt, geeft u **ALLUSERS** op in de para meter `-Scope` om te bevestigen dat het `nxautomation`-account toegang heeft.

Zie voor meer informatie over Power shell op Linux [bekende problemen voor Power shell op niet-Windows-platforms](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Een runbook starten op Hybrid Runbook Worker

[Bij het starten van een runbook in azure Automation](automation-starting-a-runbook.md) worden verschillende methoden beschreven voor het starten van een runbook. Hybrid Runbook Worker voegt een **RunOn** -optie toe, waarbij u de naam van een Hybrid Runbook worker groep kunt opgeven. Wanneer een groep is opgegeven, wordt het runbook opgehaald en uitgevoerd door een van de werk rollen in die groep. Als deze optie niet is opgegeven, wordt deze in Azure Automation als normaal uitgevoerd.

Wanneer u een runbook start in de Azure Portal, wordt er een optie **voor uitvoeren op** weer gegeven waarin u **Azure** of **Hybrid worker**kunt selecteren. Als u **Hybrid worker**selecteert, kunt u de groep selecteren in een vervolg keuzelijst.

Gebruik de para meter **RunOn** . U kunt de volgende opdracht gebruiken om een runbook met de naam test-Runbook te starten voor een Hybrid Runbook Worker groep met de naam MyHybridGroup met behulp van Windows Power shell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> De para meter **RunOn** is toegevoegd aan de cmdlet **Start-AzureAutomationRunbook** in versie 0.9.1 tot en van Microsoft Azure PowerShell. Als u een eerder geïnstalleerd hebt, moet u [de nieuwste versie downloaden](https://azure.microsoft.com/downloads/) . U hoeft deze versie alleen te installeren op een werk station waar u het runbook start vanuit Power shell. U hoeft deze niet te installeren op de werk computer, tenzij u runbooks vanaf die computer wilt starten. "

## <a name="runbook-permissions"></a>Runbook-machtigingen

Runbooks die worden uitgevoerd op een Hybrid Runbook Worker, kunnen niet dezelfde methode gebruiken die meestal wordt gebruikt voor runbooks die worden geverifieerd bij Azure-resources, omdat ze toegang hebben tot resources die niet in azure zijn. Het runbook kan een eigen verificatie bieden voor lokale bronnen of verificatie configureren met behulp [van beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). U kunt ook een runas-account opgeven om een gebruikers context te bieden voor alle runbooks.

### <a name="runbook-authentication"></a>Runbook-verificatie

Runbooks worden standaard uitgevoerd in de context van het lokale systeem account voor Windows en een speciaal gebruikers account `nxautomation` voor Linux op de on-premises computer, zodat ze hun eigen verificatie moeten opgeven voor de resources waartoe ze toegang hebben.

U kunt [referentie](automation-credentials.md) -en [certificaat](automation-certificates.md) assets in uw runbook gebruiken met-cmdlets waarmee u referenties kunt opgeven, zodat u zich op verschillende bronnen kan verifiëren. In het volgende voor beeld ziet u een deel van een runbook waarmee een computer opnieuw wordt opgestart. De referenties worden opgehaald uit een referentie-element en de naam van de computer van een variabele Asset en vervolgens deze waarden gebruikt met de cmdlet Restart-computer.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

U kunt ook [InlineScript](automation-powershell-workflow.md#inlinescript)gebruiken, waarmee u code blokken kunt uitvoeren op een andere computer met referenties die zijn opgegeven door de [algemene para meter PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Runas-account

Standaard gebruikt de Hybrid Runbook Worker lokaal systeem voor Windows en een speciaal gebruikers account `nxautomation` voor Linux om runbooks uit te voeren. In plaats van dat runbooks hun eigen verificatie bieden voor lokale resources, kunt u een **runas** -account opgeven voor een Hybrid worker-groep. U geeft een [referentie-element](automation-credentials.md) op dat toegang heeft tot lokale bronnen, met inbegrip van certificaat archieven en alle runbooks die worden uitgevoerd onder deze referenties wanneer ze worden uitgevoerd op een Hybrid Runbook worker in de groep.

De gebruikers naam voor de referentie moet een van de volgende indelingen hebben:

* vorm
* username@domain
* gebruikers naam (voor accounts lokaal op de on-premises computer)

Gebruik de volgende procedure om een runas-account op te geven voor een Hybrid worker-groep:

1. Maak een [referentie-element](automation-credentials.md) met toegang tot lokale bronnen.
2. Open het Automation-account in de Azure Portal.
3. Selecteer de tegel **Hybrid worker groepen** en selecteer vervolgens de groep.
4. Selecteer **alle instellingen** en vervolgens de instellingen van de **Hybrid worker-groep**.
5. Wijzig **uitvoeren als** van **standaard** in **aangepast**.
6. Selecteer de referentie en klik op **Opslaan**.

### <a name="managed-identities-for-azure-resources"></a>Beheerde identiteiten voor Azure-resources

Hybrid Runbook-werk rollen die worden uitgevoerd op virtuele machines van Azure kunnen beheerde identiteiten gebruiken voor Azure-resources om te verifiëren bij Azure-resources. Er zijn veel voor delen voor het gebruik van beheerde identiteiten voor Azure-resources over uitvoeren als-accounts.

* U hoeft het run as-certificaat niet te exporteren en vervolgens te importeren in Hybrid Runbook Worker
* U hoeft het certificaat dat wordt gebruikt door het run as-account niet te vernieuwen
* U hoeft het run as-verbindings object niet in uw runbook-code af te handelen

Als u een beheerde identiteit voor Azure-resources wilt gebruiken op een Hybrid Runbook worker, moet u de volgende stappen uitvoeren:

1. Een Azure-VM maken
2. [Beheerde identiteiten configureren voor Azure-resources op uw VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [Uw VM toegang verlenen tot een resource groep in Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) zelf studie-Windows-VM-toegang-arm. MD # Get-a-Access-token-using-the-vm's-door het systeem toegewezen beheerde identiteit-en-use-to-call-Azure-Resource-Manager)
4. [Installeer de Windows-Hybrid Runbook worker](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) op de virtuele machine.

Zodra de voor gaande stappen zijn voltooid, kunt u in het runbook `Connect-AzureRmAccount -Identity` gebruiken om te verifiëren bij Azure-resources. Deze configuratie vermindert de nood zaak om een uitvoeren als-account te gebruiken en het certificaat voor het uitvoeren als-account te beheren.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

> [!NOTE]
> `Connect-AzureRMAccount -Identity` werkt voor een Hybrid Runbook Worker met behulp van een door het systeem toegewezen identiteit en één door de gebruiker toegewezen identiteit. Als u meerdere door de gebruiker toegewezen identiteiten wilt gebruiken op de HRW, moet u de para meter `-AccountId` opgeven om de specifieke door de gebruiker toegewezen identiteit te selecteren.

### <a name="runas-script"></a>Uitvoeren als-account voor Automation

Als onderdeel van uw geautomatiseerde bouw proces voor het implementeren van resources in azure, hebt u mogelijk toegang tot on-premises systemen nodig ter ondersteuning van een taak of een reeks stappen in uw implementatie reeks. Als u verificatie wilt ondersteunen voor Azure met behulp van het uitvoeren als-account, moet u het certificaat van het run as-account installeren.

Met het volgende Power shell **-runbook, export-RunAsCertificateToHybridWorker**, wordt het run as-certificaat geëxporteerd uit uw Azure Automation-account en gedownload en geïmporteerd in het certificaat archief van de lokale computer op een Hybrid worker, dat is verbonden naar hetzelfde account. Zodra deze stap is voltooid, wordt gecontroleerd of de werk nemer kan worden geverifieerd bij Azure met behulp van het run as-account.

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
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
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

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

> [!IMPORTANT]
> **Add-AzureRmAccount** is nu een alias voor **Connect-AzureRmAccount**. Als u de bibliotheek items zoekt en u geen **Connect-AzureRMAccount**ziet, kunt u **add-AzureRMAccount**gebruiken, maar u kunt ook uw modules bijwerken in uw Automation-account.

Sla het runbook *export-RunAsCertificateToHybridWorker* op uw computer op met een uitbrei ding van `.ps1`. Importeer deze in uw Automation-account en bewerk het runbook, waarbij u de waarde van de variabele `$Password` wijzigt met uw eigen wacht woord. Publiceer het runbook en voer het vervolgens uit. Richt de Hybrid Worker-groep in die runbooks uitvoert en verifieert met behulp van het run as-account. De taak stroom rapporteert de poging om het certificaat te importeren in het archief van de lokale computer en wordt gevolgd door meerdere regels. Dit gedrag is afhankelijk van het aantal Automation-accounts dat u in uw abonnement definieert en als de verificatie is geslaagd.

## <a name="job-behavior"></a>Taak gedrag

Taken worden enigszins anders behandeld in Hybrid Runbook Workers dan wanneer ze worden uitgevoerd op Azure-sandboxes. Een belang rijk verschil is dat er geen limiet is voor de taak duur van Hybrid Runbook Workers. Runbooks die zijn uitgevoerd in azure-sandboxes zijn beperkt tot drie uur vanwege een [billijke share](automation-runbook-execution.md#fair-share). Voor een langlopend runbook wilt u er zeker van zijn dat de computer kan worden opgestart. Als de computer die als host fungeert voor de Hybrid worker bijvoorbeeld opnieuw wordt opgestart. Als de Hybrid worker-hostcomputer opnieuw wordt opgestart, wordt elke actieve runbook-taak opnieuw gestart vanaf het begin of van het laatste controle punt voor Power shell-werk stroom runbooks. Nadat een runbook-taak meer dan drie keer opnieuw is opgestart, wordt deze onderbroken.

## <a name="run-only-signed-runbooks"></a>Alleen ondertekende Runbooks uitvoeren

Hybrid Runbook Workers kunnen worden geconfigureerd voor het uitvoeren van alleen ondertekende runbooks met een bepaalde configuratie. In de volgende sectie wordt beschreven hoe u uw Hybrid Runbook Workers instelt voor het uitvoeren van ondertekende [Windows-Hybrid Runbook worker](#windows-hybrid-runbook-worker) en [Linux-Hybrid Runbook worker](#linux-hybrid-runbook-worker)

> [!NOTE]
> Zodra u een Hybrid Runbook Worker hebt geconfigureerd om alleen ondertekende runbooks uit te voeren, kunnen runbooks die **niet** zijn ondertekend, niet worden uitgevoerd op de werk nemer.

### <a name="windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker

#### <a name="create-signing-certificate"></a>Handtekening certificaat maken

In het volgende voor beeld wordt een zelfondertekend certificaat gemaakt dat kan worden gebruikt voor het ondertekenen van runbooks. Het voor beeld maakt het certificaat en exporteert dit. Het certificaat wordt later in de Hybrid Runbook Workers geïmporteerd. De vinger afdruk wordt geretourneerd en deze waarde wordt later gebruikt om naar het certificaat te verwijzen.

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

#### <a name="configure-the-hybrid-runbook-workers"></a>De Hybrid Runbook Workers configureren

Kopieer het certificaat dat is gemaakt voor elke Hybrid Runbook Worker in een groep. Voer het volgende script uit om het certificaat te importeren en configureer de Hybrid Worker om handtekening validatie op runbooks te gebruiken.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>Uw Runbooks ondertekenen met het certificaat

Met de Hybrid Runbook workers die zijn geconfigureerd voor het gebruik van alleen ondertekende runbooks, moet u runbooks ondertekenen die moeten worden gebruikt op de Hybrid Runbook Worker. Gebruik de volgende Power shell-voorbeeld code om runbooks te ondertekenen.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Wanneer het runbook is ondertekend, moet het worden geïmporteerd in uw Automation-account en worden gepubliceerd met het handtekening blok. Zie [een Runbook importeren vanuit een bestand in azure Automation](manage-runbooks.md#import-a-runbook)voor meer informatie over het importeren van runbooks.

### <a name="linux-hybrid-runbook-worker"></a>Linux-Hybrid Runbook Worker

Als u runbooks wilt ondertekenen op een Linux-Hybrid Runbook Worker, moet het uitvoer bare [GPG](https://gnupg.org/index.html) Hybrid Runbook worker-bestand aanwezig zijn op de computer.

#### <a name="create-a-gpg-keyring-and-keypair"></a>Een GPG-sleutel hanger en-sleutel paar maken

Als u de sleutel hanger en het sleutel paar wilt maken, moet u het Hybrid Runbook Worker-account gebruiken `nxautomation`.

Gebruik `sudo` om u aan te melden als de `nxautomation`-account.

```bash
sudo su – nxautomation
```

Genereer het GPG-sleutel paar nadat u het `nxautomation`-account hebt gebruikt.

```bash
sudo gpg --generate-key
```

GPG leidt u door de stappen voor het maken van het sleutel paar. U moet een naam, een e-mail adres, een verval tijd, een wachtwoordzin en wachten op voldoende entropie op de computer om de sleutel te genereren.

Omdat de GPG-map is gegenereerd met sudo, moet u de eigenaar wijzigen in `nxautomation`.

Voer de volgende opdracht uit om de eigenaar te wijzigen.

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>De sleutel hanger beschikbaar maken voor de Hybrid Runbook Worker

Zodra de sleutel hanger is gemaakt, moet u deze beschikbaar maken voor de Hybrid Runbook Worker. Wijzig het instellingen bestand `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf`, zodat het volgende voor beeld wordt toegevoegd aan de sectie `[worker-optional]`

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>Controleren of de handtekening validatie is ingeschakeld

Als handtekening validatie is uitgeschakeld op de computer, moet u deze inschakelen. Voer de volgende opdracht uit om handtekening validatie in te scha kelen. `<LogAnalyticsworkspaceId>` vervangen door de werk ruimte-ID.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>Een runbook ondertekenen

Zodra de handtekening validatie is geconfigureerd, kunt u de volgende opdracht gebruiken om een runbook te ondertekenen:

```bash
gpg –-clear-sign <runbook name>
```

Het ondertekende runbook krijgt de naam `<runbook name>.asc`.

Het ondertekende runbook kan nu worden geüpload naar Azure Automation en kan worden uitgevoerd als een normaal runbook.

## <a name="next-steps"></a>Volgende stappen

* Zie [starten van een runbook in azure Automation](automation-starting-a-runbook.md)voor meer informatie over de verschillende methoden die kunnen worden gebruikt om een runbook te starten.
* Zie [een Runbook bewerken in azure Automation](automation-edit-textual-runbook.md) voor meer informatie over de verschillende manieren waarop u Power shell-runbooks in azure Automation kunt gebruiken in de tekst editor.
* Als uw runbooks niet met succes worden voltooid, raadpleegt u de hand leiding voor het oplossen van [runbook-uitvoerings fouten](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Raadpleeg de [Power shell-documenten](https://docs.microsoft.com/en-us/powershell/scripting/overview)voor meer informatie over Power shell, inclusief taal referentie-en leer modules.
