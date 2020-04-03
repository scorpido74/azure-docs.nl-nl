---
title: Automatische update van de Mobiliteitsservice in Azure Site Recovery
description: Overzicht van automatische update van de Mobiliteitsservice bij het repliceren van Azure VM's met Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: 67298ecf0c17feee2d36bb8774cae37b1ca81381
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618962"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatische update van de Mobiliteitsservice in Azure-naar-Azure-replicatie

Azure Site Recovery maakt gebruik van een maandelijkse releasecadans om eventuele problemen op te lossen en bestaande functies te verbeteren of nieuwe toe te voegen. Als u actueel wilt blijven met de service, moet u elke maand een patchimplementatie plannen. Om de overhead die aan elke upgrade is gekoppeld te voorkomen, u Site Recovery toestaan om componentupdates te beheren.

Zoals vermeld in [azure-naar-Azure-noodherstelarchitectuur,](azure-to-azure-architecture.md)is de Mobiliteitsservice geïnstalleerd op alle Virtuele Azure-machines (VM's) waarop replicatie is ingeschakeld van de ene Azure-regio naar de andere. Wanneer u automatische updates gebruikt, wordt de extensie van de Mobiliteitsservice bijgewerkt in elke nieuwe versie.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Hoe automatische updates werken

Wanneer u Site recovery gebruikt om updates te beheren, wordt een globale runbook (gebruikt door Azure-services) geïmplementeerd via een automatiseringsaccount dat is gemaakt in hetzelfde abonnement als de kluis. Elke kluis maakt gebruik van één automatiseringsaccount. Voor elke vm in een kluis controleert het runbook op actieve automatische updates. Als er een nieuwere versie van de mobiliteitsservice-extensie beschikbaar is, wordt de update geïnstalleerd.

Het standaard verloopboekschema vindt dagelijks om 12:00 uur plaats in de tijdzone van de geografie van de gerepliceerde VM. U ook de runbook-planning wijzigen via het automatiseringsaccount.

> [!NOTE]
> Vanaf [Update Rollup 35](site-recovery-whats-new.md#updates-march-2019)u een bestaand automatiseringsaccount kiezen dat u wilt gebruiken voor updates. Voorafgaand aan Update Rollup 35 heeft Site Recovery standaard het automatiseringsaccount gemaakt. U deze optie alleen selecteren wanneer u replicatie inschakelt voor een virtuele machine. Het is niet beschikbaar voor een vm die al replicatie heeft ingeschakeld. De instelling die u selecteert, is van toepassing op alle Azure VM's die in dezelfde kluis zijn beveiligd.

Als u automatische updates inschakelt, hoeft u uw Azure VM's niet opnieuw op te starten of de lopende replicatie te beïnvloeden.

Taakfacturering in het automatiseringsaccount is gebaseerd op het aantal taakverloopminuten dat in een maand wordt gebruikt. Taakuitvoering duurt een paar seconden tot ongeveer een minuut per dag en wordt gedekt als gratis eenheden. Standaard worden 500 minuten opgenomen als gratis eenheden voor een automatiseringsaccount, zoals in de volgende tabel wordt weergegeven:

| Gratis eenheden inbegrepen (elke maand) | Prijs |
|---|---|
| Looptijd van de taak 500 minuten | ∙0,14/minuut

## <a name="enable-automatic-updates"></a>Automatische updates inschakelen

Er zijn verschillende manieren waarop Site Recovery de uitbreidingsupdates kan beheren:

- [Beheren als onderdeel van de replicatiestap inschakelen](#manage-as-part-of-the-enable-replication-step)
- [De instellingen voor uitbreidingsupdate in de kluis in- of uitschakelen](#toggle-the-extension-update-settings-inside-the-vault)
- [Updates handmatig beheren](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Beheren als onderdeel van de replicatiestap inschakelen

Wanneer u replicatie voor een virtuele machine inschakelt vanaf [de VM-weergave](azure-to-azure-quickstart.md) of [vanuit de kluis van herstelservices,](azure-to-azure-how-to-enable-replication.md)u Site recovery toestaan om updates voor de siteherstelextensie te beheren of handmatig beheren.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Uitbreidingsinstellingen":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>De instellingen voor uitbreidingsupdate in de kluis in- of uitschakelen

1. Ga vanuit de kluis Recovery Services naar**Site Recovery Infrastructure** **beheren.** > 
1. Selecteer **onder For Azure Virtual Machines** > **Extension Update Settings** > **Allow Site Recovery to manage**, **selecteer Op**.

   Als u de extensie handmatig wilt beheren, selecteert u **Uit .**

1. Selecteer **Opslaan**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Instellingen voor uitbreidingupdate":::

> [!IMPORTANT]
> Wanneer u **Siteherstel toestaan kiest om te beheren,** wordt de instelling toegepast op alle VM's in de kluis.

> [!NOTE]
> Beide opties waarschuwt u van het automatiseringsaccount dat wordt gebruikt voor het beheren van updates. Als u deze functie voor het eerst in een kluis gebruikt, wordt er standaard een nieuw automatiseringsaccount gemaakt. U de instelling afwisselend aanpassen en een bestaand automatiseringsaccount kiezen. Alle volgende taksen om replicatie in dezelfde kluis in te schakelen, gebruiken het eerder gemaakte automatiseringsaccount. Op dit moment wordt in het vervolgkeuzemenu alleen automatiseringsaccounts weergegeven die zich in dezelfde resourcegroep bevinden als de kluis.

> [!IMPORTANT]
> Het volgende script moet worden uitgevoerd in de context van een automatiseringsaccount.
Gebruik het volgende script voor een aangepast automatiseringsaccount:

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,

    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,

    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)

$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"

function Throw-TerminatingErrorMessage
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )

    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}

function Write-Tracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
                [String]
        $Level,

        [Parameter(Mandatory=$true)]
        [String]
        $Message,

            [Switch]
        $DisplayMessageToUser
        )

    Write-Output $Message

}

