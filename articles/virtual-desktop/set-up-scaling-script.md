---
title: Schaalsessie host Azure Automation - Azure
description: Windows Virtual Desktop-sessiehosts automatisch schalen met Azure Automation.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a853dc32f8716f3f2ba32896a7a4a239efcc5bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349867"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Sessiehosts schalen met Azure Automation

U uw totale implementatiekosten voor Virtuele Bureaublad-bureaubladen verlagen door uw virtuele machines (VM's) te schalen. Dit betekent dat u vm's voor sessiehost tijdens gebruiksuren buiten de piek moet afsluiten en deallocatie, deze vervolgens weer moet inschakelen en opnieuw moet toewijzen tijdens de piekuren.

In dit artikel vindt u meer informatie over het schalingshulpprogramma dat is gebouwd met Azure Automation en Azure Logic Apps, waarmee u automatisch virtuele machines voor sessiehost in uw Windows Virtual Desktop-omgeving schaalt. Als u wilt weten hoe u het schalingsgereedschap gebruiken, gaat u verder naar [Vereisten](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Hoe het schalingsgereedschap werkt

De schalingtool biedt een goedkope automatiseringsoptie voor klanten die hun VM-kosten voor sessiehost willen optimaliseren.

U het schalingsgereedschap gebruiken om:
 
- Plan VM's om te starten en te stoppen op basis van piek- en daluren.
- Schaal VM's uit op basis van het aantal sessies per CPU-kern.
- Schaal in VM's tijdens daluren, waardoor het minimum aantal vm's voor sessiehost wordt uitgevoerd.

Het schalingshulpprogramma maakt gebruik van een combinatie van Azure Automation PowerShell-runbooks, webhooks en Azure Logic Apps om te functioneren. Wanneer het hulpprogramma wordt uitgevoerd, roept Azure Logic Apps een webhook aan om de Azure Automation-runbook te starten. Het runbook maakt vervolgens een taak.

Tijdens de piekgebruikstijd controleert de taak het huidige aantal sessies en de VM-capaciteit van de huidige actieve sessiehost voor elke hostgroep. Deze informatie wordt gebruikt om te berekenen of de actieve sessiehost-VM's bestaande sessies kunnen ondersteunen op basis van de parameter *SessionThresholdPerCPU* die is gedefinieerd voor het **bestand createazurelogicapp.ps1.** Als de VM's van de sessiehost bestaande sessies niet kunnen ondersteunen, wordt met de taak extra sessiehostVM's in de hostgroep gestart.

>[!NOTE]
>*SessionThresholdPerCPU* beperkt het aantal sessies op de VM niet. Deze parameter bepaalt alleen wanneer nieuwe VM's moeten worden gestart om de verbindingen te laden. Als u het aantal sessies wilt beperken, moet u de instructies [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) volgen om de parameter *MaxSessionLimit* dienovereenkomstig te configureren.

Tijdens de gebruikstijd buiten de piek bepaalt de taak welke vm's voor sessiehost moeten worden afgesloten op basis van de parameter *MinimumNumberNumberOfRDSH.* De taak stelt de vm's van de sessiehost in op de afvoermodus om te voorkomen dat nieuwe sessies verbinding maken met de hosts. Als u de parameter *LimitSecondsToForceLogOffUser* instelt op een niet-nul-positieve waarde, stelt de taak alle momenteel aangemelde gebruikers op de hoogte om hun werk op te slaan, de geconfigureerde hoeveelheid tijd te wachten en de gebruikers vervolgens te dwingen zich af te melden. Zodra alle gebruikerssessies op de vm van sessiehost zijn afgemeld, wordt de taak de vm uitgeschakeld.

Als u de parameter *LimitSecondsToForceLogOffUser* op nul instelt, wordt met de taak de instelling voor sessieconfiguratie in opgegeven groepsbeleidsregels toegestaan om afmeldingssessies af te handelen. Als u dit groepsbeleid wilt bekijken, gaat u naar De**Policies** > **beheerderssjablonen** > van **Windows** > **Components** > **Terminal Services** > Terminal Server Terminal**Server-sessie****Terminal Server** > limieten . Als er actieve sessies op een vm van sessiehost zijn, wordt de sessiehost-VM uitgevoerd. Als er geen actieve sessies zijn, wordt de sessiehost-VM afgesloten.

De taak wordt periodiek uitgevoerd op basis van een ingesteld herhalingsinterval. U dit interval wijzigen op basis van de grootte van uw Windows Virtual Desktop-omgeving, maar vergeet niet dat het starten en afsluiten van virtuele machines enige tijd kan duren, dus vergeet niet om rekening te houden met de vertraging. We raden u aan het herhalingsinterval in te stellen op elke 15 minuten.

De tool heeft echter ook de volgende beperkingen:

- Deze oplossing is alleen van toepassing op gepoolde vm's voor sessiehost.
- Deze oplossing beheert VM's in elke regio, maar kan alleen worden gebruikt in hetzelfde abonnement als uw Azure Automation-account en Azure Logic Apps.

>[!NOTE]
>Met het gereedschap schalen bepaalt u de taakverdelingsmodus van de hostgroep die wordt geschaald. Het stelt het in op breedte-eerste load balancing voor zowel piek- als daluren.

## <a name="prerequisites"></a>Vereisten

Voordat u het schalingsgereedschap instelt, controleert u of u de volgende dingen klaar hebt:

- Een [Windows Virtual Desktop-tenant en hostpool](create-host-pools-arm-template.md)
- Vm's voor sessiehostgroep die zijn geconfigureerd en geregistreerd bij de Windows Virtual Desktop-service
- Een gebruiker met [toegang tot bijdragen](../role-based-access-control/role-assignments-portal.md) op Azure-abonnement

De machine die u gebruikt om het gereedschap te implementeren, moet beschikken over: 

- Windows PowerShell 5.1 of hoger
- De Microsoft Az PowerShell-module

Als je alles klaar hebt, laten we dan beginnen.

## <a name="create-an-azure-automation-account"></a>Een Azure Automation-account maken

Eerst hebt u een Azure Automation-account nodig om de PowerShell-runbook uit te voeren. Zo stel je je account in:

1. Open Windows PowerShell als administrator.
2. Voer de volgende cmdlet uit om u aan te melden bij uw Azure-account.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >Uw account moet rechten op bijdragende bijdragen hebben op het Azure-abonnement waarop u het schaalprogramma wilt implementeren.

3. Voer de volgende cmdlet uit om het script te downloaden voor het maken van het Azure Automation-account:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Voer de volgende cmdlet uit om het script uit te voeren en het Azure Automation-account te maken:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. De output van de cmdlet zal een webhook URI bevatten. Zorg ervoor dat u de URI registreert, omdat u deze als parameter gebruikt wanneer u het uitvoeringsschema voor de Azure Logic-apps instelt.

6. Nadat u uw Azure Automation-account hebt ingesteld, meldt u zich aan bij uw Azure-abonnement en controleert u of uw Azure Automation-account en de relevante runbook zijn weergegeven in uw opgegeven brongroep, zoals in de volgende afbeelding wordt weergegeven:

   ![Een afbeelding van de Azure-overzichtspagina met het nieuw gemaakte automatiseringsaccount en het runbook.](media/automation-account.png)

  Als u wilt controleren of uw webhook is waar deze moet zijn, selecteert u de naam van uw runbook. Ga vervolgens naar de sectie Resources van uw runbook en selecteer **Webhooks**.

## <a name="create-an-azure-automation-run-as-account"></a>Een Azure Automation Run As-account maken

Nu u een Azure Automation-account hebt, moet u ook een Azure Automation Run As-account maken om toegang te krijgen tot uw Azure-bronnen.

Een [Azure Automation Run As-account](../automation/manage-runas-account.md) biedt verificatie voor het beheren van resources in Azure met de Azure-cmdlets. Wanneer u een Run As-account maakt, wordt een nieuwe servicehoofdgebruiker in Azure Active Directory gemaakt en wordt de rol inzender op abonnementsniveau aan de hoofdgebruiker van de service toegeschreven, het Azure Run As-account is een geweldige manier om veilig te verifiëren met certificaten en een servicenaam zonder dat u een gebruikersnaam en wachtwoord hoeft op te slaan in een referentieobject. Zie [Run as-accountmachtigingen beperken als](../automation/manage-runas-account.md#limiting-run-as-account-permissions)voor meer informatie over Verificatie als uitvoeren.

Elke gebruiker die lid is van de rol Abonnementsbeheerders en medebeheerder van het abonnement, kan een Run As-account maken door de instructies van de volgende sectie te volgen.

Ga als lid van uw Azure-account als een Run As-account maken:

1. Selecteer **alle services**in de Azure-portal . Voer in de lijst met resources automatiseringsaccounts in en selecteer **deze.**

2. Selecteer op de pagina **Automatiseringsaccounts** de naam van uw automatiseringsaccount.

3. Selecteer In het deelvenster aan de linkerkant van het venster de optie **Uitvoeren als accounts** onder de sectie Accountinstellingen.

4. Selecteer **Azure Run as Account**. Wanneer het deelvenster **Azure Run As-account toevoegen** wordt weergegeven, controleert u de overzichtsgegevens en selecteert u **Maken** om het proces voor het maken van een account te starten.

5. Wacht een paar minuten tot Azure het Run As-account maakt. U de voortgang van het maken bijhouden in het menu onder Meldingen.

6. Wanneer het proces is voltooid, wordt een asset met de naam AzureRunAsConnection gemaakt in het opgegeven automatiseringsaccount. Het verbindingsitem bevat de toepassings-id, tenant-id, abonnements-ID en certificaatduimafdruk. Onthoud de toepassings-ID, omdat u deze later zult gebruiken.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Een roltoewijzing maken in Windows Virtual Desktop

Vervolgens moet u een roltoewijzing maken, zodat AzureRunAsConnection kan communiceren met Windows Virtual Desktop. Zorg ervoor dat u PowerShell gebruikt om u aan te melden met een account met machtigingen voor het maken van roltoewijzingen.

Download en importeer eerst de [Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) om te gebruiken in uw PowerShell-sessie als u dat nog niet hebt gedaan. Voer de volgende PowerShell-cmdlets uit om verbinding te maken met Windows Virtual Desktop en uw tenants weer te geven.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Wanneer u de tenant vindt met de hostpools die u wilt schalen, volgt u de instructies in [Een Azure Automation-account maken](#create-an-azure-automation-account) en gebruikt u de tenantnaam die u van de vorige cmdlet in de volgende cmdlet hebt gekregen om de roltoewijzing te maken:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Het Azure Logic App- en uitvoeringsschema maken

Ten slotte moet u de Azure Logic App maken en een uitvoeringsschema instellen voor uw nieuwe schalingstool.

1.  Windows PowerShell openen als beheerder

2.  Voer de volgende cmdlet uit om u aan te melden bij uw Azure-account.

     ```powershell
     Login-AzAccount
     ```

3. Voer de volgende cmdlet uit om het scriptbestand createazurelogicapp.ps1 op uw lokale machine te downloaden.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. Voer de volgende cmdlet uit om u aan te melden bij Windows Virtual Desktop met een account met machtigingen voor RDS-eigenaar of RDS-inzender.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Voer het volgende PowerShell-script uit om de Azure Logic-app en het uitvoeringsschema te maken.

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you'd like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"

     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"

     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"

     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"

     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"

     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"

     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"

     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"

     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $location = Read-Host -Prompt "Enter the name of the Azure region where you will be creating the logic app"

     $connectionAssetName = Read-Host -Prompt "Enter the name of the Azure RunAs connection asset"

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"

     $automationAccountName = Read-Host -Prompt "Enter the name of the Azure Automation Account"

     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     Nadat u het script hebt uitgevoerd, moet de Logische app worden weergegeven in een resourcegroep, zoals in de volgende afbeelding wordt weergegeven.

     ![Een afbeelding van de overzichtspagina voor een voorbeeld azure logic-app.](media/logic-app.png)

Als u wijzigingen wilt aanbrengen in het uitvoeringsschema, zoals het wijzigen van het herhalingsinterval of de tijdzone, gaat u naar de autoscale-planner en selecteert **u Bewerken** om naar de Logic Apps Designer te gaan.

![Een afbeelding van de Logic Apps Designer. De recidief- en webhookmenu's waarmee de gebruiker herhalingstijden kan bewerken en het webhookbestand zijn geopend.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Uw schalingsgereedschap beheren

Nu u uw schalingtool hebt gemaakt, hebt u toegang tot de uitvoer. In deze sectie worden een aantal functies beschreven die u mogelijk nuttig vindt.

### <a name="view-job-status"></a>Taakstatus weergeven

U een samengevatte status van alle runbook-taken bekijken of een meer diepgaande status van een specifieke runbook-taak bekijken in de Azure-portal.

Rechts van uw geselecteerde Automatiseringsaccount u onder Taakstatistieken een lijst met samenvattingen van alle runbook-taken bekijken. Als u de pagina **Vacatures** opent aan de linkerkant van het venster, worden de huidige taakstatussen, begintijden en voltooiingstijden weergegeven.

![Een schermafbeelding van de pagina met de functiestatus.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Logboeken en uitvoer van het uitvoerprogramma schalen weergeven

U de logboeken van scale-out- en scale-inbewerkingen bekijken door uw runbook te openen en de naam van uw taak te selecteren.

Navigeer naar de runbook (de standaardnaam is WVDAutoScaleRunbook) in uw resourcegroep met als host van het Azure Automation-account en selecteer **Overzicht**. Selecteer op de overzichtspagina een taak onder Recente taken om de uitvoer van het schalinggereedschap weer te geven, zoals in de volgende afbeelding wordt weergegeven.

![Een afbeelding van het uitvoervenster voor het gereedschap schalen.](media/tool-output.png)

## <a name="report-issues"></a>Problemen melden

Als u problemen ondervindt met het schalingshulpmiddel, u deze melden op de [RDS GitHub-pagina.](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)
