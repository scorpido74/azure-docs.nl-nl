---
title: Een Power shell-runbook maken in Azure Automation
description: Zelf studie laat zien hoe u een eenvoudig Power shell-runbook maakt, test en publiceert.
keywords: azure powershell, zelfstudie powershell-script, powershell-automatisering
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: b94969ff0973f68b57a1f43aa9d3205901bb1436
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81726156"
---
# <a name="tutorial-create-a-powershell-runbook"></a>Zelf studie: een Power shell-runbook maken

In deze zelfstudie wordt stap voor stap het maken van een [PowerShell-runbook](../automation-runbook-types.md#powershell-runbooks) in Azure Automation beschreven. Power shell-runbooks zijn gebaseerd op Windows Power shell. U bewerkt de code van het runbook rechtstreeks met behulp van de tekst editor in het Azure Portal.

> [!div class="checklist"]
> * Een eenvoudig Power shell-runbook maken
> * Het runbook testen en publiceren
> * De status van de runbook-taak uitvoeren en volgen
> * Het runbook bijwerken om een virtuele Azure-machine te starten met runbook-para meters

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](../automation-update-azure-modules.md).

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voor delen van uw MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](../automation-quickstart-create-account.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. Omdat u deze machine stopt en start, mag het geen productie-VM zijn.
* Indien nodig kunt u [Azure-modules importeren](../shared-resources/modules.md) of [modules bijwerken](../automation-update-azure-modules.md) op basis van de cmdlets die u gebruikt.

## <a name="differences-from-powershell-workflow-runbooks"></a>Verschillen met Power shell workflow-runbooks

Power shell-runbooks hebben dezelfde levens cyclus, mogelijkheden en beheer als Power shell workflow-runbooks. Er zijn echter enkele verschillen en beperkingen.

| Kenmerk  | Power shell-Runbooks | Power shell workflow-Runbooks |
| ------ | ----- | ----- |
| Snelheid | Snel uitvoeren omdat ze geen compilatie stap gebruiken. | Langzamer uitgevoerd. |
| Controlepunten | Controle punten worden niet ondersteund. Een Power shell-runbook kan de bewerking alleen vanaf het begin hervatten. | Controle punten gebruiken, waarmee een werkmap vanaf elk punt kan worden hervat. |
| Opdracht uitvoering | Alleen seriële uitvoering ondersteunen. | Ondersteuning voor zowel seriële als parallelle uitvoering.|
| Runs | Een enkele runs Pace voert alles uit in een script. | Een afzonderlijke runs Pace kan worden gebruikt voor een activiteit, een opdracht of een script blok. |

Naast deze verschillen hebben Power shell-runbooks enkele [syntaxis verschillen](https://technet.microsoft.com/magazine/dn151046.aspx) ten opzichte van Power shell workflow-runbooks.

## <a name="step-1---create-runbook"></a>Stap 1: runbook maken

Begin met het maken van een eenvoudig runbook waarmee de tekst `Hello World`wordt uitgevoerd.

1. Open uw Automation-account in Azure Portal.

2. Selecteer **runbooks** onder **proces automatisering** om de lijst met Runbooks te openen.

3. Maak een nieuw runbook door **een Runbook maken**te selecteren.

4. Geef het runbook de naam **MyFirstRunbook-PowerShell**.

5. In dit geval gaat u een [Power shell-runbook](../automation-runbook-types.md#powershell-runbooks)maken. Selecteer **Power shell** voor het **type Runbook**.

6. Klik op **Maken** om het runbook te maken en de teksteditor te openen.

## <a name="step-2---add-code-to-the-runbook"></a>Stap 2: code toevoegen aan het runbook

U kunt de code rechtstreeks in het runbook typen of u kunt cmdlets, runbooks en assets selecteren in het besturingselement Bibliotheek en deze laten toevoegen aan het runbook met eventuele gerelateerde parameters. Voor deze zelf studie typt u code rechtstreeks in het runbook.

1. Het runbook is momenteel leeg. Typ `Write-Output "Hello World"` de hoofd tekst van het script.

   ![Hallo wereld](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. Klik op **Opslaan** om het runbook op te slaan.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> Stap 3: het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, moet u het testen om er zeker van te zijn dat het goed werkt. Wanneer een runbook wordt getest, wordt de concept versie uitgevoerd en kunt u de uitvoer interactief bekijken.

1. Klik op **Testvenster** om het testvenster te openen.

2. Klik op **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.

3. Houd er rekening mee dat een [runbook-taak](../automation-runbook-execution.md) wordt gemaakt en dat de status ervan wordt weer gegeven in het deel venster.

   De taak status wordt in de wachtrij geplaatst, wat aangeeft dat de taak wacht totdat een runbook worker in de Cloud beschikbaar is. De status verandert in starten wanneer een werk nemer de taak claimt. Ten slotte wordt de status actief wanneer het runbook daad werkelijk wordt uitgevoerd.

4. Wanneer de runbook-taak is voltooid, wordt de uitvoer weer gegeven in het deel venster testen. In dit geval ziet `Hello World`u.

   ![Uitvoer testvenster](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. Sluit het testvenster om terug te gaan naar het papier.

## <a name="step-4---publish-and-start-the-runbook"></a>Stap 4: het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog in de concept modus. Het moet worden gepubliceerd voordat u het in productie kan uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie omdat het runbook zojuist is gemaakt.

1. Klik op **Publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer hierom wordt gevraagd.

2. Schuif naar links om het runbook weer te geven op de pagina Runbooks en houd er rekening mee dat de waarde voor de **ontwerp status** is ingesteld op **gepubliceerd**.

3. Schuif terug naar rechts om de pagina voor **MyFirstRunbook-Power shell**weer te geven.
   
   Met de opties bovenaan kunt u het runbook nu starten, een toekomstige start tijd plannen of een [webhook](../automation-webhooks.md) maken zodat het runbook kan worden gestart via een http-aanroep.

4. Selecteer **Start** en vervolgens **Ja** wanneer u wordt gevraagd het runbook te starten. 

5. Er wordt een taak venster geopend voor de runbook-taak die is gemaakt. Hoewel u dit deel venster kunt sluiten, laat u het nu geopend, zodat u de voortgang van de taak kunt bekijken. De taak status wordt weer gegeven in **taak overzicht**en mogelijke statussen zijn zoals beschreven voor het testen van het runbook.

   ![Taakoverzicht](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. Zodra de runbook-status is voltooid, klikt u op **uitvoer** om de uitvoer pagina te openen, `Hello World` waar u kunt zien dat deze wordt weer gegeven.

   ![Taakuitvoer](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. Sluit de uitvoer pagina.

8. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. In de uitvoer stroom `Hello World` ziet u alleen.

    Houd er rekening mee dat in het deel venster stromen andere stromen kunnen worden weer gegeven voor een runbook-taak, zoals uitgebreide en fout stromen, als het runbook hiernaar schrijft.

   ![Alle logboeken](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. Sluit het deel venster streams en het taak venster om terug te keren naar de pagina MyFirstRunbook-Power shell.

10. Klik onder **Details**op **taken** om de pagina taken voor dit runbook te openen. Op deze pagina worden alle taken weer gegeven die door uw runbook zijn gemaakt. Er wordt slechts één taak weer gegeven, omdat u de taak slechts één keer hebt uitgevoerd.

   ![Takenlijst](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. Klik op de taak naam om hetzelfde taak venster te openen dat u hebt bekeken tijdens het starten van het runbook. In dit deel venster kunt u de details weer geven van alle taken die voor het runbook zijn gemaakt.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Stap 5: verificatie toevoegen voor het beheren van Azure-resources

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. U wilt dat er Azure-resources mee worden beheerd. Hiervoor moet het runbook kunnen worden geverifieerd met behulp van het run as-account dat automatisch is gemaakt tijdens het maken van uw Automation-account.

Zoals u in het onderstaande voor beeld ziet, wordt de uitvoeren als-verbinding gemaakt met de cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) . Als u resources beheert over meerdere abonnementen, moet u de `AzContext` para meter gebruiken met [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0).

> [!NOTE]
> Voor Power shell- `Add-AzAccount` runbooks `Add-AzureRMAccount` en zijn aliassen `Connect-AzAccount`voor. U kunt deze cmdlets gebruiken of u kunt [uw modules](../automation-update-azure-modules.md) in uw Automation-account bijwerken naar de meest recente versies. Mogelijk moet u uw modules bijwerken, zelfs als u zojuist een nieuw Automation-account hebt gemaakt.

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

1. Open de tekst editor door te klikken op **bewerken** op de pagina MyFirstRunbook-Power shell.

2. U hebt de `Write-Output` lijn niet langer nodig. Ga gewoon door en verwijder deze.

3. Typ of kopieer en plak de volgende code, die de verificatie afhandelt met uw uitvoeren als-account voor Automation.

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

4. Klik op **test venster** zodat u het runbook kunt testen.

5. Klik op **Start** om de test te starten. Zodra het proces is voltooid, ziet u uitvoer die lijkt op het volgende, waarbij basis informatie uit uw account wordt weer gegeven. Deze uitvoer bevestigt dat het run as-account geldig is.

   ![Verifiëren](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Stap 6: code toevoegen om een virtuele machine te starten

Nu uw runbook is geverifieerd voor uw Azure-abonnement, kunt u resources beheren. Laten we een opdracht toevoegen om een virtuele machine te starten. U kunt elke wille keurige virtuele machine in uw Azure-abonnement kiezen en alleen de naam van die in het runbook nu vastleggen.

1. Voeg aan het runbook-script de cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) toe om de virtuele machine te starten. Zoals hieronder wordt weer gegeven, start de cmdlet een virtuele machine met `VMName` de naam en met een resource `ResourceGroupName`groep genaamd.

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

2. Sla het runbook op en klik vervolgens op **test venster** zodat u het kunt testen.

3. Klik op **starten** om de test te starten. Als de virtuele machine is voltooid, controleert u of deze is gestart.

## <a name="step-7---add-an-input-parameter"></a>Stap 7: een invoer parameter toevoegen

Met uw runbook wordt momenteel de virtuele machine gestart die u in het runbook hebt vastgelegd. Het runbook is handiger als u de virtuele machine opgeeft wanneer het runbook wordt gestart. Laten we invoer parameters toevoegen aan het runbook om deze functionaliteit te bieden.

1. Wijzig in de tekst editor de `Start-AzVM` cmdlet voor het gebruik van variabelen voor de `VMName` para `ResourceGroupName`meters en. 

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

2. Sla het runbook op en open het testvenster. U kunt nu waarden opgeven voor de twee invoervariabelen die in de test worden gebruikt.

3. Sluit het testvenster.

4. Klik op **Publiceren** om de nieuwe versie van het runbook te publiceren.

5. Stop de virtuele machine die u eerder hebt gestart.

6. Klik op **Starten** om het runbook te starten. 

7. Typ de waarden voor **VMNAME** en **RESOURCEGROUPNAME** voor de virtuele machine die u gaat starten en klik vervolgens op **OK**.

    ![Parameter doorgeven](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. Wanneer het runbook is voltooid, controleert u of de virtuele machine is gestart.

## <a name="next-steps"></a>Volgende stappen

* Zie de [Power shell-documenten](/powershell/scripting/overview)voor meer informatie over Power shell, inclusief taal referentie-en leer modules.
* Zie [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)(Engelstalig) voor een Power shell-cmdlet-verwijzing.
* Zie [een grafisch Runbook maken](automation-tutorial-runbook-graphical.md)om aan de slag te gaan met grafische runbooks.
* Zie [een Power shell workflow-Runbook maken](automation-tutorial-runbook-textual.md)om aan de slag te gaan met Power shell workflow-runbooks.
* Zie [Azure Automation runbook-typen](../automation-runbook-types.md)voor meer informatie over de typen runbook en hun voor delen en beperkingen.
* Zie voor meer informatie over de functie ondersteuning van het Power shell-script [systeem eigen Power shell-script ondersteuning in azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
