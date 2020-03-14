---
title: Azure Automation uitvoeren als-accounts beheren
description: In dit artikel wordt beschreven hoe u uw uitvoeren als-accounts beheert met Power shell of via de portal.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: 497dbbca6c653a7d8739aed1b0fbd033b063f9d7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278439"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Azure Automation uitvoeren als-accounts beheren

Uitvoeren als-accounts in Azure Automation bieden verificatie voor het beheren van resources in azure met behulp van de Azure-cmdlets. Wanneer u een uitvoeren als-account maakt, wordt er een nieuwe Service-Principal-gebruiker in Azure Active Directory (AD) gemaakt en wordt de rol Inzender toegewezen aan deze gebruiker op het abonnements niveau. Voor runbooks die gebruikmaken van Hybrid Runbook Workers op Azure virtual machines, kunt u [beheerde identiteiten voor Azure-resources](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) gebruiken in plaats van run as-accounts om te verifiëren bij uw Azure-resources.

De service-principal voor een uitvoeren als-account heeft geen machtigingen om Azure AD standaard te lezen. Als u machtigingen voor het lezen of beheren van Azure AD wilt toevoegen, moet u de machtigingen voor de Service-Principal verlenen onder **API-machtigingen**. Zie [machtigingen toevoegen voor toegang tot Web-api's](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)voor meer informatie.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="types-of-run-as-accounts"></a>Typen run as-accounts

Azure Automation gebruikt twee typen uitvoeren als-accounts:

* Uitvoeren als-account van Azure
* Klassiek uitvoeren als-account van Azure

>[!NOTE]
>Azure Cloud Solution Provider-abonnementen (CSP) ondersteunen alleen het Azure Resource Manager model. Niet-Azure Resource Manager services zijn niet beschikbaar in het programma. Wanneer u een CSP-abonnement gebruikt, wordt het klassieke uitvoeren als-account van Azure niet gemaakt, maar wordt het uitvoeren als-account van Azure gemaakt. Zie [beschik bare Services in CSP-abonnementen](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)voor meer informatie over CSP-abonnementen.

### <a name="run-as-account"></a>Run as-account

Het run as-account beheert resources van het [Resource Manager-implementatie model](../azure-resource-manager/management/deployment-models.md) . De volgende taken worden uitgevoerd.

* Er wordt een Azure AD-toepassing met een zelf-ondertekend certificaat gemaakt. Daarnaast wordt er voor de toepassing in Azure AD een service-principalaccount gemaakt en wordt aan dit account de rol Inzender toegewezen in uw huidige abonnement. U kunt de certificaat instelling wijzigen in eigenaar of een andere rol. Zie [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md) voor meer informatie.
  
* Er wordt een Automation-certificaatasset gemaakt met de naam **AzureRunAsCertificate** in het opgegeven Automation-account. De certificaat Asset bevat de persoonlijke sleutel van het certificaat dat door de Azure AD-toepassing wordt gebruikt.
  
* Er wordt een Automation-verbindingsasset gemaakt met de naam **AzureRunAsConnection** in het opgegeven Automation-account. Het verbindings element bevat de toepassings-ID, Tenant-ID, abonnements-ID en vinger afdruk van het certificaat.

### <a name="azure-classic-run-as-account"></a>Klassieke Azure Uitvoeren als-account

Met het klassieke uitvoeren als-account van Azure worden de resources van het [klassieke implementatie model](../azure-resource-manager/management/deployment-models.md) beheerd. U moet een mede beheerder zijn van het abonnement om dit type account te maken of te vernieuwen.

Het klassieke uitvoeren als-account van Azure voert de volgende taken uit.

  * Hiermee maakt u een beheer certificaat in het abonnement.

  * Er wordt een Automation-certificaatasset gemaakt met de naam **AzureClassicRunAsCertificate** in het opgegeven Automation-account. Het certificaatasset bevat de persoonlijke sleutel van het certificaat die wordt gebruikt door het beheercertificaat.

  * Er wordt een Automation-verbindingsasset gemaakt met de naam **AzureClassicRunAsConnection** in het opgegeven Automation-account. Het verbindings element bevat de naam van het abonnement, de abonnements-ID en de naam van de certificaat Asset.

## <a name="permissions"></a>Machtigingen voor het run as-account

In deze sectie worden de machtigingen voor reguliere run as-accounts en klassieke uitvoeren als-accounts gedefinieerd.

### <a name="permissions-to-configure-run-as-accounts"></a>Machtigingen voor het configureren van run as-accounts

Als u een uitvoeren als-account wilt maken of bijwerken, moet u specifieke bevoegdheden en machtigingen hebben. Een toepassings beheerder in Azure Active Directory en een eigenaar van een abonnement kan alle taken volt ooien. In een situatie waarin u een schei ding van taken hebt, ziet u in de volgende tabel een lijst van de taken, de overeenkomstige cmdlet en de benodigde machtigingen:

|Taak|Cmdlet  |Minimale machtigingen  |Waar u de machtigingen instelt|
|---|---------|---------|---|
|Een Azure AD-toepassing maken|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication?view=azps-3.5.0)     | Application Developer-rol<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure AD > app-registraties |
|Voeg een referentie toe aan de toepassing.|[New-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential?view=azps-3.5.0)     | Toepassings beheerder of globale beheerder<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure AD > app-registraties|
|Een Azure AD-service-principal maken en ophalen|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-3.5.0)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal?view=azps-3.5.0)     | Toepassings beheerder of globale beheerder<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure AD > app-registraties|
|De RBAC-rol voor de opgegeven Principal toewijzen of ophalen|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment?view=azps-3.5.0)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment?view=azps-3.5.0)      | Beheerder of eigenaar van de gebruikers toegang of de volgende machtigingen hebben:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Abonnement](../role-based-access-control/role-assignments-portal.md)</br>Home >-abonnementen > \<abonnements naam\>-Access Control (IAM)|
|Een Automation-certificaat maken of verwijderen|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.5.0)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate?view=azps-3.5.0)     | Inzender voor resource groep         |Resource groep voor Automation-account|
|Een Automation-verbinding maken of verwijderen|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.5.0)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection?view=azps-3.5.0)|Inzender voor resource groep |Resource groep voor Automation-account|

