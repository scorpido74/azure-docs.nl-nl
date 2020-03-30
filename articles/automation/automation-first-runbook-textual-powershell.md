---
title: Mijn eerste PowerShell-runbook in Azure Automation
description: Zelfstudie die u helpt bij het maken, testen en publiceren van een eenvoudig PowerShell-runbook.
keywords: azure powershell, zelfstudie powershell-script, powershell-automatisering
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: e0c48137f5eecc96b6e7b1cbce5f0c683b2a976a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367309"
---
# <a name="my-first-powershell-runbook"></a>Mijn eerste PowerShell-runbook

> [!div class="op_single_selector"]
> * [Grafisch](automation-first-runbook-graphical.md)
> * [Powershell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-werkstroom](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

In deze zelfstudie wordt stap voor stap het maken van een [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) in Azure Automation beschreven. Begin met een eenvoudig runbook dat u testen en publiceren, terwijl u leert hoe u de status van de runbook-taak bijhouden. Wijzig vervolgens de runbook om Azure-resources daadwerkelijk te beheren, in dit geval het starten van een virtuele Azure-machine. Voltooi de zelfstudie om het runbook robuuster te maken door runbook-parameters toe te voegen.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als je er nog geen hebt, kun je [je MSDN-abonneevoordeel activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of je aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Automation-account](automation-quickstart-create-account.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. Aangezien u stopt en deze machine start, zou het geen productie-VM moeten zijn.
* Importeer indien nodig [Azure-modules](shared-resources/modules.md) of [updatemodules](automation-update-azure-modules.md) op basis van de cmdlets die u gebruikt.

## <a name="differences-from-powershell-workflow-runbooks"></a>Verschillen met PowerShell-werkstroomwerkboeken

PowerShell-runbooks hebben dezelfde levenscyclus, mogelijkheden en beheer als PowerShell Workflow-runbooks. Er zijn echter enkele verschillen en beperkingen.

| Kenmerk  | PowerShell-runbooks | PowerShell-werkstroomlogboeken |
| ------ | ----- | ----- |
| Snelheid | Ren snel omdat ze geen compilatiestap gebruiken. | Ren langzamer. |
| Controlepunten | Steun geen controleposten. Een PowerShell-runbook kan de bewerking alleen vanaf het begin hervatten. | Gebruik controlepunten, waarmee een werkmap vanaf elk moment de werking kan hervatten. |
| Opdrachtuitvoering | Alleen seriële uitvoering ondersteunen. | Ondersteuning voor zowel seriële als parallelle uitvoering.|
| Runspace | Een enkele runspace draait alles in een script. | Een aparte runspace kan worden gebruikt voor een activiteit, een opdracht of een scriptblok. |

Naast deze verschillen hebben PowerShell-runbooks enkele [syntactische verschillen met](https://technet.microsoft.com/magazine/dn151046.aspx) PowerShell Workflow-runbooks.

## <a name="step-1---create-runbook"></a>Stap 1: runbook maken

Begin met het maken van een `Hello World`eenvoudig runbook waarmee de tekst wordt uitgevoerd.

1. Open uw Automation-account in Azure Portal.
2. Selecteer **Runbooks** onder **Procesautomatisering** om de lijst met runbooks te openen.
3. Maak een nieuw runbook door **Een runbook maken te**selecteren.
4. Geef het runbook de naam **MyFirstRunbook-PowerShell**.
5. In dit geval gaat u een [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks)maken. Selecteer **PowerShell** voor **runbook-type**.
6. Klik op **Maken** om het runbook te maken en de teksteditor te openen.

## <a name="step-2---add-code-to-the-runbook"></a>Stap 2: code toevoegen aan het runbook

U kunt de code rechtstreeks in het runbook typen of u kunt cmdlets, runbooks en assets selecteren in het besturingselement Bibliotheek en deze laten toevoegen aan het runbook met eventuele gerelateerde parameters. Voor deze zelfstudie typt u code rechtstreeks in het runbook.

1. Je runbook is momenteel leeg. Typ `Write-Output "Hello World"` de hoofdtekst van het script in.

   ![Hallo wereld](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Klik op **Opslaan** om het runbook op te slaan.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> Stap 3 - Test het runbook

Voordat u het runbook publiceert om het beschikbaar te maken in productie, moet u het testen om ervoor te zorgen dat het goed werkt. Als u een runbook test, wordt de conceptversie uitgevoerd en u de uitvoer interactief bekijken.

1. Klik op **Testvenster** om het testvenster te openen.
2. Klik op **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.
3. Houd er rekening mee dat een [runbook-taak](automation-runbook-execution.md) wordt gemaakt en de status ervan wordt weergegeven in het deelvenster.

   De taakstatus `Queued`begint als , wat aangeeft dat de taak wacht tot een runbook-werknemer in de cloud beschikbaar is. De status `Starting` verandert in wanneer een werknemer de taak claimt. Ten slotte wordt `Running` de status wanneer het runbook daadwerkelijk begint uit te voeren.

4. Wanneer de taak runbook is voltooid, wordt in het deelvenster Test de uitvoer weergegeven. In dit geval `Hello World`zie je.

   ![Uitvoer testvenster](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Sluit het testvenster om terug te gaan naar het papier.

## <a name="step-4---publish-and-start-the-runbook"></a>Stap 4: het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog steeds in de conceptmodus. Het moet worden gepubliceerd voordat u het in productie kan uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie omdat het runbook zojuist is gemaakt.

1. Klik op **Publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer hierom wordt gevraagd.
1. Schuif naar links om het runbook op de pagina Runbooks weer te geven en houd er rekening mee dat de waarde **van de status van auteur** is ingesteld op **Gepubliceerd**.
1. Schuif terug naar rechts om het deelvenster voor **MyFirstRunbook-PowerShell** weer te geven.
   
   Met de opties aan de bovenkant u het runbook nu starten, een toekomstige begintijd plannen of een [webhook](automation-webhooks.md) maken zodat het runbook kan worden gestart via een HTTP-gesprek.
1. Selecteer **Start** en vervolgens **Ja** wanneer u wordt gevraagd het runbook te starten. 
1. Er wordt een taakvenster geopend voor de runbook-taak die is gemaakt. Hoewel u dit deelvenster sluiten, laat u het nu open, zodat u de voortgang van de taak bekijken. De functiestatus wordt weergegeven in **Het overzicht van**taken en mogelijke statussen zijn zoals beschreven voor het testen van het runbook.

   ![Taakoverzicht](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Zodra de status `Completed`van de runbook wordt weergegeven, klikt `Hello World` u op **Uitvoer** om de pagina Uitvoer te openen, waar u weergegeven zien.

   ![Taakuitvoer](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Sluit de pagina Uitvoer.
1. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. U moet `Hello World` alleen zien in de uitvoerstroom.

    Houd er rekening mee dat in het deelvenster Streams andere streams voor een runbook-taak kunnen worden weergegeven, zoals Verbose- en Foutstreams, als de runbook naar deze streams schrijft.

   ![Alle logboeken](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Sluit het deelvenster Streams en het deelvenster Taak om terug te keren naar de pagina **MyFirstRunbook-PowerShell.**
1. Klik **onder Details**op **Vacatures** om de pagina Vacatures voor dit runbook te openen. Op deze pagina worden alle vacatures weergegeven die door uw runbook zijn gemaakt. U mag slechts één taak zien staan, omdat u de taak slechts één keer hebt uitgevoerd.

   ![Takenlijst](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Klik op de taaknaam om hetzelfde taakvenster te openen dat u hebt bekeken toen u het runbook begon. Gebruik dit deelvenster om de details weer te geven van een taak die voor het runbook is gemaakt.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Stap 5: verificatie toevoegen voor het beheren van Azure-resources

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. U wilt dat er Azure-resources mee worden beheerd. Hiervoor moet het runbook kunnen verifiëren met het Run As-account dat automatisch is gemaakt toen u uw Automatiseringsaccount maakte.

Zoals in het onderstaande voorbeeld wordt de Run As-verbinding gemaakt met de [connect-AzAccount-cmdlet.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) Als u resources beheert voor meerdere abonnementen, `AzContext` moet u de parameter gebruiken met [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0).

> [!NOTE]
> Voor `Add-AzAccount` PowerShell-runbooks `Add-AzureRMAccount` en zijn `Connect-AzAccount`aliassen voor . U deze cmdlets gebruiken of u [uw modules](automation-update-azure-modules.md) in uw Automation-account updaten naar de nieuwste versies. Mogelijk moet u uw modules bijwerken, zelfs als u zojuist een nieuw Automation-account hebt gemaakt.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```
1. Open de tekstuele editor door op **Bewerken** te klikken op de pagina **MyFirstRunbook-PowerShell.**
1. Je hebt de `Write-Output` lijn niet meer nodig. Ga je gang en verwijder het.
1. Typ of kopieer en plak de volgende code, waarmee de verificatie wordt verwerkt met uw Automation Run As-account.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

1. Klik **op Deelvenster Testen,** zodat u het runbook testen.
1. Klik op **Start** om de test te starten. Zodra deze is voltooid, ziet u uitvoer die vergelijkbaar is met de volgende, waarbij basisgegevens van uw account worden weergegeven. Deze uitvoer bevestigt dat de run als-account geldig is.

   ![Verifiëren](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Stap 6: code toevoegen om een virtuele machine te starten

Nu uw runbook is geauthenticerend op uw Azure-abonnement, u resources beheren. Laten we een opdracht toevoegen om een virtuele machine te starten. U elke virtuele machine in uw Azure-abonnement kiezen en die naam voorlopig hardcoderen in het runbook.

1. Voeg aan uw runbook-script de [start-AzVM-cmdlet](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) toe om de virtuele machine te starten. Zoals hieronder wordt weergegeven, start de cmdlet een virtuele machine met de naam `VMName` en met een resourcegroep met de naam `ResourceGroupName`.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Sla het runbook op en klik op **Het deelvenster Testen,** zodat u het testen.
1. Klik **op Start** om de test te starten. Zodra deze is voltooid, moet u ervoor zorgen dat de virtuele machine is gestart.

## <a name="step-7---add-an-input-parameter"></a>Stap 7 - Een invoerparameter toevoegen

Uw runbook start momenteel de virtuele machine die u hardcoded in het runbook. Het runbook is handiger als u de virtuele machine opgeeft wanneer het runbook wordt gestart. Laten we invoerparameters toevoegen aan het runbook om die functionaliteit te bieden.

1. Wijzig in de tekstuele `Start-AzVM` editor de cmdlet om `VMName` variabelen `ResourceGroupName`voor de parameters te gebruiken en . 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Sla het runbook op en open het testvenster. U kunt nu waarden opgeven voor de twee invoervariabelen die in de test worden gebruikt.
1. Sluit het testvenster.
1. Klik op **Publiceren** om de nieuwe versie van het runbook te publiceren.
1. Stop de virtuele machine die u eerder hebt gestart.
1. Klik op **Starten** om het runbook te starten. 
1. Typ de waarden voor **VMNAME** en **RESOURCEGROUPNAME** voor de virtuele machine die u gaat starten en klik op **OK**.<br><br> ![Parameter doorgeven](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Wanneer het runbook is voltooid, moet u ervoor zorgen dat de virtuele machine is gestart.

## <a name="next-steps"></a>Volgende stappen

* Zie de [PowerShell-documenten](/powershell/scripting/overview)voor meer informatie over PowerShell, inclusief taalverwijzing en leermodules.
* Zie Mijn eerste grafische runbook om aan de slag te gaan met grafische [runbooks.](automation-first-runbook-graphical.md)
* Zie [Mijn eerste PowerShell-werkstroomrunboek](automation-first-runbook-textual.md)om aan de slag te gaan met PowerShell Workflow-runbooks.
* Zie [Azure Automation runbook-typen](automation-runbook-types.md)voor meer informatie over runbook-typen en hun voordelen en beperkingen.
* Zie [Native PowerShell-scriptondersteuning in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)voor meer informatie over de functie voor ondersteuning van PowerShell-script.
