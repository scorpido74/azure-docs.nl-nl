---
title: Mijn eerste grafische runbook in Azure Automation
description: Zelfstudie die u helpt bij het maken, testen en publiceren van een eenvoudig grafisch runbook.
keywords: runbook, runbook-sjabloon, runbook-automatisering, azure-runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: 8b836ebc0adc6f0616d28b16bfb743dfc4553d1a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367412"
---
# <a name="my-first-graphical-runbook"></a>Mijn eerste grafische runbook

> [!div class="op_single_selector"]
> * [Grafisch](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-werkstroom](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

In deze zelfstudie wordt stap voor stap het maken van een [grafisch runbook](automation-runbook-types.md#graphical-runbooks) in Azure Automation beschreven. Begin met een eenvoudig runbook dat u kunt testen en publiceren, terwijl u leert hoe u de status van de runbook-taak moet volgen. Wijzig vervolgens het runbook om Azure-resources daad werkelijk te beheren. in dit geval wordt het starten van een virtuele Azure-machine gestart. Voltooi de zelf studie om het runbook betrouwbaarder te maken door runbook-para meters en voorwaardelijke koppelingen toe te voegen.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. Omdat u deze machine stopt en start, mag het geen productie-VM zijn.

## <a name="step-1---create-runbook"></a>Stap 1: runbook maken

Begin met het maken van een eenvoudig runbook waarmee de tekst `Hello World`worden uitgevoerd.

1. Open uw Automation-account in Azure Portal. 

    Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U hebt als het goed is al enkele assets. De meeste van deze activa zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. U moet ook beschikken over de referentie-Asset die aan uw abonnement is gekoppeld.
2. Selecteer **runbooks** onder **proces automatisering** om de lijst met Runbooks te openen.
3. Maak een nieuw runbook door **een Runbook maken**te selecteren.
4. Geef het runbook de naam **MyFirstRunbook-Graphical**.
5. In dit geval gaat u een [grafisch runbook](automation-graphical-authoring-intro.md)maken. Selecteer **grafisch** voor het **type Runbook**.<br> ![Nieuw runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Klik op **Maken** om het runbook te maken en de grafische editor te openen.

## <a name="step-2---add-activities"></a>Stap 2: activiteiten toevoegen

Met het besturingselement Bibliotheek aan de linkerkant van de editor kunt u activiteiten selecteren die u aan uw runbook wilt toevoegen. U gaat een `Write-Output`-cmdlet toevoegen aan uitvoer tekst uit het runbook.

1. Klik in het besturings element bibliotheek op het zoek veld en typ `write-output`. Zoek resultaten worden weer gegeven in de volgende afbeelding. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Schuif omlaag naar de onderkant van de lijst. Klik met de rechter muisknop op **Write-output** en selecteer **toevoegen aan canvas**. U kunt ook op het weglatings teken (...) naast de naam van de cmdlet klikken en vervolgens **toevoegen aan papier**selecteren.
1. Klik op de activiteit **Write-Output** op het papier. Met deze actie opent u de pagina configuratie beheer, waarmee u de activiteit kunt configureren.
1. Het **Label** veld wordt standaard ingesteld op de naam van de cmdlet, maar u kunt dit wijzigen in een duidelijkere beschrijving. Wijzig deze in `Write Hello World to output`.
1. Klik op **Parameters** om waarden op te geven voor de parameters van de cmdlet.

   Sommige cmdlets hebben meerdere parametersets, en u moet selecteren welk abonnement u wilt gebruiken. In dit geval heeft `Write-Output` slechts één para meter ingesteld.

1. Selecteer de para meter `InputObject`. Dit is de para meter die u gebruikt om de tekst op te geven die naar de uitvoer stroom moet worden verzonden.
1. De vervolg keuzelijst **gegevens bron** bevat bronnen die u kunt gebruiken om een parameter waarde in te vullen. Selecteer in dit menu de optie **Power shell-expressie**. 

   U kunt uitvoer van dergelijke bronnen gebruiken als een andere activiteit, een Automation-Asset of een Power shell-expressie. In dit geval is de uitvoer alleen `Hello World`. U kunt een PowerShell-expressie gebruiken en een tekenreeks opgeven.<br>

1. In het veld **expressie** typt u `Hello World` en klikt u vervolgens twee keer op **OK** om terug te keren naar het canvas.
1. Klik op **Opslaan** om het runbook op te slaan.

## <a name="step-3---test-the-runbook"></a>Stap 3: het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, moet u het testen om er zeker van te zijn dat het goed werkt. Wanneer een runbook wordt getest, wordt de concept versie uitgevoerd en kunt u de uitvoer interactief bekijken.

1. Selecteer **test venster** om het deel venster test te openen.
1. Klik op **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.
1. Houd er rekening mee dat een [runbook-taak](automation-runbook-execution.md) wordt gemaakt en dat de status ervan wordt weer gegeven in het deel venster.

   De taak status begint als `Queued`, wat aangeeft dat de taak wacht totdat een runbook worker in de Cloud beschikbaar is. De status wordt gewijzigd in `Starting` wanneer een werk nemer de taak claimt. Ten slotte wordt de status `Running` wanneer het runbook daad werkelijk wordt uitgevoerd.

1. Wanneer de runbook-taak is voltooid, wordt de uitvoer weer gegeven in het deel venster testen. In dit geval ziet u `Hello World`.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Sluit het testvenster om terug te gaan naar het papier.

## <a name="step-4---publish-and-start-the-runbook"></a>Stap 4: het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog in de concept modus. Het moet worden gepubliceerd voordat u het in productie kan uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie omdat het runbook zojuist is gemaakt.

1. Selecteer **publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer u hierom wordt gevraagd.
1. Schuif naar links om het runbook weer te geven op de pagina Runbooks en houd er rekening mee dat de waarde voor de **ontwerp status** is ingesteld op **gepubliceerd**.
1. Schuif terug naar rechts om de pagina voor **MyFirstRunbook-grafisch**weer te geven.

   Met de opties bovenaan kunt u het runbook nu starten, een toekomstige start tijd plannen of een [webhook](automation-webhooks.md) maken zodat het runbook kan worden gestart via een http-aanroep.

1. Selecteer **Start** en vervolgens **Ja** wanneer u wordt gevraagd het runbook te starten.
1. Er wordt een taak venster geopend voor de runbook-taak die is gemaakt. Controleer of het veld **taak status** **is voltooid**bevat.
1. Klik op **uitvoer** om de uitvoer pagina te openen, waar u `Hello World` weer gegeven kunt zien.
1. Sluit de uitvoer pagina.
1. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. In de uitvoer stroom ziet u alleen `Hello World`. 

    Houd er rekening mee dat in het deel venster stromen andere stromen kunnen worden weer gegeven voor een runbook-taak, zoals uitgebreide en fout stromen, als het runbook hiernaar schrijft.
1. Sluit het deel venster streams en het taak venster om terug te keren naar de pagina **MyFirstRunbook-graphical** .
1. Selecteer **taken** onder **resources**om alle taken voor het runbook weer te geven. De pagina taken bevat een lijst met alle taken die door het runbook zijn gemaakt. Er wordt slechts één taak weer gegeven, omdat u de taak slechts één keer hebt uitgevoerd.
1. Klik op de taak naam om hetzelfde taak venster te openen dat u hebt bekeken tijdens het starten van het runbook. In dit deel venster kunt u de details weer geven van alle taken die voor het runbook zijn gemaakt.

## <a name="step-5---create-variable-assets"></a>Stap 5: variabele assets maken

U hebt uw runbook getest en gepubliceerd, maar tot nu toe is het niet nuttig om Azure-resources te beheren. Voordat u het runbook configureert voor verificatie, moet u een variabele maken om de abonnements-ID op te slaan, een activiteit instellen voor verificatie en vervolgens naar de variabele te verwijzen. Met een verwijzing naar de context van het abonnement kunt u eenvoudig met meerdere abonnementen werken.

1. Kopieer uw abonnements-ID uit de optie **abonnementen** in het navigatie deel venster.
1. Selecteer op de pagina Automation-accounts de optie **variabelen** onder **gedeelde resources**.
1. Selecteer **een variabele toevoegen**.
1. Stel op de pagina nieuwe variabele de volgende instellingen in de velden in.

    * **Naam** : Voer `AzureSubscriptionId`in.
    * **Waarde** : Voer uw abonnements-id in. 
    * **Type** --Bewaar reeks selecteren.
    * **Versleuteling** : gebruik de standaard waarde.
1. Klik op **Maken** om de variabele te maken.

## <a name="step-6---add-authentication"></a>Stap 6: verificatie toevoegen

Nu u een variabele hebt die de abonnements-ID moet bevatten, kunt u het runbook configureren voor verificatie met de uitvoeren als-referenties voor uw abonnement. Dit doet u door de Azure run as-verbinding als een Asset toe te voegen. U moet ook de cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) en de cmdlet [set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) toevoegen aan het canvas.

>[!NOTE]
>Voor Power shell-runbooks zijn `Add-AzAccount` en `Add-AzureRMAccount` aliassen voor `Connect-AzAccount`. Houd er rekening mee dat deze aliassen niet beschikbaar zijn voor uw grafische runbooks. Een grafisch runbook kan alleen `Connect-AzAccount`zichzelf gebruiken.

1. Navigeer naar uw runbook en selecteer **bewerken** op de pagina **MyFirstRunbook-graphical** .
1. U hebt de `Write Hello World to output`-vermelding niet meer nodig. Klik op het weglatings teken en selecteer **verwijderen**.
1. Vouw in het besturings element bibliotheek de optie **assets**uit en vervolgens **verbindingen**. Voeg `AzureRunAsConnection` toe aan het canvas door **toevoegen aan canvas**te selecteren.
1. In het besturings element bibliotheek typt u `Connect-AzAccount` in het zoek veld.
1. `Connect-AzAccount` toevoegen aan het canvas.
1. Beweeg de muisaanwijzer over **Uitvoeren als-verbinding ophalen** totdat een cirkel wordt weergegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl naar `Connect-AzAccount` om een koppeling te maken. Het runbook wordt gestart met `Get Run As Connection` en wordt vervolgens uitgevoerd `Connect-AzAccount`.<br> ![Koppeling tussen activiteiten maken](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Selecteer `Connect-AzAccount`op het canvas. Typ in het deel venster configuratie-instellingen **Aanmelden bij Azure** in het **Label** veld.
1. Klik op **para meters**en de pagina parameter configuratie van activiteit wordt weer gegeven.
1. De cmdlet `Connect-AzAccount` heeft meerdere parameter sets en u moet er een selecteren voordat u parameter waarden opgeeft. Klik op **para meter set** en selecteer vervolgens **parameterset serviceprincipalcertificate**.
1. De para meters voor deze parameterset worden weer gegeven op de pagina parameter configuratie van activiteit. Klik op **APPLICATIONID**.<br> ![Azure-account parameters toevoegen](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Stel op de pagina parameter waarde de volgende instellingen in en klik vervolgens op **OK**.

   * **Gegevens bron** : Selecteer de **uitvoer**van de activiteit.
   * Gegevens bron lijst: Selecteer **uitvoeren als-verbinding ophalen**.
   * **Pad naar veld** --type `ApplicationId`. U geeft de naam van de eigenschap voor het pad naar het veld op omdat de activiteit een object met meerdere eigenschappen uitvoert.
1. Klik op **CERTIFICATETHUMBPRINT**en voer op de pagina parameter waarde de volgende instellingen in en klik vervolgens op **OK**.

    * **Gegevens bron** : Selecteer de **uitvoer**van de activiteit.
    * Gegevens bron lijst: Selecteer **uitvoeren als-verbinding ophalen**.
    * **Pad naar veld** --type `CertificateThumbprint`.
1. Klik op **SERVICEPRINCIPAL**en selecteer op de pagina parameter waarde **ConstantValue** voor het veld **gegevens bron** ; Klik op de optie **waar**. en klik vervolgens op **OK**.
1. Klik op **TENANTID**en maak de volgende instellingen op de pagina parameter waarde. Wanneer u klaar bent, klikt u twee keer op **OK** .

    * **Gegevens bron** : Selecteer de **uitvoer**van de activiteit. 
    * Gegevens bron lijst: Selecteer **uitvoeren als-verbinding ophalen**.
    * **Pad naar veld** --type `TenantId`. 
1. In het besturings element bibliotheek typt u `Set-AzContext` in het zoek veld.
1. `Set-AzContext` toevoegen aan het canvas.
1. Selecteer `Set-AzContext` op het canvas. Voer in het deel venster configuratie-instellingen `Specify Subscription Id` in het veld **Label** in.
1. Klik op **para meters** en de pagina parameter configuratie van activiteit wordt weer gegeven.
1. De cmdlet `Set-AzContext` heeft meerdere parameter sets en u moet er een selecteren voordat u parameter waarden opgeeft. Klik op **para meter set** en selecteer vervolgens **SubscriptionId**.
1. De para meters voor deze parameterset worden weer gegeven op de pagina parameter configuratie van activiteit. Klik op **SubscriptionID**.
1. Selecteer op de pagina parameter waarde de optie **variabel activum** voor het veld **gegevens bron** en selecteer **AzureSubscriptionId** in de lijst met bronnen. Wanneer u klaar bent, klikt u twee keer op **OK** .
1. Beweeg de muis aanwijzer over `Login to Azure` totdat een cirkel wordt weer gegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl naar `Specify Subscription Id`. 

Uw runbook zou er op dit punt als volgt moeten uitzien: <br>![Configuratie runbookverificatie](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Stap 7: activiteit toevoegen om een virtuele machine te starten

Nu moet u een `Start-AzVM`-activiteit toevoegen om een virtuele machine te starten. U kunt een wille keurige VM in uw Azure-abonnement kiezen en u hebt nu de naam hardcoding in de cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) .

1. In het besturings element bibliotheek typt u `Start-Az` in het zoek veld.
2. Voeg `Start-AzVM` toe aan het canvas en klik en sleep deze onder `Specify Subscription Id`.
1. Beweeg de muis aanwijzer over `Specify Subscription Id` totdat een cirkel wordt weer gegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl naar `Start-AzVM`.
1. Selecteer `Start-AzVM`. Klik op **para meters** en vervolgens op **set para meters** om de sets voor de activiteit weer te geven.
1. Selecteer **ResourceGroupNameParameterSetName** voor de parameterset. Naast de velden **ResourceGroupName** en **name** staan uitroep tekens om aan te geven dat ze de vereiste para meters zijn. Houd er rekening mee dat in beide velden teken reeks waarden worden verwacht.
1. Selecteer **Name**. Kies een **Power shell-expressie** voor het veld van de **gegevens bron** . Voor de virtuele machine die u gebruikt om dit runbook te starten, typt u de naam van de computer tussen dubbele aanhalings tekens. Klik op **OK**.
1. Selecteer **ResourceGroupName**. Gebruik de waarde **Power shell-expressie** voor het veld **gegevens bron** en typ de naam van de resource groep, omgeven door dubbele aanhalings tekens. Klik op **OK**.
1. Klik op **test venster** zodat u het runbook kunt testen.
1. Klik op **starten** om de test te starten. Als het proces is voltooid, controleert u of de VM is gestart. 

Uw runbook zou er op dit punt als volgt moeten uitzien: <br>![Configuratie runbookverificatie](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Stap 8: extra invoer parameters toevoegen

Met uw runbook wordt momenteel de virtuele machine gestart in de resource groep die u hebt opgegeven voor de `Start-AzVM`-cmdlet. Het runbook is handiger als u zowel naam als resource groep opgeeft wanneer het runbook wordt gestart. Laten we invoer parameters toevoegen aan het runbook om deze functionaliteit te bieden.

1. Open de grafische editor door te klikken op **bewerken** in het deel venster **MyFirstRunbook-grafisch** .
1. Selecteer **invoer en uitvoer** en **Voeg vervolgens invoer** toe om het deel venster invoer parameter van Runbook te openen.
1. Maak de volgende instellingen in de beschik bare velden en klik vervolgens op **OK**.
   * **Naam** : Geef `VMName`op.
   * **Typ** --behoud de teken reeks instelling.
   * **Verplicht** : Wijzig de waarde in **Ja**.
1. Maak een tweede verplichte invoer parameter met de naam `ResourceGroupName` en klik vervolgens op **OK** om het deel venster invoer en uitvoer te sluiten.<br> ![Invoerparameters voor runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Selecteer de activiteit `Start-AzVM` en klik vervolgens op **para meters**.
1. Wijzig de **naam** van het **gegevens bron** veld in **Runbook-invoer**. Selecteer vervolgens **VMName**.
1. Wijzig het **gegevens bron** veld voor **ResourceGroupName** in **Runbook-invoer** en selecteer vervolgens **ResourceGroupName**.<br> ![para meters voor het starten van AzVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Sla het runbook op en open het testvenster. U kunt nu waarden opgeven voor de twee invoervariabelen die in de test worden gebruikt.
1. Sluit het testvenster.
1. Klik op **Publiceren** om de nieuwe versie van het runbook te publiceren.
1. Stop de VM die u eerder hebt gestart.
1. Klik op **Starten** om het runbook te starten. Typ de waarden voor `VMName` en `ResourceGroupName` in voor de VM die u wilt starten.
1. Wanneer het runbook is voltooid, controleert u of de virtuele machine is gestart.

## <a name="step-9---create-a-conditional-link"></a>Stap 9: een voorwaardelijke koppeling maken

U kunt het runbook nu aanpassen zodat alleen wordt geprobeerd de virtuele machine te starten als deze nog niet is gestart. Doe dit door een cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) toe te voegen waarmee de status van het exemplaar niveau van de virtuele machine wordt opgehaald. Vervolgens kunt u een Power shell-werk stroom code module met de naam `Get Status` toevoegen met een code fragment van Power shell om te bepalen of de status van de virtuele machine wordt uitgevoerd of gestopt. Een voorwaardelijke koppeling van de module `Get Status` wordt alleen uitgevoerd `Start-AzVM` als de huidige uitvoerings status is gestopt. Aan het einde van deze procedure gebruikt uw runbook de `Write-Output` cmdlet om een bericht uit te voeren om u te informeren als de VM is gestart.

1. Open **MyFirstRunbook-grafisch** in de grafische editor.
1. Verwijder de koppeling tussen `Specify Subscription Id` en `Start-AzVM` door erop te klikken en vervolgens op **Delete**te drukken.
1. In het besturings element bibliotheek typt u `Get-Az` in het zoek veld.
1. `Get-AzVM` toevoegen aan het canvas.
1. Selecteer `Get-AzVM` en klik vervolgens op **para meter set** om de sets voor de cmdlet weer te geven. 
1. Selecteer de parameterset **GetVirtualMachineInResourceGroupNameParamSet**. De velden **ResourceGroupName** en **name** bevatten een uitroep teken ernaast, waarmee wordt aangegeven dat de vereiste para meters worden opgegeven. Houd er rekening mee dat in beide velden teken reeks waarden worden verwacht.
1. Selecteer onder **gegevens bron** voor **naam**de optie **Runbook-invoer**en vervolgens op **VMName**. Klik op **OK**.
1. Selecteer onder **gegevens bron** voor **ResourceGroupName**de optie **Runbook-invoer**en vervolgens op **ResourceGroupName**. Klik op **OK**.
1. Selecteer onder **gegevens bron** voor **status**de optie **constante waarde**en vervolgens **waar**. Klik op **OK**.
1. Maak een koppeling van `Specify Subscription Id` naar `Get-AzVM`.
1. Vouw in het besturings element bibliotheek de **optie Runbook Control** uit en voeg **code** toe aan het canvas.  
1. Maak een koppeling van `Get-AzVM` naar `Code`.  
1. Klik op `Code` en wijzig in het deel venster configuratie het label in **status ophalen**.
1. Selecteer `Code` en de pagina code-editor wordt weer gegeven.  
1. Plak het volgende code fragment in de editor-pagina.

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Maak een koppeling van `Get Status` naar `Start-AzVM`.<br> ![Runbook met codemodule](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Selecteer de koppeling en wijzig in het deel venster configuratie de **voor waarde Toep assen** op **Ja**. Houd er rekening mee dat de koppeling een stippel lijn wordt, waarmee wordt aangegeven dat de doel activiteit alleen wordt uitgevoerd als de voor waarde wordt omgezet in True.  
1. Voor de **expressie voor waarde**typt u `$ActivityOutput['Get Status'] -eq "Stopped"`. `Start-AzVM` wordt nu alleen uitgevoerd als de virtuele machine is gestopt.
1. Vouw in het besturingselement Bibliotheek **Cmdlets** uit en vervolgens **Microsoft.PowerShell.Utility**.
1. Voeg `Write-Output` twee keer toe aan het canvas.
1. Voor het eerste `Write-Output` besturings element klikt u op **para meters** en wijzigt u de waarde van het **Label** om VM op de **hoogte te stellen**.
1. Voor **input object**wijzigt u de **gegevens bron** in **Power shell-expressie**en typt u de expressie `$VMName successfully started.`.
1. Op de tweede `Write-Output` besturings element klikt u op **para meters** en wijzigt u de waarde van het **Label** om het starten van de **VM te melden is mislukt**.
1. Voor **input object**wijzigt u de **gegevens bron** in **Power shell-expressie**en typt u de expressie `$VMName could not start.`.
1. Koppelingen van `Start-AzVM` naar `Notify VM Started` en `Notify VM Start Failed`maken.
1. Selecteer de koppeling naar `Notify VM Started` en wijzig de **voor waarde apply** in True.
1. Typ `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`voor de **expressie voor waarde**. Dit `Write-Output` besturings element wordt nu alleen uitgevoerd als de virtuele machine is gestart.
1. Selecteer de koppeling naar `Notify VM Start Failed` en wijzig de **voor waarde apply** in True.
1. Typ `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`voor het veld **voor waarde-expressie** . Dit `Write-Output` besturings element wordt nu alleen uitgevoerd als de virtuele machine niet is gestart. Het runbook moet er ongeveer uitzien als in de volgende afbeelding: <br> ![Runbook met Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Sla het runbook op en open het testvenster.
1. Start het runbook met de VM gestopt en start de computer opnieuw op.

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over grafisch ontwerpen [grafisch ontwerpen in azure Automation](automation-graphical-authoring-intro.md).
* Zie [Mijn eerste PowerShell-runbook](automation-first-runbook-textual-powershell.md) om aan de slag te gaan met PowerShell-runbooks.
* Zie [mijn eerste Power shell workflow-runbook](automation-first-runbook-textual.md)om aan de slag te gaan met Power shell workflow-runbooks.