<sup>1</sup> gebruikers die geen beheerder zijn in uw Azure AD-TENANT kunnen [ad-toepassingen registreren](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) als de optie gebruikers van de Azure AD-Tenant **kunnen toepassingen registreren** op de pagina gebruikers instellingen is ingesteld op **Ja**. Als de instelling voor de registratie van de toepassing **Nee**is, moet de gebruiker die deze actie uitvoert, worden gedefinieerd in deze tabel.

Als u geen lid bent van het Active Directory exemplaar van het abonnement voordat u wordt toegevoegd aan de rol van globale beheerder van het abonnement, wordt u als gast toegevoegd. In dit geval ontvangt u een bericht dat **u geen machtigingen hebt om te maken...** waarschuwing op de pagina Automation-account toevoegen. 

Als u lid bent van het Active Directory exemplaar van het abonnement wanneer de rol globale beheerder is toegewezen, kunt u ook een bericht ontvangen **dat u geen machtigingen hebt om te maken...** waarschuwing op de pagina Automation-account toevoegen. In dit geval kunt u het verwijderen aanvragen van het Active Directory exemplaar van het abonnement en vervolgens aanvragen om opnieuw toe te voegen, zodat u een volledige gebruiker wordt in Active Directory. 

Om te controleren of de situatie die het fout bericht produceert, is opgelost:

1. Selecteer in het deel venster Azure Active Directory in het Azure Portal de optie **gebruikers en groepen**. 
2. Selecteer **alle gebruikers**.
3. Kies uw naam en selecteer vervolgens **profiel**. 
4. Zorg ervoor dat de waarde van het kenmerk **gebruikers type** onder het profiel van de gebruiker niet is ingesteld op **gast**.

### <a name="permissions-classic"></a>Machtigingen voor het configureren van klassieke uitvoeren als-accounts