function Write-InformationTracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )

    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}

function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }

        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }

        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }

        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")

        $Count = 0
                $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }

                return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}

function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)

        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion

        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName

        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]

        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }

            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
                continue;
            }

            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count

            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }

            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}

$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")

$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser

ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)

$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json

if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}

Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)

try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName

            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]

            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;

            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }

    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)

                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }

            # Rate controlling the get calls to maximum 120 calls each minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }

        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)

        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }

        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)

if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-updates-manually"></a>Updates handmatig beheren

1. Als er nieuwe updates zijn voor de Mobiliteitsservice die op uw VM's zijn geïnstalleerd, ziet u de volgende melding: **Nieuwe replicatieagent-update voor siteherstel is beschikbaar. Klik om te installeren.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Venster Gerepliceerde items":::

1. Selecteer de melding om de vm-selectiepagina te openen.
1. Kies de VM's die u wilt upgraden en selecteer **OK**. De updatemobiliteitsservice start voor elke geselecteerde VM.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="VM-lijst met gerepliceerde items":::

## <a name="common-issues-and-troubleshooting"></a>Veelvoorkomende problemen en probleemoplossing

Als er een probleem is met de automatische updates, ziet u een foutmelding onder **Configuratieproblemen** in het vault-dashboard.

Als u automatische updates niet inschakelen, raadpleegt u de volgende veelvoorkomende fouten en aanbevolen acties:

- **Fout:** u hebt geen machtigingen om een Azure Run As-account (serviceprincipal) te maken en de rol Inzender toe te kennen aan de serviceprincipal.

  **Aanbevolen actie:** zorg ervoor dat het aangemelde account is toegewezen als Bijdrager en probeer het opnieuw. Zie het gedeelte vereiste machtigingen van [Hoe: Gebruik de portal om een Azure AD-toepassing en serviceprincipal te maken die toegang heeft tot bronnen](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)voor meer informatie over het toewijzen van machtigingen.

  Als u de meeste problemen wilt oplossen nadat u automatische updates hebt ingeschakeld, selecteert u **Herstellen**. Als de reparatieknop niet beschikbaar is, raadpleegt u het foutbericht dat wordt weergegeven in het deelvenster Instellingen voor extensie-updates.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Knop Site Recovery-servicereparatie in instellingen voor uitbreidingupdate":::

- **Fout:** Het account Uitvoeren als heeft niet de toestemming om toegang te krijgen tot de bron van herstelservices.

  **Aanbevolen actie**: Verwijder en [maak vervolgens het account uitvoeren als opnieuw](/azure/automation/automation-create-runas-account). Of zorg ervoor dat de Azure Active Directory-toepassing van het Azure-account van het Azure Run Als-account toegang heeft tot de bron voor herstelservices.

- **Fout:** Uitvoeren als account wordt niet gevonden. Een van deze is verwijderd of niet gemaakt - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connection asset - of de Thumbprint is niet identiek tussen Certificaat en Verbinding.

  **Aanbevolen actie**: Verwijder en [maak vervolgens het account uitvoeren als opnieuw](/azure/automation/automation-create-runas-account).

- **Fout:** de Azure Run as Certificate die wordt gebruikt door het automatiseringsaccount, loopt bijna af.

  Het zelfondertekende certificaat dat is gemaakt voor het Run As-account verloopt een jaar na de datum van aanmaken. U kunt het certificaat op elk gewenst moment vernieuwen voordat het verloopt. Als u zich hebt aangemeld voor e-mailmeldingen, ontvangt u ook e-mails wanneer een actie van uw kant vereist is. Deze fout wordt twee maanden voor de vervaldatum weergegeven en verandert in een kritieke fout als het certificaat is verlopen. Zodra het certificaat is verlopen, is de automatische update pas functioneel als u hetzelfde verlengt.

  **Aanbevolen actie:** Als u dit probleem wilt oplossen, selecteert u **Herstel** en vervolgens **Certificaat verlengen**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="vernieuwing-cert":::

  > [!NOTE]
  > Nadat u het certificaat hebt vernieuwd, vernieuwt u de pagina om de huidige status weer te geven.
