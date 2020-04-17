---
title: Azure Automation Run beheren als accounts
description: In dit artikel wordt beschreven hoe u uw Run As-accounts beheert met PowerShell of via de portal.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: 4a043bcc2f81214b68b166d443baead6cd230184
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457498"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Azure Automation Run beheren als accounts

Uitvoeren Als accounts in Azure Automation verificatie bieden voor het beheren van resources in Azure met behulp van de Azure-cmdlets. Wanneer u een Run As-account maakt, wordt een nieuwe hoofdgebruiker van de service gemaakt in Azure Active Directory (AD) en wordt de rol inzender aan deze gebruiker toegeschreven op abonnementsniveau. Voor runbooks die hybride runbook-werknemers op virtuele Azure-machines gebruiken, u [beheerde identiteiten voor Azure-resources](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) gebruiken in plaats van Als-accounts uitvoeren om te verifiëren voor uw Azure-bronnen.

De serviceprincipal voor een Run as-account heeft geen machtigingen om Azure AD standaard te lezen. Als u machtigingen wilt toevoegen om Azure AD te lezen of te beheren, moet u de machtigingen voor de serviceprincipal onder **API-machtigingen**verlenen. Zie [Machtigingen toevoegen om toegang te krijgen tot webAPI's voor](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)meer informatie.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="types-of-run-as-accounts"></a>Typen Run As-accounts

Azure Automation gebruikt twee typen Run As-accounts:

* Azure Run As-account
* Azure Classic Run As-account

>[!NOTE]
>CSP-abonnementen (Azure Cloud Solution Provider) ondersteunen alleen het Azure Resource Manager-model. Niet-Azure Resource Manager-services zijn niet beschikbaar in het programma. Wanneer u een CSP-abonnement gebruikt, wordt het Azure Classic Run As-account niet gemaakt, maar wordt het Azure Run As-account gemaakt. Zie [Beschikbare services in CSP-abonnementen](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)voor meer informatie over CSP-abonnementen.

### <a name="run-as-account"></a>Run As-account

Het run As-account beheert [resources voor het implementatiemodel resourcebeheer.](../azure-resource-manager/management/deployment-models.md) Het doet de volgende taken.

* Er wordt een Azure AD-toepassing met een zelf-ondertekend certificaat gemaakt. Daarnaast wordt er voor de toepassing in Azure AD een service-principalaccount gemaakt en wordt aan dit account de rol Inzender toegewezen in uw huidige abonnement. U de certificaatinstelling wijzigen in Eigenaar of een andere rol. Zie [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md) voor meer informatie.
  
* Hiermee maakt u `AzureRunAsCertificate` een automatiseringscertificaatasset met de naam in het opgegeven automatiseringsaccount. Het certificaatitem bevat de privésleutel van het certificaat die de Azure AD-toepassing gebruikt.
  
* Hiermee maakt u `AzureRunAsConnection` een automatiseringsverbindingselement met de naam in het opgegeven automatiseringsaccount. Het verbindingsitem bevat de toepassings-id, tenant-id, abonnements-ID en certificaatduimafdruk.

### <a name="azure-classic-run-as-account"></a>Klassieke Azure Uitvoeren als-account

Het Azure Classic Run As-account beheert [klassieke implementatiemodelbronnen.](../azure-resource-manager/management/deployment-models.md) U moet een medebeheerder zijn bij het abonnement om dit type account aan te maken of te verlengen.

Het Azure Classic Run As-account voert de volgende taken uit.

  * Hiermee maakt u een beheercertificaat in het abonnement.

  * Hiermee maakt u `AzureClassicRunAsCertificate` een automatiseringscertificaatasset met de naam in het opgegeven automatiseringsaccount. Het certificaatasset bevat de persoonlijke sleutel van het certificaat die wordt gebruikt door het beheercertificaat.

  * Hiermee maakt u `AzureClassicRunAsConnection` een automatiseringsverbindingselement met de naam in het opgegeven automatiseringsaccount. Het verbindingsitem bevat de naam van het abonnement, de abonnements-ID en de naam van het certificaatitem.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Machtigingen voor als account uitvoeren

In deze sectie worden machtigingen gedefinieerd voor zowel gewone Run As-accounts als Classic Run As-accounts.

### <a name="permissions-to-configure-run-as-accounts"></a>Machtigingen voor het configureren van Run As-accounts

Als u een Run As-account wilt maken of bijwerken, moet u specifieke bevoegdheden en machtigingen hebben. Een toepassingsbeheerder in Azure Active Directory en een eigenaar in een abonnement kunnen alle taken uitvoeren. In een situatie waarin u takenscheiding hebt, wordt in de volgende tabel een lijst weergegeven van de taken, de gelijkwaardige cmdlet en de machtigingen die nodig zijn:

|Taak|Cmdlet  |Minimummachtigingen  |Waar u de machtigingen instelt|
|---|---------|---------|---|
|Azure AD-toepassing maken|[Nieuwe-AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication?view=azps-3.5.0)     | Rol 1<sup>1</sup> toepassingsontwikkelaar        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>> Azure AD->-app-registraties |
|Voeg een referentie toe aan de toepassing.|[Nieuw-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential?view=azps-3.5.0)     | Toepassingsbeheerder of globale beheerder<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>> Azure AD->-app-registraties|
|Een Azure AD-serviceprincipal maken en deze krijgen|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-3.5.0)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal?view=azps-3.5.0)     | Toepassingsbeheerder of globale beheerder<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>> Azure AD->-app-registraties|
|De RBAC-rol toewijzen of krijgen voor de opgegeven principal|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment?view=azps-3.5.0)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment?view=azps-3.5.0)      | Beheerder of eigenaar van gebruikerstoegang, of de volgende machtigingen:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Abonnement](../role-based-access-control/role-assignments-portal.md)</br>Home >-abonnementen \<\> > abonnementsnaam - Toegangsbeheer (IAM)|
|Een automatiseringscertificaat maken of verwijderen|[Nieuw-AzAutomationCertificaat](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.5.0)</br>[Certificaat voor het verwijderen van azautomation](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate?view=azps-3.5.0)     | Bijdrager op resourcegroep         |Automatiseringsaccountbrongroep|
|Een automatiseringsverbinding maken of verwijderen|[Nieuwe-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.5.0)</br>[Verbinding verwijderen-azautomation](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection?view=azps-3.5.0)|Bijdrager op resourcegroep |Automatiseringsaccountbrongroep|

<sup>1</sup> Niet-beheerdersgebruikers in uw Azure AD-tenant kunnen [AD-toepassingen registreren](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) als de gebruikers van de Azure AD-tenant de optie **Toepassingen kunnen registreren** op de pagina Gebruikersinstellingen is ingesteld op **Ja**. Als de instelling voor toepassingsregistratie **nee**is, moet de gebruiker die deze actie uitvoert, zijn zoals gedefinieerd in deze tabel.

Als u geen lid bent van het Active Directory-exemplaar van het abonnement voordat u wordt toegevoegd aan de rol globale beheerder van het abonnement, wordt u als gast toegevoegd. In deze situatie ontvangt `You do not have permissions to create…` u een waarschuwing op de pagina Automatiseringsaccount toevoegen. 

Als u lid bent van het Active Directory-exemplaar van het abonnement wanneer `You do not have permissions to create…` de functie Globale beheerder is toegewezen, u ook een waarschuwing ontvangen op de pagina Automatiseringsaccount toevoegen. In dit geval u verwijdering aanvragen uit het Active Directory-exemplaar van het abonnement en vervolgens vragen om opnieuw te worden toegevoegd, zodat u een volledige gebruiker in Active Directory wordt.

Ga als volgt te werk om te controleren of de situatie waarin de foutmelding wordt geproduceerd, is verholpen:

1. Selecteer **gebruikers en groepen**in het azure Active Directory-deelvenster in de Azure-portal. 
2. Selecteer **Alle gebruikers**selecteren .
3. Kies uw naam en selecteer **Profiel**. 
4. Controleer of de waarde van het kenmerk **Gebruikerstype** onder het profiel van uw gebruiker niet is ingesteld op **Gast.**

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Machtigingen voor het configureren van Classic Run As-accounts

Als u Classic Run As-accounts wilt configureren of vernieuwen, moet u de rol van co-beheerder op abonnementsniveau hebben. Zie [Azure classic subscription administrators](../role-based-access-control/classic-administrators.md#add-a-co-administrator)voor meer informatie over klassieke abonnementsmachtigingen.

## <a name="creating-a-run-as-account-in-azure-portal"></a>Een Run As-account maken in Azure-portal

Voer de volgende stappen uit om uw Azure Automation-account bij te werken in de Azure-portal. Maak de Run As en Classic Run As-accounts afzonderlijk. Als u geen klassieke resources hoeft te beheren, kunt u alleen de Uitvoeren als-account van Azure maken.

1. Meld u bij Azure Portal aan met een account dat lid is van de rol Abonnementsbeheerders en dat medebeheerder is van het abonnement.
2. Zoeken naar en selecteer **Automatiseringsaccounts**.
3. Selecteer op de pagina Automatiseringsaccounts uw automatiseringsaccount in de lijst.
4. Selecteer In het linkerdeelvenster de optie **Als accounts uitvoeren** in de sectie Accountinstellingen.
5. Afhankelijk van welk account u nodig hebt, selecteert u **Uitvoeren als-account van Azure ** of **Klassiek Uitvoeren als-account van Azure**. 
6. Afhankelijk van het account van belang, gebruikt u het deelvenster **Azure Run As** toevoegen of Azure Classic Run toevoegen als **account.** Klik na het bekijken van de overzichtsinformatie op **Maken**.
6. Terwijl in Azure het Uitvoeren als-account wordt gemaakt, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen. Er wordt ook een banner weergegeven waarin staat dat het account wordt gemaakt. Het proces kan enkele minuten in beslag nemen.

## <a name="creating-a-run-as-account-using-powershell"></a>Een Run As-account maken met PowerShell

In de volgende lijst vindt u de vereisten voor het maken van een Run As-account in PowerShell. Deze vereisten zijn van toepassing op beide typen Run As-accounts.

* Windows 10 of Windows Server 2016 met Azure Resource Manager-modules 3.4.1 en hoger. Het PowerShell-script biedt geen ondersteuning voor eerdere versies van Windows.
* Azure PowerShell 1.0 en hoger. Zie [Azure PowerShell installeren en configureren](/powershell/azureps-cmdlets-docs) voor meer informatie over de PowerShell 1.0-release.
* Een automatiseringsaccount, dat wordt aangeduid als `AutomationAccountName` `ApplicationDisplayName` de waarde voor de parameters en parameters.
* Machtigingen die gelijkwaardig zijn aan de machtigingen die worden vermeld in [Vereiste machtigingen om Run As-accounts te configureren.](#permissions)

Voer de volgende `SubscriptionId` `ResourceGroupName`stappen uit om de waarden voor , en welke parameters vereist is voor het PowerShell-script te voltooien.

1. Selecteer **automatiseringsaccounts**in de Azure-portal .
1. Selecteer op de pagina Automatiseringsaccounts uw automatiseringsaccount.
1. Selecteer **Eigenschappen**in de sectie Accountinstellingen .
1. Let op de waarden voor **NAAM,** **ABONNEMENTS-ID**en **RESOURCEGROEP** op de pagina Eigenschappen. Deze waarden komen overeen `AutomationAccountName` `SubscriptionId`met `ResourceGroupName` de waarden voor respectievelijk de scriptparameters , en PowerShell.

   ![Pagina Eigenschappen van automatiseringsaccount](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>PowerShell-script om een Run As-account te maken

In deze sectie wordt een PowerShell-script aangemaakt om een Run As-account te maken. Het script bevat ondersteuning voor verschillende configuraties.

* Een Uitvoeren als-account maken met een zelfondertekend certificaat.
* Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat.
* Maak een Uitvoeren als-account en een klassiek Uitvoeren als-account via een certificaat dat is uitgegeven door de certificeringsinstantie van uw bedrijf.
* Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat in de cloud van Azure Government.

Het script maakt gebruik van meerdere Azure Resource Manager-cmdlets om resources te maken. Zie Machtigingen voor het configureren van [Run As-accounts voor](#permissions-to-configure-run-as-accounts)de cmdlets en de machtigingen die ze nodig hebben.

Sla het script op uw computer op met de bestandsnaam **New-RunAsAccount.ps1**.

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
>`Add-AzAccount`en `Add-AzureRMAccount` zijn aliassen voor [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). U deze cmdlets gebruiken of u [uw modules](automation-update-azure-modules.md) in uw Automation-account updaten naar de nieuwste versies. Mogelijk moet u uw modules bijwerken, zelfs als u zojuist een nieuw Automation-account hebt gemaakt.

### <a name="execute-the-powershell-script"></a>Het PowerShell-script uitvoeren

1. Start op uw computer **Windows PowerShell** op vanaf het **Start**scherm met verhoogde gebruikersrechten.
1. Ga vanuit de shell van de opdrachtregel naar de map met uw script.
1. Voer het script uit met behulp van de parameterwaarden voor de configuratie die u nodig hebt.
1. Als u een Classic Run As-account maakt, uploadt u, nadat het script is uitgevoerd, het openbare certificaat (**.cer** filename extension) naar het beheerarchief voor het abonnement waarin het automatiseringsaccount is gemaakt.

Nadat het script is uitgevoerd, wordt u gevraagd te verifiëren met Azure. Meld u aan met een account dat lid is van de rol van abonnementsbeheerders en medebeheerder van het abonnement.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Een Run As-account maken met een zelfondertekend certificaat

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Een Run As-account en een Classic Run As-account maken met een zelfondertekend certificaat

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Een Run As-account en een Classic Run As-account maken met behulp van een bedrijfscertificaat

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Als u een Classic Run As-account hebt gemaakt met een openbaar ondernemingscertificaat **(.cer-bestand),** gebruikt u dit certificaat. Zie [Een beheer-API-certificaat uploaden naar de Azure-portal](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat in de cloud van Azure Government

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Als u een Classic Run As-account hebt gemaakt met een zelfondertekend openbaar certificaat (**.cer-bestand),** maakt en slaat het script op in de map tijdelijke bestanden op uw computer. Het is te vinden `%USERPROFILE%\AppData\Local\Temp`in het gebruikersprofiel , dat u hebt gebruikt om de PowerShell-sessie uit te voeren.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Een run als- of klassiek run-account verwijderen

In deze sectie wordt beschreven hoe u een Run As- of Classic Run As-account verwijdert. Als u deze actie uitvoert, blijft het Automation-account behouden. Nadat u het account hebt verwijderd, u het opnieuw maken in de Azure-portal.

1. Open in Azure Portal het Automation-account.

2. Selecteer In het linkerdeelvenster de optie **Als accounts uitvoeren** in de sectie Accountinstellingen.

3. Selecteer op de pagina Uitvoeren als-accounts het Uitvoeren als- of klassieke Uitvoeren als-account dat u wilt verwijderen. 

4. Klik in het deelvenster Eigenschappen voor het geselecteerde account op **Verwijderen**.

   ![Uitvoeren als-account verwijderen](media/manage-runas-account/automation-account-delete-runas.png)

5. Terwijl het account wordt verwijderd, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen.

6. Nadat het account is verwijderd, kunt u het opnieuw maken door op de eigenschappenpagina Uitvoeren als-accounts de optie **Azure Uitvoeren als-account** te selecteren.

   ![Het Automation uitvoeren als-account opnieuw maken](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>Een zelfondertekend certificaat vernieuwen

Het zelfondertekende certificaat dat u hebt gemaakt voor het Run As-account verloopt een jaar na de datum van aanmaken. Op een gegeven moment voordat je Run As-account verloopt, moet je het certificaat verlengen. U het op elk gewenst moment verlengen voordat het verloopt. 

Wanneer u het zelfondertekende certificaat verlengt, blijft het huidige geldige certificaat behouden om ervoor te zorgen dat alle runbooks die in de wachtrij staan of actief worden uitgevoerd en die zich verifiëren met het account Uitvoeren als, niet negatief worden beïnvloed. Het certificaat blijft geldig tot de vervaldatum.

>[!NOTE]
>Als u denkt dat het run as-account is gecompromitteerd, u het zelfondertekende certificaat verwijderen en opnieuw maken.

>[!NOTE]
>Als u uw Run As-account hebt geconfigureerd om een certificaat te gebruiken dat is uitgegeven door uw enterprise certificate authority en u de optie gebruikt om een zelfondertekende certificaatoptie te verlengen, wordt het bedrijfscertificaat vervangen door een zelfondertekend certificaat.

Gebruik de volgende stappen om het zelfondertekende certificaat te vernieuwen.

1. Open in Azure Portal het Automation-account.

1. Selecteer **Als-accounts uitvoeren** in de sectie Accountinstellingen.

    ![Eigenschappendeelvenster voor Automation-account](media/manage-runas-account/automation-account-properties-pane.png)

1. Selecteer op de eigenschappenpagina Als-accounts uitvoeren de optie Als-account uitvoeren of het account Classic Run As waarvoor u het certificaat wilt vernieuwen.

1. Klik in het eigenschappenvenster voor het geselecteerde account op **Certificaat verlengen**.

    ![Certificaat vernieuwen voor Uitvoeren als-account](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Terwijl het certificaat wordt gemaakt, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Automatische certificaatverlenging instellen met een automatiseringsrunbook

Als u certificaten automatisch wilt vernieuwen, u een automatiseringsrunbook gebruiken. Dit script op [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) maakt deze functionaliteit mogelijk in uw Automation-account.

>[!NOTE]
>U moet een globale beheerder of bedrijfsbeheerder in Azure AD zijn om het script uit te voeren.

Met dit script wordt een wekelijks schema gemaakt om Run As-accountcertificaten te vernieuwen. Het voegt een **Update-AutomationRunAsCredential** runbook toe aan uw Automatiseringsaccount. U de runbook-code bekijken op GitHub, in het script [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). U de PowerShell-code in het bestand gebruiken om certificaten handmatig te vernieuwen, indien nodig.

Gebruik de volgende stappen om het vernieuwingsproces onmiddellijk te testen.

1. Bewerk de **runbook Update-AutomationRunAsCredential** en plaats een opmerkingsteken (#) op regel 122, voor de opdracht **Exit(1).**

   ```powershell
   #Exit(1)
   ```

2. Publiceer het runbook.
3. Start het loopboek.
4. Controleer succesvolle verlenging met de volgende code:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Uitvoer:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Bewerk na de test het runbook en verwijder het opmerkingsteken dat u in stap 1 hebt toegevoegd.
6. Publiceer het runbook.

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>Machtigingen voor run als-account beperken

Als u de targeting van Automatisering wilt beheren op resources in Azure, u het script [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) uitvoeren. Dit script wijzigt uw bestaande run as-accountserviceprincipal om een aangepaste roldefinitie te maken en te gebruiken. De rol heeft machtigingen voor alle resources, behalve [Key Vault.](https://docs.microsoft.com/azure/key-vault/)

>[!IMPORTANT]
>Nadat u het script **Update-AutomationRunAsAccountRoleAssignments.ps1** hebt uitgevoerd, werken boeken die toegang hebben tot Key Vault via het gebruik van Run As-accounts niet meer. Voordat u het script uitvoert, moet u runbooks in uw account controleren op oproepen naar Azure Key Vault. Als u toegang tot Key Vault vanuit Azure Automation-runbooks wilt inschakelen, moet u [het Run As-account toevoegen aan de machtigingen van Key Vault.](#add-permissions-to-key-vault)

Als u wilt beperken, verder wat de run als serviceprincipal kan `NotActions` doen, u andere resourcetypen toevoegen aan het element van de aangepaste roldefinitie. In het volgende voorbeeld `Microsoft.Compute/*`wordt de toegang tot . Als u dit resourcetype toevoegt aan `NotActions` de roldefinitie, heeft de rol geen toegang tot een Compute-bron. Zie [Roldefinities voor Azure-resources begrijpen voor](../role-based-access-control/role-definitions.md)meer informatie over roldefinities.

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

U bepalen of de serviceprincipal die wordt gebruikt door uw Run As-account zich bevindt in de definitie van de rolmedewerker of een aangepaste. 

1. Ga naar uw automatiseringsaccount en selecteer **Uitvoeren als accounts** in de sectie Accountinstellingen.
2. Selecteer **Azure Run as Account**. 
3. Selecteer **Rol** om de roldefinitie te zoeken die wordt gebruikt.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

U ook de roldefinitie bepalen die wordt gebruikt door de accounts Uitvoeren als voor meerdere abonnementen of automatiseringsaccounts. Doe dit met het script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) in de PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Machtigingen toevoegen aan Key Vault

U Azure Automation toestaan om te controleren of Key Vault en uw Run As-accountserviceprincipal een aangepaste roldefinitie gebruiken. U moet:

* Machtigingen verlenen aan Key Vault.
* Stel het toegangsbeleid in.

U het script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) gebruiken in de PowerShell Gallery om uw Run As-accountmachtigingen te geven aan Key Vault. Zie [Toegang tot toepassingen verlenen tot een sleutelkluis](../key-vault/general/group-permissions-for-apps.md) voor meer informatie over het instellen van machtigingen op Key Vault.

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Foutconfiguratieproblemen voor Run As-accounts oplossen

Sommige configuratie-items die nodig zijn voor een Run As- of Classic Run As-account, zijn mogelijk verwijderd of onjuist gemaakt tijdens de eerste installatie. Mogelijke gevallen van verkeerde configuratie zijn:

* Certificaatasset
* Verbindingsasset
* Als-account uitvoeren dat is verwijderd uit de rol Inzender
* Service-principal of toepassing in Azure AD

Voor dergelijke foutconfiguratie-exemplaren detecteert het automatiseringsaccount de `Incomplete` wijzigingen en geeft het een status weer van in het eigenschappenvenster Uitvoeren als accounts voor het account.

![Onvolledige Uitvoeren als-configuratiestatus](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Wanneer u het account Uitvoeren als selecteert, wordt in het deelvenster Accounteigenschappen het volgende foutbericht weergegeven:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

U kunt deze problemen met het Uitvoeren als-account snel oplossen door het account te verwijderen en opnieuw te maken.

## <a name="next-steps"></a>Volgende stappen

* Zie [Toepassingsobjecten en serviceprincipalobjecten](../active-directory/develop/app-objects-and-service-principals.md)voor meer informatie over serviceprincipals.
* Zie Overzicht van certificaten voor [Azure Cloud Services voor](../cloud-services/cloud-services-certs-create.md)meer informatie over certificaten en Azure-services.
