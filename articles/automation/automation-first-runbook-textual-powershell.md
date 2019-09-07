---
title: Mijn eerste Power shell-runbook in Azure Automation
description: Zelfstudie die u helpt bij het maken, testen en publiceren van een eenvoudig PowerShell-runbook.
keywords: azure powershell, zelfstudie powershell-script, powershell-automatisering
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae9daaf797d3d82200ee094b63bad1f5c1ff68cc
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743822"
---
# <a name="my-first-powershell-runbook"></a>Mijn eerste PowerShell-runbook

> [!div class="op_single_selector"]
> * [Grafisch](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-werkstroom](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

In deze zelfstudie wordt stap voor stap het maken van een [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) in Azure Automation beschreven. U begint met een eenvoudig runbook dat u test en publiceert terwijl u leert hoe u de status van de runbook-taak kunt volgen. Vervolgens wijzigt u het runbook zodanig dat Azure-resources daadwerkelijk worden beheerd, in dit geval door een virtuele machine van Azure te starten. Ten slotte maakt u het runbook betrouwbaarder door runbook-para meters toe te voegen.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-quickstart-create-account.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. U stopt en start deze machine, dus het mag geen productie-VM zijn.
* Mogelijk moet u [uw Azure-modules bijwerken](automation-update-azure-modules.md) op basis van de cmdlets die u gebruikt.

## <a name="create-new-runbook"></a>Nieuw runbook maken

U begint met het maken van een eenvoudig runbook waarmee de tekst *Hallo wereld*worden uitgevoerd.

1. Open uw Automation-account in Azure Portal.
2. Klik onder **proces automatisering** op **Runbooks** om de lijst met Runbooks te openen.
3. Maak een nieuw runbook door te klikken op de knop **+ een Runbook toevoegen** en vervolgens **een nieuw runbook te maken**.
4. Geef het runbook de naam *MyFirstRunbook-PowerShell*.
5. In dit geval gaat u een [Power shell-runbook](automation-runbook-types.md#powershell-runbooks) maken, dus Selecteer **Power shell** voor het **type runbook**.
6. Klik op **Maken** om het runbook te maken en de teksteditor te openen.

## <a name="add-code-to-the-runbook"></a>Code toevoegen aan het runbook

U kunt de code rechtstreeks in het runbook typen of u kunt cmdlets, runbooks en assets selecteren in het besturingselement Bibliotheek en deze laten toevoegen aan het runbook met eventuele gerelateerde parameters. Voor dit scenario typt u direct in het runbook.

1. Het runbook is momenteel leeg. Typ *Write-Output Hallo wereld.* in de hoofdtekst van het script.

   ![Hallo wereld](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Klik op **Opslaan** om het runbook op te slaan.

## <a name="step-3---test-the-runbook"></a> Het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, wilt u het testen om er zeker van te zijn dat het goed werkt. Wanneer u een runbook test, voert u de **concept**versie uit en geeft u de uitvoer interactief weer.

1. Klik op **Testvenster** om het testvenster te openen.
2. Klik op **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.
3. Een [runbooktaak](automation-runbook-execution.md) wordt gemaakt en de status ervan wordt weergegeven.

   De taak status wordt in de *wachtrij geplaatst* om aan te geven dat er wordt gewacht tot een runbook worker in de Cloud beschikbaar is. Het wordt verplaatst naar het *begin* wanneer een werk nemer de taak claimt en vervolgens *wordt uitgevoerd* wanneer het runbook daad werkelijk wordt uitgevoerd.

4. Wanneer de runbooktaak is voltooid, wordt de uitvoer ervan weergegeven. In uw geval ziet u *Hallo wereld*.

   ![Uitvoer testvenster](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Sluit het testvenster om terug te gaan naar het papier.

## <a name="publish-and-start-the-runbook"></a>Het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog steeds in de modus Concept. Het moet worden gepubliceerd voordat u deze in productie kunt uitvoeren.  Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In uw geval hebt u nog geen gepubliceerde versie omdat u het runbook zojuist hebt gemaakt.

1. Klik op **Publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer hierom wordt gevraagd.
1. Als u naar links schuift om het runbook nu weer te geven in het deel venster **Runbooks** , wordt de **ontwerp status** **gepubliceerd**weer gegeven.
1. Schuif terug naar rechts om het deelvenster voor **MyFirstRunbook-PowerShell** weer te geven.
   Met de opties bovenaan kunnen we het runbook starten, het runbook weergeven, plannen dat het op een bepaald moment in de toekomst start of een [webhook](automation-webhooks.md) maken, zodat het kan worden gestart via een HTTP-aanroep.
1. U wilt het runbook starten, dus klik op **Start** en klik vervolgens op **OK** wanneer de pagina runbook starten wordt geopend.
1. Er wordt een taak pagina geopend voor de runbook-taak die u hebt gemaakt. U kunt dit deel venster sluiten, maar in dit geval laat u het geopend, zodat u de voortgang van de taak kunt bekijken.
1. De taak status wordt weer gegeven in **taak samenvatting** en komt overeen met de statussen die u hebt gezien tijdens het testen van het runbook.

   ![Taakoverzicht](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Zodra de status van het runbook *is voltooid*, klikt u onder **overzicht** op **uitvoer**. Het deel venster uitvoer wordt geopend en u kunt uw *Hallo wereld*zien.

   ![Taakuitvoer](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Sluit de uitvoer pagina.
1. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. U moet alleen *Hallo wereld* zien in de uitvoer stroom, maar deze uitvoer kan andere stromen weer geven voor een runbook-taak, zoals uitgebreid en fout als het runbook hiernaar schrijft.

   ![Alle logboeken](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Sluit de pagina stromen en de pagina taak om terug te keren naar de pagina MyFirstRunbook-Power shell.
1. Klik onder **Details**op **taken** om het deel venster taken voor dit runbook te openen. Op deze pagina worden alle taken weer gegeven die door dit runbook zijn gemaakt. U zou slechts één weergegeven taak moeten zien, aangezien de taak slechts eenmaal is uitgevoerd.

   ![Takenlijst](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. U kunt op deze taak klikken om hetzelfde taakvenster te openen dat u hebt bekeken toen u het runbook startte. Met deze actie kunt u teruggaan in de tijd en de details weer geven van alle taken die voor een bepaald runbook zijn gemaakt.

## <a name="add-authentication-to-manage-azure-resources"></a>Verificatie toevoegen voor het beheren van Azure-resources

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. U wilt dat er Azure-resources mee worden beheerd. Dit is niet mogelijk, tenzij u de verificatie uitvoert met een run as-verbinding die automatisch wordt gemaakt wanneer u uw Automation-account maakt. U gebruikt de run as-verbinding met de cmdlet **Connect-AzureRmAccount** . Als u resources beheert over meerdere abonnementen, moet u de para meter **-AzureRmContext** gebruiken in combi natie met [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Open de tekst editor door te klikken op **bewerken** op de pagina MyFirstRunbook-Power shell.
1. U hebt de regel voor **Write-output** niet meer nodig, dus kunt u deze verwijderen.
1. Typ of kopieer en plak de volgende code waarmee de verificatie wordt uitgevoerd met uw Uitvoeren als-account voor Automation:

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** en **login-AzureRmAccount** zijn nu aliassen voor **Connect-AzureRmAccount**. Als de cmdlet **Connect-AzureRMAccount** niet bestaat, kunt u **add-AzureRMAccount** of **login-AzureRMAccount**gebruiken, of u kunt uw modules in uw Automation-account bijwerken naar de nieuwste versies.

1. Klik op **test venster** zodat u het runbook kunt testen.
1. Klik op **Start** om de test te starten. Zodra deze is voltooid, ontvangt u uitvoer zoals hieronder afgebeeld, waarin basisinformatie van uw account wordt weergegeven. Deze uitvoer bevestigt dat het run as-account geldig is.

   ![Authenticeren](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Code toevoegen om een virtuele machine te starten

Nu uw runbook is geverifieerd voor uw Azure-abonnement, kunt u resources beheren. U voegt een opdracht toe om een virtuele machine te starten. U kunt een wille keurige virtuele machine in uw Azure-abonnement kiezen en u hebt nu die naam voorlopig hardcoderen we in het runbook.

1. Na *Connect-AzureRmAccount*typt u *Start-AzureRmVM-name ' VMName '-ResourceGroupName ' NameofResourceGroup '* om de naam en de naam van de resource groep op te geven van de virtuele machine die moet worden gestart.

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Sla het runbook op en klik vervolgens op **test venster** zodat u het kunt testen.
1. Klik op **Start** om de test te starten. Nadat deze is voltooid, controleert u of de virtuele machine is gestart.

## <a name="add-an-input-parameter"></a>Een invoer parameter toevoegen

Met uw runbook wordt momenteel de virtuele machine gestart die u in het runbook hebt hardcoded, maar dit is handiger als u de virtuele machine opgeeft wanneer het runbook wordt gestart. U voegt invoer parameters aan het runbook toe om deze functionaliteit te bieden.

1. Voeg para meters voor *VMName* en *ResourceGroupName* toe aan het runbook en gebruik deze variabelen met de cmdlet **Start-AzureRmVM** , zoals in het volgende voor beeld.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Sla het runbook op en open het testvenster. U kunt nu waarden opgeven voor de twee invoervariabelen die in de test worden gebruikt.
1. Sluit het testvenster.
1. Klik op **Publiceren** om de nieuwe versie van het runbook te publiceren.
1. Stop de virtuele machine die u in de vorige stap hebt gestart.
1. Klik op **OK** om het runbook te starten. Typ waarden voor **VMName** en **ResourceGroupName** voor de virtuele machine die u wilt starten.<br><br> ![Parameter doorgeven](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Wanneer het runbook is voltooid, controleert u of de virtuele machine is gestart.

## <a name="differences-from-powershell-workflow"></a>Verschillen met PowerShell Workflow

PowerShell-runbooks hebben dezelfde levenscyclus, mogelijkheden en beheer als PowerShell Workflow-runbooks, maar er zijn enkele verschillen en beperkingen:

1. PowerShell-runbooks worden snel uitgevoerd vergeleken met PowerShell Workflow-runbooks omdat ze geen compilatiestap hebben.
2. Power shell workflow-runbooks ondersteunen controle punten, met behulp van controle punten, waarna Power shell-werk stroom-runbooks vanaf elk punt in het runbook kunnen worden hervat. Power shell-runbooks kunnen alleen vanaf het begin worden hervat.
3. Power shell workflow-runbooks ondersteunen parallelle en seriële uitvoering. Power shell-runbooks kunnen alleen opdrachten serieel uitvoeren.
4. In een Power shell workflow-runbook kan een activiteit, een opdracht of een script blok een eigen runs Pace hebben. In een Power shell-runbook wordt alles in een script in één runs Pace uitgevoerd. Er zijn ook een aantal [syntactische verschillen](https://technet.microsoft.com/magazine/dn151046.aspx) tussen een systeemeigen PowerShell-runbook en een PowerShell Workflow-runbook.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Power shell-documenten](/powershell/scripting/overview)voor meer informatie over Power shell, inclusief taal referentie-en leer modules.
* Zie [Mijn eerste grafische runbook](automation-first-runbook-graphical.md) om aan de slag te gaan met grafische runbooks
* Zie [Mijn eerste PowerShell Workflow-runbook](automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks
* Zie [Azure Automation-runbooktypen](automation-runbook-types.md) voor meer informatie over runbooktypen, hun voordelen en beperkingen
* Zie [Systeemeigen PowerShell-scriptondersteuning in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) voor meer informatie over de functie voor PowerShelll-scriptondersteuning