Als u klassieke uitvoeren als-accounts wilt configureren of vernieuwen, moet u de rol co-beheerder hebben op het abonnements niveau. Zie voor meer informatie over de machtigingen voor klassieke abonnementen [Azure Classic Subscription Administrators](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="creating-a-run-as-account-in-azure-portal"></a>Een uitvoeren als-account maken in Azure Portal

Voer de volgende stappen uit om uw Azure Automation-account bij te werken in de Azure Portal. U moet de uitvoeren als-en klassieke uitvoeren als-accounts afzonderlijk maken. Als u geen klassieke resources hoeft te beheren, kunt u alleen de Uitvoeren als-account van Azure maken.

1. Meld u bij Azure Portal aan met een account dat lid is van de rol Abonnementsbeheerders en dat medebeheerder is van het abonnement.
2. Zoek en selecteer **Automation-accounts**.
3. Selecteer op de pagina Automation-accounts uw Automation-account in de lijst met Automation-accounts.
4. Selecteer in het linkerdeel venster **uitvoeren als-accounts** in de sectie account instellingen.
5. Afhankelijk van welk account u nodig hebt, selecteert u **Uitvoeren als-account van Azure**  of **Klassiek Uitvoeren als-account van Azure**. 
6. Afhankelijk van het gewenste account, gebruikt u het deel venster Azure uitvoeren als-account toevoegen of klassiek uitvoeren als-accounts toevoegen. Nadat u de overzichts informatie hebt bekeken, klikt u op **maken** om door te gaan met het maken van het run as-account.
6. Terwijl in Azure het Uitvoeren als-account wordt gemaakt, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen. Er wordt ook een banner weer gegeven met de mede deling dat het account wordt gemaakt. Het proces kan een paar minuten duren.

## <a name="creating-a-run-as-account-using-powershell"></a>Een uitvoeren als-account maken met behulp van Power shell

De volgende lijst bevat de vereisten voor het maken van een uitvoeren als-account in Power shell. Deze vereisten zijn van toepassing op beide typen run as-accounts.

* Windows 10 of Windows Server 2016 met Azure Resource Manager modules 3.4.1 en hoger. Het PowerShell-script biedt geen ondersteuning voor eerdere versies van Windows.
* Azure PowerShell 1.0 en hoger. Zie [Azure PowerShell installeren en configureren](/powershell/azureps-cmdlets-docs) voor meer informatie over de PowerShell 1.0-release.
* Een Automation-account waarnaar wordt verwezen als de waarde voor de para meters *AutomationAccountName* en *ApplicationDisplayName* .
* Machtigingen die gelijk zijn aan wat wordt vermeld in de [vereiste machtigingen voor het configureren van run as-accounts](#permissions).

Voer de volgende stappen uit om de waarden op te halen voor *SubscriptionId*, *ResourceGroupName*en *AutomationAccountName*, die de vereiste para meters voor het Power shell-script zijn.

1. Selecteer in de Azure Portal **Automation-accounts**.
1. Selecteer uw Automation-account op de pagina Automation-accounts.
1. Selecteer in de sectie account instellingen de optie **Eigenschappen**.
1. Noteer de waarden voor **naam**, **abonnements-id**en **resource groep** op de pagina Eigenschappen. Deze waarden komen overeen met de waarden voor respectievelijk de Power shell-script parameters *AutomationAccountName*, *SubscriptionId*en *ResourceGroupName* .

   ![Eigenschappen pagina voor Automation-account](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Power shell-script voor het maken van een uitvoeren als-account

Deze sectie bevat een Power shell-script voor het maken van een uitvoeren als-account. Het script bevat ondersteuning voor verschillende configuraties.

* Een Uitvoeren als-account maken met een zelfondertekend certificaat.
* Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat.
* Maak een Uitvoeren als-account en een klassiek Uitvoeren als-account via een certificaat dat is uitgegeven door de certificeringsinstantie van uw bedrijf.
* Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat in de cloud van Azure Government.

Het script maakt gebruik van meerdere Azure Resource Manager-cmdlets voor het maken van resources. Zie machtigingen voor het [configureren van run as-accounts](#permissions-to-configure-run-as-accounts)voor de cmdlets en de machtigingen die ze nodig hebben.

Sla het script op uw computer op met de bestands naam **New-RunAsAccount. ps1**.

```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory = $false)]
        [string] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",

        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )

    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }

    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid

        # Create an Azure AD application, AD App Credential, AD ServicePrincipal

        # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
        $Application = New-AzADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
        Remove-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRm.Profile
    Import-Module AzureRm.Resources

    $AureRmProfileVersion = (Get-Module AzureRm.Profile).Version
    if (!(($AzureRmProfileVersion.Major -ge 3 -and $AzureRmProfileVersion.Minor -ge 4) -or ($AzureRmProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts, please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation account see https://docs.microsoft.com/azure/automation/az-modules.

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzAccount -Environment $EnvironmentName
    $Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId

    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"

    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }

    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"

        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red       $UploadMessage
    }
```

>[!NOTE]
>**Add-AzAccount** en **add-AzureRMAccount** zijn aliassen voor [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). U kunt deze cmdlets gebruiken of u kunt [uw modules](automation-update-azure-modules.md) in uw Automation-account bijwerken naar de meest recente versies. Mogelijk moet u uw modules bijwerken, zelfs als u zojuist een nieuw Automation-account hebt gemaakt.

### <a name="execute-the-powershell-script"></a>Het Power shell-script uitvoeren

1. Start op uw computer **Windows PowerShell** op vanaf het **Start**scherm met verhoogde gebruikersrechten.
1. Ga vanuit de opdrachtregel-shell met verhoogde bevoegdheden naar de map die het script bevat dat u in stap 1 hebt gemaakt.
1. Voer het script uit met de parameterwaarden voor de configuratie die u nodig hebt.
1. Als u een klassiek uitvoeren als-account maakt nadat het script is uitgevoerd, uploadt u het open bare certificaat (. CER filename) naar het beheer Archief voor het abonnement waarin het Automation-account is gemaakt.

Nadat het script is uitgevoerd, wordt u gevraagd zich te verifiëren met Azure. Meld u aan met een account dat lid is van de rol Abonnementsbeheerders en dat medebeheerder is van het abonnement.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Een uitvoeren als-account maken met behulp van een zelfondertekend certificaat

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Een uitvoeren als-account en een klassiek uitvoeren als-account maken met behulp van een zelfondertekend certificaat

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Een uitvoeren als-account en een klassiek uitvoeren als-account maken met behulp van een bedrijfs certificaat

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Als u een klassiek uitvoeren als-account hebt gemaakt met een openbaar certificaat (. cer-bestand) van een onderneming, gebruikt u dit certificaat. Volg de instructies voor [het uploaden van een API-beheer certificaat naar de Azure Portal](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Een uitvoeren als-account en een klassiek uitvoeren als-account maken met behulp van een zelfondertekend certificaat in de Azure Government Cloud

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Als u een klassiek uitvoeren als-account hebt gemaakt met een zelfondertekend openbaar certificaat (. cer-bestand), wordt het door het script gemaakt en opgeslagen in de map met tijdelijke bestanden op uw computer. U kunt dit vinden in het gebruikers profiel **%userprofile%\AppData\Local\Temp**, dat u hebt gebruikt om de Power shell-sessie uit te voeren.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Een uitvoeren als-of klassiek uitvoeren als-account verwijderen

In deze sectie wordt beschreven hoe u een uitvoeren als-of klassiek uitvoeren als-account verwijdert. Als u deze actie uitvoert, blijft het Automation-account behouden. Nadat u het account hebt verwijderd, kunt u het opnieuw maken in de Azure Portal.

1. Open in Azure Portal het Automation-account.

2. Selecteer in het linkerdeel venster **uitvoeren als-accounts** in de sectie account instellingen.

3. Selecteer op de pagina run as-accounts de optie uitvoeren als-account of klassiek uitvoeren als-account dat u wilt verwijderen. 

4. Klik in het deel venster Eigenschappen voor het geselecteerde account op **verwijderen**.

   ![Uitvoeren als-account verwijderen](media/manage-runas-account/automation-account-delete-runas.png)

5. Terwijl het account wordt verwijderd, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen.

6. Nadat het account is verwijderd, kunt u het opnieuw maken op de pagina eigenschappen van uitvoeren als-accounts door de optie maken **Azure uitvoeren als-account**te selecteren.

   ![Het Automation uitvoeren als-account opnieuw maken](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Een zelfondertekend certificaat vernieuwen

Het zelfondertekende certificaat dat u voor het uitvoeren als-account hebt gemaakt, verloopt één jaar na de aanmaak datum. Op een bepaald moment voordat het run as-account verloopt, moet u het certificaat vernieuwen. U kunt het certificaat op elk gewenst moment vernieuwen voordat het verloopt. 

Wanneer u het zelfondertekende certificaat verlengt, wordt het huidige geldige certificaat bewaard om ervoor te zorgen dat alle runbooks die in de wachtrij zijn geplaatst of actief actief zijn, en die worden geverifieerd met het uitvoeren als-account, niet negatief worden beïnvloed. Het certificaat blijft geldig tot de vervaldatum.

>[!NOTE]
>Als u denkt dat het uitvoeren als-account is aangetast, kunt u het zelfondertekende certificaat verwijderen en opnieuw maken.

>[!NOTE]
>Als u uw uitvoeren als-account hebt geconfigureerd voor het gebruik van een certificaat dat is uitgegeven door de certificerings instantie van uw bedrijf en u de optie voor het vernieuwen van een zelfondertekend certificaat gebruikt, wordt het ondernemings certificaat vervangen door een zelfondertekend certificaat.

Gebruik de volgende stappen om het zelfondertekende certificaat te vernieuwen.

1. Open in Azure Portal het Automation-account.

1. Selecteer **uitvoeren als-accounts** in de sectie account instellingen.

    ![Eigenschappendeelvenster voor Automation-account](media/manage-runas-account/automation-account-properties-pane.png)

1. Selecteer op de pagina uitvoeren als-accounts het uitvoeren als-account of het klassieke uitvoeren als-account waarvoor u het certificaat wilt vernieuwen.

1. Klik in het deel venster Eigenschappen voor het geselecteerde account op **certificaat vernieuwen**.

    ![Certificaat vernieuwen voor Uitvoeren als-account](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Terwijl het certificaat wordt gemaakt, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen.

## <a name="auto-cert-renewal"></a>Automatische certificaat vernieuwing instellen met een Automation-runbook

Als u certificaten automatisch wilt vernieuwen, kunt u een Automation-runbook gebruiken. Met dit script op [github](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) kunt u deze functionaliteit inschakelen in uw Automation-account.

>[!NOTE]
>U moet een globale beheerder of bedrijfs beheerder zijn in azure AD om het script uit te voeren.

Met dit script maakt u een wekelijks schema voor het vernieuwen van run as-account certificaten. Er wordt een **Update-AutomationRunAsCredential-** runbook toegevoegd aan uw Automation-account. U kunt de runbook-code weer geven op GitHub in het script [Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). U kunt de Power shell-code in het bestand gebruiken om certificaten, indien nodig, hand matig te vernieuwen.

Gebruik de volgende stappen om het vernieuwings proces direct te testen.

1. Bewerk het runbook **Update-AutomationRunAsCredential** en plaats een commentaar teken (#) op regel 122, vóór de opdracht **Exit (1)** .

   ```powershell
   #Exit(1)
   ```

2. Publiceer het runbook.
3. Start het runbook.
4. Controleer de geslaagde vernieuwing met de volgende code:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Uitvoer:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Nadat de test is, bewerkt u het runbook en verwijdert u het opmerkings teken dat u in stap 1 hebt toegevoegd.
6. Publiceer het runbook.

## <a name="limiting-run-as-account-permissions"></a>Machtigingen voor het uitvoeren als-account beperken

U kunt het script [Update-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug8) uitvoeren om het doel te bepalen van automatisering op basis van resources in Azure. Met dit script wordt de bestaande service-principal van het run as-account gewijzigd om een aangepaste roldefinitie te maken en te gebruiken. De rol heeft machtigingen voor alle resources, met uitzonde ring van [Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>Nadat u het script **Update-AutomationRunAsAccountRoleAssignments. ps1** hebt uitgevoerd, werken runbooks die toegang Key Vault via het gebruik van run as-accounts niet meer. Voordat u het script uitvoert, moet u runbooks in uw account controleren op aanroepen naar Azure Key Vault. Als u toegang tot Key Vault van Azure Automation runbooks wilt inschakelen, moet u [het uitvoeren als-account toevoegen aan de machtigingen van Key Vault](#add-permissions-to-key-vault).

Als u verder wilt beperken wat de run as-service-principal kan doen, kunt u andere resource typen toevoegen aan het element **intact** van de definitie van de aangepaste rol. In het volgende voor beeld wordt de toegang tot `Microsoft.Compute/*`beperkt. Als u dit resource type toevoegt aan de functie definitie, heeft de rol geen **toegang tot een** reken resource. Zie [inzicht in roldefinities voor Azure-resources](../role-based-access-control/role-definitions.md)voor meer informatie over functie definities.

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

U kunt bepalen of de service-principal die wordt gebruikt door het run as-account zich in de roldefinitie van de rol van Inzender bevindt of een aangepast item. Om dit te doen:

1. Ga naar uw Automation-account en selecteer **uitvoeren als-accounts** in de sectie account instellingen.
2. Selecteer een **uitvoeren als-account voor Azure**. 
3. Selecteer **rol** om de roldefinitie te zoeken die wordt gebruikt.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

U kunt ook de roldefinitie bepalen die wordt gebruikt door de run as-accounts voor meerdere abonnementen of Automation-accounts. Dit doet u door het script [Check-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug5) te gebruiken in de PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Machtigingen toevoegen aan Key Vault

U kunt Azure Automation laten verifiëren of Key Vault en de service-principal van het run as-account een aangepaste roldefinitie gebruiken. Hiervoor moet u het volgende doen:

* Machtigingen verlenen aan Key Vault.
* Stel het toegangs beleid in.

U kunt het script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault. ps1](https://aka.ms/AA5hugb) in de PowerShell Gallery gebruiken om uw uitvoeren als-account machtigingen te geven voor Key Vault. Zie [toepassingen toegang verlenen tot een sleutel kluis](../key-vault/key-vault-group-permissions-for-apps.md) voor meer informatie over het instellen van machtigingen voor Key Vault.

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Onjuiste configuratie problemen oplossen voor Run as-accounts

Bepaalde configuratie-items die nodig zijn voor een uitvoeren als-of klassiek uitvoeren als-account zijn mogelijk verwijderd of onjuist gemaakt tijdens de eerste installatie. Enkele instanties van de configuratie zijn onder andere:

* certificaatasset
* Verbindingsasset
* Het run as-account is verwijderd uit de rol Inzender
* Service-principal of toepassing in Azure AD

Voor dergelijke onjuiste configuratie-instanties detecteert het Automation-account de wijzigingen en geeft de status **onvolledig** weer in het deel venster Eigenschappen van run as-accounts voor het account.

![Onvolledige Uitvoeren als-configuratiestatus](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Wanneer u het uitvoeren als-account selecteert, wordt het volgende fout bericht weer gegeven in het deel venster Eigenschappen van account:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

U kunt deze problemen met het Uitvoeren als-account snel oplossen door het account te verwijderen en opnieuw te maken.

## <a name="next-steps"></a>Volgende stappen

* Zie [Application Objects and Service Principal Objects](../active-directory/develop/app-objects-and-service-principals.md)(Engelstalig) voor meer informatie over service-principals.
* Zie [overzicht van certificaten voor azure Cloud Services](../cloud-services/cloud-services-certs-create.md)voor meer informatie over certificaten en Azure-Services.
