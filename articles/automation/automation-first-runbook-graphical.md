---
title: Mijn eerste grafische runbook in Azure Automation
description: Zelfstudie die u helpt bij het maken, testen en publiceren van een eenvoudig grafisch runbook.
keywords: runbook, runbook-sjabloon, runbook-automatisering, azure-runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: e208982145f0c3178a26526c73af81ab009a8d46
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605205"
---
# <a name="my-first-graphical-runbook"></a>Mijn eerste grafische runbook

> [!div class="op_single_selector"]
> * [Grafisch](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-werkstroom](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

In deze zelfstudie wordt stap voor stap het maken van een [grafisch runbook](automation-runbook-types.md#graphical-runbooks) in Azure Automation beschreven. Begin met een eenvoudig runbook dat wordt getest en gepubliceerd, terwijl u leert hoe u de status van de runbook-taak kunt volgen. Wijzig vervolgens het runbook om Azure-resources daad werkelijk te beheren. in dit geval wordt het starten van een virtuele Azure-machine gestart. Voltooi de zelf studie om het runbook betrouwbaarder te maken door runbook-para meters en voorwaardelijke koppelingen toe te voegen.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. Omdat u deze machine stopt en start, mag het geen productie-VM zijn.

## <a name="step-1---create-runbook"></a>Stap 1: runbook maken

Begin met het maken van een eenvoudig runbook dat de tekst ' Hallo wereld ' uitvoert.

1. Open uw Automation-account in Azure Portal. 

    Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U hebt als het goed is al enkele assets. De meeste van deze activa zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. U moet ook beschikken over de referentie-Asset die aan uw abonnement is gekoppeld.
2. Selecteer **runbooks** onder **proces automatisering** om de lijst met Runbooks te openen.
3. Maak een nieuw runbook door **+ een Runbook maken**te selecteren.
4. Geef het runbook de naam "MyFirstRunbook-graphical".
5. In dit geval gaat u een [grafisch runbook](automation-graphical-authoring-intro.md)maken. Selecteer **grafisch** voor het **type Runbook**.<br> ![Nieuw runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Klik op **Maken** om het runbook te maken en de grafische editor te openen.

## <a name="step-2---add-activities"></a>Stap 2: activiteiten toevoegen

Met het besturingselement Bibliotheek aan de linkerkant van de editor kunt u activiteiten selecteren die u aan uw runbook wilt toevoegen. U gaat een cmdlet **Write-Output** toevoegen om tekst als uitvoer van het runbook te krijgen.

1. Klik in het besturings element bibliotheek op het zoek veld en typ ' write-output '. Zoek resultaten worden weer gegeven in de volgende afbeelding: <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Schuif omlaag naar de onderkant van de lijst. Klik met de rechter muisknop op **Write-output** en selecteer **toevoegen aan canvas**. U kunt ook op het weglatings teken (...) naast de naam van de cmdlet klikken en vervolgens **toevoegen aan papier**selecteren.
1. Klik op de activiteit **Write-Output** op het papier. Met deze actie opent u de pagina configuratie beheer, waarmee u de activiteit kunt configureren.
1. Het **Label** veld wordt standaard ingesteld op de naam van de cmdlet, maar u kunt dit wijzigen in een duidelijkere beschrijving. Wijzig de waarde in ' Schrijf Hallo wereld naar uitvoer '.
1. Klik op **Parameters** om waarden op te geven voor de parameters van de cmdlet.

   Sommige cmdlets hebben meerdere parametersets, en u moet selecteren welk abonnement u wilt gebruiken. In dit geval heeft **Write-output** slechts één para meter ingesteld.

1. Selecteer de parameter *InputObject*. Dit is de para meter die u gebruikt om de tekst op te geven die naar de uitvoer stroom moet worden verzonden.
1. De vervolg keuzelijst **gegevens bron** bevat bronnen die u kunt gebruiken om een parameter waarde in te vullen. Selecteer in dit menu de optie **Power shell-expressie**. 

   U kunt uitvoer van dergelijke bronnen gebruiken als een andere activiteit, een Automation-Asset of een Power shell-expressie. In dit geval is de uitvoer gewoon "Hallo wereld". U kunt een PowerShell-expressie gebruiken en een tekenreeks opgeven.<br>

1. Typ Hallo wereld in het veld **expressie** en klik vervolgens twee keer op **OK** om terug te keren naar het canvas.
1. Klik op **Opslaan** om het runbook op te slaan.

## <a name="step-3---test-the-runbook"></a>Stap 3: het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, moet u het testen om er zeker van te zijn dat het goed werkt. Wanneer een runbook wordt getest, wordt de concept versie uitgevoerd en kunt u de uitvoer interactief bekijken.

1. Selecteer **test venster** om het deel venster test te openen.
1. Klik op **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.
1. Houd er rekening mee dat een [runbook-taak](automation-runbook-execution.md) wordt gemaakt en dat de status ervan wordt weer gegeven in het deel venster.

   De taak status wordt in de wachtrij geplaatst, wat aangeeft dat de taak wacht totdat een runbook worker in de Cloud beschikbaar is. De status verandert in starten wanneer een werk nemer de taak claimt. Ten slotte wordt de status actief wanneer het runbook daad werkelijk wordt uitgevoerd.

1. Wanneer de runbook-taak is voltooid, wordt de uitvoer van de test pagn weer gegeven. In dit geval ziet u "Hallo wereld".<br> ![Hallo wereld](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Sluit het testvenster om terug te gaan naar het papier.

## <a name="step-4---publish-and-start-the-runbook"></a>Stap 4: het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog in de concept modus. Het moet worden gepubliceerd voordat u het in productie kan uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie omdat het runbook zojuist is gemaakt.

1. Selecteer **publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer u hierom wordt gevraagd.
1. Schuif naar links om het runbook weer te geven op de pagina Runbooks en houd er rekening mee dat de waarde voor de **ontwerp status** is ingesteld op gepubliceerd.
1. Schuif terug naar rechts om de pagina voor MyFirstRunbook-grafisch weer te geven.

   Met de opties bovenaan kunt u het runbook nu starten, een toekomstige start tijd plannen of een [webhook](automation-webhooks.md) maken zodat het runbook kan worden gestart via een http-aanroep.

1. Selecteer **Start** en vervolgens **Ja** wanneer u wordt gevraagd het runbook te starten.
1. Er wordt een taak venster geopend voor de runbook-taak die is gemaakt. Controleer of de **taak status** is voltooid.
1. Klik op **uitvoer** om de uitvoer pagina te openen, waar u de weer gegeven Hallo wereld kunt zien.
1. Sluit de uitvoer pagina.
1. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. Zie alleen ' Hallo wereld ' in de uitvoer stroom. 

    Houd er rekening mee dat in het deel venster stromen andere stromen kunnen worden weer gegeven voor een runbook-taak, zoals uitgebreide en fout stromen, als het runbook hiernaar schrijft.
1. Sluit het deel venster streams en het taak venster om terug te keren naar de pagina MyFirstRunbook-graphical.
1. Selecteer **taken** onder **resources**om alle taken voor het runbook weer te geven. De pagina taken bevat een lijst met alle taken die door het runbook zijn gemaakt. Er wordt slechts één taak weer gegeven, omdat u de taak slechts één keer hebt uitgevoerd.
1. Klik op de taak naam om hetzelfde taak venster te openen dat u hebt bekeken tijdens het starten van het runbook. In dit deel venster kunt u de details weer geven van alle taken die voor het runbook zijn gemaakt.

## <a name="step-5---create-variable-assets"></a>Stap 5: variabele assets maken

U hebt uw runbook getest en gepubliceerd, maar tot nu toe is het niet nuttig om Azure-resources te beheren. Voordat u het runbook configureert voor verificatie, moet u een variabele maken om de abonnements-ID op te slaan, een activiteit instellen om te verifiëren en vervolgens naar de variabele te verwijzen. Met een verwijzing naar de context van het abonnement kunt u eenvoudig met meerdere abonnementen werken.

1. Kopieer uw abonnements-ID uit de optie **abonnementen** in het navigatie deel venster.
1. Selecteer op de pagina Automation-accounts de optie **variabelen** onder **gedeelde resources**.
1. Selecteer **+ een variabele toevoegen**.
1. Stel op de pagina nieuwe variabele de volgende instellingen in de velden in.

    * **Naam** : Voer ' AzureSubscriptionId ' in.
    * **Waarde** : Voer uw abonnements-id in. 
    * **Type** --Bewaar reeks selecteren.
    * **Versleuteling** : gebruik de standaard waarde.
1. Klik op **Maken** om de variabele te maken.

## <a name="step-6---add-authentication"></a>Stap 6: verificatie toevoegen

Nu u een variabele hebt die de abonnements-ID moet bevatten, kunt u het runbook configureren voor verificatie met de uitvoeren als-referenties voor uw abonnement. Dit doet u door de Azure run as-verbinding als een Asset toe te voegen. U moet ook de cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) en de cmdlet [set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) toevoegen aan het canvas.

>[!NOTE]
>Voor Power shell-runbooks zijn **add-AzAccount** en **add-AzureRMAccount** aliassen voor **Connect-AzAccount**. Houd er rekening mee dat deze aliassen niet beschikbaar zijn voor uw grafische runbooks. Een grafisch runbook kan alleen **Connect-AzAccount** gebruiken.

1. Navigeer naar uw runbook en selecteer **bewerken** op de pagina MyFirstRunbook-graphical.
1. U hebt de vermelding ' Schrijf Hallo wereld naar uitvoer ' niet meer nodig. Klik op het weglatings teken en selecteer **verwijderen**.
1. Vouw in het besturings element bibliotheek de optie **assets**uit en vervolgens **verbindingen**. Voeg ' AzureRunAsConnection ' toe aan het canvas door **toevoegen aan canvas**te selecteren.
1. Typ ' Connect-AzAccount ' in het zoek veld in het besturings element van de bibliotheek.
1. Voeg **Connect-AzAccount** toe aan het canvas.
1. Beweeg de muisaanwijzer over **Uitvoeren als-verbinding ophalen** totdat een cirkel wordt weergegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl om **verbinding te maken-AzAccount** om een koppeling te vormen. Het runbook wordt gestart met **uitvoeren als-verbinding ophalen** en voert vervolgens **Connect-AzAccount**uit.<br> ![Koppeling tussen activiteiten maken](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Selecteer **verbinden-AzAccount**op het canvas. Typ in het deel venster configuratie beheer ' aanmelden bij Azure ' in het veld **Label** .
1. Klik op **para meters**en de pagina parameter configuratie van activiteit wordt weer gegeven.
1. De cmdlet **Connect-AzAccount** heeft meerdere parameter sets en u moet er een selecteren voordat u parameter waarden opgeeft. Klik op **para meter set** en selecteer de para meter parameterset serviceprincipalcertificate.
1. De para meters voor deze parameterset worden weer gegeven op de pagina parameter configuratie van activiteit. Klik op **APPLICATIONID**.<br> ![Azure-account parameters toevoegen](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Stel op de pagina parameter waarde de volgende instellingen in en klik vervolgens op **OK**.

   * **Gegevens bron** : Selecteer de **uitvoer**van de activiteit.
   * Gegevens bron lijst: Selecteer **uitvoeren als-verbinding ophalen**.
   * **Pad naar veld** --type "ApplicationId". U geeft de naam van de eigenschap voor het pad naar het veld op omdat de activiteit een object met meerdere eigenschappen uitvoert.
1. Klik op **CERTIFICATETHUMBPRINT**en voer op de pagina parameter waarde de volgende instellingen in en klik vervolgens op **OK**.

    * **Gegevens bron** : Selecteer de **uitvoer**van de activiteit.
    * Gegevens bron lijst: Selecteer **uitvoeren als-verbinding ophalen**.
    * **Pad naar veld** --type "CertificateThumbprint".
1. Klik op **SERVICEPRINCIPAL**en selecteer op de pagina parameter waarde ConstantValue voor het veld **gegevens bron** ; Klik op de optie **waar**. en klik vervolgens op **OK**.
1. Klik op **TENANTID**en maak de volgende instellingen op de pagina parameter waarde. Wanneer u klaar bent, klikt u twee keer op **OK** .

    * **Gegevens bron** : Selecteer de **uitvoer**van de activiteit. 
    * Gegevens bron lijst: Selecteer **uitvoeren als-verbinding ophalen**.
    * **Pad naar veld** --type "TenantId". 
1. Typ ' set-AzContext ' in het zoek veld in het besturings element van de bibliotheek.
1. Voeg **set-AzContext** toe aan het canvas.
1. Selecteer **set-AzContext** op het canvas. Typ in het deel venster configuratie-element de tekst abonnements-id opgeven in het veld **Label** .
1. Klik op **para meters** en de pagina parameter configuratie van activiteit wordt weer gegeven.
1. De cmdlet **set-AzContext** heeft meerdere parameter sets en u moet er een selecteren voordat u parameter waarden opgeeft. Klik op **para meter set** en selecteer vervolgens de para meter SubscriptionId.
1. De para meters voor deze parameterset worden weer gegeven op de pagina parameter configuratie van activiteit. Klik op **SubscriptionID**.
1. Selecteer op de pagina parameter waarde de optie **variabel activum** voor het veld **gegevens bron** en selecteer **AzureSubscriptionId** in de lijst met bronnen. Wanneer u klaar bent, klikt u twee keer op **OK** .
1. Beweeg de muisaanwijzer over **Aanmelden bij Azure** totdat een cirkel wordt weergegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl naar **Abonnements-id opgeven**. 

Uw runbook zou er op dit punt als volgt moeten uitzien: <br>![Configuratie runbookverificatie](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Stap 7: activiteit toevoegen om een virtuele machine te starten

Nu moet u een **Start-AzVM** -activiteit toevoegen om een virtuele machine te starten. U kunt een wille keurige VM in uw Azure-abonnement kiezen en u bent nu hardcoding die naam in de cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) .

1. Typ in het besturings element bibliotheek **Start-AZ** in het zoek veld.
2. Voeg **Start-AzVM** toe aan het canvas en klik en sleep het onder **Geef de abonnements-id**op.
1. Beweeg de muisaanwijzer over **Abonnements-id opgeven** totdat een cirkel wordt weergegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl naar **Start-AzVM**.
1. Selecteer **Start-AzVM**. Klik op **para meters** en vervolgens op **set para meters** om de sets voor de activiteit weer te geven.
1. Selecteer de parameterset **ResourceGroupNameParameterSetName**. Naast de velden **ResourceGroupName** en **name** staan uitroep tekens om aan te geven dat ze de vereiste para meters zijn. Houd er rekening mee dat in beide velden teken reeks waarden worden verwacht.
1. Selecteer **Name**. Selecteer **Power shell-expressie** voor het veld **gegevens bron** . Voor de virtuele machine die u gebruikt om dit runbook te starten, typt u de naam van de computer tussen dubbele aanhalings tekens. Klik op **OK**.
1. Selecteer **ResourceGroupName**. Gebruik de waarde Power shell-expressie voor het veld **gegevens bron** en typ de naam van de resource groep, omgeven door dubbele aanhalings tekens. Klik op **OK**.
1. Klik op **test venster** zodat u het runbook kunt testen.
1. Klik op **Start** om de test te starten. Als het proces is voltooid, controleert u of de VM is gestart. 

Uw runbook zou er op dit punt als volgt moeten uitzien: <br>![Configuratie runbookverificatie](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Stap 8: extra invoer parameters toevoegen

Met uw runbook wordt momenteel de virtuele machine gestart in de resource groep die u hebt opgegeven voor de cmdlet **Start-AzVM** . Het runbook is handiger als u zowel naam als resource groep opgeeft wanneer het runbook wordt gestart. Laten we invoer parameters toevoegen aan het runbook om deze functionaliteit te bieden.

1. Open de grafische editor door te klikken op **bewerken** in het deel venster MyFirstRunbook-grafisch.
1. Selecteer **invoer en uitvoer** en **Voeg vervolgens invoer** toe om het deel venster invoer parameter van Runbook te openen.
1. Maak de volgende instellingen in de beschik bare velden en klik vervolgens op **OK**.
   * **Naam** : Geef *VMName*op.
   * **Typ** --behoud de teken reeks instelling.
   * **Verplicht** : Wijzig de waarde in Ja.
1. Maak een tweede verplichte invoer parameter met de naam *ResourceGroupName* en klik vervolgens op **OK** om het deel venster invoer en uitvoer te sluiten.<br> ![Invoerparameters voor runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Selecteer de activiteit **Start-AzVM** en klik vervolgens op **para meters**.
1. Wijzig de **naam** van het **gegevens bron** veld in **Runbook-invoer**. Selecteer vervolgens **VMName**.
1. Wijzig het **gegevens bron** veld voor **ResourceGroupName** in **Runbook-invoer** en selecteer vervolgens **ResourceGroupName**.<br> ![para meters voor het starten van AzVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Sla het runbook op en open het testvenster. U kunt nu waarden opgeven voor de twee invoervariabelen die in de test worden gebruikt.
1. Sluit het testvenster.
1. Klik op **Publiceren** om de nieuwe versie van het runbook te publiceren.
1. Stop de VM die u eerder hebt gestart.
1. Klik op **Starten** om het runbook te starten. Typ de waarden voor **VMName** en **ResourceGroupName** voor de virtuele machine die u wilt starten.
1. Wanneer het runbook is voltooid, controleert u of de virtuele machine is gestart.

## <a name="step-9---create-a-conditional-link"></a>Stap 9: een voorwaardelijke koppeling maken

U wijzigt het runbook nu zodanig dat er alleen wordt geprobeerd de virtuele machine te starten als deze nog niet is gestart. Doe dit door een cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) toe te voegen waarmee de status van het exemplaar niveau van de virtuele machine wordt opgehaald. Vervolgens kunt u een Power shell-werk stroom code module met de naam status ophalen toevoegen met een code fragment van Power shell om te bepalen of de status van de virtuele machine wordt uitgevoerd of gestopt. Met een voorwaardelijke koppeling van de module status ophalen wordt **Start-AzVM** alleen uitgevoerd als de huidige uitvoerings status is gestopt. Aan het einde van deze procedure gebruikt uw runbook de cmdlet **Write-output** om een bericht uit te voeren waarin u wordt gewaarschuwd als de virtuele machine is gestart.

1. Open MyFirstRunbook-grafisch in de grafische editor.
1. Verwijder de koppeling tussen **abonnements-id opgeven** en **Start-AzVM** door erop te klikken en vervolgens op **Delete**te drukken.
1. Typ in het besturings element bibliotheek ' Get-AZ ' in het zoek veld.
1. Voeg **Get-AzVM** toe aan het canvas.
1. Selecteer **Get-AzVM** en vervolgens **para meter set** om de sets voor **Get-AzVM**weer te geven. 
1. Selecteer de para meter set GetVirtualMachineInResourceGroupNameParamSet. Er worden in de velden **ResourceGroupName** en **name** een uitroep teken naast de waarden vermeld, waarmee wordt aangegeven dat de vereiste para meters worden opgegeven. Houd er rekening mee dat in beide velden teken reeks waarden worden verwacht.
1. Selecteer onder **gegevens bron** voor **naam**de optie **Runbook-invoer**en vervolgens op **VMName**. Klik op **OK**.
1. Selecteer onder **gegevens bron** voor **ResourceGroupName**de optie **Runbook-invoer**en vervolgens op **ResourceGroupName**. Klik op **OK**.
1. Selecteer onder **gegevens bron** voor **status**de optie **constante waarde**en vervolgens **waar**. Klik op **OK**.
1. Maak een koppeling van **abonnements-id opgeven** voor **Get-AzVM**.
1. Vouw in het besturings element bibliotheek de **optie Runbook Control** uit en voeg **code** toe aan het canvas.  
1. Maak een koppeling vanuit **Get-AzVM** naar **code**.  
1. Klik op **code** en wijzig in het deel venster configuratie het label in **status ophalen**.
1. Selecteer **code** en de pagina code-editor wordt weer gegeven.  
1. Plak het volgende code fragment in de code-editor.

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

1. Maak een koppeling van de **Get-status** naar **Start-AzVM**.<br> ![Runbook met codemodule](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Selecteer de koppeling en wijzig in het deel venster configuratie de **voor waarde Toep assen** op Ja. Houd er rekening mee dat de koppeling een stippel lijn wordt, waarmee wordt aangegeven dat de doel activiteit alleen wordt uitgevoerd als de voor waarde wordt omgezet in True.  
1. Voor de **expressie voor waarde**typt u `$ActivityOutput['Get Status'] -eq "Stopped"`. **Start-AzVM** wordt nu alleen uitgevoerd als de virtuele machine is gestopt.
1. Vouw in het besturingselement Bibliotheek **Cmdlets** uit en vervolgens **Microsoft.PowerShell.Utility**.
1. Voeg **Write-Output** tweemaal aan het papier toe.
1. Voor het eerste besturings element **Write-output** klikt u op **para meters** en wijzigt u de waarde van het **Label** om VM op de **hoogte te stellen**.
1. Voor **input object**wijzigt u de **gegevens bron** in **Power shell-expressie**en typt u de expressie "$VMName is gestart.".
1. Klik in het tweede besturings element **Write-output** op **para meters** en wijzig de waarde van het **Label** om het starten van de **VM te melden is mislukt**.
1. Voor **input object**wijzigt u de **gegevens bron** in **Power shell-expressie**en typt u de expressie "$VMName kan niet worden gestart.".
1. Maak koppelingen van **Start-AzVM** om het starten van de **VM te melden** en het melden van de **VM is mislukt**.
1. Selecteer de koppeling voor het melden van de **VM gestart** en wijzig de **voor waarde apply** in True.
1. Typ `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`voor de **expressie voor waarde**. Dit besturings element **Write-output** wordt nu alleen uitgevoerd als de virtuele machine is gestart.
1. Selecteer de koppeling voor het starten van de start van de **VM is mislukt** en wijzig de **voor waarde apply** in True.
1. Typ `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`voor het veld **voor waarde-expressie** . Dit besturings element **Write-output** wordt nu alleen uitgevoerd als de virtuele machine niet is gestart. Het runbook moet er ongeveer uitzien als in de volgende afbeelding: <br> ![Runbook met Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Sla het runbook op en open het testvenster.
1. Start het runbook met de VM gestopt en start de computer opnieuw op.

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over grafisch ontwerpen [Grafisch ontwerpen in Azure Automation](automation-graphical-authoring-intro.md).
* Zie [Mijn eerste PowerShell-runbook](automation-first-runbook-textual-powershell.md) om aan de slag te gaan met PowerShell-runbooks.
* Zie [Mijn eerste PowerShell Workflow-runbook](automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks