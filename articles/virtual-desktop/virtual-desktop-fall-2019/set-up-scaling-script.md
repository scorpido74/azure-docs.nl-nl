---
title: Sessie met host schalen Azure Automation-Azure
description: Windows Virtual Desktop Session hosts automatisch schalen met Azure Automation.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f3a82665f197301fe81c448dd18181f0602bdbef
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85209790"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Sessie-hosts schalen met behulp van Azure Automation

>[!IMPORTANT]
>Deze inhoud is van toepassing op de update uit het najaar van 2019 die geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten.

U kunt de totale kosten voor het implementeren van virtuele Windows-Bureau bladen verlagen door uw virtuele machines (Vm's) te schalen. Dit betekent dat de virtuele machines van de host worden afgesloten en opnieuw worden toegewezen tijdens gebruiks uren en vervolgens weer worden ingeschakeld en opnieuw worden toegewezen tijdens piek uren.

In dit artikel vindt u meer informatie over het hulp programma voor schalen dat is gemaakt met Azure Automation en Azure Logic Apps waarmee de virtuele machines van de sessiehost automatisch worden geschaald in uw virtuele Windows-desktop omgeving. Voor meer informatie over het gebruik van het hulp programma schalen gaat u verder met [vereisten](#prerequisites).

## <a name="report-issues"></a>Problemen melden

Probleem rapporten voor het hulp programma voor schalen worden momenteel verwerkt op GitHub in plaats van Microsoft Ondersteuning. Als u problemen ondervindt met het hulp programma voor schalen, kunt u ze melden dat Bu een GitHub-probleem met de naam ' 4a-WVD-scaling-logicapps ' op de [pagina RDS github](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)opent.

## <a name="how-the-scaling-tool-works"></a>De werking van het hulp programma voor schalen

Het hulp programma voor schalen biedt een voordelige automatiserings optie voor klanten die hun host-VM-kosten voor hun sessie willen optimaliseren.

U kunt het hulp programma schalen gebruiken voor het volgende:

- Plan Vm's om te starten en stoppen op basis van pieken en kantoor uren.
- Uitschalen van Vm's op basis van het aantal sessies per CPU-kern.
- Schaal in Vm's tijdens rustige uren, waardoor het minimale aantal actieve host-Vm's wordt uitgevoerd.

Het hulp programma voor schalen maakt gebruik van een combi natie van Azure Automation Power shell-runbooks, webhooks en Azure Logic Apps om te werken. Wanneer het hulp programma wordt uitgevoerd, wordt door Azure Logic Apps een webhook aangeroepen om het Azure Automation runbook te starten. Het runbook maakt vervolgens een taak.

Tijdens de piek duur van het gebruik controleert de taak het huidige aantal sessies en de VM-capaciteit van de huidige actieve sessiehost voor elke hostgroep. Deze informatie wordt gebruikt om te berekenen of de actieve host-Vm's bestaande sessies kunnen ondersteunen op basis van de para meter *SessionThresholdPerCPU* die voor het **createazurelogicapp.ps1** bestand is gedefinieerd. Als de virtuele machines van de sessiehost geen bestaande sessies kunnen ondersteunen, start de taak extra host-Vm's in de hostgroep.

>[!NOTE]
>*SessionThresholdPerCPU* beperkt het aantal sessies op de virtuele machine niet. Deze para meter bepaalt alleen wanneer nieuwe Vm's moeten worden gestart om de verbindingen te verdelen. Als u het aantal sessies wilt beperken, moet u de instructies [set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) volgen om de *MaxSessionLimit* -para meter dienovereenkomstig te configureren.

Tijdens de gebruiks tijd bepaalt de taak welke host-Vm's moeten worden afgesloten op basis van de para meter *MinimumNumberOfRDSH* . Met de taak worden de Vm's van de sessie-host ingesteld op de afvoer modus om te voor komen dat nieuwe sessies verbinding maken met de hosts. Als u de para meter *LimitSecondsToForceLogOffUser* instelt op een positieve waarde die niet gelijk is aan nul, wordt door de taak een melding verzonden naar gebruikers die momenteel zijn aangemeld om hun werk op te slaan, de geconfigureerde tijds duur te wachten en vervolgens af te dwingen dat de gebruikers zich afmelden. Zodra alle gebruikers sessies op de sessiehost van de host-VM zijn afgemeld, wordt de VM door de taak afgesloten.

Als u de para meter *LimitSecondsToForceLogOffUser* instelt op nul, kan de sessie configuratie-instelling in opgegeven groeps beleid voor het afmelden van gebruikers sessies worden afgehandeld. Als u dit groeps beleid wilt zien, gaat u naar **computer configuratie**  >  **beleid**  >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **Terminal Services**  >  **Terminal Server**  >  **sessie tijds limieten**. Als er actieve sessies op een host-VM zijn, wordt de VM van de host in de sessie uitgevoerd. Als er geen actieve sessies zijn, wordt de host-VM van de sessie afgesloten met de taak.

De taak wordt regel matig uitgevoerd op basis van een ingesteld interval van het terugkeer patroon. U kunt dit interval wijzigen op basis van de grootte van uw virtueel-bureaublad omgeving van Windows, maar houd er rekening mee dat het starten en afsluiten van virtuele machines enige tijd kan duren. Vergeet dus niet om de vertraging te onthouden. We raden u aan het interval voor het terugkeer patroon in te stellen op elke 15 minuten.

Het hulp programma heeft echter ook de volgende beperkingen:

- Deze oplossing is alleen van toepassing op Vm's met een gegroepeerde sessie-host.
- Deze oplossing beheert Vm's in een wille keurige regio, maar kan alleen worden gebruikt in hetzelfde abonnement als uw Azure Automation-account en Azure Logic Apps.

>[!NOTE]
>Het hulp programma voor schalen bepaalt de taakverdelings modus van de hostgroep die wordt geschaald. Deze wordt ingesteld op breedte-eerste taak verdeling voor zowel de piek als de piek uren.

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het instellen van het hulp programma voor schalen, moet u de volgende dingen doen:

- Een [Windows-Tenant en-hostgroep voor virtueel bureau blad](create-host-pools-arm-template.md)
- Vm's van de hostgroep zijn geconfigureerd en geregistreerd bij de virtuele bureau blad-service van Windows
- Een gebruiker met [toegang tot Inzender](../../role-based-access-control/role-assignments-portal.md) op Azure-abonnement

De computer die u gebruikt om het hulp programma te implementeren, moet beschikken over:

- Windows Power shell 5,1 of hoger
- De micro soft AZ Power shell-module

Als u alles klaar hebt, kunt u aan de slag.

## <a name="create-an-azure-automation-account"></a>Een Azure Automation-account maken

Eerst hebt u een Azure Automation-account nodig om het Power shell-runbook uit te voeren. U kunt als volgt uw account instellen:

1. Open Windows PowerShell als beheerder.
2. Voer de volgende cmdlet uit om u aan te melden bij uw Azure-account.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >Uw account moet bijdrage rechten hebben voor het Azure-abonnement waarvoor u het hulp programma voor schalen wilt implementeren.

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

5. De uitvoer van de cmdlet bevat een webhook-URI. Zorg ervoor dat u een record van de URI bewaart, omdat u deze gebruikt als para meter bij het instellen van het uitvoerings schema voor de Azure Logic apps.

6. Nadat u uw Azure Automation-account hebt ingesteld, meldt u zich aan bij uw Azure-abonnement en controleert u of uw Azure Automation-account en het relevante runbook zijn weer gegeven in de opgegeven resource groep, zoals in de volgende afbeelding:

![Een afbeelding van de pagina overzicht van Azure met het zojuist gemaakte Automation-account en runbook.](../media/automation-account.png)

  Als u wilt controleren of de webhook het moet zijn, selecteert u de naam van uw runbook. Ga vervolgens naar het gedeelte resources van het runbook en selecteer **webhooks**.

## <a name="create-an-azure-automation-run-as-account"></a>Een Azure Automation uitvoeren als-account maken

Nu u een Azure Automation account hebt, moet u ook een Azure Automation uitvoeren als-account maken om toegang te krijgen tot uw Azure-resources.

Een [Azure Automation uitvoeren als-account](../../automation/manage-runas-account.md) biedt verificatie voor het beheren van resources in azure met de Azure-cmdlets. Wanneer u een uitvoeren als-account maakt, wordt er in Azure Active Directory een nieuwe Service-Principal-gebruiker gemaakt en wordt de rol Inzender toegewezen aan de Service-Principal-gebruiker op het abonnements niveau. het uitvoeren als-account van Azure is een uitstekende manier om veilig te verifiëren met certificaten en een Service Principal Name zonder dat u een gebruikers naam en wacht woord in een referentie object hoeft op te slaan. Zie machtigingen voor het [uitvoeren als-account beperken](../../automation/manage-runas-account.md#limit-run-as-account-permissions)voor meer informatie over uitvoeren als-verificatie.

Elke gebruiker die lid is van de rol abonnements beheerders en mede beheerder van het abonnement kan een uitvoeren als-account maken door de instructies in de volgende sectie te volgen.

Een uitvoeren als-account maken in uw Azure-account:

1. Selecteer in de Azure-portal de optie **Alle services**. In de lijst met resources voert u **Automation-accounts**in en selecteert u deze.

2. Selecteer op de pagina **Automation-accounts** de naam van uw Automation-account.

3. Selecteer in het deel venster aan de linkerkant van het venster **uitvoeren als-accounts** in de sectie account instellingen.

4. Selecteer een **uitvoeren als-account voor Azure**. Wanneer het deel venster **uitvoeren als-account voor Azure toevoegen** wordt weer gegeven, controleert u de overzichts informatie en selecteert u vervolgens **maken** om het proces voor het maken van het account te starten.

5. Wacht enkele minuten totdat het run as-account is gemaakt in Azure. U kunt de voortgang van het maken volgen in het menu onder meldingen.

6. Wanneer het proces is voltooid, wordt er een Asset gemaakt met de naam AzureRunAsConnection in het opgegeven Automation-account. Het verbindings element bevat de toepassings-ID, Tenant-ID, abonnements-ID en vinger afdruk van het certificaat. Onthoud de toepassings-ID omdat u deze later gebruikt.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Een roltoewijzing maken in Windows Virtual Desktop

Vervolgens moet u een roltoewijzing maken zodat AzureRunAsConnection kan communiceren met het virtuele bureau blad van Windows. Zorg ervoor dat u Power shell gebruikt om u aan te melden met een account dat is gemachtigd om roltoewijzingen te maken.

Down load en Importeer eerst de [Windows Virtual Desktop Power shell-module](/powershell/windows-virtual-desktop/overview/) voor gebruik in uw Power shell-sessie als u dat nog niet hebt gedaan. Voer de volgende PowerShell-cmdlets uit om verbinding te maken met Windows Virtual Desktop en uw tenants weer te geven.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Wanneer u de Tenant met de hostgroepen die u wilt schalen vindt, volgt u de instructies in [een Azure Automation-account maken](#create-an-azure-automation-account) en gebruikt u de naam van de Tenant die u van de vorige cmdlet in de volgende cmdlet hebt gekregen om de roltoewijzing te maken:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>De Azure Logic-app en het uitvoerings schema maken

Ten slotte moet u de Azure Logic-app maken en een uitvoerings schema instellen voor het nieuwe hulp programma voor schalen.

1.  Windows Power shell openen als beheerder

2.  Voer de volgende cmdlet uit om u aan te melden bij uw Azure-account.

     ```powershell
     Login-AzAccount
     ```

3. Voer de volgende cmdlet uit om het createazurelogicapp.ps1-script bestand op uw lokale computer te downloaden.

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazurelogicapp.ps1"
     ```

4. Voer de volgende cmdlet uit om u aan te melden bij Windows Virtual Desktop met een account met de machtigingen RDS owner of RDS contributor.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Voer het volgende Power shell-script uit om de Azure Logic-app en het uitvoerings schema te maken.

     ```powershell
     $aadTenantId = (Get-AzContext).Tenant.Id

     $azureSubscription = Get-AzSubscription | Out-GridView -PassThru -Title "Select your Azure Subscription"
     Select-AzSubscription -Subscription $azureSubscription.Id
     $subscriptionId = $azureSubscription.Id

     $resourceGroup = Get-AzResourceGroup | Out-GridView -PassThru -Title "Select the resource group for the new Azure Logic App"
     $resourceGroupName = $resourceGroup.ResourceGroupName
     $location = $resourceGroup.Location

     $wvdTenant = Get-RdsTenant | Out-GridView -PassThru -Title "Select your WVD tenant"
     $tenantName = $wvdTenant.TenantName

     $wvdHostpool = Get-RdsHostPool -TenantName $wvdTenant.TenantName | Out-GridView -PassThru -Title "Select the host pool you'd like to scale"
     $hostPoolName = $wvdHostpool.HostPoolName

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"
     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $automationAccount = Get-AzAutomationAccount -ResourceGroupName $resourceGroup.ResourceGroupName | Out-GridView -PassThru
     $automationAccountName = $automationAccount.AutomationAccountName
     $automationAccountConnection = Get-AzAutomationConnection -ResourceGroupName $resourceGroup.ResourceGroupName -AutomationAccountName $automationAccount.AutomationAccountName | Out-GridView -PassThru -Title "Select the Azure RunAs connection asset"
     $connectionAssetName = $automationAccountConnection.Name

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"
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

     Nadat u het script hebt uitgevoerd, moet de logische app worden weer gegeven in een resource groep, zoals wordt weer gegeven in de volgende afbeelding.

     ![Een afbeelding van de overzichts pagina voor een voor beeld van een Azure Logic-app.](../media/logic-app.png)

Als u wijzigingen wilt aanbrengen in het uitvoerings schema, zoals het wijzigen van het terugkeer-interval of de tijd zone, gaat u naar de scheduler automatisch schalen en selecteert u **bewerken** om naar de Logic apps Designer te gaan.

![Een afbeelding van de ontwerp functie voor Logic Apps. De herhalings-en webhook-menu's waarmee de gebruiker terugkeer tijden en het webhookbestand kunnen bewerken.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Uw schaal programma beheren

Nu u het hulp programma voor schalen hebt gemaakt, hebt u toegang tot de uitvoer. In deze sectie worden enkele functies beschreven die u mogelijk nuttig vindt.

### <a name="view-job-status"></a>Taakstatus weergeven

U kunt een samenvattings status van alle runbook-taken weer geven of een uitgebreide status van een bepaalde runbook-taak weer geven in de Azure Portal.

Rechts van het geselecteerde Automation-account kunt u onder ' taak statistieken ' een lijst met samen vattingen van alle runbook-taken weer geven. Als u de pagina **taken** aan de linkerkant van het venster opent, worden de huidige statussen van de taak, start tijden en voltooiings tijden weer gegeven.

![Een scherm opname van de pagina taak status.](../media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Uitvoer van Logboeken en hulpprogram ma's voor schalen weer geven

U kunt de logboeken van scale-out-en schaal bewerkingen bekijken door uw runbook te openen en de naam van uw taak te selecteren.

Ga naar het runbook (de standaard naam is WVDAutoScaleRunbook) in de resource groep die als host fungeert voor het Azure Automation-account en selecteer **overzicht**. Selecteer op de pagina overzicht een taak onder recente taken om de uitvoer van het hulp programma weer te geven, zoals wordt weer gegeven in de volgende afbeelding.

![Een afbeelding van het uitvoer venster voor het hulp programma voor schalen.](../media/tool-output.png)

