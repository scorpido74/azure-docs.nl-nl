---
title: Problemen met VPN-gateways oplossen en bewaken - Azure Automation
titleSuffix: Azure Network Watcher
description: In dit artikel wordt beschreven hoe u on-premises connectiviteit met Azure Automation en Network Watcher diagnosticeren
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 74c9f44ff5fbbbb50bba1594d371633fd49857eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845033"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>VPN-gateways bewaken met problemen met Network Watcher

Het verkrijgen van diepgaande inzichten over uw netwerkprestaties is essentieel om betrouwbare services aan klanten te bieden. Het is daarom van cruciaal belang om de omstandigheden van netwerkuitval snel te detecteren en corrigerende maatregelen te nemen om de storingstoestand te beperken. Azure Automation stelt u in staat om een taak op een programmatische manier te implementeren en uit te voeren via runbooks. Het gebruik van Azure Automation is een perfect recept voor het uitvoeren van continue en proactieve netwerkbewaking en -waarschuwingen.

## <a name="scenario"></a>Scenario

Het scenario in de volgende afbeelding is een multi-tiered applicatie, met on-premises connectiviteit vastgesteld met behulp van een VPN Gateway en tunnel. Ervoor zorgen dat de VPN-gateway operationeel is, is van cruciaal belang voor de prestaties van toepassingen.

Er wordt een runbook gemaakt met een script om te controleren op de verbindingsstatus van de VPN-tunnel, waarbij de API voor het oplossen van problemen met resources wordt gebruikt om te controleren of de status van de verbindingstunnel is. Als de status niet in orde is, wordt er een e-mailtrigger naar beheerders verzonden.

![Voorbeeldscenario][scenario]

Dit scenario zal:

- Een runbook maken `Start-AzureRmNetworkWatcherResourceTroubleshooting` waarin de cmdlet wordt aangeroepen om de verbindingsstatus op te lossen
- Een planning koppelen aan het runbook

## <a name="before-you-begin"></a>Voordat u begint

Voordat u met dit scenario begint, moet u de volgende vereisten hebben:

- Een Azure-automatiseringsaccount in Azure. Zorg ervoor dat het automatiseringsaccount over de nieuwste modules beschikt en ook de AzureRM.Network-module heeft. De AzureRM.Network-module is beschikbaar in de modulegalerie als u deze wilt toevoegen aan uw automatiseringsaccount.
- U moet een set referenties hebben geconfigureerd in Azure Automation. Meer informatie bij [Azure Automation security](../automation/automation-security-overview.md)
- Een geldige SMTP-server (Office 365, uw on-premises e-mail of een andere) en referenties die zijn gedefinieerd in Azure Automation
- Een geconfigureerde Virtual Network Gateway in Azure.
- Een bestaand opslagaccount met een bestaande container om de logboeken op te slaan.

> [!NOTE]
> De infrastructuur in de voorgaande afbeelding is ter illustratie en wordt niet gemaakt met de stappen in dit artikel.

### <a name="create-the-runbook"></a>Het runbook maken

De eerste stap voor het configureren van het voorbeeld is het maken van de runbook. In dit voorbeeld wordt een run-as-account gebruikt. Ga naar [Runbooks verifiëren met Azure Run As-account](../automation/automation-create-runas-account.md) voor meer informatie over run-as-accounts

### <a name="step-1"></a>Stap 1

Navigeren naar Azure Automation in de [Azure-portal](https://portal.azure.com) en klik op **Runbooks**

![overzicht van automatiseringsaccount][1]

### <a name="step-2"></a>Stap 2

Klik **op Een runbook toevoegen** om het maakproces van het runbook te starten.

![runbooks blade][2]

### <a name="step-3"></a>Stap 3

Klik **onder Snel maken**op Een nieuw **runbook maken** om de runbook te maken.

![een runbook-blad toevoegen][3]

### <a name="step-4"></a>Stap 4

In deze stap geven we het runbook een naam, in het voorbeeld heet het **Get-VPNGatewayStatus**. Het is belangrijk om het runbook een beschrijvende naam te geven en aanbevolen om het een naam te geven die voldoet aan de standaard PowerShell-naamgevingsnormen. Het type runbook voor dit voorbeeld is **PowerShell,** de andere opties zijn grafische, PowerShell-workflow en grafische PowerShell-workflow.

![runbook blad][4]

### <a name="step-5"></a>Stap 5

In deze stap wordt het runbook gemaakt, in het volgende codevoorbeeld worden alle code gegeven die nodig is voor het voorbeeld. De items in de \<\> code die waarde bevatten, moeten worden vervangen door de waarden van uw abonnement.

Gebruik de volgende code als klik op **Opslaan**

```powershell
# Set these variables to the proper values for your environment
$o365AutomationCredential = "<Office 365 account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for Office 365 account
$cred = Get-AutomationPSCredential -Name $o365AutomationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>Stap 6

Zodra het runbook is opgeslagen, moet er een schema aan worden gekoppeld om het begin van het runbook te automatiseren. Als u het proces wilt starten, klikt u op **Planning**.

![Stap 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Een planning koppelen aan het runbook

Er moet een nieuwe planning worden gemaakt. Klik **op Een planning koppelen aan uw runbook**.

![Stap 7][7]

### <a name="step-1"></a>Stap 1

Klik **in** het blad Schema op **Een nieuw schema maken**

![Stap 8][8]

### <a name="step-2"></a>Stap 2

Vul op het **nieuwe schema** blad de planningsinformatie in. De waarden die kunnen worden ingesteld, staan in de volgende lijst:

- **Naam** - De vriendelijke naam van het schema.
- **Beschrijving** - Een beschrijving van het schema.
- **Start** : deze waarde is een combinatie van datum, tijd en tijdzone die deel uitmaken van de tijd die de planning activeert.
- **Herhaling** - Deze waarde bepaalt de herhaling van schema's.  Geldige waarden zijn **Eenmaal** of **Terugkerend**.
- **Elke keer terugkeren** - Het herhalingsinterval van het schema in uren, dagen, weken of maanden.
- **Verloop instellen** : de waarde bepaalt of de planning verloopt of niet. Kan worden ingesteld op **Ja** of **Nee.** Een geldige datum en tijd moeten worden verstrekt als ja wordt gekozen.

> [!NOTE]
> Als u een runbook vaker dan elk uur moet laten uitvoeren, moeten er meerdere schema's met verschillende intervallen worden gemaakt (dat wil zeggen, 15, 30, 45 minuten na het uur)

![Stap 9][9]

### <a name="step-3"></a>Stap 3

Klik op Opslaan om de planning op te slaan in het runbook.

![Stap 10][10]

## <a name="next-steps"></a>Volgende stappen

Nu u meer inzicht hebt in het integreren van probleemoplossing voor netwerkwatchers met Azure Automation, leest u hoe u pakketopnamen op VM-waarschuwingen activeren door een waarschuwing te gebruiken [voor het vastleggen van een waarschuwing met Azure Network Watcher](network-watcher-alert-triggered-packet-capture.md).

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png
