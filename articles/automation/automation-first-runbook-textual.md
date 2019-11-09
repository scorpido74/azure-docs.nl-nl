---
title: Mijn eerste Power shell workflow-runbook in Azure Automation
description: Zelfstudie die u helpt bij het maken, testen en publiceren van een eenvoudig tekstrunbook met behulp van PowerShell Workflow.
keywords: powershell-werkstroom, voorbeelden powershell-werkstroom, werkstroom powershell
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 09/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4973a5215d93a1ce9d0a80f83264dd8c1fbe0888
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887226"
---
# <a name="my-first-powershell-workflow-runbook"></a>Mijn eerste PowerShell Workflow-runbook

> [!div class="op_single_selector"]
> * [Grafisch](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-werkstroom](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

In deze zelfstudie wordt stap voor stap het maken van een [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) in Azure Automation beschreven. U begint met een eenvoudig runbook dat u test en publiceert terwijl u uitlegt hoe u de status van de runbook-taak kunt volgen. Vervolgens wijzigt u het runbook zodanig dat Azure-resources daadwerkelijk worden beheerd, in dit geval door een virtuele machine van Azure te starten. Ten slotte maakt u het runbook betrouwbaarder door runbook-para meters toe te voegen.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) om het runbook te bevatten en te verifiëren voor Azure-resources.  Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. u stopt en start deze machine, dus het mag geen productie-VM zijn.

## <a name="step-1---create-new-runbook"></a>Stap 1: nieuw runbook maken

U begint met het maken van een eenvoudig runbook waarmee de tekst *Hallo wereld*worden uitgevoerd.

