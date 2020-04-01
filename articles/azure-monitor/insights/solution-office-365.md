---
title: Office 365-beheeroplossing in Azure | Microsoft Documenten
description: In dit artikel vindt u informatie over de configuratie en het gebruik van de Office 365-oplossing in Azure.  Het bevat een gedetailleerde beschrijving van de Office 365-records die zijn gemaakt in Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: 5aa025fb366634e796abfb2eb9c0035d9b87dc3c
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437050"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Office 365-beheeroplossing in Azure (voorbeeld)

![Office 365-logo](media/solution-office-365/icon.png)


> [!IMPORTANT]
> ## <a name="solution-update"></a>Oplossingsupdate
> Deze oplossing is vervangen door de [office 365-oplossing](../../sentinel/connect-office-365.md) voor algemene beschikbaarheid in [Azure Sentinel](../../sentinel/overview.md) en de [Azure AD-rapportage- en bewakingsoplossing.](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) Samen bieden ze een bijgewerkte versie van de vorige Azure Monitor Office 365-oplossing met een verbeterde configuratie-ervaring. U de bestaande oplossing tot 30 juli 2020 blijven gebruiken.
> 
> Azure Sentinel is een cloud native Security Information and Event Management-oplossing die logboeken inneemt en extra SIEM-functionaliteit biedt, waaronder detecties, onderzoeken, jacht- en machine learning-gestuurde inzichten. Als u Azure Sentinel gebruikt, u nu office 365-activiteits- en Exchange-beheerlogboeken opnemen.
> 
> Azure AD-rapportage biedt een uitgebreidere weergave van logboeken van Azure AD-activiteit in uw omgeving, inclusief aanmeldingsgebeurtenissen, controlegebeurtenissen en wijzigingen in uw map. Als u Azure AD-logboeken wilt verbinden, u de [Azure Sentinel Azure AD-connector](../../sentinel/connect-azure-active-directory.md) gebruiken of [Azure AD-logboeken-integratie](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)configureren met Azure Monitor. 
>
> De verzameling Azure AD-logboek is onderworpen aan azure monitor-prijzen.  Zie [Azure Monitor-prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor meer informatie.
>
> Ga als beste de Azure Sentinel Office 365-oplossing gebruiken:
> 1. Het gebruik van de Office 365-connector in Azure Sentinel is van invloed op de prijzen voor uw werkruimte. Zie [Azure Sentinel-prijzen](https://azure.microsoft.com/pricing/details/azure-sentinel/)voor meer informatie.
> 2. Als u de Azure Monitor Office 365-oplossing al gebruikt, moet u deze eerst verwijderen met behulp van het script in het [gedeelte Verwijderen hieronder.](#uninstall)
> 3. [Schakel de Azure Sentinel-oplossing](../../sentinel/quickstart-onboard.md) in op uw werkruimte.
> 4. Ga naar de pagina **Gegevensconnectors** in Azure Sentinel en schakel de **Office 365-connector** in.
>
> ## <a name="frequently-asked-questions"></a>Veelgestelde vragen
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-april-30th"></a>V: Is het mogelijk om de Office 365 Azure Monitor-oplossing tussen nu en 30 april aan boord te nemen?
> Nee, de onboardingscripts van de Azure Monitor Office 365-oplossing zijn niet meer beschikbaar. De oplossing wordt op 30 april verwijderd.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>V: Worden de tabellen en schema's gewijzigd?
> De naam en het schema van de **OfficeActivity-tabel** blijven hetzelfde als in de huidige oplossing. U dezelfde query's blijven gebruiken in de nieuwe oplossing, met uitzondering van query's die verwijzen naar Azure AD-gegevens.
> 
> De nieuwe [Azure AD-rapportage- en bewakingsoplossingslogboeken](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) worden opgenomen in de tabellen [SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) en [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) in plaats van **OfficeActivity**. Zie voor meer informatie [hoe u Azure AD-logboeken analyseert,](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md)wat ook relevant is voor Azure Sentinel- en Azure Monitor-gebruikers.
> 
> Hieronder volgen voorbeelden voor het converteren van query's van **OfficeActivity** naar **SigninLogs:**
> 
> **Aanmeldingen voor query's zijn mislukt voor de gebruiker:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId    
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Azure AD-bewerkingen weergeven:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>V: Hoe kan ik Azure Sentinel aan boord hebben?
> Azure Sentinel is een oplossing die u inschakelen op nieuwe of bestaande Log Analytics-werkruimte. Zie [Azure Sentinel on-boarding documentatie](../../sentinel/quickstart-onboard.md)voor meer informatie.
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>V: Heb ik Azure Sentinel nodig om de Azure AD-logboeken te verbinden?
> U [Azure AD-logboekenintegratie](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)configureren met Azure Monitor, die niet gerelateerd is aan de Azure Sentinel-oplossing. Azure Sentinel biedt een native connector en out-of-the-box-inhoud voor Azure AD-logboeken. Zie voor meer informatie onderstaande vraag over kant-en-klare beveiligingsgerichte inhoud.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>V: Wat zijn de verschillen bij het verbinden van Azure AD-logboeken vanuit Azure Sentinel en Azure Monitor?
> Azure Sentinel en Azure Monitor maken verbinding met Azure AD-logboeken op basis van dezelfde [Azure AD-rapportage- en bewakingsoplossing.](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) Azure Sentinel biedt een native connector met één klik die dezelfde gegevens verbindt en bewakingsinformatie biedt.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>V: Wat moet ik wijzigen wanneer ik overstap naar de nieuwe Azure AD-rapportage- en bewakingstabellen?
> Alle query's met Azure AD-gegevens, inclusief query's in waarschuwingen, dashboards en alle inhoud die u hebt gemaakt met Office 365 Azure AD-gegevens, moeten opnieuw worden gemaakt met behulp van de nieuwe tabellen.
>
> Azure Sentinel en Azure AD bieden ingebouwde inhoud die u gebruiken bij de overstap naar de Azure AD-rapportage- en bewakingsoplossing. Zie de volgende vraag over kant-en-klare beveiligingsinhoud en Het gebruik van [Azure Monitor-werkmappen voor Azure Active Directory-rapporten voor](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md)meer informatie. 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>V: Hoe kan ik de out-of-the-box beveiligingsinhoud van Azure Sentinel gebruiken?
> Azure Sentinel biedt kant-en-klare beveiligingsgeoriënteerde dashboards, aangepaste waarschuwingsquery's, jachtquery's, onderzoek en geautomatiseerde reactiemogelijkheden op basis van de AD-logboeken van Office 365 en Azure. Bekijk de Azure Sentinel GitHub en tutorials voor meer informatie:
>
> - [Detecteer out-of-the-box bedreigingen](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Aangepaste analyseregels maken om verdachte bedreigingen te detecteren](../../sentinel/tutorial-detect-threats-custom.md)
> - [Uw gegevens bewaken](../../sentinel/tutorial-monitor-your-data.md)
> - [Incidenten met Azure Sentinel onderzoeken](../../sentinel/tutorial-investigate-cases.md)
> - [Geautomatiseerde bedreigingsreacties instellen in Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Azure Sentinel GitHub-community](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>V: Biedt Azure Sentinel extra connectors als onderdeel van de oplossing?
> Ja, zie [Azure Sentinel connect databronnen](../../sentinel/connect-data-sources.md).
> 
> ###    <a name="q-what-will-happen-on-april-30-do-i-need-to-offboard-beforehand"></a>V: Wat gebeurt er op 30 april? Moet ik van tevoren offboarden?
> 
> - U geen gegevens ontvangen van de **Office365-oplossing.** De oplossing zal niet langer beschikbaar zijn in de Marketplace
> - Voor Azure Sentinel-klanten wordt de Log Analytics-werkruimteoplossing **Office365** opgenomen in de Azure Sentinel **SecurityInsights-oplossing.**
> - Als u uw oplossing niet handmatig buiten boord houdt, worden uw gegevens op 30 april automatisch verbroken.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>V: Worden mijn gegevens overgezet naar de nieuwe oplossing?
> Ja. Wanneer u de **Office 365-oplossing** uit uw werkruimte verwijdert, worden de gegevens tijdelijk niet meer beschikbaar omdat het schema wordt verwijderd. Wanneer u de nieuwe **Office 365-connector** inSchakelt in Sentinel, wordt het schema hersteld naar de werkruimte en worden alle gegevens die al zijn verzameld, beschikbaar. 
 

Met de Office 365-beheeroplossing u uw Office 365-omgeving controleren in Azure Monitor.

- Houd gebruikersactiviteiten op uw Office 365-accounts in de gaten om gebruikspatronen te analyseren en gedragstrends te identificeren. U bijvoorbeeld specifieke gebruiksscenario's extraheren, zoals bestanden die buiten uw organisatie worden gedeeld of de populairste SharePoint-sites.
- Beheerdersactiviteiten bewaken om configuratiewijzigingen of bewerkingen met hoge bevoegdheden bij te houden.
- Detecteer en onderzoek ongewenst gebruikersgedrag, dat kan worden aangepast aan uw organisatorische behoeften.
- Aantonen van audit en compliance. U bijvoorbeeld bestandstoegangsbewerkingen controleren op vertrouwelijke bestanden, wat u kan helpen bij het audit- en nalevingsproces.
- Voer operationele probleemoplossing uit met behulp van [logboekquery's](../log-query/log-query-overview.md) boven de activiteitsgegevens van uw organisatie voor Office 365.


## <a name="uninstall"></a>Verwijderen

U de Office 365-beheeroplossing verwijderen met behulp van het proces in [Een beheeroplossing verwijderen.](solutions.md#remove-a-monitoring-solution) Hierdoor worden gegevens van Office 365 niet meer opgehaald in Azure Monitor. Volg de onderstaande procedure om u af te melden voor Office 365 en stop met het verzamelen van gegevens.

1. Sla het volgende script op als *office365_unsubscribe.ps1*.

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
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
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

U wordt gevraagd om referenties. Geef de referenties op voor uw Log Analytics-werkruimte.

## <a name="data-collection"></a>Gegevensverzameling

Het kan enkele uren duren voordat gegevens in eerste instantie worden verzameld. Zodra het begint te verzamelen, stuurt Office 365 een [webhook-melding](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) met gedetailleerde gegevens naar Azure Monitor telkens wanneer een record wordt gemaakt. Deze record is binnen enkele minuten na ontvangst beschikbaar in Azure Monitor.

## <a name="using-the-solution"></a>De oplossing gebruiken

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Wanneer u de Office 365-oplossing toevoegt aan uw logboekanalysewerkruimte, wordt de **tegel Office 365** aan uw dashboard toegevoegd. Deze tegel toont het aantal computers in uw omgeving, evenals een grafische voorstelling hiervan, en de bijbehorende updatenaleving.<br><br>
![Office 365-overzichtstegel](media/solution-office-365/tile.png)  

Klik op de **tegel Office 365** om het **Office 365-dashboard** te openen.

![Office 365-dashboard](media/solution-office-365/dashboard.png)  

Het dashboard bevat de kolommen in de volgende tabel. Elke kolom bevat de top tien waarschuwingen op telling die overeenkomt met de criteria van die kolom voor het opgegeven bereik en tijdsbereik. U een logboekzoekopdracht uitvoeren die de hele lijst biedt door onder aan de kolom te klikken op Alles weergeven of door op de kolomkop te klikken.

| Kolom | Beschrijving |
|:--|:--|
| Bewerkingen | Hier vindt u informatie over de actieve gebruikers van uw alle bewaakte Office 365-abonnementen. U zult ook in staat zijn om het aantal activiteiten die gebeuren in de tijd te zien.
| Exchange | Hiermee wordt het uitsplitsing van Exchange Server-activiteiten weergegeven, zoals machtiging voor postvak toevoegen of Set-Mailbox. |
| SharePoint | Toont de belangrijkste activiteiten die gebruikers uitvoeren in SharePoint-documenten. Wanneer u inzoomt op deze tegel, worden op de zoekpagina de details van deze activiteiten weergegeven, zoals het doeldocument en de locatie van deze activiteit. Voor een gebeurtenis Met bestandstoegang u bijvoorbeeld het document zien dat wordt geopend, de bijbehorende accountnaam en IP-adres. |
| Azure Active Directory | Bevat de belangrijkste gebruikersactiviteiten, zoals Gebruikerswachtwoord opnieuw instellen en aanmeldingspogingen. Wanneer u inzoomt, u de details van deze activiteiten zien, zoals de resultaatstatus. Dit is vooral handig als u verdachte activiteiten in uw Azure Active Directory wilt controleren. |




## <a name="azure-monitor-log-records"></a>Logboekrecords voor Azure Monitor

Alle records die zijn gemaakt in de werkruimte Log Analytics in Azure Monitor door de Office 365-oplossing hebben een **type** **OfficeActivity**.  De eigenschap **OfficeWorkload** bepaalt naar welke Office 365-service de record verwijst: Exchange, AzureActiveDirectory, SharePoint of OneDrive.  De eigenschap **RecordType** geeft het type bewerking aan.  De eigenschappen variëren voor elk bewerkingstype en worden weergegeven in de onderstaande tabellen.

### <a name="common-properties"></a>Algemene eigenschappen

De volgende eigenschappen zijn gemeenschappelijk voor alle Office 365-records.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Type | *OfficeActiviteit* |
| ClientIP | Het IP-adres van het apparaat waarmee de activiteit is uitgevoerd. Het IP-adres wordt weergegeven in de IPv4- of IPv6-adresindeling. |
| OfficeWorkload | Office 365-service waarnaar de record verwijst.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Bewerking | De naam van de activiteit door de gebruiker of beheerder.  |
| OrganizationId | De GUID voor de Office 365-tenant van uw organisatie. Deze waarde is altijd hetzelfde voor uw organisatie, ongeacht de Office 365-service waarin deze optreedt. |
| RecordType | Type bewerking uitgevoerd. |
| ResultStatus | Geeft aan of de actie (opgegeven in de eigenschap Operation) al dan niet is gelukt. Mogelijke waarden zijn geslaagd, gedeeltelijk geslaagd of mislukt. Voor Exchange-beheeractiviteit is de waarde Waar of Onwaar. |
| UserId | De UPN (User Principal Name) van de gebruiker die de actie heeft uitgevoerd die ertoe heeft geleid dat de record is geregistreerd; bijvoorbeeld . my_name@my_domain_name Houd er rekening mee dat records voor activiteiten die worden uitgevoerd door systeemaccounts (zoals SHAREPOINT\system of NTAUTHORITY\SYSTEM) ook zijn opgenomen. | 
| UserKey | Een alternatieve id voor de gebruiker die is geïdentificeerd in de eigenschap UserId.  Deze eigenschap wordt bijvoorbeeld gevuld met de unieke id (Passport ID) voor gebeurtenissen die worden uitgevoerd door gebruikers in SharePoint, OneDrive voor Bedrijven en Exchange. Deze eigenschap kan ook dezelfde waarde opgeven als de eigenschap UserID voor gebeurtenissen die zich voordoen in andere services en gebeurtenissen die worden uitgevoerd door systeemaccounts|
| UserType | Het type gebruiker dat de bewerking heeft uitgevoerd.<br><br>Beheerder<br>Toepassing<br>DcAdmin (DcAdmin)<br>Standaard<br>Gereserveerd<br>ServicePrincipal<br>Systeem |


### <a name="azure-active-directory-base"></a>Azure Active Directory-basis

De volgende eigenschappen zijn gemeenschappelijk voor alle Azure Active Directory-records.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Het type Azure AD-gebeurtenis. |
| ExtendedProperties ExtendedProperties | De uitgebreide eigenschappen van de gebeurtenis Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Aanmelding in Azure Active Directory-account

Deze records worden gemaakt wanneer een Active Directory-gebruiker zich probeert aan te melden.

| Eigenschap | Beschrijving |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | De toepassing die de aanmeldingsgebeurtenis voor het account activeert, zoals Office 15. |
| `Client` | Details over het clientapparaat, het apparaat besturingssysteem en de apparaatbrowser die is gebruikt voor de aanmeldingsgebeurtenis van het account. |
| `LoginStatus` | Deze accommodatie is rechtstreeks van OrgIdLogon.LoginStatus. Het in kaart brengen van verschillende interessante aanmeldingsfouten kan worden gedaan door algoritmen te waarschuwen. |
| `UserDomain` | De Tenant Identity Information (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Deze records worden gemaakt wanneer wijzigingen of toevoegingen worden aangebracht aan Azure Active Directory-objecten.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget (AADTarget) | De gebruiker waarop de actie (geïdentificeerd door de eigenschap Operation) is uitgevoerd. |
| Acteur | De gebruiker of serviceprincipal die de actie heeft uitgevoerd. |
| ActorContextId (ActorContextId) | GuiD van de organisatie dat de acteur tot behoort. |
| ActorIpAddress ActorIpAddress | Het IP-adres van de actor in de IPV4- of IPV6-adresindeling. |
| InterSystemsId (InterSystemsId) | De GUID die de acties tussen onderdelen binnen de Office 365-service bijhoudt. |
| IntraSystemId (IntraSystemId) |     De GUID die wordt gegenereerd door Azure Active Directory om de actie bij te houden. |
| SupportTicketId | De customer support ticket ID voor de actie in "act-on-behalf-of" situaties. |
| TargetContextId | De GUID van de organisatie waartoe de beoogde gebruiker behoort. |


### <a name="data-center-security"></a>Beveiliging van datacenters

Deze records zijn gemaakt op uit controlegegevens van Data Center Security.  

| Eigenschap | Beschrijving |
|:--- |:--- |
| EffectiefOrganisatie | De naam van de huurder waarop de hoogte/cmdlet was gericht. |
| ElevationApprovedTime | De tijdstempel voor wanneer de verhoging werd goedgekeurd. |
| ElevationApprover | De naam van een Microsoft-manager. |
| Hoogteduur | De duur waarvoor de verhoging actief was. |
| ElevationRequestId |     Een unieke id voor de hoogteaanvraag. |
| Hoogterol | De rol waarvoor de verhoging werd gevraagd. |
| Hoogtetijd | De begintijd van de verhoging. |
| Start_Time | De begintijd van de cmdlet uitvoering. |


### <a name="exchange-admin"></a>Exchange-beheerder

Deze records worden gemaakt wanneer wijzigingen worden aangebracht in de Exchange-configuratie.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin (ExchangeAdmin) |
| Externetoegang |     Hiermee geeft u op of de cmdlet is uitgevoerd door een gebruiker in uw organisatie, door microsoft-datacenterpersoneel of een datacenterserviceaccount of door een gedelegeerde beheerder. De waarde False geeft aan dat de cmdlet is uitgevoerd door iemand in uw organisatie. De waarde True geeft aan dat de cmdlet is uitgevoerd door datacenterpersoneel, een datacenterserviceaccount of een gedelegeerde beheerder. |
| ModifiedObjectResolvedName |     Dit is de gebruiksvriendelijke naam van het object dat is gewijzigd door de cmdlet. Dit wordt alleen geregistreerd als de cmdlet het object wijzigt. |
| OrganizationName | De naam van de huurder. |
| Oorspronkelijke Server | De naam van de server van waaruit de cmdlet is uitgevoerd. |
| Parameters | De naam en waarde voor alle parameters die zijn gebruikt met de cmdlet die is geïdentificeerd in de eigenschap Operations. |


### <a name="exchange-mailbox"></a>Exchange-postvak

Deze records worden gemaakt wanneer wijzigingen of toevoegingen worden aangebracht in Exchange-postvakken.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Informatie over de e-mailclient die is gebruikt om de bewerking uit te voeren, zoals een browserversie, Outlook-versie en gegevens over mobiele apparaten. |
| Client_IPAddress | Het IP-adres van het apparaat dat werd gebruikt toen de bewerking werd geregistreerd. Het IP-adres wordt weergegeven in de IPv4- of IPv6-adresindeling. |
| ClientMachineName | De machinenaam die de Outlook-client host. |
| ClientProcessName | De e-mailclient die is gebruikt om toegang te krijgen tot het postvak. |
| ClientVersie | De versie van de e-mailclient . |
| InternalLogonType | Gereserveerd voor intern gebruik. |
| Logon_Type | Geeft het type gebruiker aan dat toegang heeft tot het postvak en de bewerking heeft uitgevoerd die is geregistreerd. |
| LogonUserDisplayName |     De gebruiksvriendelijke naam van de gebruiker die de bewerking heeft uitgevoerd. |
| LogonUserSid | De SID van de gebruiker die de bewerking heeft uitgevoerd. |
| MailboxGuid | De Exchange GUID van de postvak die is geopend. |
| BrievenbusEigenaarMasterAccountSid | De masteraccount SID van de postvakeigenaar-account. |
| BrievenbusEigenaarSid | De SID van de brievenbuseigenaar. |
| BrievenbuseigenaarUPN | Het e-mailadres van de persoon die eigenaar is van het postvak dat is geopend. |


### <a name="exchange-mailbox-audit"></a>Exchange-postvakcontrole

Deze records worden gemaakt wanneer een postvakcontrole-item wordt gemaakt.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Item | Geeft het item weer waarop de bewerking is uitgevoerd | 
| SendAsUserMailboxGuid | De Exchange GUID van het postvak dat is geopend om e-mail te verzenden als. |
| SendAsUserSmtp SendAsUserSmtp | SMTP-adres van de gebruiker die wordt nagebootst. |
| SendonBehalfOfUserMailboxGuid | De Exchange GUID van het postvak dat is geopend om e-mail namens te verzenden. |
| SendOnBehalfOfUserSmtp | SMTP-adres van de gebruiker namens wie de e-mail wordt verzonden. |


### <a name="exchange-mailbox-audit-group"></a>Controlegroep postvak exchange

Deze records worden gemaakt wanneer wijzigingen of toevoegingen worden aangebracht in Exchange-groepen.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroep |
| Getroffen items | Informatie over elk item in de groep. |
| CrossMailboxOperations | Geeft aan of de bewerking meer dan één postvak omvatte. |
| DestMailboxId | Stel alleen in als de parameter CrossMailboxOperations True is. Hiermee geeft u de GUID van het doelpostvak op. |
| DestMailboxOwnerMasterAccountsid | Stel alleen in als de parameter CrossMailboxOperations True is. Hiermee geeft u de SID op voor het hoofdaccount SID van de eigenaar van de doelpostvak. |
| DestMailboxOwnerSid | Stel alleen in als de parameter CrossMailboxOperations True is. Hiermee geeft u de SID van het doelpostvak op. |
| DestMailboxOwnerUPN | Stel alleen in als de parameter CrossMailboxOperations True is. Hiermee geeft u de UPN van de eigenaar van het doelpostvak op. |
| DestFolder | De doelmap, voor bewerkingen zoals Verplaatsen. |
| Map | De map waarin een groep items zich bevindt. |
| Mappen |     Informatie over de bronmappen die betrokken zijn bij een bewerking; bijvoorbeeld als mappen zijn geselecteerd en vervolgens worden verwijderd. |


### <a name="sharepoint-base"></a>SharePoint-basis

Deze eigenschappen zijn gemeenschappelijk voor alle SharePoint-records.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Hiermee wordt aangegeven dat er een gebeurtenis heeft plaatsgevonden in SharePoint. Mogelijke waarden zijn SharePoint of ObjectModel. |
| ItemType | Het type object dat is geopend of gewijzigd. Zie de tabel ItemType voor meer informatie over de typen objecten. |
| MachineDomainInfo | Informatie over het synchroniseren van apparaten. Deze informatie wordt alleen gerapporteerd als deze aanwezig is in het verzoek. |
| MachineId (MachineId) |     Informatie over het synchroniseren van apparaten. Deze informatie wordt alleen gerapporteerd als deze aanwezig is in het verzoek. |
| Site_ | De GUID van de site waar het bestand of de map is gevestigd die door de gebruiker is geopend. |
| Source_Name | De entiteit die de gecontroleerde bewerking heeft geactiveerd. Mogelijke waarden zijn SharePoint of ObjectModel. |
| Useragent | Informatie over de client of browser van de gebruiker. Deze informatie wordt verstrekt door de client of browser. |


### <a name="sharepoint-schema"></a>SharePoint-schema

Deze records worden gemaakt wanneer configuratiewijzigingen worden aangebracht in SharePoint.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| Aangepastegebeurtenis | Optionele tekenreeks voor aangepaste gebeurtenissen. |
| Event_Data |     Optionele payload voor aangepaste gebeurtenissen. |
| Gewijzigde eigenschappen | De eigenschap is opgenomen voor beheerdersgebeurtenissen, zoals het toevoegen van een gebruiker als lid van een site of een beheerdersgroep voor siteverzamelingen. De eigenschap bevat de naam van de eigenschap die is gewijzigd (bijvoorbeeld de groep Sitebeheerder), de nieuwe waarde van de gewijzigde eigenschap (zoals de gebruiker die is toegevoegd als sitebeheerder) en de vorige waarde van het gewijzigde object. |


### <a name="sharepoint-file-operations"></a>SharePoint-bestandsbewerkingen

Deze records worden gemaakt als reactie op bestandsbewerkingen in SharePoint.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | De bestandsextensie van een bestand dat wordt gekopieerd of verplaatst. Deze eigenschap wordt alleen weergegeven voor bestandsgekopieerde en filemoved-gebeurtenissen. |
| DestinationFileName | De naam van het bestand dat wordt gekopieerd of verplaatst. Deze eigenschap wordt alleen weergegeven voor bestandsgekopieerde en filemoved-gebeurtenissen. |
| BestemmingRelativeUrl | De URL van de doelmap waarin een bestand wordt gekopieerd of verplaatst. De combinatie van de waarden voor parameters SiteURL, DestinationRelativeURL en DestinationFileName is dezelfde als de waarde voor de eigenschap ObjectID, de volledige padnaam voor het bestand dat is gekopieerd. Deze eigenschap wordt alleen weergegeven voor bestandsgekopieerde en filemoved-gebeurtenissen. |
| SharingType | Het type machtigingen voor delen dat is toegewezen aan de gebruiker waarmee de resource is gedeeld. Deze gebruiker wordt geïdentificeerd door de parameter UserSharedWith. |
| Site_Url | De URL van de site waar het bestand of de map is gevestigd die door de gebruiker is geopend. |
| SourceFileExtension BronFileExtension | De bestandsextensie van het bestand dat door de gebruiker is geopend. Deze eigenschap is leeg als het object dat is geopend een map is. |
| SourceFileName |     De naam van het bestand of de map die door de gebruiker wordt geopend. |
| BronRelativeUrl | De URL van de map die het bestand bevat dat door de gebruiker is geopend. De combinatie van de waarden voor de parameters SiteURL, SourceRelativeURL en SourceFileName is dezelfde als de waarde voor de eigenschap ObjectID, de volledige padnaam voor het bestand dat door de gebruiker wordt geopend. |
| UserSharedWith |     De gebruiker waarmee een resource is gedeeld. |




## <a name="sample-log-queries"></a>Voorbeeldlogboekquery's

In de volgende tabel vindt u voorbeeldlogboekquery's voor updaterecords die door deze oplossing zijn verzameld.

| Query’s uitvoeren | Beschrijving |
| --- | --- |
|Aantal bewerkingen op uw Office 365-abonnement |OfficeActivity &#124; aantal() per bewerking samenvatten |
|Gebruik van SharePoint-sites|OfficeActivity &#124; waar OfficeWorkload =~ 'sharepoint' &#124; \| het aantal() per SiteUrl sorteren op Count asc samenvatten|
|Bestandstoegangsbewerkingen op gebruikerstype | OfficeActivity &#124; het aantal() op UserType samen te vatten |
|Externe acties op Exchange controleren|OfficeActivity &#124; waar OfficeWorkload =~ "exchange" en ExternalAccess == true|



## <a name="next-steps"></a>Volgende stappen

* Gebruik [logboekquery's in Azure Monitor](../log-query/log-query-overview.md) om gedetailleerde updategegevens weer te geven.
* [Maak uw eigen dashboards](../learn/tutorial-logs-dashboards.md) om uw favoriete Office 365-zoekopdrachten weer te geven.
* [Maak waarschuwingen](../platform/alerts-overview.md) om proactief op de hoogte te worden gesteld van belangrijke Office 365-activiteiten.  
