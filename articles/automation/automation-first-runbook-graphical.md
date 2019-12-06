---
title: Mijn eerste grafische runbook in Azure Automation
description: Zelfstudie die u helpt bij het maken, testen en publiceren van een eenvoudig grafisch runbook.
keywords: runbook, runbook-sjabloon, runbook-automatisering, azure-runbook
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/13/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cdd015d9f29c3fb672d626f32a485271e2757c2
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850309"
---
# <a name="my-first-graphical-runbook"></a>Mijn eerste grafische runbook

> [!div class="op_single_selector"]
> * [Grafisch](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-werkstroom](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

In deze zelfstudie wordt stap voor stap het maken van een [grafisch runbook](automation-runbook-types.md#graphical-runbooks) in Azure Automation beschreven. We beginnen met een eenvoudig runbook dat we testen en publiceren terwijl wordt uitgelegd hoe u de status van de runbooktaak kunt bijhouden. Vervolgens wijzigt u het runbook zodanig dat Azure-resources daadwerkelijk worden beheerd, in dit geval door een virtuele machine van Azure te starten. Daarna rondt u de zelfstudie af door het runbook geavanceerder te maken. Dit doet u door runbookparameters en voorwaardelijke koppelingen toe te voegen.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. U stopt en start deze machine, dus het mag geen productiemachine zijn.

## <a name="create-runbook"></a>Runbook maken

Begin met het maken van een eenvoudig runbook waarmee de tekst *Hallo wereld* als uitvoer wordt gegeven.

1. Open uw Automation-account in Azure Portal.

   Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U zou al enkele assets moeten hebben. De meeste van deze assets zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. Ook moet u de referentieasset hebben die wordt genoemd in de [vereisten](#prerequisites).

2. Selecteer **Runbooks** onder **Procesbeheer** om de lijst van runbooks te openen.
3. Maak een nieuw runbook door **+ een Runbook toevoegen**te selecteren en klik vervolgens op **een nieuw runbook maken**.
4. Geef het runbook de naam *MyFirstRunbook-Graphical*.
5. In dit geval gaat u een [grafisch runbook](automation-graphical-authoring-intro.md) maken, dus selecteer **Grafisch** voor **Runbooktype**.<br> ![Nieuw runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Klik op **Maken** om het runbook te maken en de grafische editor te openen.

## <a name="add-activities"></a>Activiteiten toevoegen

Met het besturingselement Bibliotheek aan de linkerkant van de editor kunt u activiteiten selecteren die u aan uw runbook wilt toevoegen. U gaat een cmdlet **Write-Output** toevoegen om tekst als uitvoer van het runbook te krijgen.

1. Klik in het besturingselement Bibliotheek in het zoektekstvak en typ **Write-Output**. De zoekresultaten worden weergegeven op de volgende afbeelding: <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Schuif omlaag naar de onderkant van de lijst. U kunt met de rechtermuisknop klikken op **Write-Output** en **Toevoegen aan papier** selecteren of klikken op de knop met de drie puntjes naast de cmdlet en **Toevoegen aan papier** selecteren.
1. Klik op de activiteit **Write-Output** op het papier. Met deze actie opent u de pagina configuratie beheer, waarmee u de activiteit kunt configureren.
1. Het **label** krijgt standaard de naam van de cmdlet, maar u kunt dit wijzigen in een duidelijkere beschrijving. Wijzig dit in *Schrijf Hallo Wereld naar uitvoer*.
1. Klik op **Parameters** om waarden op te geven voor de parameters van de cmdlet.

   Sommige cmdlets hebben meerdere parametersets. U moet dan selecteren welke u wilt gebruiken. In dit geval heeft **Write-Output** slechts één parameterset, dus u hoeft er geen te selecteren.

1. Selecteer de parameter **InputObject**. Dit is de parameter waarin u de tekst opgeeft die naar de uitvoerstroom moet worden verzonden.
1. Selecteer in de vervolgkeuzelijst **Gegevensbron** de optie **PowerShell-expressie**. In de vervolgkeuzelijst **Gegevensbron** staan verschillende bronnen die u kunt gebruiken om een parameterwaarde te vullen.

   U kunt uitvoer van deze bronnen gebruiken, zoals een andere activiteit, een Automation-asset of een PowerShell-expressie. In dit geval is de uitvoer gewoon *Hallo wereld*. U kunt een PowerShell-expressie gebruiken en een tekenreeks opgeven.<br>

1. Typ in het vak **Expressie** de tekst *"Hallo wereld"* en klik vervolgens tweemaal op **OK** om terug te gaan naar het papier.
1. Klik op **Opslaan** om het runbook op te slaan.

## <a name="test-the-runbook"></a>Het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, wilt u het testen om er zeker van te zijn dat het goed werkt. Wanneer u een runbook test, voert u de **concept**versie uit en geeft u de uitvoer interactief weer.

1. Selecteer **test venster** om de pagina test te openen.
1. Klik op **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.
1. Een [runbooktaak](automation-runbook-execution.md) wordt gemaakt en de status ervan wordt in het venster weergegeven.

   In eerste instantie is de taakstatus *In de wachtrij geplaatst*. Hiermee wordt aangegeven dat er wordt gewacht tot er in de cloud een runbook worker beschikbaar is. De taakstatus verandert daarna in *Starten* wanneer een werkrol de taak claimt en daarna in *Wordt uitgevoerd* wanneer het runbook daadwerkelijk wordt uitgevoerd.

1. Wanneer de runbooktaak is voltooid, wordt de uitvoer ervan weergegeven. In dit geval ziet u *Hallo wereld*.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Sluit de pagina test om terug te gaan naar het canvas.

## <a name="publish-and-start-the-runbook"></a>Het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog steeds in de modus Concept. Het moet worden gepubliceerd voordat u het in productie kan uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie omdat het runbook zojuist is gemaakt.

1. Selecteer **publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer u hierom wordt gevraagd.
1. Als u naar links schuift om het runbook weer te geven op de pagina **Runbooks** , wordt de **ontwerp status** **gepubliceerd**weer gegeven.
1. Schuif terug naar rechts om de pagina voor **MyFirstRunbook-grafisch**weer te geven.

   Met de opties bovenaan kunnen we het runbook starten, plannen dat het op een bepaald moment in de toekomst start of een [webhook](automation-webhooks.md) maken zodat het kan worden gestart via een HTTP-aanroep.

1. Selecteer **Start** en vervolgens **Ja** wanneer u wordt gevraagd het runbook te starten.
1. Er wordt een taak pagina geopend voor de runbook-taak die is gemaakt. Controleer of de **taakstatus** **Voltooid** aangeeft.
1. Zodra voor het runbook de status *Voltooid* wordt weergegeven, klikt u op **Uitvoer**. De **uitvoer** pagina wordt geopend en u ziet de *Hallo wereld* in het deel venster.
1. Sluit de uitvoer pagina.
1. Klik op **alle logboeken** om de pagina stromen voor de runbook-taak te openen. U zou alleen *Hallo wereld* moeten zien in de uitvoerstroom, maar er kunnen ook andere stromen voor een runbooktaak worden weergegeven, zoals Uitgebreid en Fout als hiernaar wordt geschreven met het runbook.
1. Sluit de pagina alle logboeken en de taak pagina om terug te keren naar de pagina MyFirstRunbook-graphical.
1. Als u alle taken voor het runbook wilt weer geven, sluit u de **taak** pagina en selecteert u **taken** onder **resources**. Op deze blade worden alle taken weergegeven die met dit runbook zijn gemaakt. U zou slechts één weergegeven taak moeten zien, aangezien de taak slechts eenmaal is uitgevoerd.
1. U kunt op deze taak klikken om hetzelfde taakvenster te openen dat u hebt bekeken toen u het runbook startte. Hiermee kunt u teruggaan in de tijd en de details bekijken van elke taak die voor een bepaald runbook is gemaakt.

## <a name="create-variable-assets"></a>Variabele assets maken

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. U wilt dat er Azure-resources mee worden beheerd. Voordat u het runbook configureert voor verificatie, maakt u een variabele die het abonnements-id moet bevatten en u verwijst hiernaar nadat u de activiteit hebt ingesteld om te verifiëren in stap 6 hieronder. Door een verwijzing naar de abonnementscontext op te nemen, kunt u eenvoudig werken met verschillende abonnementen. Voordat u verdergaat, kopieert u uw abonnements-id uit de optie Abonnementen in het deelvenster Navigatie.

1. Selecteer op de pagina Automation-accounts de optie **variabelen** onder **gedeelde resources**.
1. Selecteer **een variabele toevoegen**.
1. Voer op de pagina nieuwe variabele in het vak **naam** **AzureSubscriptionId** in en voer in het vak **waarde** de abonnements-id in. Behoud *tekenreeks* voor het **type** en de standaardwaarde voor **Versleuteling**.
1. Klik op **Maken** om de variabele te maken.

## <a name="add-authentication"></a>Verificatie toevoegen

Nu u een variabele hebt die onze abonnements-id kan bevatten, kunt u uw runbook configureren voor verificatie met de Uitvoeren als-referenties waarnaar wordt verwezen in de [vereisten](#prerequisites). U doet dit door de cmdlet Azure uitvoeren als verbindings **Asset** en **Connect-AzureRmAccount** toe te voegen aan het canvas.

1. Ga terug naar uw runbook en selecteer **bewerken** op de pagina MyFirstRunbook-graphical.
1. U hebt de schrijf Hallo wereld niet meer nodig **om te worden uitgevoerd** . Klik op de weglatings tekens (...) en selecteer **verwijderen**.
1. Vouw in het besturingselement Bibliotheek **Assets**, **Verbindingen** uit en voeg **AzureRunAsConnection** toe aan het papier door **Toevoegen aan papier** te selecteren.
1. Typ in het besturings element bibliotheek **Connect-AzureRmAccount** in het tekstvak zoeken.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** is nu een alias voor **Connect-AzureRmAccount**. Als u de bibliotheek items zoekt en u geen **Connect-AzureRMAccount**ziet, kunt u **add-AzureRMAccount**gebruiken, maar u kunt ook uw modules bijwerken in uw Automation-account.

1. Voeg **Connect-AzureRmAccount** toe aan het canvas.
1. Beweeg de muisaanwijzer over **Uitvoeren als-verbinding ophalen** totdat een cirkel wordt weergegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl om **verbinding te maken-AzureRmAccount**. De pijl die u hebt gemaakt, is een *koppeling*. Het runbook wordt gestart met **uitvoeren als-verbinding ophalen** en vervolgens **Connect-AzureRmAccount**uitvoeren.<br> ![Koppeling tussen activiteiten maken](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Selecteer op het canvas de optie **Connect-AzureRmAccount** en typ **Aanmelden bij Azure** in het deel venster configuratie van het tekstvak **Label** .
1. Klik op **para meters** en de pagina parameter configuratie van activiteit wordt weer gegeven.
1. **Connect-AzureRmAccount** heeft meerdere parameter sets, dus u moet er een selecteren voordat u parameter waarden kunt opgeven. Klik op **Parameterset** en selecteer vervolgens de parameterset **ServicePrincipalCertificate**.
1. Zodra u de parameterset hebt geselecteerd, worden de para meters weer gegeven op de pagina parameter configuratie van activiteit. Klik op **APPLICATIONID**.<br> ![Parameters voor Azure RM-account toevoegen](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Selecteer op de pagina parameter waarde de optie **uitvoer van activiteit** voor de **gegevens bron** en selecteer **uitvoeren als-verbinding ophalen** in de lijst, Typ in het tekstvak **pad naar veld** type **ApplicationId**en klik vervolgens op **OK**. U geeft de naam van de eigenschap voor het pad naar het veld op omdat de uitvoer van de activiteit een object met meerdere eigenschappen bevat.
1. Klik op **CERTIFICATETHUMBPRINT**en selecteer de uitvoer van de **activiteit** voor de **gegevens bron**op de pagina parameter waarde. Selecteer **Uitvoeren als-verbinding ophalen** in de lijst, typ in het tekstvak **Pad naar veld** **CertificateThumbprint** en klik vervolgens op **OK**.
1. Klik op **SERVICEPRINCIPAL**en selecteer op de pagina parameter waarde **ConstantValue** voor de **gegevens bron**, klik op de optie **waar**en klik vervolgens op **OK**.
1. Klik op **TENANTID**en selecteer op de pagina parameter waarde de optie **uitvoer van activiteit** voor de **gegevens bron**. Selecteer **Uitvoeren als-verbinding ophalen** in de lijst, typ in het tekstvak **Pad naar veld** **TenantId** en klik vervolgens tweemaal op **OK**.
1. Typ in het besturingselement Bibliotheek **Set-AzureRmContext** in het zoektekstvak.
1. Voeg **Set-AzureRmContext** toe aan het papier.
1. Selecteer op het papier **Set-AzureRmContext** en typ in het besturingselementvenster Configuratie **Abonnements-id opgeven** in het tekstvak **Label**.
1. Klik op **para meters** en de pagina parameter configuratie van activiteit wordt weer gegeven.
1. **Set-AzureRmContext** bevat meerdere parametersets, dus moet u er een selecteren voordat u parameterwaarden kunt opgeven. Klik op **Parameterset** en selecteer vervolgens de parameterset **SubscriptionId**.
1. Zodra u de parameterset hebt geselecteerd, worden de para meters weer gegeven op de pagina parameter configuratie van activiteit. Klik op **SubscriptionID**.
1. Selecteer op de pagina parameter waarde de optie **variabel activum** voor de **gegevens bron** en selecteer **AzureSubscriptionId** in de lijst en klik vervolgens twee maal op **OK** .
1. Beweeg de muisaanwijzer over **Aanmelden bij Azure** totdat een cirkel wordt weergegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl naar **Abonnements-id opgeven**.

Uw runbook zou er op dit punt als volgt moeten uitzien: <br>![Configuratie runbookverificatie](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="add-activity-to-start-a-vm"></a>Activiteit toevoegen om een virtuele machine te starten

Hier voegt u een activiteit **Start-AzureRmVM** toe om een virtuele machine te starten. U kunt elke virtuele machine in uw Azure-abonnement selecteren en voorlopig hardcoderen we deze naam in de cmdlet.

1. Typ in het besturingselement Bibliotheek **Start-AzureRm** in het zoektekstvak.
2. Voeg **Start-AzureRmVM** toe aan het papier, klik erop en sleep het onder **Abonnements-id opgeven**.
1. Beweeg de muisaanwijzer over **Abonnements-id opgeven** totdat een cirkel wordt weergegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl naar **Start-AzureRmVM**.
1. Selecteer **Start-AzureRmVM**. Klik op **Parameters** en vervolgens op **Parameterset** om de sets voor **Start-AzureRmVM** weer te geven. Selecteer de parameterset **ResourceGroupNameParameterSetName**. Naast **ResourceGroupName** en **Name** staan uitroeptekens. Hiermee wordt aangegeven dat dit vereiste parameters zijn. Voor beide parameters worden tekenreekswaarden verwacht.
1. Selecteer **Name**. Selecteer **PowerShell-expressie** bij **Gegevensbron** en typ tussen dubbele aanhalingstekens de naam van de virtuele machine die u met dit runbook gaat starten. Klik op **OK**.
1. Selecteer **ResourceGroupName**. Gebruik **PowerShell-expressie** voor de **gegevensbron** en typ de naam van de resourcegroep tussen dubbele aanhalingstekens. Klik op **OK**.
1. Klik op Testvenster zodat u het runbook kunt testen.
1. Klik op **Start** om de test te starten. Nadat deze is voltooid, controleert u of de virtuele machine is gestart.

Uw runbook zou er op dit punt als volgt moeten uitzien: <br>![Configuratie runbookverificatie](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="add-additional-input-parameters"></a>Aanvullende invoer parameters toevoegen

Met ons runbook wordt op dit moment de virtuele machine gestart in de resourcegroep die u hebt opgegeven in de cmdlet **Start-AzureRmVM**. Het runbook zou nuttiger zijn als we beide namen konden opgeven op het moment dat het runbook wordt gestart. U gaat daarom invoerparameters toevoegen aan het runbook om deze functionaliteit te bieden.

1. Open de grafische editor door te klikken op **bewerken** in het deel venster **MyFirstRunbook-grafisch** .
1. Selecteer **invoer en uitvoer** en **Voeg vervolgens invoer** toe om het deel venster invoer parameter van Runbook te openen.
1. Geef *VMName* op voor de **Naam**. Behoud *tekenreeks* voor het **type**, maar wijzig **Verplicht** in *Ja*. Klik op **OK**.
1. Maak een tweede verplichte invoerparameter met de naam *ResourceGroupName* en klik vervolgens op **OK** om het venster **Invoer en uitvoer** te sluiten.<br> ![Invoerparameters voor runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Selecteer de activiteit **Start-AzureRmVM** en klik vervolgens op **Parameters**.
1. Wijzig de **gegevensbron** voor **Name** in **Runbookinvoer** en selecteer vervolgens **VMName**.
1. Wijzig de **gegevensbron** voor **ResourceGroupName** in **Runbookinvoer** en selecteer vervolgens **ResourceGroupName**.<br> ![Start-AzureVM-parameters](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Sla het runbook op en open het testvenster. U kunt nu waarden opgeven voor de twee invoervariabelen die in de test worden gebruikt.
1. Sluit het testvenster.
1. Klik op **Publiceren** om de nieuwe versie van het runbook te publiceren.
1. Stop de virtuele machine die u in de vorige stap hebt gestart.
1. Klik op **Starten** om het runbook te starten. Typ waarden voor **VMName** en **ResourceGroupName** voor de virtuele machine die u wilt starten.
1. Wanneer het runbook is voltooid, controleert u of de virtuele machine is gestart.

## <a name="create-a-conditional-link"></a>Een voorwaardelijke koppeling maken

U gaat het runbook nu wijzigen zodat alleen wordt geprobeerd de virtuele machine te starten als deze nog niet is gestart. We doen dit door een cmdlet **Get-AzureRmVM** toe te voegen aan het runbook waarmee de exemplaarniveaustatus van de virtuele machine wordt opgehaald. We voegen vervolgens een PowerShell Workflow-codemodule genaamd **Status ophalen** toe met een PowerShell-codefragment om te bepalen of de status van de virtuele machine 'actief' of 'gestopt' is. Met een voorwaardelijke koppeling van de module **Status ophalen** wordt **Start-AzureRmVM** alleen uitgevoerd als de huidige uitvoeringsstatus 'gestopt' is. Ten slotte zorgt u dat een bericht wordt weergegeven waarin staat of de virtuele machine is gestart of niet, met de PowerShell-cmdlet Write-Output.

1. Open **MyFirstRunbook-grafisch** in de grafische editor.
1. Verwijder de koppeling tussen **Abonnements-id opgeven** en **Start-AzureRmVM** door erop te klikken en vervolgens te drukken op de toets *Delete*.
1. Typ in het besturingselement Bibliotheek **Get-AzureRm** in het zoektekstvak.
1. Voeg **Get-AzureRmVM** toe aan het papier.
1. Selecteer **Get-AzureRmVM** en vervolgens **Parameterset** om de sets voor **Get-AzureRmVM** weer te geven. Selecteer de parameterset **GetVirtualMachineInResourceGroupNameParamSet**. Naast **ResourceGroupName** en **Name** staan uitroeptekens. Hiermee wordt aangegeven dat dit vereiste parameters zijn. Voor beide parameters worden tekenreekswaarden verwacht.
1. Selecteer onder **Gegevensbron** voor **Name** de optie **Runbookinvoer** en selecteer vervolgens **VMName**. Klik op **OK**.
1. Selecteer onder **Gegevensbron** voor **ResourceGroupName** de optie **Runbookinvoer** en selecteer vervolgens **ResourceGroupName**. Klik op **OK**.
1. Selecteer onder **Gegevensbron** voor **Status** de optie **Constante waarde** en klik vervolgens op **Waar**. Klik op **OK**.
1. Maak een koppeling van **Abonnements-id opgeven** naar **Get-AzureRmVM**.
1. Vouw in het besturingselement Bibliotheek **Runbookbesturing** uit en voeg **Code** toe aan het papier.  
1. Maak een koppeling van **Get-AzureRmVM** naar **Code**.  
1. Klik op **Code** en wijzig in het deelvenster Configuratie het label in **Status ophalen**.
1. Selecteer **code** parameter en de pagina **code-editor** wordt weer gegeven.  
1. Plak het volgende codefragment in de code-editor:

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Maak een koppeling van **Status ophalen** naar **Start-AzureRmVM**.<br> ![Runbook met codemodule](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Selecteer de koppeling en wijzig in het deelvenster Configuratie **Voorwaarde toepassen** in **Ja**. De koppeling wordt een gestreepte lijn waarmee wordt aangegeven dat de doelactiviteit alleen wordt uitgevoerd als de voorwaarde 'waar' is.  
1. Voor **Expressie van voorwaarde** typt u *$ActivityOutput['Get Status'] -eq "Stopped"* . **Start-AzureRmVM** wordt nu alleen uitgevoerd als de virtuele machine is gestopt.
1. Vouw in het besturingselement Bibliotheek **Cmdlets** uit en vervolgens **Microsoft.PowerShell.Utility**.
1. Voeg **Write-Output** tweemaal aan het papier toe.
1. In het eerste besturingselement **Write-Output** klikt u op **Parameters** en wijzigt u de waarde voor **Label** in *VM gestart melden*.
1. Voor **InputObject** wijzigt u **Gegevensbron** in **PowerShell-expressie** en typt u de expressie *"$VMName successfully started."* .
1. In het tweede besturingselement **Write-Output** klikt u op **Parameters** en wijzigt u de waarde voor **Label** in *Starten VM mislukt melden*.
1. Voor **InputObject** wijzigt u **Gegevensbron** in **PowerShell-expressie** en typt u de expressie *"$VMName could not start."* .
1. Maak een koppeling van **Start-AzureRmVM** naar **VM gestart melden** en **Starten VM mislukt melden**.
1. Selecteer de koppeling naar **VM gestart melden** en wijzig **Voorwaarde toepassen** in **Waar**.
1. Voor **Expressie van voorwaarde** typt u *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*. Dit besturingselement Write-Output wordt nu alleen uitgevoerd als de virtuele machine is gestart.
1. Selecteer de koppeling naar **Starten VM mislukt melden** en wijzig **Voorwaarde toepassen** in **Waar**.
1. Voor **Expressie van voorwaarde** typt u *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*. Dit besturingselement Write-Output wordt nu alleen uitgevoerd als de virtuele machine niet is gestart. Het runbook moet er ongeveer uitzien als in de volgende afbeelding: <br> ![Runbook met Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Sla het runbook op en open het testvenster.
1. Start het runbook als de virtuele machine is gestopt; deze zou moeten starten.

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over grafisch ontwerpen [Grafisch ontwerpen in Azure Automation](automation-graphical-authoring-intro.md)
* Zie [Mijn eerste PowerShell-runbook](automation-first-runbook-textual-powershell.md) om aan de slag te gaan met PowerShell-runbooks
* Zie [Mijn eerste PowerShell Workflow-runbook](automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks


