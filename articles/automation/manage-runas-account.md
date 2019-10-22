---
title: Azure Automation uitvoeren als-accounts beheren
description: In dit artikel wordt beschreven hoe u uw uitvoeren als-accounts beheert met Power shell of via de portal.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 05/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fd7e94261d8302224b0e31e5f4ac46978dfa812f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690885"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Azure Automation uitvoeren als-accounts beheren

Run as-accounts in Azure Automation worden gebruikt voor de verificatie van het beheer van resources in azure met de Azure-cmdlets.

Wanneer u een uitvoeren als-account maakt, wordt er in Azure Active Directory een nieuwe Service-Principal-gebruiker gemaakt en wordt de rol Inzender toegewezen aan deze gebruiker op het abonnements niveau. Voor runbooks die gebruikmaken van Hybrid Runbook Workers op Azure virtual machines, kunt u [beheerde identiteiten voor Azure-resources](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) gebruiken in plaats van run as-accounts om te verifiëren bij uw Azure-resources.

Er zijn twee typen uitvoeren als-accounts:

* **Uitvoeren als-account van Azure** : dit account wordt gebruikt voor het beheren van resources van het [Resource Manager-implementatie model](../azure-resource-manager/resource-manager-deployment-model.md) .
  * Er wordt een Azure AD-toepassing met een zelf-ondertekend certificaat gemaakt. Daarnaast wordt er voor de toepassing in Azure AD een service-principalaccount gemaakt en wordt aan dit account de rol Inzender toegewezen in uw huidige abonnement. U kunt deze instelling wijzigen in Eigenaar of een andere rol. Zie [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md) voor meer informatie.
  * Er wordt een Automation-certificaatasset gemaakt met de naam *AzureRunAsCertificate* in het opgegeven Automation-account. Het certificaatasset bevat de persoonlijke sleutel van het certificaat die wordt gebruikt door de Azure AD-toepassing.
  * Er wordt een Automation-verbindingsasset gemaakt met de naam *AzureRunAsConnection* in het opgegeven Automation-account. Het verbindingsasset bevat de toepassing-id, tenant-id, abonnement-id en certificaatvingerafdruk.

* **Klassiek uitvoeren als-account van Azure** : dit account wordt gebruikt voor het beheren van de resources van het [klassieke implementatie model](../azure-resource-manager/resource-manager-deployment-model.md) .
  * Hiermee maakt u een beheer certificaat in het abonnement
  * Er wordt een Automation-certificaatasset gemaakt met de naam *AzureClassicRunAsCertificate* in het opgegeven Automation-account. Het certificaatasset bevat de persoonlijke sleutel van het certificaat die wordt gebruikt door het beheercertificaat.
  * Er wordt een Automation-verbindingsasset gemaakt met de naam *AzureClassicRunAsConnection* in het opgegeven Automation-account. Het verbindingsasset bevat de naam van het abonnement, de abonnements-id en de certificaatassetnaam.
  * Moet een mede beheerder zijn van het abonnement om te maken of te vernieuwen

  > [!NOTE]
  > Azure Cloud Solution Provider-abonnementen (Azure CSP) ondersteunen alleen het Azure Resource Manager model, niet-Azure Resource Manager services zijn niet beschikbaar in het programma. Wanneer u een CSP-abonnement gebruikt, wordt het klassieke uitvoeren als-account van Azure niet gemaakt. Het uitvoeren als-account van Azure wordt nog steeds gemaakt. Zie [beschik bare Services in CSP-abonnementen](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments)voor meer informatie over CSP-abonnementen.

  > [!NOTE]
  > De service-principal voor een uitvoeren als-account heeft niet de bevoegdheid om Azure Active Directory standaard te lezen. Als u machtigingen wilt toevoegen voor het lezen of beheren van Azure Active Directory, moet u die machtiging verlenen aan de Service-Principal onder **API-machtigingen**. Zie [machtigingen toevoegen voor toegang tot Web-api's](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)voor meer informatie.

## <a name="permissions"></a>Machtigingen voor het configureren van run as-accounts

Als u een uitvoeren als-account wilt maken of bijwerken, moet u specifieke bevoegdheden en machtigingen hebben. Een toepassings beheerder in Azure Active Directory en een eigenaar van een abonnement kan alle taken volt ooien. In een situatie waarin u een schei ding van taken hebt, ziet u in de volgende tabel een lijst van de taken, de overeenkomstige cmdlet en de benodigde machtigingen:

|Taak|Cmdlet  |Minimale machtigingen  |Waar u de machtigingen instelt|
|---|---------|---------|---|
|Een Azure AD-toepassing maken|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Application Developer-rol<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure Active Directory > app-registraties |
|Voeg een referentie toe aan de toepassing.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Toepassings beheerder of globale beheerder<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure Active Directory > app-registraties|
|Een Azure AD-service-principal maken en ophalen|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Toepassings beheerder of globale beheerder<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure Active Directory > app-registraties|
|De RBAC-rol voor de opgegeven Principal toewijzen of ophalen|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | U moet over de volgende machtigingen beschikken:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br>Of een:</br></br>Beheerder of eigenaar van gebruikers toegang        | [Abonnement](../role-based-access-control/role-assignments-portal.md)</br>Home >-abonnementen > \<subscription name \>-Access Control (IAM)|
|Een Automation-certificaat maken of verwijderen|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Inzender voor resource groep         |Resource groep voor Automation-account|
|Een Automation-verbinding maken of verwijderen|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Inzender voor resource groep |Resource groep voor Automation-account|

<sup>1</sup> gebruikers zonder beheerders rechten in uw Azure AD-TENANT [kunnen AD-toepassingen registreren](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) als de optie gebruikers van de Azure AD-Tenant **kunnen toepassingen registreren** in de pagina **gebruikers instellingen** is ingesteld op **Ja**. Als de instelling app-registraties is ingesteld op **Nee**, moet de gebruiker die deze actie uitvoert, zijn gedefinieerd in de voor gaande tabel.

Als u geen lid bent van het Active Directory exemplaar van het abonnement voordat u wordt toegevoegd aan de rol van **globale beheerder** van het abonnement, wordt u als gast toegevoegd. In dit geval ontvangt u een `You do not have permissions to create…` waarschuwing op de pagina **Automation-account toevoegen** . Gebruikers die zijn toegevoegd aan de rol van **globale beheerder** , kunnen worden verwijderd uit het Active Directory-exemplaar van het abonnement en opnieuw worden toegevoegd, zodat ze een volledige gebruiker zijn in Active Directory. U kunt deze situatie controleren door in het deelvenster **Azure Active Directory** van Azure Portal **Gebruikers en groepen** te selecteren. Selecteer vervolgens **Alle gebruikers**, de specifieke gebruiker en **Profiel**. De waarde van het kenmerk **Gebruikerstype** onder het gebruikersprofiel mag niet gelijk zijn aan **Gast**.

## <a name="permissions-classic"></a>Machtigingen voor het configureren van klassieke uitvoeren als-accounts

Als u klassieke uitvoeren als-accounts wilt configureren of vernieuwen, moet u de rol **co-beheerder** hebben op het abonnements niveau. Zie voor meer informatie over klassieke machtigingen [Azure Classic Subscription Administrators](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-the-portal"></a>Een uitvoeren als-account maken in de portal

In deze sectie voert u de volgende stappen uit om uw Azure Automation-account bij te werken in Azure Portal. U maakt afzonderlijke Uitvoeren als- en Klassiek Uitvoeren als-accounts. Als u geen klassieke resources hoeft te beheren, kunt u alleen de Uitvoeren als-account van Azure maken.

1. Meld u aan bij Azure Portal met een account dat lid is van de rol Abonnementsbeheerders en dat medebeheerder is van het abonnement.
2. Klik in Azure Portal op **Alle services**. Typ in de lijst met resources **Automation**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Automation-accounts**.
3. Selecteer op de pagina **Automation-accounts** uw Automation-account uit de lijst met Automation-accounts.
4. Selecteer in het linkerdeelvenster de optie **Uitvoeren als-accounts** in de sectie **Accountinstellingen**.
5. Afhankelijk van welk account u nodig hebt, selecteert u **Uitvoeren als-account van Azure**  of **Klassiek Uitvoeren als-account van Azure**. Na deze selectie wordt het deelvenster **Azure uitvoeren als-account toevoegen** of **Klassiek Uitvoeren als-account toevoegen** weergegeven. Controleer de overzichtsgegevens en klik op **Maken** om verder te gaan met het maken van een Uitvoeren als-account.
6. Terwijl in Azure het Uitvoeren als-account wordt gemaakt, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen. Er wordt ook een banner weergegeven waarin staat dat het account wordt gemaakt. Dit proces kan enkele minuten duren.

## <a name="create-run-as-account-using-powershell"></a>Een uitvoeren als-account maken met behulp van Power shell

## <a name="prerequisites"></a>Vereisten

De volgende lijst bevat de vereisten voor het maken van een uitvoeren als-account in Power shell:

* Windows 10 of Windows Server 2016 met Azure Resource Manager modules 3.4.1 en hoger. Het PowerShell-script biedt geen ondersteuning voor eerdere versies van Windows.
* Azure PowerShell 1.0 en hoger. Zie [Azure PowerShell installeren en configureren](/powershell/azureps-cmdlets-docs) voor meer informatie over de PowerShell 1.0-release.
* Een Automation-account waarnaar wordt verwezen als de waarde voor de parameter *– AutomationAccountName* en *- ApplicationDisplayName*.
* Machtigingen die gelijk zijn aan wat wordt vermeld in de [vereiste machtigingen voor het configureren van run as-accounts](#permissions)

Voer de volgende stappen uit om de waarden op te halen voor *SubscriptionID*, *ResourceGroup*en *AutomationAccountName*, die vereiste para meters zijn voor het script:

1. Klik in Azure Portal op **Alle services**. Typ in de lijst met resources **Automation**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Automation-accounts**.
1. Selecteer op de Automation-accountpagina uw Automation-account en selecteer vervolgens onder **Accountinstellingen** de optie **Eigenschappen**.
1. Noteer de waarden van de **abonnements-id**, de **naam**en de **resource groep** op de **Eigenschappen** pagina.

   ![De pagina eigenschappen van het Automation-account](media/manage-runas-account/automation-account-properties.png)

Dit PowerShell-script biedt ondersteuning voor de volgende configuraties:

* Een Uitvoeren als-account maken met een zelfondertekend certificaat.
* Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat.
* Maak een Uitvoeren als-account en een klassiek Uitvoeren als-account via een certificaat dat is uitgegeven door de certificeringsinstantie van uw bedrijf.
* Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat in de cloud van Azure Government.

>[!NOTE]
> Als u ervoor kiest om een klassiek Uitvoeren als-account te maken, moet u na het uitvoeren van het script het openbare certificaat (bestandsnaamextensie .cer) uploaden naar het beheerarchief voor het abonnement waarin het Automation-account is gemaakt.

1. Sla het volgende script op uw computer op. Sla het bestand in dit voorbeeld op met de bestandsnaam *New-RunAsAccount.ps1*.

   Het script maakt gebruik van meerdere Azure Resource Manager-cmdlets voor het maken van resources. In de voor gaande [machtigingen](#permissions) tabel worden de cmdlets en de bijbehorende machtigingen weer gegeven.

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
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzureRmAccount -Environment $EnvironmentName
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

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
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
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

    > [!IMPORTANT]
    > **Add-AzureRmAccount** is nu een alias voor **Connect-AzureRmAccount**. Als u de bibliotheek items zoekt en u geen **Connect-AzureRMAccount**ziet, kunt u **add-AzureRMAccount**gebruiken, maar u kunt ook [uw modules bijwerken](automation-update-azure-modules.md) in uw Automation-account.

1. Start op uw computer **Windows PowerShell** op vanaf het **Start**scherm met verhoogde gebruikersrechten.
1. Ga vanuit de opdrachtregel-shell met verhoogde bevoegdheden naar de map die het script bevat dat u in stap 1 hebt gemaakt.
1. Voer het script uit met de parameterwaarden voor de configuratie die u nodig hebt.

    **Een Uitvoeren als-account maken met een zelfondertekend certificaat**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een bedrijfscertificaat**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat in de cloud van Azure Government**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > Nadat het script is uitgevoerd, wordt u gevraagd zich te verifiëren met Azure. Meld u aan met een account dat lid is van de rol Abonnementsbeheerders en dat medebeheerder is van het abonnement.

Let op het volgende nadat het script is uitgevoerd:

* Als u een klassiek Uitvoeren als-account hebt gemaakt met een zelfondertekend openbaar certificaat (.cer-bestand), wordt het door het script gemaakt en opgeslagen in de map met tijdelijke bestanden op uw computer, onder het gebruikersprofiel *%USERPROFILE%\AppData\Local\Temp*, dat u hebt gebruikt voor het uitvoeren van de PowerShell-sessie.

* Gebruik dit certificaat als u een klassiek Uitvoeren als-account hebt gemaakt met een openbaar certificaat (.cer-bestand). Volg de instructies voor [het uploaden van een API-beheer certificaat naar de Azure Portal](../azure-api-management-certs.md).

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Een Uitvoeren als- of klassiek Uitvoeren als-account verwijderen

In dit gedeelte wordt beschreven hoe u uw Uitvoeren als- of klassieke Uitvoeren als-account kunt verwijderen en opnieuw kunt maken. Als u deze actie uitvoert, blijft het Automation-account behouden. Nadat u het Uitvoeren als- of klassieke Uitvoeren als-account hebt verwijderd, kunt u het opnieuw maken in Azure Portal.

1. Open in Azure Portal het Automation-account.

2. Selecteer op de pagina **Automation-account** de optie **Uitvoeren als-accounts**.

3. Selecteer op de pagina **Uitvoeren als-accounts** het Uitvoeren als- of klassieke Uitvoeren als-account dat u wilt verwijderen. Klik vervolgens in het deelvenster **Eigenschappen** voor het geselecteerde account op **Verwijderen**.

   ![Uitvoeren als-account verwijderen](media/manage-runas-account/automation-account-delete-runas.png)

1. Terwijl het account wordt verwijderd, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen.

1. Nadat het account is verwijderd, kunt u het opnieuw maken door op de eigenschappenpagina **Uitvoeren als-accounts** de optie **Azure Uitvoeren als-account** te selecteren.

   ![Het Automation uitvoeren als-account opnieuw maken](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Zelfondertekend certificaat vernieuwen

Op een bepaald moment voordat het run as-account verloopt, moet u het certificaat vernieuwen. Als u denkt dat het Uitvoeren als-account is aangetast, kunt u het verwijderen en opnieuw maken. In deze sectie wordt besproken hoe u deze bewerkingen uitvoert.

Het zelfondertekende certificaat dat u voor het Uitvoeren als-account hebt gemaakt, verloopt één jaar na de aanmaakdatum. U kunt het certificaat op elk gewenst moment vernieuwen voordat het verloopt. Wanneer u deze vernieuwt, wordt het huidige geldige certificaat bewaard om ervoor te zorgen dat alle runbooks die in de wachtrij zijn geplaatst of actief actief zijn, en die worden geverifieerd met het uitvoeren als-account, niet negatief worden beïnvloed. Het certificaat blijft geldig tot de vervaldatum.

> [!NOTE]
> Als u het Uitvoeren als-account voor Automation hebt geconfigureerd om een certificaat te gebruiken dat is uitgegeven door de certificeringsinstantie van uw bedrijf, en u deze optie gebruikt, wordt het bedrijfscertificaat vervangen door een zelfondertekend certificaat.

Ga als volgt te werk om het certificaat te vernieuwen:

1. Open in Azure Portal het Automation-account.

1. Selecteer **uitvoeren als-accounts** onder **account instellingen**.

    ![Eigenschappendeelvenster voor Automation-account](media/manage-runas-account/automation-account-properties-pane.png)

1. Selecteer op de pagina **Uitvoeren als-accounts** het Uitvoeren als- of klassieke Uitvoeren als-account waarvoor u het certificaat wilt vernieuwen.

1. Klik in het deelvenster **Eigenschappen** voor het geselecteerde account op **Certificaat vernieuwen**.

    ![Certificaat vernieuwen voor Uitvoeren als-account](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Terwijl het certificaat wordt gemaakt, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen.

## <a name="auto-cert-renewal"></a>Automatische certificaat vernieuwing instellen met een Automation-runbook

Als u certificaten automatisch wilt vernieuwen, kunt u een Automation-runbook gebruiken. Met het volgende script op [github](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) is deze functionaliteit mogelijk in uw Automation-account.

- Het `GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1` script maakt een wekelijks schema voor het vernieuwen van run as-account certificaten.
- Het script voegt een **Update-AutomationRunAsCredential-** runbook toe aan uw Automation-account.
  - U kunt ook de runbook-code weer geven op GitHub in het script: [Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1).
  - U kunt ook de Power shell-code in het bestand gebruiken om certificaten naar behoefte hand matig te vernieuwen.

Als u het vernieuwings proces onmiddellijk wilt testen, gebruikt u de volgende stappen:

1. Bewerk het runbook **Update-AutomationRunAsCredential** en plaats een commentaar teken (`#`) op regel 122, vóór de `Exit(1)` opdracht, zoals hieronder wordt weer gegeven.

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

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Nadat de test is, bewerkt u het runbook en verwijdert u het opmerkings teken dat u in **stap 1**hebt toegevoegd.
6. **Publiceer** het runbook.

> [!NOTE]
> U moet een **globale beheerder** of een beheerder van het **bedrijf** zijn in azure Active Directory om het script uit te voeren.

## <a name="limiting-run-as-account-permissions"></a>Machtigingen voor het uitvoeren als-account beperken

Als u het doel van automatisering wilt bepalen op basis van resources in azure, kunt u het script [Update-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug8) uitvoeren in de Power shell Gallery om uw bestaande service-principal van het run as-account te wijzigen voor het maken en gebruiken van een aangepaste rol inhoudsindexdefinitie. Deze rol heeft machtigingen voor alle resources, met uitzonde ring van [Key Vault](https://docs.microsoft.com/azure/key-vault/).

> [!IMPORTANT]
> Na het uitvoeren van het `Update-AutomationRunAsAccountRoleAssignments.ps1` script, werken runbooks die gebruikmaken van de sleutel kluis door het gebruik van runas-accounts niet meer. U moet runbooks in uw account controleren voor aanroepen naar Azure-sleutel kluis.
>
> Als u toegang tot de sleutel kluis van Azure Automation runbooks wilt inschakelen, moet u [het runas-account toevoegen aan de machtigingen van de sleutel kluis](#add-permissions-to-key-vault).

Als u wilt beperken wat de RunAs-service-principal verder kan doen, kunt u andere resource typen toevoegen aan de `NotActions` van de definitie van de aangepaste rol. In het volgende voor beeld wordt de toegang tot `Microsoft.Compute` beperkt. Als u dit toevoegt aan de **intact** van de roldefinitie, heeft deze rol geen toegang tot een reken resource. Zie [inzicht in roldefinities voor Azure-resources](../role-based-access-control/role-definitions.md)voor meer informatie over functie definities.

```powershell
$roleDefinition = Get-AzureRmRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzureRMRoleDefinition
```

Als u wilt bepalen of de service-principal die wordt gebruikt door het run as-account in de **Inzender** of een aangepaste roldefinitie, gaat u naar uw Automation-account en selecteert u onder **account instellingen**de optie **uitvoeren als-accounts**  > **Azure uitvoeren als-account**. Onder **rol** vindt u de roldefinitie die wordt gebruikt.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

U kunt het script [Check-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug5) gebruiken in het PowerShell Gallery om de roldefinitie te bepalen die wordt gebruikt door de run as-accounts Automation voor meerdere abonnementen of Automation-accounts.

### <a name="add-permissions-to-key-vault"></a>Machtigingen toevoegen aan Key Vault

Als u Azure Automation wilt toestaan om Key Vault te beheren en als service-principal van de uitvoeren als-account gebruikmaakt van een aangepaste roldefinitie, moet u extra stappen uitvoeren om dit gedrag toe te staan:

* Machtigingen verlenen aan de Key Vault
* Het toegangs beleid instellen

U kunt het script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault. ps1](https://aka.ms/AA5hugb) in de PowerShell Gallery gebruiken om uw uitvoeren als-account machtigingen te geven voor de sleutel kluis of om [toegang te verlenen aan toepassingen](../key-vault/key-vault-group-permissions-for-apps.md) voor meer informatie over instellingen machtigingen voor de sleutel kluis.

## <a name="misconfiguration"></a>Onjuiste configuratie

Bepaalde configuratie-items die nodig zijn voor het juist functioneren van het Uitvoeren als- of klassieke Uitvoeren als-account, zijn mogelijk verwijderd of niet juist gemaakt tijdens de initiële configuratie. Dit zijn onder meer de volgende items:

* Certificaatasset
* Verbindingsasset
* Uitvoeren als-account is verwijderd uit de rol Inzender
* Service-principal of toepassing in Azure AD

In het voorgaande en andere gevallen van onjuiste configuratie detecteert het Automation-account de wijzigingen en geeft het de status *Onvolledig* weer op de eigenschappenpagina **Uitvoeren als-accounts** voor het account.

![Onvolledige Uitvoeren als-configuratiestatus](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Als u het Uitvoeren als-account selecteert, wordt het volgende foutbericht weergegeven in het deelvenster **Eigenschappen** van het account:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

U kunt deze problemen met het Uitvoeren als-account snel oplossen door het account te verwijderen en opnieuw te maken.

## <a name="next-steps"></a>Volgende stappen

* Zie [Application Objects and Service Principal Objects](../active-directory/develop/app-objects-and-service-principals.md)(Engelstalig) voor meer informatie over service-principals.
* Zie [overzicht van certificaten voor azure Cloud Services](../cloud-services/cloud-services-certs-create.md)voor meer informatie over certificaten en Azure-Services.