1. Open uw Automation-account in Azure Portal.

   Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U hebt als het goed is al enkele assets. De meeste van deze assets zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. Ook moet u de referentieasset hebben die wordt genoemd in de [vereisten](#prerequisites).

1. Klik onder **proces automatisering** op **Runbooks** om de lijst met Runbooks te openen.
1. Maak een nieuw runbook door te klikken op de knop **+ een Runbook toevoegen** en vervolgens **een nieuw runbook te maken**.
1. Geef het runbook de naam *MyFirstRunbook-Workflow*.
1. In dit geval gaat u een [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) maken, dus Selecteer **Power shell-werk stroom** voor het **runbook-type**.
1. Klik op **Maken** om het runbook te maken en de teksteditor te openen.

## <a name="step-2---add-code-to-the-runbook"></a>Stap 2: code toevoegen aan het runbook

U kunt de code rechtstreeks in het runbook typen of u kunt cmdlets, runbooks en assets selecteren in het besturingselement Bibliotheek en deze laten toevoegen aan het runbook met eventuele gerelateerde parameters. Voor dit scenario typt u direct in het runbook.

1. Uw runbook is momenteel leeg met alleen het vereiste *werk stroom* sleutelwoord, de naam van uw runbook en de accolades die de hele werk stroom encases.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Typ *Write-Output "Hallo wereld".* tussen de accolades.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Klik op **Opslaan** om het runbook op te slaan.

## <a name="step-3---test-the-runbook"></a>Stap 3: het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, wilt u het testen om er zeker van te zijn dat het goed werkt. Wanneer u een runbook test, voert u de **concept**versie uit en geeft u de uitvoer interactief weer.

1. Klik op **Testvenster** om het testvenster te openen.
1. Klik op **Start** om de test te starten. Deze optie moet de enige ingeschakelde optie zijn.
1. Een [runbooktaak](automation-runbook-execution.md) wordt gemaakt en de status ervan wordt weergegeven.

   De taak status wordt gestart als een *wachtrij* die aangeeft dat er wordt gewacht tot een runbook worker in de Cloud beschikbaar is. Het wordt verplaatst naar het *begin* wanneer een werk nemer de taak claimt en vervolgens *wordt uitgevoerd* wanneer het runbook daad werkelijk wordt uitgevoerd.

1. Wanneer de runbooktaak is voltooid, wordt de uitvoer ervan weergegeven. In uw geval ziet u *Hallo wereld*.

   ![Hallo wereld](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Sluit het testvenster om terug te gaan naar het papier.

## <a name="step-4---publish-and-start-the-runbook"></a>Stap 4: het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog steeds in de modus Concept. U moet deze publiceren voordat u deze in productie kunt uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In uw geval hebt u nog geen gepubliceerde versie omdat u het runbook zojuist hebt gemaakt.

1. Klik op **Publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer hierom wordt gevraagd.
1. Als u naar links schuift om het runbook nu weer te geven in het deel venster **Runbooks** , wordt de **ontwerp status** **gepubliceerd**weer gegeven.
1. Schuif terug naar rechts om het deelvenster voor **MyFirstRunbook-Workflow** weer te geven.
   Met de opties bovenaan kunnen we het runbook starten, plannen dat het op een bepaald moment in de toekomst start of een [webhook](automation-webhooks.md) maken zodat het kan worden gestart via een HTTP-aanroep.
1. u wilt het runbook starten, dus klik op **Start** en vervolgens op **Ja** wanneer u hierom wordt gevraagd.

   ![Runbook starten](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Er wordt een taak venster geopend voor de runbook-taak die u hebt gemaakt. u kunt dit deel venster sluiten, maar in dit geval laat u het geopend, zodat u de voortgang van de taak kunt bekijken.
1. De taak status wordt weer gegeven in **taak samenvatting** en komt overeen met de statussen die u hebt gezien tijdens het testen van het runbook.

   ![Taakoverzicht](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Zodra voor het runbook de status *Voltooid* wordt weergegeven, klikt u op **Uitvoer**. Het deel venster uitvoer wordt geopend en u kunt uw *Hallo wereld*zien.

   ![Taakoverzicht](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Sluit het deelvenster Uitvoer.
1. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. u moet alleen *Hallo wereld* zien in de uitvoer stroom, maar deze weer gave kan andere stromen weer geven voor een runbook-taak, zoals uitgebreid en fout als het runbook hiernaar schrijft.

   ![Taakoverzicht](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Sluit de pagina stromen en de pagina taak om terug te keren naar de pagina MyFirstRunbook.
1. Klik op **taken** om de pagina taken voor dit runbook te openen. Op deze pagina worden alle taken weer gegeven die door dit runbook zijn gemaakt. Er wordt slechts één taak weer gegeven, omdat u de taak slechts één keer hebt uitgevoerd.

   ![Taken](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. U kunt op deze taak klikken om dezelfde taak pagina te openen die u hebt bekeken tijdens het starten van het runbook. Met deze actie kunt u teruggaan in de tijd en de details weer geven van alle taken die voor een bepaald runbook zijn gemaakt.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Stap 5: verificatie toevoegen voor het beheren van Azure-resources

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. U wilt dat er Azure-resources mee worden beheerd. Dit kan echter niet, tenzij u hebt geverifieerd met de referenties die in de [vereisten](#prerequisites)worden genoemd. Dit doet u met de cmdlet **Connect-AzAccount** .

1. Open de teksteditor door te klikken op **Bewerken** in het deelvenster MyFirstRunbook-Workflow.
2. u hebt de regel voor **Write-output** niet meer nodig, dus kunt u deze verwijderen.
3. Plaats de cursor op een lege regel tussen de accolades.
4. Typ of kopieer en plak de volgende code waarmee de verificatie wordt uitgevoerd met uw Uitvoeren als-account voor Automation:

   ```powershell-interactive
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

   > [!IMPORTANT]
   > **Add-AzAccount** en **login-AzAccount** zijn nu aliassen voor **Connect-AzAccount**. Als de cmdlet **Connect-AzAccount** niet bestaat, kunt u **add-AzAccount** of **login-AzAccount**gebruiken, of u kunt [uw modules](automation-update-azure-modules.md) in uw Automation-account bijwerken naar de nieuwste versies.

> [!NOTE]
> Mogelijk moet u [uw modules bijwerken](automation-update-azure-modules.md) , zelfs als u zojuist een nieuw Automation-account hebt gemaakt.

1. Klik op **test venster** zodat u het runbook kunt testen.
1. Klik op **Start** om de test te starten. Zodra deze is voltooid, ontvangt u uitvoer zoals hieronder afgebeeld, waarin basisinformatie van uw account wordt weergegeven. Met deze actie wordt bevestigd dat de referentie geldig is.

   ![Verifiëren](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Stap 6: code toevoegen om een virtuele machine te starten

Nu uw runbook is geverifieerd voor uw Azure-abonnement, kunt u resources beheren. u voegt een opdracht toe om een virtuele machine te starten. U kunt een wille keurige virtuele machine in uw Azure-abonnement kiezen en u hebt nu die naam hardcoding in het runbook. Als u resources beheert over meerdere abonnementen, moet u de para meter **-AzContext** gebruiken in combi natie met [Get-AzContext](/powershell/module/az.accounts/get-azcontext).

1. Na *Connect-AzAccount*typt u *Start-AzVM-name ' VMName '-ResourceGroupName ' NameofResourceGroup '* om de naam en de naam van de resource groep op te geven van de virtuele machine die moet worden gestart.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Sla het runbook op en klik vervolgens op **test venster** zodat u het kunt testen.
1. Klik op **Start** om de test te starten. Nadat deze is voltooid, controleert u of de virtuele machine is gestart.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Stap 7: een invoerparameter toevoegen aan het runbook

met uw runbook wordt momenteel de virtuele machine gestart die u in het runbook hebt hardcoded, maar dit is handiger als u de virtuele machine zou kunnen opgeven wanneer het runbook wordt gestart. U voegt invoer parameters aan het runbook toe om deze functionaliteit te bieden.

1. Voeg para meters voor *VMName* en *ResourceGroupName* toe aan het runbook en gebruik deze variabelen met de cmdlet **Start-AzVM** , zoals in het onderstaande voor beeld.

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
5. Stop de virtuele machine die u in de vorige stap hebt gestart.
6. Klik op **Starten** om het runbook te starten. Typ waarden voor **VMName** en **ResourceGroupName** voor de virtuele machine die u wilt starten.

   ![Runbook starten](media/automation-first-runbook-textual/automation-pass-params.png)

7. Wanneer het runbook is voltooid, controleert u of de virtuele machine is gestart.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Power shell-documenten](https://docs.microsoft.com/powershell/scripting/overview)voor meer informatie over Power shell, inclusief taal referentie-en leer modules.
* Zie [Mijn eerste grafische runbook](automation-first-runbook-graphical.md) om aan de slag te gaan met grafische runbooks
* Zie [Mijn eerste PowerShell-runbook](automation-first-runbook-textual-powershell.md) om aan de slag te gaan met PowerShell-runbooks
* Zie [Azure Automation-runbooktypen](automation-runbook-types.md) voor meer informatie over runbooktypen, hun voordelen en beperkingen
* Zie [Systeemeigen PowerShell-scriptondersteuning in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) voor meer informatie over de functie voor PowerShelll-scriptondersteuning
