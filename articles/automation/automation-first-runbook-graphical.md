---
title: Mijn eerste grafische runbook in Azure Automation
description: Zelfstudie die u helpt bij het maken, testen en publiceren van een eenvoudig grafisch runbook.
keywords: runbook, runbook-sjabloon, runbook-automatisering, azure-runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: 6bd360b2075c337e3ed3d69d84368d16571a9335
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536051"
---
# <a name="my-first-graphical-runbook"></a>Mijn eerste grafische runbook

> [!div class="op_single_selector"]
> * [Grafisch](automation-first-runbook-graphical.md)
> * [Powershell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-werkstroom](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

In deze zelfstudie wordt stap voor stap het maken van een [grafisch runbook](automation-runbook-types.md#graphical-runbooks) in Azure Automation beschreven. Begin met een eenvoudig runbook dat u testen en publiceren, terwijl u leert hoe u de status van de runbook-taak bijhouden. Wijzig vervolgens de runbook om Azure-resources daadwerkelijk te beheren, in dit geval het starten van een virtuele Azure-machine. Voltooi de zelfstudie om het runbook robuuster te maken door runbook-parameters en voorwaardelijke koppelingen toe te voegen.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als je er nog geen hebt, kun je [je MSDN-abonneevoordeel activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of je aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Automation-account](automation-offering-get-started.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. Aangezien u stopt en deze machine start, zou het geen productie-VM moeten zijn.

## <a name="step-1---create-runbook"></a>Stap 1: runbook maken

Begin met het maken van een `Hello World`eenvoudig runbook waarmee de tekst wordt uitgevoerd.

1. Open uw Automation-account in Azure Portal. 

    Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U hebt als het goed is al enkele assets. De meeste van deze activa zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. U moet ook het referentie-item aan uw abonnement hebben gekoppeld.
2. Selecteer **Runbooks** onder **Procesautomatisering** om de lijst met runbooks te openen.
3. Maak een nieuw runbook door **Een runbook maken te**selecteren.
4. Geef het runbook de naam **MyFirstRunbook-Graphical**.
5. In dit geval maak je een [grafisch runbook.](automation-graphical-authoring-intro.md) Selecteer **Grafisch** voor **runbook-type**.<br> ![Nieuw runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Klik op **Maken** om het runbook te maken en de grafische editor te openen.

## <a name="step-2---add-activities"></a>Stap 2 - Activiteiten toevoegen

Met het besturingselement Bibliotheek aan de linkerkant van de editor kunt u activiteiten selecteren die u aan uw runbook wilt toevoegen. Je gaat een `Write-Output` cmdlet toevoegen aan de uitvoertekst uit het runbook.

1. Klik in het besturingselement Bibliotheek in `write-output`het zoekveld en typ . Zoekresultaten worden weergegeven in de volgende afbeelding. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Schuif omlaag naar de onderkant van de lijst. Klik met de rechtermuisknop op **Schrijfuitvoer** en selecteer **Toevoegen aan canvas**. U ook op de ellips (...) naast de naam van de cmdlet klikken en vervolgens **Toevoegen aan canvas**selecteren.
1. Klik op de activiteit **Write-Output** op het papier. Met deze actie wordt de pagina Configuratiebeheer geopend, waarmee u de activiteit configureren.
1. Het veld **Label** standaard op de naam van de cmdlet, maar u het wijzigen in iets vriendelijker. Verander het `Write Hello World to output`in .
1. Klik op **Parameters** om waarden op te geven voor de parameters van de cmdlet.

   Sommige cmdlets hebben meerdere parametersets en u moet selecteren welke u wilt gebruiken. In dit `Write-Output` geval is er slechts één parameter ingesteld.

1. Selecteer `InputObject` de parameter. Dit is de parameter die u gebruikt om de tekst op te geven die u naar de uitvoerstroom wilt verzenden.
1. Het vervolgkeuzemenu **Gegevensbron** bevat bronnen die u gebruiken om een parameterwaarde in te vullen. Selecteer **powershell-expressie**in dit menu . 

   U uitvoer gebruiken uit bronnen zoals een andere activiteit, een automatiseringselement of een PowerShell-expressie. In dit geval is `Hello World`de output gewoon . U kunt een PowerShell-expressie gebruiken en een tekenreeks opgeven.<br>

1. Typ **in** het `Hello World` veld Expressie en klik twee keer op **OK** om terug te keren naar het canvas.
1. Klik op **Opslaan** om het runbook op te slaan.

## <a name="step-3---test-the-runbook"></a>Stap 3: het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, moet u het testen om ervoor te zorgen dat het goed werkt. Als u een runbook test, wordt de conceptversie uitgevoerd en u de uitvoer interactief bekijken.

1. Selecteer **Het deelvenster Testen** om het deelvenster Testen te openen.
1. Klik op **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.
1. Houd er rekening mee dat een [runbook-taak](automation-runbook-execution.md) wordt gemaakt en de status ervan wordt weergegeven in het deelvenster.

   De taakstatus `Queued`begint als , wat aangeeft dat de taak wacht tot een runbook-werknemer in de cloud beschikbaar is. De status `Starting` verandert in wanneer een werknemer de taak claimt. Ten slotte wordt `Running` de status wanneer het runbook daadwerkelijk begint uit te voeren.

1. Wanneer de taak runbook is voltooid, wordt in het deelvenster Test de uitvoer weergegeven. In dit geval `Hello World`zie je.

    ![Hallo wereld](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Sluit het testvenster om terug te gaan naar het papier.

## <a name="step-4---publish-and-start-the-runbook"></a>Stap 4: het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog steeds in de conceptmodus. Het moet worden gepubliceerd voordat u het in productie kan uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie omdat het runbook zojuist is gemaakt.

1. Selecteer **Publiceren** om het runbook te publiceren en vervolgens **Ja** wanneer daarom wordt gevraagd.
1. Schuif naar links om het runbook op de pagina Runbooks weer te geven en houd er rekening mee dat de waarde **van de status van auteur** is ingesteld op **Gepubliceerd**.
1. Scroll terug naar rechts om de pagina voor **MyFirstRunbook-Graphical**te bekijken.

   Met de opties aan de bovenkant u het runbook nu starten, een toekomstige begintijd plannen of een [webhook](automation-webhooks.md) maken zodat het runbook kan worden gestart via een HTTP-gesprek.

1. Selecteer **Start** en vervolgens **Ja** wanneer u wordt gevraagd het runbook te starten.
1. Er wordt een taakvenster geopend voor de runbook-taak die is gemaakt. Controleer of in het veld **Taakstatus** Voltooid wordt **weergegeven**.
1. Klik **op Uitvoer** om de pagina `Hello World` Uitvoer te openen, waar u weergegeven zien.
1. Sluit de pagina Uitvoer.
1. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. U moet `Hello World` alleen zien in de uitvoerstroom. 

    Houd er rekening mee dat in het deelvenster Streams andere streams voor een runbook-taak kunnen worden weergegeven, zoals Verbose- en Foutstreams, als de runbook naar deze streams schrijft.
1. Sluit het deelvenster Streams en het deelvenster Taak om terug te keren naar de pagina **MyFirstRunbook-Graphical.**
1. Als u alle taken voor het runbook wilt weergeven, selecteert u **Taken** onder **Resources**. Op de pagina Vacatures worden alle taken weergegeven die door uw runbook zijn gemaakt. U zou slechts één taak moeten zien vermeld, aangezien u de baan slechts eenmaal hebt uitgevoerd.
1. Klik op de taaknaam om hetzelfde taakvenster te openen dat u hebt bekeken toen u het runbook begon. Gebruik dit deelvenster om de details weer te geven van een taak die voor het runbook is gemaakt.

## <a name="step-5---create-variable-assets"></a>Stap 5: variabele assets maken

U hebt uw runbook getest en gepubliceerd, maar tot nu toe is het niet handig om Azure-bronnen te beheren. Voordat u het runbook configureert om te verifiëren, moet u een variabele maken om de abonnements-id vast te houden, een activiteit in te stellen om te verifiëren en vervolgens naar de variabele te verwijzen. Met een verwijzing naar de abonnementscontext u eenvoudig met meerdere abonnementen werken.

1. Kopieer uw abonnements-ID vanuit de optie **Abonnementen** in het navigatiedeelvenster.
1. Selecteer op de pagina Automatiseringsaccounts de optie **Variabelen** onder **Gedeelde resources**.
1. Selecteer **Een variabele toevoegen**.
1. Maak op de pagina Nieuw variabele de volgende instellingen in de velden.

    * **Naam** -- `AzureSubscriptionId`voer in.
    * **Waarde** : voer uw abonnements-ID in. 
    * **Tekst** -- houd tekenreeks geselecteerd.
    * **Versleuteling** - gebruik de standaardwaarde.
1. Klik op **Maken** om de variabele te maken.

## <a name="step-6---add-authentication"></a>Stap 6 - Verificatie toevoegen

Nu u een variabele hebt om de abonnements-ID vast te houden, u de runbook configureren om te verifiëren met de referenties uitvoeren als voor uw abonnement. Doe dit door de Azure Run As-verbinding toe te voegen als een asset. U moet ook de [cmdlet Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) en de [cmdlet Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) aan het canvas toevoegen.

>[!NOTE]
>Voor `Add-AzAccount` PowerShell-runbooks `Add-AzureRMAccount` en zijn `Connect-AzAccount`aliassen voor . Houd er rekening mee dat deze aliassen niet beschikbaar zijn voor uw grafische runbooks. Een grafisch runbook `Connect-AzAccount`kan alleen zichzelf gebruiken.

1. Navigeer naar uw runbook en selecteer **Bewerken** op de pagina **MyFirstRunbook-Graphical.**
1. Je hebt de `Write Hello World to output` ingang niet meer nodig. Klik op de ellips en selecteer **Verwijderen**.
1. Vouw in het besturingselement Bibliotheek **ASSETS**uit , vervolgens **Verbindingen**. Toevoegen `AzureRunAsConnection` aan het canvas door **Toevoegen aan canvas te**selecteren .
1. Hernoem `AzureRunAsConnection` `Get Run As Connection`de naam naar .
1. Typ `Connect-AzAccount` in het besturingselement Bibliotheek het zoekveld.
1. Toevoegen `Connect-AzAccount` aan het canvas.
1. Deplaats `Get Run As Connection` over totdat er een cirkel op de onderkant van de vorm verschijnt. Klik op de cirkel `Connect-AzAccount` en sleep de pijl om een koppeling te vormen. Het runbook `Get Run As Connection` begint met `Connect-AzAccount`en wordt vervolgens uitgevoerd.<br> ![Koppeling tussen activiteiten maken](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Selecteer op het `Connect-AzAccount`canvas . Typ **Aanmelding bij Azure** in het veld **Label** in het configuratiebeheervenster.
1. Klik **op Parameters**en de pagina Activiteitsparameterconfiguratie wordt weergegeven.
1. De `Connect-AzAccount` cmdlet heeft meerdere parametersets en u moet er een selecteren voordat u parameterwaarden opgeeft. Klik **op Parameterset** en selecteer **ServicePrincipalCertificateWithSubscriptionId**.
1. De parameters voor deze parameterset worden weergegeven op de pagina Activiteitsparameterconfiguratie. Klik op **APPLICATIONID**.<br> ![Azure-accountparameters toevoegen](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Voer op de pagina Parameterwaarde de volgende instellingen uit en klik op **OK**.

   * **Gegevensbron** -- selecteer **Activiteitsuitvoer**.
   * Lijst met gegevensbronnen : selecteer **Automatiseringsverbinding opvragen**.
   * **Veldpad** -- `ApplicationId`typ . U geeft de naam van de eigenschap voor het veldpad op omdat de activiteit een object met meerdere eigenschappen uitbrengt.

1. Klik op **CERTIFICATETHUMBPRINT**en voer op de pagina Parameterwaarde de volgende instellingen uit en klik vervolgens op **OK**.

    * **Gegevensbron** -- selecteer **Activiteitsuitvoer**.
    * Lijst met gegevensbronnen : selecteer **Automatiseringsverbinding opvragen**.
    * **Veldpad** -- `CertificateThumbprint`typ .
1. Klik op **SERVICEPRINCIPAL**en selecteer op de pagina Parameterwaarde de optie **ConstantValue** voor het veld **Gegevensbron.** klik op de optie **Waar**; en klik vervolgens op **OK.**
1. Klik **op TENANTID**en maak de volgende instellingen op de pagina Parameterwaarde. Klik twee keer op **OK** als u klaar bent.

    * **Gegevensbron** -- selecteer **Activiteitsuitvoer**. 
    * Lijst met gegevensbronnen : selecteer **Automatiseringsverbinding opvragen**.
    * **Veldpad** -- `TenantId`typ . 
1. Typ `Set-AzContext` in het besturingselement Bibliotheek het zoekveld.
1. Toevoegen `Set-AzContext` aan het canvas.
1. Selecteer `Set-AzContext` op het canvas. Voer in het configuratiecontroledeelvenster het `Specify Subscription Id` veld **Label** in.
1. Klik **op Parameters** en de pagina Configuratie van activiteitsparameter wordt weergegeven.
1. De `Set-AzContext` cmdlet heeft meerdere parametersets en u moet er een selecteren voordat u parameterwaarden opgeeft. Klik **op Parameterset** en selecteer **Vervolgens SubscriptionId**.
1. De parameters voor deze parameterset worden weergegeven op de pagina Activiteitsparameterconfiguratie. Klik **op Abonnement-id**.
1. Selecteer op de pagina Parameterwaarde de optie **Variabele asset** voor het veld **Gegevensbron** en selecteer **AzureSubscriptionId** in de bronlijst. Klik twee keer op **OK** als u klaar bent.
1. Deplaats `Login to Azure` over totdat er een cirkel op de onderkant van de vorm verschijnt. Klik op de cirkel `Specify Subscription Id`en sleep de pijl naar . Uw runbook moet er op dit moment als volgt uitzien.

    ![Configuratie runbookverificatie](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Stap 7: activiteit toevoegen om een virtuele machine te starten

Nu moet u `Start-AzVM` een activiteit toevoegen om een virtuele machine te starten. U elke VM in uw Azure-abonnement kiezen en voor nu bent u hardcoderen de naam in de [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) cmdlet.

1. Typ `Start-Az` in het besturingselement Bibliotheek het zoekveld.
2. Voeg `Start-AzVM` toe aan het canvas en `Specify Subscription Id`klik en sleep het eronder.
1. Deplaats `Specify Subscription Id` over totdat er een cirkel op de onderkant van de vorm verschijnt. Klik op de cirkel `Start-AzVM`en sleep de pijl naar .
1. Selecteer `Start-AzVM`. Klik op **Parameters** en vervolgens **op Parameterset** om de sets voor de activiteit weer te geven.
1. Selecteer **ResourceGroupNameParameterSetName** voor de parameterset. De velden **ResourceGroupName** en **Name** hebben uitroeptekens ernaast om aan te geven dat er vereiste parameters zijn. Houd er rekening mee dat beide velden tekenreekswaarden verwachten.
1. Selecteer **Name**. Kies **PowerShell-expressie** voor het veld **Gegevensbron.** Voor de VM die u gebruikt om dit runbook te starten, typt u de machinenaam met dubbele aanhalingstekens. Klik op **OK**.
1. Selecteer **ResourceGroupName**. Gebruik de **energiebronexpressie** waarde voor het veld **Gegevensbron** en typ de naam van de resourcegroep met dubbele aanhalingstekens. Klik op **OK**.
1. Klik **op Deelvenster Testen,** zodat u het runbook testen.
1. Klik **op Start** om de test te starten. Zodra deze is voltooid, moet u ervoor zorgen dat de VM is gestart. Uw runbook moet er op dit moment als volgt uitzien.

    ![Configuratie runbookverificatie](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Stap 8 - Extra invoerparameters toevoegen

Uw runbook start momenteel de VM in de `Start-AzVM` resourcegroep die u voor de cmdlet hebt opgegeven. Het runbook is handiger als u zowel de naam als de resourcegroep opgeeft wanneer het runbook wordt gestart. Laten we invoerparameters toevoegen aan het runbook om die functionaliteit te bieden.

1. Open de grafische editor door te klikken op **Bewerken** in het deelvenster **MyFirstRunbook-Grafisch.**
1. Selecteer **Invoer en uitvoer** en voeg vervolgens invoer **toe** om het deelvenster Parameter van runbook-invoer te openen.
1. Voer de volgende instellingen in de opgegeven velden uit en klik op **OK**.
   * **Naam** -- `VMName`opgeven .
   * **Typ** - houd de tekenreeksinstelling.
   * **Verplicht** - verander de waarde in **Ja**.
1. Maak een tweede verplichte `ResourceGroupName` invoerparameter aangeroepen en klik op **OK** om het deelvenster Invoer en uitvoer te sluiten.<br> ![Invoerparameters voor runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Selecteer `Start-AzVM` de activiteit en klik op **Parameters**.
1. Wijzig het veld **Gegevensbron** voor **naam** in **invoer van runbook**. Selecteer vervolgens **VMName**.
1. Wijzig het **veld Gegevensbron** voor **ResourceGroupName** in **Runbook-invoer** en selecteer **Vervolgens ResourceGroupName**.<br> ![Begin-AzVM-parameters](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Sla het runbook op en open het testvenster. U kunt nu waarden opgeven voor de twee invoervariabelen die in de test worden gebruikt.
1. Sluit het testvenster.
1. Klik op **Publiceren** om de nieuwe versie van het runbook te publiceren.
1. Stop de VM die u eerder hebt gestart.
1. Klik op **Starten** om het runbook te starten. Typ de waarden `VMName` `ResourceGroupName` voor en voor de VM die u gaat starten.
1. Wanneer het runbook is voltooid, moet u ervoor zorgen dat de VM is gestart.

## <a name="step-9---create-a-conditional-link"></a>Stap 9: een voorwaardelijke koppeling maken

U nu het runbook wijzigen, zodat deze alleen probeert de VM te starten als deze nog niet is gestart. Doe dit door een [Get-AzVM-cmdlet](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) toe te voegen waarmee de status van de vm op instantieniveau wordt opgehaald. Vervolgens u een PowerShell-werkstroomcodemodule toevoegen die wordt aangeroepen `Get Status` met een fragment van PowerShell-code om te bepalen of de VM-status wordt uitgevoerd of gestopt. Een voorwaardelijke koppeling `Get Status` van `Start-AzVM` de module wordt alleen uitgevoerd als de huidige werkstatus is gestopt. Aan het einde van deze procedure `Write-Output` gebruikt uw runbook de cmdlet om een bericht uit te voeren om u te informeren of de VM is gestart.

1. Open **MyFirstRunbook-Graphical** in de grafische editor.
1. Verwijder de `Specify Subscription Id` koppeling `Start-AzVM` tussen en door erop te klikken en druk vervolgens op **Verwijderen**.
1. Typ `Get-Az` in het besturingselement Bibliotheek het zoekveld.
1. Toevoegen `Get-AzVM` aan het canvas.
1. Selecteer `Get-AzVM` en klik vervolgens op **Parameterset** om de sets voor de cmdlet weer te geven. 
1. Selecteer de parameterset **GetVirtualMachineInResourceGroupNameParamSet**. De velden **ResourceGroupName** en **Name** hebben uitroeptekens ernaast, wat aangeeft dat ze vereiste parameters opgeven. Houd er rekening mee dat beide velden tekenreekswaarden verwachten.
1. Selecteer onder **Gegevensbron** voor **Naam**de optie **Runbook-invoer**, vervolgens **VMName**. Klik op **OK**.
1. Selecteer onder **Gegevensbron** voor **ResourceGroupName**de optie **Runbook-invoer**, vervolgens **ResourceGroupName**. Klik op **OK**.
1. Selecteer Onder **Gegevensbron** voor **Status**de optie **Constante waarde**, vervolgens **True**. Klik op **OK**.
1. Maak een `Specify Subscription Id` koppeling `Get-AzVM`van naar .
1. Vouw in het besturingselement Bibliotheek **Runbook uit** en voeg **Code** toe aan het canvas.  
1. Maak een `Get-AzVM` koppeling `Code`van naar .  
1. Klik `Code` en wijzig in het deelvenster Configuratie het label in **Status opdoen**.
1. Selecteer `Code` en de pagina Codeeditor wordt weergegeven.  
1. Plak het volgende codefragment op de editorpagina.

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

1. Maak een `Get Status` koppeling `Start-AzVM`van naar .

    ![Runbook met codemodule](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Selecteer de koppeling en wijzig in het deelvenster Configuratie de **voorwaarde Toepassen op** **Ja**. Houd er rekening mee dat de koppeling een stippellijn wordt, wat aangeeft dat de doelactiviteit alleen wordt uitgevoerd als de voorwaarde true wordt opgelost.  
1. Typ **voor voorwaardeexpressie** `$ActivityOutput['Get Status'] -eq "Stopped"`. `Start-AzVM`wordt nu alleen uitgevoerd als de VM is gestopt.
1. Vouw in het besturingselement Bibliotheek **Cmdlets** uit en vervolgens **Microsoft.PowerShell.Utility**.
1. Voeg `Write-Output` twee keer toe aan het canvas.
1. Klik voor `Write-Output` het eerste besturingselement op **Parameters** en wijzig de **labelwaarde** **in VM Gestart melden**.
1. Wijzig **voor InputObject** **gegevensbron** in **PowerShell-expressie** `$VMName successfully started.`en typ de expressie .
1. Klik op `Write-Output` het tweede besturingselement op **Parameters** en wijzig de **labelwaarde** **om VM Start melden mislukt**.
1. Wijzig **voor InputObject** **gegevensbron** in **PowerShell-expressie** `$VMName could not start.`en typ de expressie .
1. Koppelingen maken `Start-AzVM` `Notify VM Started` van `Notify VM Start Failed`en .
1. Selecteer de `Notify VM Started` koppeling naar en wijzig **De voorwaarde Toepassen** in true.
1. Typ **voor**de `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`expressie Voorwaarde . Dit `Write-Output` besturingselement wordt nu alleen uitgevoerd als de VM wordt gestart.
1. Selecteer de `Notify VM Start Failed` koppeling naar en wijzig **De voorwaarde Toepassen** in true.
1. Typ voor het veld `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` **Voorwaardeexpressie** . Dit `Write-Output` besturingselement wordt nu alleen uitgevoerd als de VM niet is gestart. Uw runbook moet er uitzien als de volgende afbeelding.

    ![Runbook met Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Sla het runbook op en open het testvenster.
1. Start het runbook met de VM gestopt en de machine moet starten.

## <a name="next-steps"></a>Volgende stappen

* Zie [Grafische ontwerpen in Azure Automation](automation-graphical-authoring-intro.md)voor meer informatie over grafisch ontwerpen.
* Zie [Mijn eerste PowerShell-runbook](automation-first-runbook-textual-powershell.md)om aan de slag te gaan met PowerShell-runbooks.
* Zie [Mijn eerste PowerShell-werkstroomrunboek](automation-first-runbook-textual.md)om aan de slag te gaan met PowerShell-werkstroomboeken.