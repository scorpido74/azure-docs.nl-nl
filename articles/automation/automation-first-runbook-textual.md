---
title: Mijn eerste PowerShell-werkstroom-runbook in Azure Automation
description: Zelfstudie die u helpt bij het maken, testen en publiceren van een eenvoudig tekstrunbook met behulp van PowerShell Workflow.
keywords: powershell-werkstroom, voorbeelden powershell-werkstroom, werkstroom powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: 8b103437ab30c05ddab88b7a8a723cd2b4b1d5f6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405895"
---
# <a name="my-first-powershell-workflow-runbook"></a>Mijn eerste PowerShell Workflow-runbook

> [!div class="op_single_selector"]
> * [Grafisch](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-werkstroom](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

In deze zelfstudie wordt stap voor stap het maken van een [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) in Azure Automation beschreven. Begin met een eenvoudig runbook dat u test en publiceert terwijl u leert hoe u de status van de runbook-taak bijhouden. Wijzig vervolgens de runbook om Azure-resources daadwerkelijk te beheren, geïllustreerd door een virtuele Azure-machine te starten. Maak ten slotte het runbook robuuster door runbook-parameters toe te voegen.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als je er nog geen hebt, kun je [je MSDN-abonneevoordeel activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of je aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Automation-account](automation-offering-get-started.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. Aangezien u stopt en deze machine start, zou het geen productie-VM moeten zijn.

## <a name="step-1---create-new-runbook"></a>Stap 1: nieuw runbook maken

Begin met het maken van een `Hello World`eenvoudig runbook waarmee de tekst wordt uitgevoerd.

1. Open uw Automation-account in Azure Portal.

   Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U hebt als het goed is al enkele assets. De meeste van deze activa zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. U moet ook het referentie-item aan uw abonnement hebben gekoppeld.
 
1. Selecteer **Runbooks** onder **Procesautomatisering** om de lijst met runbooks te openen.
1. Maak een nieuw runbook door **Een runbook maken te**selecteren.
1. Geef het runbook de naam **MyFirstRunbook-Workflow**.
1. In dit geval gaat u een [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks)maken. Selecteer **PowerShell-werkstroom** voor **runbook-type**.
1. Klik op **Maken** om het runbook te maken en de teksteditor te openen.

## <a name="step-2---add-code-to-the-runbook"></a>Stap 2: code toevoegen aan het runbook

U code rechtstreeks in het runbook typen of cmdlets, runbooks en assets selecteren uit het besturingselement Bibliotheek en deze toevoegen aan het runbook met alle gerelateerde parameters. Voor deze zelfstudie typt u code rechtstreeks in het runbook.

1. Uw runbook is momenteel leeg `Workflow` met alleen het vereiste zoekwoord, de naam van het runbook en de accolades die de hele werkstroom omgeven.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Typ `Write-Output "Hello World"` tussen de beugels.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Klik op **Opslaan** om het runbook op te slaan.

## <a name="step-3---test-the-runbook"></a>Stap 3: het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, moet u het testen om ervoor te zorgen dat het goed werkt. Als u een runbook test, wordt de conceptversie uitgevoerd en u de uitvoer interactief bekijken.

1. Selecteer **Het deelvenster Testen** om het deelvenster Testen te openen.
1. Klik **op Start** om de test te starten, waarbij de enige ingeschakelde optie wordt getest.
1. Houd er rekening mee dat een [runbook-taak](automation-runbook-execution.md) wordt gemaakt en de status ervan wordt weergegeven in het deelvenster.

   De taakstatus begint als Queued, wat aangeeft dat de taak wacht tot een runbook-werknemer in de cloud beschikbaar is. De status wordt gewijzigd in Starten wanneer een werknemer de taak claimt. Ten slotte wordt de status Actief wanneer het runbook daadwerkelijk begint uit te voeren.

1. Wanneer de taak runbook is voltooid, wordt in het deelvenster Test de uitvoer weergegeven. In dit geval `Hello World`zie je.

   ![Hallo wereld](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Sluit het testvenster om terug te gaan naar het papier.

## <a name="step-4---publish-and-start-the-runbook"></a>Stap 4: het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog steeds in de conceptmodus. U moet het publiceren voordat u het in productie uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie omdat het runbook zojuist is gemaakt.

1. Klik op **Publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer hierom wordt gevraagd.
1. Schuif naar links om het runbook op de pagina **Runbooks weer** te geven en houd er rekening mee dat het veld **Ontwerpstatus** is ingesteld op **Gepubliceerd**.
1. Scroll terug naar rechts om de pagina voor **MyFirstRunbook-Workflow**weer te geven.

   Met de opties aan de bovenkant u het runbook nu starten, een toekomstige begintijd plannen of een [webhook](automation-webhooks.md) maken zodat het runbook kan worden gestart via een HTTP-gesprek.

1. Selecteer **Start** en vervolgens **Ja** wanneer u wordt gevraagd het runbook te starten.

   ![Runbook starten](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Er wordt een taakvenster geopend voor de runbook-taak die is gemaakt. Laat in dit geval het deelvenster open, zodat u de voortgang van de taak bekijken.

1. Houd er rekening mee dat de functiestatus wordt weergegeven in **het overzicht van de taak**. Deze status komt overeen met de statussen die u hebt gezien bij het testen van het runbook.

   ![Taakoverzicht](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Zodra de status van het runbook voltooid wordt weergegeven, klikt u op **Uitvoer**. De pagina Uitvoer wordt geopend, `Hello World` waar u uw bericht zien.

   ![Taakoverzicht](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Sluit de pagina Uitvoer.

1. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. U moet `Hello World` alleen zien in de uitvoerstroom. Houd er rekening mee dat in het deelvenster Streams andere streams voor een runbook-taak kunnen worden weergegeven, zoals verbose- en foutstreams, als de runbook naar deze streams schrijft.

   ![Taakoverzicht](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Sluit het deelvenster Streams en het deelvenster Taak om terug te keren naar de pagina MyFirstRunbook.
1. Klik op **Vacatures** onder **Resources** om de pagina Vacatures voor dit runbook te openen. Op deze pagina worden alle vacatures weergegeven die door uw runbook zijn gemaakt. U mag slechts één taak zien die wordt vermeld, omdat u de taak slechts één keer hebt uitgevoerd.

   ![Taken](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Klik op de taaknaam om hetzelfde taakvenster te openen dat u hebt bekeken toen u het runbook begon. Gebruik dit deelvenster om de details weer te geven van een taak die voor het runbook is gemaakt.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Stap 5: verificatie toevoegen voor het beheren van Azure-resources

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. U wilt dat er Azure-resources mee worden beheerd. Het kan dat niet doen, tenzij het verifieert met behulp van de referenties voor het abonnement. Verificatie maakt gebruik van de [cmdlet Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0)

>[!NOTE]
>Voor `Add-AzAccount` PowerShell-runbooks `Add-AzureRMAccount` en zijn `Connect-AzAccount`aliassen voor . U deze cmdlets gebruiken of u [uw modules](automation-update-azure-modules.md) in uw Automation-account updaten naar de nieuwste versies. Mogelijk moet u uw modules bijwerken, zelfs als u zojuist een nieuw Automation-account hebt gemaakt.

1. Navigeer naar de pagina MyFirstRunbook-Workflow en open de tekstuele editor door op **Bewerken te**klikken.
2. Verwijder `Write-Output` de regel.
3. Plaats de cursor op een lege regel tussen de accolades.
4. Typ of kopieer en plak de volgende code, waarmee de verificatie wordt verwerkt met uw Automation Run As-account.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Klik **op Deelvenster Testen,** zodat u het runbook testen.
1. Klik op **Start** om de test te starten. Zodra deze is voltooid, ziet u uitvoer die vergelijkbaar is met de volgende, waarbij basisgegevens van uw account worden weergegeven. Deze actie bevestigt dat de referentie geldig is.

   ![Verifiëren](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Stap 6: code toevoegen om een virtuele machine te starten

Nu uw runbook is geauthenticerend op het Azure-abonnement, u resources beheren. Laten we een opdracht toevoegen om een virtuele machine te starten. U elke virtuele machine kiezen in uw Azure-abonnement en voor nu bent u hardcoding die naam in het runbook. Als u resources beheert voor meerdere abonnementen, `AzContext` moet u de parameter gebruiken met de [cmdlet Get-AzContext.](/powershell/module/az.accounts/get-azcontext)

1. Geef de naam en resourcegroepnaam van de VM op om te beginnen met het invoeren van een aanroep naar de [cmdlet Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) zoals hieronder weergegeven. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Sla het runbook op en klik op **Het deelvenster Testen,** zodat u het testen.
1. Klik op **Start** om de test te starten. Controleer na het voltooien of de VM is gestart.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Stap 7: een invoerparameter toevoegen aan het runbook

Uw runbook start momenteel de VM die u hardcoded hebt in het runbook. Het is handiger als u de VM opgeven wanneer het runbook wordt gestart. Laten we invoerparameters toevoegen aan het runbook om die functionaliteit te bieden.

1. Voeg variabelen voor `VMName` `ResourceGroupName` de parameters en parameters toe aan het `Start-AzVM` runbook en gebruik de variabelen met de cmdlet zoals hieronder weergegeven.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Sla het runbook op en open het testvenster. U nu waarden opgeven voor de twee invoervariabelen die in de test zitten.
3. Sluit het testvenster.
4. Klik op **Publiceren** om de nieuwe versie van het runbook te publiceren.
5. Stop de VM die u hebt gestart.
6. Klik op **Starten** om het runbook te starten. 
7. Typ de waarden voor **VMNAME** en **RESOURCEGROUPNAME** voor de VM die u gaat starten.

   ![Runbook starten](media/automation-first-runbook-textual/automation-pass-params.png)

8. Controleer wanneer het runbook is voltooid of de VM is gestart.

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over PowerShell, inclusief taalverwijzingen en leermodules, de [PowerShell-documenten](https://docs.microsoft.com/powershell/scripting/overview).
* Zie [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)voor een PowerShell-cmdletreferentie.
* Zie Mijn eerste grafische runbook om aan de slag te gaan met grafische [runbooks.](automation-first-runbook-graphical.md)
* Zie [Mijn eerste PowerShell-runbook](automation-first-runbook-textual-powershell.md)om aan de slag te gaan met PowerShell-runbooks.
* Zie [Azure Automation runbook-typen](automation-runbook-types.md)voor meer informatie over runbook-typen en hun voordelen en beperkingen.
* Zie [Native PowerShell-scriptondersteuning in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)voor meer informatie over powershell-scriptondersteuningsfuncties.