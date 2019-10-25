---
title: Office 365-beheer oplossing in azure | Microsoft Docs
description: Dit artikel bevat informatie over de configuratie en het gebruik van de Office 365-oplossing in Azure.  Het bevat een gedetailleerde beschrijving van de Office 365-records die zijn gemaakt in Azure Monitor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 84af0484ed9fb792bef6bbbe9c53395b569acb3c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793869"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Office 365-beheer oplossing in azure (preview-versie)

![Logo van Office 365](media/solution-office-365/icon.png)


> [!NOTE]
> De aanbevolen methode voor het installeren en configureren van de Office 365-oplossing is het inschakelen van de [office 365-connector](../../sentinel/connect-office-365.md) in [Azure Sentinel](../../sentinel/overview.md) in plaats van de stappen in dit artikel uit te voeren. Dit is een bijgewerkte versie van de Office 365-oplossing met een verbeterde configuratie-ervaring. Als u Azure AD-logboeken wilt verbinden, kunt u de [Azure Sentinel Azure AD-connector](../../sentinel/connect-azure-active-directory.md) gebruiken of de [Diagnostische Azure AD-instellingen configureren](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Dit biedt uitgebreide logboek gegevens dan Office 365-beheer Logboeken. 
>
> Wanneer u de [Azure-Sentinel onboardt](../../sentinel/quickstart-onboard.md), geeft u de log Analytics-werk ruimte op waarop u de Office 365-oplossing wilt installeren. Wanneer u de connector inschakelt, is de oplossing beschikbaar in de werk ruimte en gebruikt deze precies hetzelfde als andere bewakings oplossingen die u hebt geïnstalleerd.
>
> Gebruikers van de Azure Government-Cloud moeten Office 365 installeren met behulp van de stappen in dit artikel, omdat Azure Sentinel nog niet beschikbaar is in de Government Cloud.

Met de Office 365-beheer oplossing kunt u uw Office 365-omgeving in Azure Monitor bewaken.

- Bewaak gebruikers activiteiten in uw Office 365-accounts om gebruiks patronen te analyseren en gedrags trends te identificeren. U kunt bijvoorbeeld specifieke gebruiks scenario's extra heren, zoals bestanden die worden gedeeld buiten uw organisatie of de populairste share point-sites.
- Beheer activiteiten controleren om configuratie wijzigingen of bewerkingen met hoge bevoegdheden bij te houden.
- Detecteer en onderzoek ongewenste gebruikers gedrag dat kan worden aangepast aan de behoeften van uw organisatie.
- Demonstratie van controle en naleving. U kunt bijvoorbeeld bestands toegangs bewerkingen bewaken op vertrouwelijke bestanden, die u kunnen helpen bij het controle-en nalevings proces.
- Voer operationele problemen op te lossen met behulp van [logboek query's](../log-query/log-query-overview.md) op Office 365-activiteit gegevens van uw organisatie.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Het volgende is vereist voordat deze oplossing wordt geïnstalleerd en geconfigureerd.

- Organisatie Office 365-abonnement.
- Referenties voor een gebruikers account dat een globale beheerder is.
- Als u controle gegevens wilt ontvangen, moet u de [controle configureren](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) in uw Office 365-abonnement.  Houd er rekening mee dat [Postvak controle](https://technet.microsoft.com/library/dn879651.aspx) afzonderlijk is geconfigureerd.  Als controle niet is geconfigureerd, kunt u nog steeds de oplossing installeren en andere gegevens verzamelen.
 

## <a name="management-packs"></a>Management packs

Met deze oplossing worden geen Management Packs in [verbonden beheer groepen](../platform/om-agents.md)geïnstalleerd.
  

## <a name="install-and-configure"></a>Installeren en configureren

Begin door de [Office 365-oplossing toe te voegen aan uw abonnement](solutions.md#install-a-monitoring-solution). Zodra de app is toegevoegd, moet u de configuratie stappen in deze sectie uitvoeren om deze toegang te geven tot uw Office 365-abonnement.

### <a name="required-information"></a>Vereiste informatie

Voordat u met deze procedure begint, moet u de volgende informatie verzamelen.

Vanuit uw Log Analytics-werk ruimte:

- Werkruimte naam: de werk ruimte waar de Office 365-gegevens worden verzameld.
- Naam van resource groep: de resource groep die de werk ruimte bevat.
- Azure-abonnements-ID: het abonnement met de werk ruimte.

Van uw Office 365-abonnement:

- Gebruikers naam: e-mail adres van een Administrator-account.
- Tenant-ID: unieke ID voor Office 365-abonnement.

De volgende informatie moet worden verzameld tijdens het maken en configureren van Office 365-toepassing in Azure Active Directory:

- Toepassings-ID (client): 16 tekens teken reeks die de Office 365-client vertegenwoordigt.
- Client geheim: versleutelde teken reeks die nodig is voor authenticatie.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Een Office 365-toepassing maken in Azure Active Directory

De eerste stap is het maken van een toepassing in Azure Active Directory dat de beheer oplossing zal gebruiken om toegang te krijgen tot uw Office 365-oplossing.

1. Meld u aan bij de Azure-portal op [https://portal.azure.com](https://portal.azure.com/).
1. Selecteer **Azure Active Directory** en **app-registraties**.
1. Klik op **nieuwe registratie**.

    ![App-registratie toevoegen](media/solution-office-365/add-app-registration.png)
1. Voer een toepassings **naam**in. Selecteer **accounts in elke organisatie Directory (een Azure AD-Directory-multi tenant)** voor de **ondersteunde account typen**.
    
    ![Toepassing maken](media/solution-office-365/create-application.png)
1. Klik op **registreren** en valideer de toepassings gegevens.

    ![Geregistreerde app](media/solution-office-365/registered-app.png)

1. Sla de toepassings-ID op in samen met de rest van de gegevens die eerder zijn verzameld.


### <a name="configure-application-for-office-365"></a>Toepassing voor Office 365 configureren

1. Selecteer **verificatie** en controleer of **accounts in elke organisatie Directory (een Azure AD-adres lijst-multi tenant)** zijn geselecteerd onder **ondersteunde account typen**.

    ![Instellingen multi tenant](media/solution-office-365/settings-multitenant.png)

1. Selecteer **API-machtigingen** en **Voeg vervolgens een machtiging toe**.
1. Klik op **Office 365-beheer-api's**. 

    ![API selecteren](media/solution-office-365/select-api.png)

1. Onder **welk type machtigingen vereist uw toepassing?** Selecteer de volgende opties voor zowel **toepassings machtigingen** als **gedelegeerde machtigingen**:
   - Informatie over de service status voor uw organisatie lezen
   - Gegevens van de activiteit voor uw organisatie lezen
   - Activiteiten rapporten voor uw organisatie lezen

     ![API selecteren](media/solution-office-365/select-permissions-01.png)![API selecteren](media/solution-office-365/select-permissions-02.png)

1. Klik op **machtigingen toevoegen**.
1. Klik op **toestemming beheerder verlenen** en klik vervolgens op **Ja** wanneer u wordt gevraagd om te verifiëren.


### <a name="add-a-secret-for-the-application"></a>Een geheim voor de toepassing toevoegen

1. Selecteer **certificaten & geheimen** en vervolgens **Nieuw client geheim**.

    ![Sleutels](media/solution-office-365/secret.png)
 
1. Typ een **Beschrijving** en **duur** voor de nieuwe sleutel.
1. Klik op **toevoegen** en sla de **waarde** die is gegenereerd als het client geheim samen met de rest van de gegevens die eerder zijn verzameld.

    ![Sleutels](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Toestemming van de beheerder toevoegen

Als u het beheerders account voor de eerste keer wilt inschakelen, moet u een administratieve toestemming voor de toepassing opgeven. U kunt dit doen met een Power shell-script. 

1. Sla het volgende script op als *office365_consent. ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us'; break}
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. Voer het script uit met de volgende opdracht. U wordt twee keer om referenties gevraagd. Geef eerst de referenties voor uw Log Analytics werk ruimte op en vervolgens de globale beheerders referenties voor uw Office 365-Tenant.

    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```

    Voorbeeld:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Er wordt een venster weer gegeven dat vergelijkbaar is met de volgende. Klik op **accepteren**.
    
    ![toestemming van de beheerder](media/solution-office-365/admin-consent.png)

> [!NOTE]
> U wordt mogelijk omgeleid naar een pagina die niet bestaat. Beschouw het als een succes.

### <a name="subscribe-to-log-analytics-workspace"></a>Abonneren op Log Analytics werk ruimte

De laatste stap is het abonneren van de toepassing op uw Log Analytics-werk ruimte. U kunt dit ook doen met een Power shell-script.

De laatste stap is het abonneren van de toepassing op uw Log Analytics-werk ruimte. U kunt dit ook doen met een Power shell-script.

1. Sla het volgende script op als *office365_subscription. ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }

    Function RESTAPI-Auth { 
    $global:SubscriptionID = $Subscription.Subscription.Id
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()

    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.SharePoint'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. Voer het script uit met de volgende opdracht:

    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```

    Voorbeeld:

    ```powershell
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>Problemen oplossen

Mogelijk wordt de volgende fout weer geven als uw toepassing al is geabonneerd op deze werk ruimte of als deze Tenant is geabonneerd op een andere werk ruimte.

```Output
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Mogelijk ziet u de volgende fout als er ongeldige parameter waarden worden gegeven.

```Output
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>Verwijderen

U kunt de Office 365-beheer oplossing verwijderen met behulp van het proces in [een beheer oplossing verwijderen](solutions.md#remove-a-monitoring-solution). Hierdoor worden er geen gegevens meer verzameld van Office 365 in Azure Monitor. Volg de onderstaande procedure om u af te melden bij Office 365 en stop het verzamelen van gegevens.

1. Sla het volgende script op als *office365_unsubscribe. ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Voer het script uit met de volgende opdracht:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Voorbeeld:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Gegevensverzameling

### <a name="supported-agents"></a>Ondersteunde agents

De Office 365-oplossing haalt geen gegevens op van de [log Analytics agents](../platform/agent-data-sources.md).  De gegevens worden rechtstreeks opgehaald uit Office 365.

### <a name="collection-frequency"></a>Verzamelingsfrequentie

Het kan enkele uren duren voordat gegevens worden verzameld. Zodra het verzamelen begint, verzendt Office 365 een [webhook-melding](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) met gedetailleerde gegevens om Azure monitor telkens wanneer een record wordt gemaakt. Deze record is binnen een paar minuten na ontvangst beschikbaar in Azure Monitor.

## <a name="using-the-solution"></a>De oplossing gebruiken

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Wanneer u de Office 365-oplossing aan uw Log Analytics-werk ruimte toevoegt, wordt de tegel **office 365** toegevoegd aan uw dash board. Deze tegel toont het aantal computers in uw omgeving, evenals een grafische voorstelling hiervan, en de bijbehorende updatenaleving.<br><br>
![Office 365-overzichts tegel](media/solution-office-365/tile.png)  

Klik op de tegel **office 365** om het **Office 365** -dash board te openen.

![Office 365-dash board](media/solution-office-365/dashboard.png)  

Het dashboard bevat de kolommen in de volgende tabel. Elke kolom bevat de bovenste tien waarschuwingen per aantal dat overeenkomt met de criteria van die kolom voor het opgegeven bereik en tijds bereik. U kunt een zoek opdracht in het logboek uitvoeren met de volledige lijst door te klikken op alles weer geven onder aan de kolom of door op de kolomkop te klikken.

| Kolom | Beschrijving |
|:--|:--|
| Operations | Bevat informatie over de actieve gebruikers uit uw alle bewaakte Office 365-abonnementen. U kunt ook het aantal activiteiten zien dat na verloop van tijd plaatsvindt.
| Uitwisselen | Toont de uitsplitsing van Exchange Server-activiteiten, zoals de machtiging toevoegen-postvak of set-mailbox. |
| SharePoint | Toont de belangrijkste activiteiten die gebruikers uitvoeren op share point-documenten. Wanneer u inzoomt op deze tegel, worden in de zoek pagina de details van deze activiteiten weer gegeven, zoals het doel document en de locatie van deze activiteit. Voor een gebeurtenis die toegang heeft tot een bestand kunt u bijvoorbeeld het document zien dat wordt geopend, de bijbehorende account naam en het IP-adres. |
| Azure Active Directory | Bevat de belangrijkste gebruikers activiteiten, zoals het opnieuw instellen van gebruikers wachtwoorden en aanmeldings pogingen. Wanneer u inzoomt, kunt u de details van deze activiteiten zien, zoals de status van het resultaat. Dit is vooral nuttig als u verdachte activiteiten op uw Azure Active Directory wilt bewaken. |




## <a name="azure-monitor-log-records"></a>Azure Monitor logboek records

Alle records die zijn gemaakt in de Log Analytics-werk ruimte in Azure Monitor door de Office 365-oplossing, hebben een **type** **OfficeActivity**.  De eigenschap **OfficeWorkload** bepaalt welke Office 365-service de record verwijst naar-Exchange, AzureActiveDirectory, share point of OneDrive.  De eigenschap **record type** geeft het type bewerking aan.  De eigenschappen variëren per bewerkings type en worden weer gegeven in de onderstaande tabellen.

### <a name="common-properties"></a>Algemene eigenschappen

De volgende eigenschappen zijn gebruikelijk voor alle Office 365-records.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Type | *OfficeActivity* |
| Client | Het IP-adres van het apparaat dat is gebruikt tijdens het vastleggen van de activiteit. Het IP-adres wordt weer gegeven in de notatie van een IPv4-of IPv6-adres. |
| OfficeWorkload | Office 365-service waarnaar de record verwijst.<br><br>AzureActiveDirectory<br>Uitwisselen<br>SharePoint|
| Bewerking | De naam van de activiteit van de gebruiker of beheerder.  |
| OrganizationId | De GUID voor de Office 365-Tenant van uw organisatie. Deze waarde is altijd hetzelfde voor uw organisatie, ongeacht de Office 365-service waarin deze zich voordoet. |
| Record type | Het type bewerking dat is uitgevoerd. |
| ResultStatus | Hiermee wordt aangegeven of de actie (opgegeven in de eigenschap Operation) is geslaagd of niet. Mogelijke waarden zijn geslaagd, PartiallySucceeded of mislukt. Voor Exchange-beheer activiteiten is de waarde True of false. |
| Naam | De UPN (User Principal Name) van de gebruiker die de actie heeft uitgevoerd die heeft geresulteerd in het registreren van de record. bijvoorbeeld my_name@my_domain_name. Houd er rekening mee dat records voor activiteiten die worden uitgevoerd door systeem accounts (zoals SHAREPOINT\system of NTAUTHORITY\SYSTEM) ook worden opgenomen. | 
| UserKey | Een alternatieve ID voor de gebruiker die in de eigenschap UserId is geïdentificeerd.  Deze eigenschap wordt bijvoorbeeld gevuld met de unieke Pass Port-ID (PUID) voor gebeurtenissen die worden uitgevoerd door gebruikers in share point, OneDrive voor bedrijven en Exchange. Deze eigenschap kan ook dezelfde waarde opgeven als de eigenschap UserID voor gebeurtenissen die optreden in andere services en gebeurtenissen die worden uitgevoerd door systeem accounts|
| User type | Het type gebruiker dat de bewerking heeft uitgevoerd.<br><br>Beheerder<br>Toepassing<br>DcAdmin<br>Standaard<br>Gereserveerd<br>ServicePrincipal<br>Systeem |


### <a name="azure-active-directory-base"></a>Azure Active Directory basis

De volgende eigenschappen zijn gebruikelijk voor alle Azure Active Directory records.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| Record type     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Het type Azure AD-gebeurtenis. |
| extendedProperties | De uitgebreide eigenschappen van de Azure AD-gebeurtenis. |


### <a name="azure-active-directory-account-logon"></a>Aanmelding bij Azure Active Directory-account

Deze records worden gemaakt wanneer een Active Directorye gebruiker zich probeert aan te melden.

| Eigenschap | Beschrijving |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | De toepassing die de aanmeldings gebeurtenis van het account activeert, zoals Office 15. |
| `Client` | Details over het client apparaat, het besturings systeem van het apparaat en de browser van het apparaat dat is gebruikt voor de aanmeldings gebeurtenis van het account. |
| `LoginStatus` | Deze eigenschap is rechtstreeks van OrgIdLogon. LoginStatus. De toewijzing van verschillende interessante aanmeldings fouten kan worden uitgevoerd door middel van waarschuwings algoritmen. |
| `UserDomain` | De gegevens van de Tenant-id (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Deze records worden gemaakt wanneer er wijzigingen of toevoegingen worden aangebracht aan Azure Active Directory-objecten.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| Record type     | AzureActiveDirectory |
| AADTarget | De gebruiker die de actie (geïdentificeerd door de eigenschap Operation) is uitgevoerd. |
| Actor | De gebruiker of service-principal die de actie heeft uitgevoerd. |
| ActorContextId | De GUID van de organisatie waartoe de actor behoort. |
| ActorIpAddress | Het IP-adres van de actor in IPV4-of IPV6-adres indeling. |
| InterSystemsId | De GUID waarmee de acties voor verschillende onderdelen in de Office 365-service worden bijgehouden. |
| IntraSystemId |   De GUID die wordt gegenereerd door Azure Active Directory om de actie bij te houden. |
| SupportTicketId | De ID van het klant ondersteunings ticket voor de actie in ' namen van ' Act-of-scenario's. |
| TargetContextId | De GUID van de organisatie waartoe de doel gebruiker behoort. |


### <a name="data-center-security"></a>Beveiliging van data centers

Deze records worden gemaakt op basis van de beveiligings controle gegevens van data centers.  

| Eigenschap | Beschrijving |
|:--- |:--- |
| EffectiveOrganization | De naam van de Tenant waarop de uitbrei ding/cmdlet is gericht. |
| ElevationApprovedTime | De tijds tempel voor het moment waarop de verhoging werd goedgekeurd. |
| ElevationApprover | De naam van een micro soft-Manager. |
| ElevationDuration | De duur waarvoor de verhoging actief was. |
| ElevationRequestId |  Een unieke id voor de aanvraag voor benodigde bevoegdheden. |
| ElevationRole | De rol waarvoor de uitbrei ding is aangevraagd. |
| ElevationTime | De begin tijd van de verhoging. |
| Start_Time | De begin tijd van de uitvoering van de cmdlet. |


### <a name="exchange-admin"></a>Exchange-beheerder

Deze records worden gemaakt wanneer er wijzigingen worden aangebracht in de Exchange-configuratie.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | Uitwisselen |
| Record type     | ExchangeAdmin |
| ExternalAccess |  Hiermee geeft u op of de cmdlet is uitgevoerd door een gebruiker in uw organisatie, door het micro soft data center-personeel of een datacenter service-account of door een gedelegeerd beheerder. De waarde False geeft aan dat de cmdlet is uitgevoerd door iemand in uw organisatie. De waarde True geeft aan dat de cmdlet is uitgevoerd door een datacenter medewerker, een Data Center-service account of een gedelegeerd beheerder. |
| ModifiedObjectResolvedName |  Dit is de beschrijvende naam van de gebruiker van het object dat door de cmdlet is gewijzigd. Dit wordt alleen vastgelegd als de cmdlet het object wijzigt. |
| OrganizationName | De naam van de Tenant. |
| OriginatingServer | De naam van de server van waaruit de cmdlet is uitgevoerd. |
| Parameters | De naam en waarde voor alle para meters die zijn gebruikt met de cmdlet die wordt geïdentificeerd in de eigenschap Operations. |


### <a name="exchange-mailbox"></a>Exchange-postvak

Deze records worden gemaakt wanneer wijzigingen of toevoegingen worden doorgevoerd in Exchange-post vakken.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | Uitwisselen |
| Record type     | ExchangeItem |
| ClientInfoString | Informatie over de e-mailclient die is gebruikt om de bewerking uit te voeren, zoals een browser versie, Outlook-versie en informatie over mobiele apparaten. |
| Client_IPAddress | Het IP-adres van het apparaat dat is gebruikt tijdens het registreren van de bewerking. Het IP-adres wordt weer gegeven in de notatie van een IPv4-of IPv6-adres. |
| ClientMachineName | De naam van de computer die als host fungeert voor de Outlook-client. |
| ClientProcessName | De e-mailclient die is gebruikt voor toegang tot het postvak. |
| ClientVersion | De versie van de e-mailclient. |
| InternalLogonType | Gereserveerd voor intern gebruik. |
| Logon_Type | Hiermee wordt het type gebruiker aangegeven dat toegang heeft gekregen tot het postvak en de geregistreerde bewerking heeft uitgevoerd. |
| LogonUserDisplayName |    De beschrijvende naam van de gebruiker die de bewerking heeft uitgevoerd. |
| LogonUserSid | De SID van de gebruiker die de bewerking heeft uitgevoerd. |
| MailboxGuid | De Exchange-GUID van het postvak waartoe toegang is verkregen. |
| MailboxOwnerMasterAccountSid | De hoofd account-SID van het postvak eigenaar-account. |
| MailboxOwnerSid | De SID van de postbus eigenaar. |
| MailboxOwnerUPN | Het e-mail adres van de persoon die eigenaar is van het postvak dat is geopend. |


### <a name="exchange-mailbox-audit"></a>Exchange-postvak controle

Deze records worden gemaakt wanneer een postvak controle vermelding wordt gemaakt.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | Uitwisselen |
| Record type     | ExchangeItem |
| Item | Hiermee wordt het item aangeduid waarop de bewerking is uitgevoerd | 
| SendAsUserMailboxGuid | De Exchange-GUID van het postvak dat is gebruikt voor het verzenden van e-mail. |
| SendAsUserSmtp | Het SMTP-adres van de gebruiker die wordt geïmiteerd. |
| SendonBehalfOfUserMailboxGuid | De Exchange-GUID van het postvak dat is gebruikt voor het verzenden van e-mail namens. |
| SendOnBehalfOfUserSmtp | Het SMTP-adres van de gebruiker aan wie het e-mail bericht wordt verzonden. |


### <a name="exchange-mailbox-audit-group"></a>Audit groep voor Exchange-postvak

Deze records worden gemaakt wanneer wijzigingen of toevoegingen worden aangebracht aan Exchange-groepen.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | Uitwisselen |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informatie over elk item in de groep. |
| CrossMailboxOperations | Geeft aan of de bewerking meer dan een postvak heeft betrokken. |
| DestMailboxId | Stel dit in als de para meter CrossMailboxOperations is ingesteld op True. Hiermee geeft u de GUID van het doel postvak op. |
| DestMailboxOwnerMasterAccountSid | Stel dit in als de para meter CrossMailboxOperations is ingesteld op True. Hiermee geeft u de SID voor de SID van het hoofd account van de eigenaar van het doel postvak. |
| DestMailboxOwnerSid | Stel dit in als de para meter CrossMailboxOperations is ingesteld op True. Hiermee geeft u de SID van het doel postvak. |
| DestMailboxOwnerUPN | Stel dit in als de para meter CrossMailboxOperations is ingesteld op True. Hiermee geeft u de UPN van de eigenaar van het doel postvak. |
| DestFolder | De doelmap, voor bewerkingen zoals verplaatsen. |
| Map | De map waarin een groep items zich bevindt. |
| Mappen |     Informatie over de bron mappen die zijn betrokken bij een bewerking; Als bijvoorbeeld mappen zijn geselecteerd en vervolgens worden verwijderd. |


### <a name="sharepoint-base"></a>Share point-basis

Deze eigenschappen zijn gebruikelijk voor alle share point-records.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| Source | Hiermee wordt aangegeven dat er een gebeurtenis is opgetreden in share point. Mogelijke waarden zijn share point of ObjectModel. |
| Item type | Het type object dat is geopend of gewijzigd. Zie de tabel item type voor meer informatie over de typen objecten. |
| MachineDomainInfo | Informatie over synchronisatie bewerkingen voor apparaten. Deze informatie wordt alleen gerapporteerd als deze aanwezig is in de aanvraag. |
| MachineId |   Informatie over synchronisatie bewerkingen voor apparaten. Deze informatie wordt alleen gerapporteerd als deze aanwezig is in de aanvraag. |
| Site | De GUID van de site waar het bestand of de map die door de gebruiker wordt gebruikt, zich bevindt. |
| Source_Name | De entiteit die de gecontroleerde bewerking heeft geactiveerd. Mogelijke waarden zijn share point of ObjectModel. |
| User agent | Informatie over de client of browser van de gebruiker. Deze informatie wordt verstrekt door de client of browser. |


### <a name="sharepoint-schema"></a>Share point-schema

Deze records worden gemaakt wanneer er configuratie wijzigingen worden aangebracht in share point.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Optionele teken reeks voor aangepaste gebeurtenissen. |
| Event_Data |  Optionele Payload voor aangepaste gebeurtenissen. |
| ModifiedProperties | De eigenschap is opgenomen voor beheer gebeurtenissen, zoals het toevoegen van een gebruiker als lid van een-site of een beheer groep voor een site verzameling. De eigenschap bevat de naam van de eigenschap die is gewijzigd (bijvoorbeeld de groep site beheerder), de nieuwe waarde van de gewijzigde eigenschap (bijvoorbeeld de gebruiker die is toegevoegd als een site beheerder) en de vorige waarde van het gewijzigde object. |


### <a name="sharepoint-file-operations"></a>Share point-Bestands bewerkingen

Deze records worden gemaakt als reactie op Bestands bewerkingen in share point.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | De bestands extensie van een bestand dat wordt gekopieerd of verplaatst. Deze eigenschap wordt alleen weer gegeven voor FileCopied-en FileMoved-gebeurtenissen. |
| DestinationFileName | De naam van het bestand dat wordt gekopieerd of verplaatst. Deze eigenschap wordt alleen weer gegeven voor FileCopied-en FileMoved-gebeurtenissen. |
| DestinationRelativeUrl | De URL van de doelmap waarnaar een bestand wordt gekopieerd of verplaatst. De combi natie van de waarden voor SiteURL-, DestinationRelativeURL-en DestinationFileName-para meters is hetzelfde als de waarde voor de eigenschap ObjectID, de volledige padnaam voor het bestand dat is gekopieerd. Deze eigenschap wordt alleen weer gegeven voor FileCopied-en FileMoved-gebeurtenissen. |
| SharingType | Het type machtigingen voor delen dat is toegewezen aan de gebruiker waarmee de resource is gedeeld. Deze gebruiker wordt aangeduid met de para meter UserSharedWith. |
| Site_Url | De URL van de site waar het bestand of de map die door de gebruiker wordt geopend, zich bevindt. |
| SourceFileExtension | De bestands extensie van het bestand dat door de gebruiker is geopend. Deze eigenschap is leeg als het object waartoe toegang is verkregen, een map is. |
| SourceFileName |  De naam van het bestand of de map die door de gebruiker wordt gebruikt. |
| SourceRelativeUrl | De URL van de map met het bestand dat door de gebruiker wordt gebruikt. De combi natie van de waarden voor de para meter SiteURL, SourceRelativeURL en SourceFileName is hetzelfde als de waarde voor de eigenschap ObjectID, de volledige padnaam voor het bestand dat door de gebruiker wordt gebruikt. |
| UserSharedWith |  De gebruiker waarmee een resource is gedeeld. |




## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken

De volgende tabel biedt voorbeeldzoekopdrachten in logboeken voor updaterecords die worden verzameld door deze oplossing.

| Query | Beschrijving |
| --- | --- |
|Aantal bewerkingen voor uw Office 365-abonnement |Aantal &#124; samen vattingen van OfficeActivity () per bewerking |
|Gebruik van share point-sites|OfficeActivity &#124; waarbij OfficeWorkload = ~ "share point &#124; -samenvattings aantal () door siteurl \| sorteren op aantal ASC|
|Bestands toegangs bewerkingen op gebruikers type|zoeken in (OfficeActivity) OfficeWorkload = ~ "azureactivedirectory" en "MyTest"|
|Zoeken met een specifiek tref woord|Type = OfficeActivity OfficeWorkload = azureactivedirectory "MyTest"|
|Externe acties op Exchange controleren|OfficeActivity &#124; waarbij OfficeWorkload = ~ "Exchange" en ExternalAccess = = True|



## <a name="next-steps"></a>Volgende stappen

* Gebruik [logboek query's in azure monitor](../log-query/log-query-overview.md) om gedetailleerde update gegevens weer te geven.
* [Maak uw eigen Dash boards](../learn/tutorial-logs-dashboards.md) om uw favoriete Office 365-Zoek query's weer te geven.
* [Maak waarschuwingen](../platform/alerts-overview.md) om proactief te worden geïnformeerd over belang rijke Office 365-activiteiten.  
