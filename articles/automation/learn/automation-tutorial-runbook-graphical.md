---
title: Een grafisch runbook maken in Azure Automation
description: Zelf studie laat zien hoe u een eenvoudig grafisch runbook kunt maken, testen en publiceren in Azure Automation.
keywords: runbook, runbook-sjabloon, runbook-automatisering, azure-runbook
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: f87f389667043e26f066886eddcdb8061df0319f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81726184"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Zelf studie: een grafisch runbook maken

In deze zelfstudie wordt stap voor stap het maken van een [grafisch runbook](../automation-runbook-types.md#graphical-runbooks) in Azure Automation beschreven. U kunt grafisch en grafisch Power shell-werk stroom-runbooks maken en bewerken met behulp van de grafische editor in de Azure Portal. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een eenvoudig grafisch runbook maken
> * Het runbook testen en publiceren
> * De status van de runbook-taak uitvoeren en volgen
> * Het runbook bijwerken om een virtuele Azure-machine te starten met runbook-para meters en voorwaardelijke koppelingen

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](../automation-update-azure-modules.md).

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voor delen van uw MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](../automation-offering-get-started.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. Omdat u deze machine stopt en start, mag het geen productie-VM zijn.

## <a name="step-1---create-runbook"></a>Stap 1: runbook maken

Begin met het maken van een eenvoudig runbook waarmee de tekst `Hello World`wordt uitgevoerd.

1. Open uw Automation-account in Azure Portal. 

    Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U hebt als het goed is al enkele assets. De meeste van deze activa zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. U moet ook beschikken over de referentie-Asset die aan uw abonnement is gekoppeld.

2. Selecteer **runbooks** onder **proces automatisering** om de lijst met Runbooks te openen.

3. Maak een nieuw runbook door **een Runbook maken**te selecteren.

4. Geef het runbook de naam **MyFirstRunbook-Graphical**.

5. In dit geval gaat u een [grafisch runbook](../automation-graphical-authoring-intro.md)maken. Selecteer **grafisch** voor het **type Runbook**.<br> ![Nieuw runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)<br>

6. Klik op **Maken** om het runbook te maken en de grafische editor te openen.

## <a name="step-2---add-activities"></a>Stap 2: activiteiten toevoegen

Met het besturingselement Bibliotheek aan de linkerkant van de editor kunt u activiteiten selecteren die u aan uw runbook wilt toevoegen. U gaat een `Write-Output` cmdlet toevoegen aan uitvoer tekst van het runbook.

1. Klik in het besturings element bibliotheek op het zoek veld en typ `write-output`. Zoek resultaten worden weer gegeven in de volgende afbeelding. <br> ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Schuif omlaag naar de onderkant van de lijst. Klik met de rechter muisknop op **Write-output** en selecteer **toevoegen aan canvas**. U kunt ook op het weglatings teken (...) naast de naam van de cmdlet klikken en vervolgens **toevoegen aan papier**selecteren.

3. Klik op de activiteit **Write-Output** op het papier. Met deze actie opent u de pagina configuratie beheer, waarmee u de activiteit kunt configureren.

4. Het **Label** veld wordt standaard ingesteld op de naam van de cmdlet, maar u kunt dit wijzigen in een duidelijkere beschrijving. Wijzig deze in `Write Hello World to output`.

5. Klik op **Parameters** om waarden op te geven voor de parameters van de cmdlet.

   Sommige cmdlets hebben meerdere parametersets, en u moet selecteren welk abonnement u wilt gebruiken. In dit geval `Write-Output` heeft er slechts één para meter ingesteld.

6. Selecteer de `InputObject` para meter. Dit is de para meter die u gebruikt om de tekst op te geven die naar de uitvoer stroom moet worden verzonden.

7. De vervolg keuzelijst **gegevens bron** bevat bronnen die u kunt gebruiken om een parameter waarde in te vullen. Selecteer in dit menu de optie **Power shell-expressie**. 

   U kunt uitvoer van dergelijke bronnen gebruiken als een andere activiteit, een Automation-Asset of een Power shell-expressie. In dit geval is de uitvoer gewoon `Hello World`. U kunt een PowerShell-expressie gebruiken en een tekenreeks opgeven.<br>

8. Typ `Hello World` in het veld **expressie** en klik vervolgens twee keer op **OK** om terug te keren naar het canvas.

9. Klik op **Opslaan** om het runbook op te slaan.

## <a name="step-3---test-the-runbook"></a>Stap 3: het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, moet u het testen om er zeker van te zijn dat het goed werkt. Wanneer een runbook wordt getest, wordt de concept versie uitgevoerd en kunt u de uitvoer interactief bekijken.

1. Selecteer **test venster** om het deel venster test te openen.

2. Klik op **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.

3. Houd er rekening mee dat een [runbook-taak](../automation-runbook-execution.md) wordt gemaakt en dat de status ervan wordt weer gegeven in het deel venster.

   De taak status begint op `Queued`, wat aangeeft dat de taak wacht totdat een runbook worker in de Cloud beschikbaar is. De status verandert in `Starting` wanneer een werk nemer de taak claimt. Ten slotte wordt `Running` de status weer wanneer het runbook daad werkelijk wordt uitgevoerd.

4. Wanneer de runbook-taak is voltooid, wordt de uitvoer weer gegeven in het deel venster testen. In dit geval ziet `Hello World`u.

    ![Hallo wereld](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. Sluit het testvenster om terug te gaan naar het papier.

## <a name="step-4---publish-and-start-the-runbook"></a>Stap 4: het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog in de concept modus. Het moet worden gepubliceerd voordat u het in productie kan uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie omdat het runbook zojuist is gemaakt.

1. Selecteer **publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer u hierom wordt gevraagd.

2. Schuif naar links om het runbook weer te geven op de pagina Runbooks en houd er rekening mee dat de waarde voor de **ontwerp status** is ingesteld op **gepubliceerd**.

3. Schuif terug naar rechts om de pagina voor **MyFirstRunbook-grafisch**weer te geven.

   Met de opties bovenaan kunt u het runbook nu starten, een toekomstige start tijd plannen of een [webhook](../automation-webhooks.md) maken zodat het runbook kan worden gestart via een http-aanroep.

4. Selecteer **Start** en vervolgens **Ja** wanneer u wordt gevraagd het runbook te starten.

5. Er wordt een taak venster geopend voor de runbook-taak die is gemaakt. Controleer of het veld **taak status** **is voltooid**bevat.

6. Klik op **uitvoer** om de uitvoer pagina te openen, waar u `Hello World` kunt zien dat deze wordt weer gegeven.

7. Sluit de uitvoer pagina.

8. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. In de uitvoer stroom `Hello World` ziet u alleen. 

    Houd er rekening mee dat in het deel venster stromen andere stromen kunnen worden weer gegeven voor een runbook-taak, zoals uitgebreide en fout stromen, als het runbook hiernaar schrijft.

9. Sluit het deel venster streams en het taak venster om terug te keren naar de pagina MyFirstRunbook-graphical.

10. Selecteer **taken** onder **resources**om alle taken voor het runbook weer te geven. De pagina taken bevat een lijst met alle taken die door het runbook zijn gemaakt. Er wordt slechts één taak weer gegeven, omdat u de taak slechts één keer hebt uitgevoerd.

11. Klik op de taak naam om hetzelfde taak venster te openen dat u hebt bekeken tijdens het starten van het runbook. In dit deel venster kunt u de details weer geven van alle taken die voor het runbook zijn gemaakt.

## <a name="step-5---create-variable-assets"></a>Stap 5: variabele assets maken

U hebt uw runbook getest en gepubliceerd, maar tot nu toe is het niet nuttig om Azure-resources te beheren. Voordat u het runbook configureert voor verificatie, moet u een variabele maken om de abonnements-ID op te slaan, een activiteit instellen voor verificatie en vervolgens naar de variabele te verwijzen. Met een verwijzing naar de context van het abonnement kunt u eenvoudig met meerdere abonnementen werken.

1. Kopieer uw abonnements-ID uit de optie **abonnementen** in het navigatie deel venster.

2. Selecteer op de pagina Automation-accounts de optie **variabelen** onder **gedeelde resources**.

3. Selecteer **een variabele toevoegen**.

4. Stel op de pagina nieuwe variabele de volgende instellingen in de velden in.

    * **Naam** : Voer `AzureSubscriptionId`in.
    * **Waarde** : Voer uw abonnements-id in. 
    * **Type** --Bewaar reeks selecteren.
    * **Versleuteling** : gebruik de standaard waarde.

5. Klik op **Maken** om de variabele te maken.

## <a name="step-6---add-authentication"></a>Stap 6: verificatie toevoegen

Nu u een variabele hebt die de abonnements-ID moet bevatten, kunt u het runbook configureren voor verificatie met de uitvoeren als-referenties voor uw abonnement. Dit doet u door de Azure run as-verbinding als een Asset toe te voegen. U moet ook de cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) en de cmdlet [set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) toevoegen aan het canvas.

>[!NOTE]
>Voor Power shell- `Add-AzAccount` runbooks `Add-AzureRMAccount` en zijn aliassen `Connect-AzAccount`voor. Houd er rekening mee dat deze aliassen niet beschikbaar zijn voor uw grafische runbooks. Een grafisch runbook kan alleen worden `Connect-AzAccount`gebruikt.

1. Navigeer naar uw runbook en selecteer **bewerken** op de pagina MyFirstRunbook-graphical.

2. U hebt de `Write Hello World to output` post niet meer nodig. Klik op het weglatings teken en selecteer **verwijderen**.

3. Vouw in het besturings element bibliotheek de optie **assets**uit en vervolgens **verbindingen**. Voeg `AzureRunAsConnection` aan het canvas toe door **toevoegen aan canvas**te selecteren.

4. Wijzig `AzureRunAsConnection` de `Get Run As Connection`naam in.

5. Typ `Connect-AzAccount` in het besturings element bibliotheek in het zoek veld.

6. Toevoegen `Connect-AzAccount` aan het canvas.

7. Beweeg de `Get Run As Connection` muis aanwijzer totdat een cirkel wordt weer gegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl `Connect-AzAccount` naar om een koppeling te maken. Het runbook wordt gestart `Get Run As Connection` met en wordt `Connect-AzAccount`uitgevoerd.<br> ![Koppeling tussen activiteiten maken](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. Selecteer `Connect-AzAccount`op het canvas. Typ in het deel venster configuratie-instellingen **Aanmelden bij Azure** in het **Label** veld.

9. Klik op **para meters**en de pagina parameter configuratie van activiteit wordt weer gegeven.

10. De `Connect-AzAccount` cmdlet heeft meerdere parameter sets en u moet er een selecteren voordat u parameter waarden opgeeft. Klik op **para meter set** en selecteer vervolgens **ServicePrincipalCertificateWithSubscriptionId**.

11. De para meters voor deze parameterset worden weer gegeven op de pagina parameter configuratie van activiteit. Klik op **APPLICATIONID**.<br> ![Azure-account parameters toevoegen](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. Stel op de pagina parameter waarde de volgende instellingen in en klik vervolgens op **OK**.

   * **Gegevens bron** : Selecteer de **uitvoer**van de activiteit.
   * Gegevens bron lijst: Selecteer **Automation-verbinding ophalen**.
   * **Pad naar veld** -- `ApplicationId`type. U geeft de naam van de eigenschap voor het pad naar het veld op omdat de activiteit een object met meerdere eigenschappen uitvoert.

13. Klik op **CERTIFICATETHUMBPRINT**en voer op de pagina parameter waarde de volgende instellingen in en klik vervolgens op **OK**.

    * **Gegevens bron** : Selecteer de **uitvoer**van de activiteit.
    * Gegevens bron lijst: Selecteer **Automation-verbinding ophalen**.
    * **Pad naar veld** -- `CertificateThumbprint`type.

14. Klik op **SERVICEPRINCIPAL**en selecteer op de pagina parameter waarde **ConstantValue** voor het veld **gegevens bron** ; Klik op de optie **waar**. en klik vervolgens op **OK**.

15. Klik op **TENANTID**en maak de volgende instellingen op de pagina parameter waarde. Wanneer u klaar bent, klikt u twee keer op **OK** .

    * **Gegevens bron** : Selecteer de **uitvoer**van de activiteit. 
    * Gegevens bron lijst: Selecteer **Automation-verbinding ophalen**.
    * **Pad naar veld** -- `TenantId`type. 

16. Typ `Set-AzContext` in het besturings element bibliotheek in het zoek veld.

17. Toevoegen `Set-AzContext` aan het canvas.

18. Selecteer `Set-AzContext` op het canvas. Voer `Specify Subscription Id` in het deel venster configuratie-instellingen in het veld **Label** in.

19. Klik op **para meters** en de pagina parameter configuratie van activiteit wordt weer gegeven.

20. De `Set-AzContext` cmdlet heeft meerdere parameter sets en u moet er een selecteren voordat u parameter waarden opgeeft. Klik op **para meter set** en selecteer vervolgens **SubscriptionId**.

21. De para meters voor deze parameterset worden weer gegeven op de pagina parameter configuratie van activiteit. Klik op **SubscriptionID**.

22. Selecteer op de pagina parameter waarde de optie **variabel activum** voor het veld **gegevens bron** en selecteer **AzureSubscriptionId** in de lijst met bronnen. Wanneer u klaar bent, klikt u twee keer op **OK** .

23. Beweeg de `Login to Azure` muis aanwijzer totdat een cirkel wordt weer gegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl `Specify Subscription Id`naar. Het runbook moet er nu als volgt uitzien.

    ![Configuratie runbookverificatie](../media/automation-tutorial-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Stap 7: activiteit toevoegen om een virtuele machine te starten

Nu moet u een `Start-AzVM` activiteit toevoegen om een virtuele machine te starten. U kunt een wille keurige VM in uw Azure-abonnement kiezen en u hebt nu de naam hardcoding in de cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) .

1. Typ `Start-Az` in het besturings element bibliotheek in het zoek veld.

2. Voeg `Start-AzVM` toe aan het canvas en klik en sleep het `Specify Subscription Id`onder.

3. Beweeg de `Specify Subscription Id` muis aanwijzer totdat een cirkel wordt weer gegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl `Start-AzVM`naar.

4. Selecteer `Start-AzVM`. Klik op **para meters** en vervolgens op **set para meters** om de sets voor de activiteit weer te geven.

5. Selecteer **ResourceGroupNameParameterSetName** voor de parameterset. Naast de velden **ResourceGroupName** en **name** staan uitroep tekens om aan te geven dat ze de vereiste para meters zijn. Houd er rekening mee dat in beide velden teken reeks waarden worden verwacht.

6. Selecteer **Name**. Kies een **Power shell-expressie** voor het veld van de **gegevens bron** . Voor de virtuele machine die u gebruikt om dit runbook te starten, typt u de naam van de computer tussen dubbele aanhalings tekens. Klik op **OK**.

7. Selecteer **ResourceGroupName**. Gebruik de waarde **Power shell-expressie** voor het veld **gegevens bron** en typ de naam van de resource groep, omgeven door dubbele aanhalings tekens. Klik op **OK**.

8. Klik op **test venster** zodat u het runbook kunt testen.

9. Klik op **starten** om de test te starten. Als het proces is voltooid, controleert u of de VM is gestart. Het runbook moet er nu als volgt uitzien.

    ![Configuratie runbookverificatie](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Stap 8: extra invoer parameters toevoegen

Met uw runbook wordt momenteel de virtuele machine gestart in de resource groep die u `Start-AzVM` hebt opgegeven voor de cmdlet. Het runbook is handiger als u zowel naam als resource groep opgeeft wanneer het runbook wordt gestart. Laten we invoer parameters toevoegen aan het runbook om deze functionaliteit te bieden.

1. Open de grafische editor door te klikken op **bewerken** op de pagina MyFirstRunbook-graphical.

2. Selecteer **invoer en uitvoer** en **Voeg vervolgens invoer** toe om het deel venster invoer parameter van Runbook te openen.

3. Maak de volgende instellingen in de beschik bare velden en klik vervolgens op **OK**.
   * **Naam** : Geef `VMName`op.
   * **Typ** --behoud de teken reeks instelling.
   * **Verplicht** : Wijzig de waarde in **Ja**.

4. Maak een tweede verplichte invoer parameter met `ResourceGroupName` de naam en klik vervolgens op **OK** om het deel venster invoer en uitvoer te sluiten.<br> ![Invoerparameters voor runbook](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Selecteer de `Start-AzVM` activiteit en klik vervolgens op **para meters**.

6. Wijzig de **naam** van het **gegevens bron** veld in **Runbook-invoer**. Selecteer vervolgens **VMName**.

7. Wijzig het **gegevens bron** veld voor **ResourceGroupName** in **Runbook-invoer** en selecteer vervolgens **ResourceGroupName**.<br> ![Start-AzVM-para meters](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Sla het runbook op en open het testvenster. U kunt nu waarden opgeven voor de twee invoervariabelen die in de test worden gebruikt.

9. Sluit het testvenster.

10. Klik op **Publiceren** om de nieuwe versie van het runbook te publiceren.

11. Stop de VM die u eerder hebt gestart.

12. Klik op **Starten** om het runbook te starten. Typ de waarden voor `VMName` en `ResourceGroupName` voor de virtuele machine die u wilt starten.

13. Wanneer het runbook is voltooid, controleert u of de virtuele machine is gestart.

## <a name="step-9---create-a-conditional-link"></a>Stap 9: een voorwaardelijke koppeling maken

U kunt het runbook nu aanpassen zodat alleen wordt geprobeerd de virtuele machine te starten als deze nog niet is gestart. Doe dit door een cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) toe te voegen waarmee de status van het exemplaar niveau van de virtuele machine wordt opgehaald. Vervolgens kunt u een Power shell-werk stroom code `Get Status` module met de naam een code fragment van Power shell toevoegen om te bepalen of de status van de virtuele machine wordt uitgevoerd of gestopt. Een voorwaardelijke koppeling van de `Get Status` module wordt alleen `Start-AzVM` uitgevoerd als de huidige uitvoerings status is gestopt. Aan het einde van deze procedure gebruikt uw runbook de `Write-Output` cmdlet om een bericht uit te voeren om u te informeren of de virtuele machine is gestart.

1. Open **MyFirstRunbook-grafisch** in de grafische editor.

2. Verwijder de koppeling tussen `Specify Subscription Id` en `Start-AzVM` klik erop en druk vervolgens op **Delete**.

3. Typ `Get-Az` in het besturings element bibliotheek in het zoek veld.

4. Toevoegen `Get-AzVM` aan het canvas.

5. Selecteer `Get-AzVM` en klik vervolgens op **parameterset** om de sets voor de cmdlet weer te geven. 

6. Selecteer de parameterset **GetVirtualMachineInResourceGroupNameParamSet**. De velden **ResourceGroupName** en **name** bevatten een uitroep teken ernaast, waarmee wordt aangegeven dat de vereiste para meters worden opgegeven. Houd er rekening mee dat in beide velden teken reeks waarden worden verwacht.

7. Selecteer onder **gegevens bron** voor **naam**de optie **Runbook-invoer**en vervolgens op **VMName**. Klik op **OK**.

8. Selecteer onder **gegevens bron** voor **ResourceGroupName**de optie **Runbook-invoer**en vervolgens op **ResourceGroupName**. Klik op **OK**.

9. Selecteer onder **gegevens bron** voor **status**de optie **constante waarde**en vervolgens **waar**. Klik op **OK**.

10. Maak een koppeling van `Specify Subscription Id` naar `Get-AzVM`.

11. Vouw in het besturings element bibliotheek de **optie Runbook Control** uit en voeg **code** toe aan het canvas.  

12. Maak een koppeling van `Get-AzVM` naar `Code`.  

13. Klik `Code` op en wijzig in het deel venster configuratie het label in **status ophalen**.

14. Selecteren `Code` en de pagina code-editor wordt weer gegeven.  

15. Plak het volgende code fragment in de editor-pagina.

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

16. Maak een koppeling van `Get Status` naar `Start-AzVM`.

    ![Runbook met codemodule](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Selecteer de koppeling en wijzig in het deel venster configuratie de **voor waarde Toep assen** op **Ja**. Houd er rekening mee dat de koppeling een stippel lijn wordt, waarmee wordt aangegeven dat de doel activiteit alleen wordt uitgevoerd als de voor waarde wordt omgezet in True.  

18. Typ **Condition expression** `$ActivityOutput['Get Status'] -eq "Stopped"`voor voor waarde-expressie. `Start-AzVM`wordt nu alleen uitgevoerd als de virtuele machine is gestopt.

19. Vouw in het besturingselement Bibliotheek **Cmdlets** uit en vervolgens **Microsoft.PowerShell.Utility**.

20. Voeg `Write-Output` twee keer aan het canvas toe.

21. Voor het eerste `Write-Output` besturings element klikt u op **para meters** en wijzigt u de waarde van het **Label** om VM op de **hoogte te stellen**.

22. Voor **input object**wijzigt u de **gegevens bron** in **Power shell-expressie**en typt u `$VMName successfully started.`de expressie.

23. Klik in het `Write-Output` tweede besturings element op **para meters** en wijzig de waarde van het **Label** om het starten van de **VM te melden is mislukt**.

24. Voor **input object**wijzigt u de **gegevens bron** in **Power shell-expressie**en typt u `$VMName could not start`de expressie.

25. Koppelingen maken van `Start-AzVM` naar `Notify VM Started` en `Notify VM Start Failed`.

26. Selecteer de koppeling naar `Notify VM Started` en wijzig **voor waarde Toep assen** in waar.

27. Typ `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`voor de **voor waarde-expressie**. Dit `Write-Output` besturings element wordt nu alleen uitgevoerd als de virtuele machine is gestart.

28. Selecteer de koppeling naar `Notify VM Start Failed` en wijzig **voor waarde Toep assen** in waar.

29. Typ `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`voor het veld **voor waarde-expressie** . Dit `Write-Output` besturings element wordt nu alleen uitgevoerd als de virtuele machine niet is gestart. Uw runbook moet eruitzien zoals in de volgende afbeelding.

    ![Runbook met Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Sla het runbook op en open het testvenster.

31. Start het runbook met de VM gestopt en start de computer opnieuw op.

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over grafisch ontwerpen [grafisch ontwerpen in azure Automation](../automation-graphical-authoring-intro.md).
* Zie [een Power shell-Runbook maken](automation-tutorial-runbook-textual-powershell.md)om aan de slag te gaan met Power shell-runbooks.
* Zie [een Power shell workflow-Runbook maken](automation-tutorial-runbook-textual.md)om aan de slag te gaan met Power shell workflow-runbooks.
* Zie [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)(Engelstalig) voor een Power shell-cmdlet-verwijzing.