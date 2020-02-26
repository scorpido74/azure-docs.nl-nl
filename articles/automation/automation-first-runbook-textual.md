---
title: Mijn eerste Power shell workflow-runbook in Azure Automation
description: Zelfstudie die u helpt bij het maken, testen en publiceren van een eenvoudig tekstrunbook met behulp van PowerShell Workflow.
keywords: powershell-werkstroom, voorbeelden powershell-werkstroom, werkstroom powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: b96860afd649f33936ee8dd2954e6873f908a369
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605073"
---
# <a name="my-first-powershell-workflow-runbook"></a>Mijn eerste PowerShell Workflow-runbook

> [!div class="op_single_selector"]
> * [Grafisch](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-werkstroom](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

In deze zelfstudie wordt stap voor stap het maken van een [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) in Azure Automation beschreven. Begin met een eenvoudig runbook dat u test en publiceert terwijl u leert hoe u de status van de runbook-taak kunt volgen. Wijzig vervolgens het runbook om Azure-resources daad werkelijk te beheren, zoals wordt geïllustreerd door een virtuele machine van Azure te starten. Maak het runbook voor het laatst krachtig door runbook-para meters toe te voegen.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. Omdat u deze machine stopt en start, mag het geen productie-VM zijn.

## <a name="step-1---create-new-runbook"></a>Stap 1: nieuw runbook maken

Begin met het maken van een eenvoudig runbook dat de tekst ' Hallo wereld ' uitvoert.

1. Open uw Automation-account in Azure Portal.

   Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U hebt als het goed is al enkele assets. De meeste van deze activa zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. U moet ook beschikken over de referentie-Asset die aan uw abonnement is gekoppeld.
 
1. Selecteer **runbooks** onder **proces automatisering** om de lijst met Runbooks te openen.
1. Maak een nieuw runbook door **+ een Runbook maken**te selecteren.
1. Geef het runbook de naam MyFirstRunbook-workflow.
1. In dit geval gaat u een [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks)maken. Selecteer daarom **Power shell-werk stroom** voor het **type Runbook**.
1. Klik op **Maken** om het runbook te maken en de teksteditor te openen.

## <a name="step-2---add-code-to-the-runbook"></a>Stap 2: code toevoegen aan het runbook

U kunt code rechtstreeks in het runbook typen of u kunt cmdlets, runbooks en assets selecteren in het besturings element bibliotheek en deze toevoegen aan het runbook met alle gerelateerde para meters. Voor dit scenario typt u code rechtstreeks in het runbook.

1. Uw runbook is momenteel leeg met alleen het vereiste **werk stroom** sleutelwoord, de naam van het runbook en de accolades die de hele werk stroom omsluiten.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Typ `Write-Output "Hello World"` tussen de accolades.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Klik op **Opslaan** om het runbook op te slaan.

## <a name="step-3---test-the-runbook"></a>Stap 3: het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, moet u het testen om er zeker van te zijn dat het goed werkt. Wanneer een runbook wordt getest, wordt de concept versie uitgevoerd en kunt u de uitvoer interactief bekijken.

1. Selecteer **test venster** om het deel venster test te openen.
1. Klik op **Start** om de test te starten, waarbij u alleen de optie ingeschakeld kunt testen.
1. Houd er rekening mee dat een [runbook-taak](automation-runbook-execution.md) wordt gemaakt en dat de status ervan wordt weer gegeven in het deel venster.

   De taak status wordt in de wachtrij geplaatst, wat aangeeft dat de taak wacht totdat een runbook worker in de Cloud beschikbaar is. De status verandert in starten wanneer een werk nemer de taak claimt. Ten slotte wordt de status actief wanneer het runbook daad werkelijk wordt uitgevoerd.

1. Wanneer de runbook-taak is voltooid, wordt de uitvoer weer gegeven in het deel venster testen. In dit geval ziet u "Hallo wereld".

   ![Hallo wereld](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Sluit het testvenster om terug te gaan naar het papier.

## <a name="step-4---publish-and-start-the-runbook"></a>Stap 4: het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog in de concept modus. U moet deze publiceren voordat u deze in productie kunt uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie omdat het runbook zojuist is gemaakt.

1. Klik op **Publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer hierom wordt gevraagd.
1. Schuif naar links om het runbook in het deel venster **Runbooks** weer te geven en controleer of de waarde voor de **ontwerp status** is ingesteld op gepubliceerd.
1. Schuif terug naar rechts om het deel venster voor MyFirstRunbook-workflow weer te geven.

   Met de opties bovenaan kunt u het runbook nu starten, een toekomstige start tijd plannen of een [webhook](automation-webhooks.md) maken zodat het runbook kan worden gestart via een http-aanroep.

1. Selecteer **Start** en vervolgens **Ja** wanneer u wordt gevraagd het runbook te starten.

   ![Runbook starten](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Er wordt een taak venster geopend voor de runbook-taak die is gemaakt. In dit geval laat u het deel venster geopend, zodat u de voortgang van de taak kunt bekijken.

1. Houd er rekening mee dat de taak status wordt weer gegeven in **taak samenvatting**. Deze status komt overeen met de statussen die u hebt gezien bij het testen van het runbook.

   ![Taakoverzicht](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Zodra de status van het runbook is voltooid, klikt u op **uitvoer**. De uitvoer pagina wordt geopend, waar het bericht Hallo wereld kan worden weer gegeven.

   ![Taakoverzicht](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Sluit de uitvoer pagina.

1. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. Zie alleen ' Hallo wereld ' in de uitvoer stroom. Houd er rekening mee dat in het deel venster stromen andere stromen kunnen worden weer gegeven voor een runbook-taak, zoals uitgebreide en fout stromen, als het runbook hiernaar schrijft.

   ![Taakoverzicht](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Sluit het deel venster streams en het taak venster om terug te keren naar de pagina MyFirstRunbook.
1. Klik op **taken** onder **resources** om de pagina taken voor dit runbook te openen. Op deze pagina worden alle taken weer gegeven die door uw runbook zijn gemaakt. Er wordt slechts één taak weer gegeven, omdat u de taak slechts één keer hebt uitgevoerd.

   ![Taken](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Klik op de taak naam om hetzelfde taak venster te openen dat u hebt bekeken tijdens het starten van het runbook. In dit deel venster kunt u de details weer geven van alle taken die voor het runbook zijn gemaakt.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Stap 5: verificatie toevoegen voor het beheren van Azure-resources

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. U wilt dat er Azure-resources mee worden beheerd. Dit kan alleen gebeuren als de verificatie wordt uitgevoerd met behulp van de referenties voor het abonnement. Verificatie maakt gebruik van de cmdlet **Connect-AzAccount** .

>[!NOTE]
>Voor Power shell-runbooks zijn **add-AzAccount** en **add-AzureRMAccount** aliassen voor **Connect-AzAccount**. U kunt deze cmdlets gebruiken of u kunt [uw modules](automation-update-azure-modules.md) in uw Automation-account bijwerken naar de meest recente versies. Mogelijk moet u uw modules bijwerken, zelfs als u zojuist een nieuw Automation-account hebt gemaakt.

1. Ga naar de pagina MyFirstRunbook-werk stroom en open de tekst editor door te klikken op **bewerken**.
2. Verwijder de regel voor **Write-output** .
3. Plaats de cursor op een lege regel tussen de accolades.
4. Typ of kopieer en plak de volgende code waarmee de verificatie wordt verwerkt met het run as-account van Automation.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Klik op **test venster** zodat u het runbook kunt testen.
1. Klik op **Start** om de test te starten. Zodra het proces is voltooid, ziet u uitvoer die lijkt op het volgende, waarbij basis informatie uit uw account wordt weer gegeven. Met deze actie wordt bevestigd dat de referentie geldig is.

   ![Verifiëren](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Stap 6: code toevoegen om een virtuele machine te starten

Nu uw runbook is geverifieerd voor uw Azure-abonnement, kunt u resources beheren. Laten we een opdracht toevoegen om een virtuele machine te starten. U kunt een wille keurige VM in uw Azure-abonnement kiezen en u hebt nu die naam hardcoding in het runbook. Als u resources beheert over meerdere abonnementen, moet u de para meter *AzContext* gebruiken met de cmdlet [Get-AzContext](/powershell/module/az.accounts/get-azcontext) .

1. Geef de naam en de naam van de resource groep op van de virtuele machine die u wilt starten door een aanroep naar de [Start-AzVM-](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) cmdlet in te voeren, zoals hieronder wordt weer gegeven. 

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

1. Sla het runbook op en klik vervolgens op **test venster** zodat u het kunt testen.
1. Klik op **Start** om de test te starten. Als de VM is voltooid, controleert u of de virtuele machine is gestart.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Stap 7: een invoerparameter toevoegen aan het runbook

Met uw runbook wordt momenteel de virtuele machine gestart die u hebt hardcoded in het runbook. Het is handiger als u de virtuele machine kunt opgeven wanneer het runbook wordt gestart. Voeg invoer parameters toe aan het runbook om deze functionaliteit te bieden.

1. Voeg waarden voor *VMName* en *ResourceGroupName* toe aan het runbook en gebruik de bijbehorende variabelen met de cmdlet **Start-AzVM** , zoals hieronder wordt weer gegeven.

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

2. Sla het runbook op en open het testvenster. U kunt nu waarden opgeven voor de twee invoer variabelen die in de test zijn.
3. Sluit het testvenster.
4. Klik op **Publiceren** om de nieuwe versie van het runbook te publiceren.
5. Stop de virtuele machine die u hebt gestart.
6. Klik op **Starten** om het runbook te starten. 
7. Typ de waarden voor **VMNAME** en **RESOURCEGROUPNAME** voor de virtuele machine die u wilt starten.

   ![Runbook starten](media/automation-first-runbook-textual/automation-pass-params.png)

8. Wanneer het runbook is voltooid, controleert u of de virtuele machine is gestart.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Power shell-documenten](https://docs.microsoft.com/powershell/scripting/overview)voor meer informatie over Power shell, inclusief taal referentie-en leer modules.
* Zie [mijn eerste grafische runbook](automation-first-runbook-graphical.md)om aan de slag te gaan met grafische runbooks.
* Zie [Mijn eerste PowerShell-runbook](automation-first-runbook-textual-powershell.md) om aan de slag te gaan met PowerShell-runbooks.
* Zie [Azure Automation runbook-typen](automation-runbook-types.md)voor meer informatie over de typen runbook en hun voor delen en beperkingen.
* Zie [systeem eigen ondersteuning voor Power shell-scripts in azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)voor meer informatie over functies voor het ondersteunen van Power shell-scripts.
