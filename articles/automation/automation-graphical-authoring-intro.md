---
title: Grafische ontwerpen in Azure Automation
description: Met grafische ontwerpen u runbooks maken voor Azure Automation zonder met code te werken. Dit artikel biedt een inleiding tot grafische authoring en alle details die nodig zijn om te beginnen met het maken van een grafisch runbook.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 09afca7eaf385795baf9c4a3c94232622527e357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501063"
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafische ontwerpen in Azure Automation

Met grafische ontwerpen u runbooks maken voor Azure Automation zonder de complexiteit van de onderliggende Windows PowerShell- of PowerShell-werkstroomcode. U activiteiten toevoegen aan het canvas vanuit een bibliotheek met cmdlets en runbooks, ze aan elkaar koppelen en configureren om een werkstroom te vormen. Als u ooit hebt gewerkt met System Center Orchestrator of Service Management Automation (SMA), moet grafische ontwerpen er vertrouwd uitzien. Dit artikel geeft een inleiding tot de concepten die u nodig hebt om aan de slag te gaan met het maken van een grafisch runbook.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="graphical-runbooks"></a>Grafische runbooks

Alle runbooks in Azure Automation zijn Windows PowerShell-werkstromen. Grafische runbooks en grafische PowerShell Workflow runbooks genereren PowerShell-code die de automatiseringsmedewerkers uitvoeren, maar die u niet bekijken of wijzigen. U een grafisch runbook converteren naar een grafisch PowerShell Workflow-runbook en vice versa. U deze runbooks echter niet converteren naar een tekstueel runbook. Bovendien kan de grafische editor Automation geen tekstueel runbook importeren.

## <a name="overview-of-graphical-editor"></a>Overzicht van grafische editor

U de grafische editor in de Azure-portal openen door een grafisch runbook te maken of te bewerken.

![Grafische werkruimte](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

In de volgende secties worden de besturingselementen in de grafische editor beschreven.

### <a name="canvas-control"></a>Canvasbesturingselement

Met het canvasbesturingselement u uw runbook ontwerpen. U activiteiten van de knooppunten in het besturingselement Bibliotheek toevoegen aan het runbook en deze verbinden met koppelingen om runbook-logica te definiëren. Aan de onderkant van het canvas zijn er besturingselementen waarmee u in- en uitzoomen.

### <a name="library-control"></a>Bibliotheekbesturingselement

Met het besturingselement Bibliotheek u [activiteiten](#activities) selecteren die u aan uw runbook wilt toevoegen. U voegt ze toe aan het canvas, waar u ze verbinden met andere activiteiten. Het besturingselement Bibliotheek bevat de secties die in de volgende tabel zijn gedefinieerd.

| Sectie | Beschrijving |
|:--- |:--- |
| Cmdlets |Alle cmdlets die in uw runbook kunnen worden gebruikt. Cmdlets worden per module georganiseerd. Alle modules die u in uw Automatiseringsaccount hebt geïnstalleerd, zijn beschikbaar. |
| Runbooks |De runbooks in uw Automation-account. U deze runbooks toevoegen aan het canvas om te worden gebruikt als onderliggende runbooks. Alleen runbooks van hetzelfde kerntype als het runbook dat wordt bewerkt, worden weergegeven. Voor grafische runbooks worden alleen op PowerShell gebaseerde runbooks weergegeven. Voor grafische PowerShell Workflow-runbooks worden alleen op PowerShell-werkstroomgebaseerde runbooks weergegeven. |
| Assets |De [automatiseringselementen](/previous-versions/azure/dn939988(v=azure.100)) in uw Automatiseringsaccount die u gebruiken in uw runbook. Als u een element toevoegt aan een runbook, wordt een werkstroomactiviteit toegevoegd die het geselecteerde element krijgt. In het geval van variabele activa u selecteren of u een activiteit wilt toevoegen om de variabele te krijgen of de variabele in te stellen. |
| Runbookbesturing |Beheer activiteiten die kunnen worden gebruikt in uw huidige runbook. Een junctionactiviteit neemt meerdere ingangen en wacht totdat alle zijn voltooid voordat de werkstroom wordt voortgezet. Bij een codeactiviteit worden een of meer regels PowerShell- of PowerShell-werkstroomcode uitgevoerd, afhankelijk van het grafische runbook-type. U deze activiteit gebruiken voor aangepaste code of voor functionaliteit die moeilijk te bereiken is met andere activiteiten. |

### <a name="configuration-control"></a>Configuratiebesturingselement

Met het configuratiebesturingselement u details opgeven voor een object dat op het canvas is geselecteerd. De eigenschappen die beschikbaar zijn in dit besturingselement zijn afhankelijk van het type object dat is geselecteerd. Wanneer u een optie kiest in het configuratiebesturingselement, worden extra bladen geopend om meer informatie te geven.

### <a name="test-control"></a>Testbesturingselement

Het besturingselement Testen wordt niet weergegeven wanneer de grafische editor voor het eerst wordt gestart. Het wordt geopend wanneer u interactief een grafisch runbook test.

## <a name="activities"></a>Activiteiten

Activiteiten zijn de bouwstenen van een runbook. Een activiteit kan een PowerShell-cmdlet, een onderliggende runbook of een werkstroom zijn. U een activiteit aan het runbook toevoegen door er met de rechtermuisknop op te klikken in het besturingselement Bibliotheek en **Toevoegen aan canvas te**selecteren. U vervolgens klikken en de activiteit slepen om deze overal op het canvas te plaatsen dat u leuk vindt. De locatie van de activiteit op het canvas heeft geen invloed op de werking van het runbook. U uw runbook op elke manier die u het meest geschikt om de werking te visualiseren.

![Toevoegen aan canvas](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Selecteer een activiteit op het canvas om de eigenschappen en parameters ervan in het configuratieblad te configureren. U het label van de activiteit wijzigen in een naam die u beschrijvend vindt. Het runbook loopt nog steeds de originele cmdlet. U wijzigt gewoon de weergavenaam die de grafische editor gebruikt. Houd er rekening mee dat het label uniek moet zijn in het runbook.

### <a name="parameter-sets"></a>Parametersets

Een parameterset definieert de verplichte en optionele parameters die waarden voor een bepaalde cmdlet accepteren. Alle cmdlets hebben ten minste één parameterset en sommige hebben meerdere sets. Als een cmdlet meerdere parametersets heeft, moet u de optie selecteren die u wilt gebruiken voordat u parameters configureren. U de parameterset wijzigen die door een activiteit wordt gebruikt door **Parameterset te** selecteren en een andere set te kiezen. In dit geval gaan alle parameterwaarden die u al hebt geconfigureerd, verloren.

In het volgende voorbeeld heeft de [get-AzVM-cmdlet](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) drie parametersets. In het voorbeeld wordt één set met de naam **ListVirtualMachineInResourceGroupParamSet**gebruikt , met één optionele parameter, voor het retourneren van alle virtuele machines in een resourcegroep. In het voorbeeld wordt ook de parameter **GetVirtualMachineInResourceGroupParamSet** gebruikt voor het opgeven van de virtuele machine om terug te keren. Deze set heeft twee verplichte parameters en één optionele parameter.

![Parameterset](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Parameterwaarden

Wanneer u een waarde voor een parameter opgeeft, selecteert u een gegevensbron om te bepalen hoe de waarde is opgegeven. De gegevensbronnen die beschikbaar zijn voor een bepaalde parameter zijn afhankelijk van de geldige waarden voor die parameter. Null is bijvoorbeeld geen beschikbare optie voor een parameter die geen null-waarden toestaat.

| Gegevensbron | Beschrijving |
|:--- |:--- |
| Constante waarde |Typ een waarde voor de parameter. Deze gegevensbron is alleen beschikbaar voor de volgende gegevenstypen: Int32, Int64, String, Boolean, DateTime, Switch. |
| Activiteitsuitvoer |Uitvoer gebruiken van een activiteit die voorafgaat aan de huidige activiteit in de werkstroom. Alle geldige activiteiten worden vermeld. Gebruik voor de parameterwaarde alleen de activiteit die de uitvoer produceert. Als de activiteit een object met meerdere eigenschappen uitzet, u de naam van een specifieke eigenschap intypen nadat u de activiteit hebt geselecteerd. |
| Runbook-invoer |Selecteer een runbook-invoer als invoer voor de activiteitsparameter. |
| Variabele asset |Selecteer een variabele Automatisering als invoer. |
| Referentie-asset |Selecteer een automatiseringsreferentie als invoer. |
| Certificaatasset |Selecteer een automatiseringscertificaat als invoer. |
| Verbindingsasset |Selecteer een automatiseringsverbinding als invoer. |
| PowerShell-expressie |Geef een eenvoudige [PowerShell-expressie op](#powershell-expressions). De expressie wordt geëvalueerd vóór de activiteit en het resultaat wordt gebruikt voor de parameterwaarde. U variabelen gebruiken om te verwijzen naar de uitvoer van een activiteit of een parameter voor runbook-invoer. |
| Niet geconfigureerd |Een waarde wissen die eerder is geconfigureerd. |

#### <a name="optional-additional-parameters"></a>Optionele extra parameters

Alle cmdlets hebben de mogelijkheid om extra parameters te bieden. Dit zijn PowerShell-gangbare parameters of andere aangepaste parameters. De grafische editor presenteert een tekstvak waarin u parameters opgeven met de syntaxis van PowerShell. Als u bijvoorbeeld `Verbose` de algemene parameter `-Verbose:$True`wilt gebruiken, moet u opgeven .

### <a name="retry-activity"></a>Activiteit opnieuw proberen

Met de functionaliteit voor een activiteit u de functionaliteit meerdere keren opnieuw uitvoeren totdat aan een bepaalde voorwaarde is voldaan, net als een lus. U deze functie gebruiken voor activiteiten die meerdere keren moeten worden uitgevoerd, foutgevoelig zijn, mogelijk meer dan één poging tot succes nodig hebben of de uitvoergegevens van de activiteit op geldige gegevens moeten testen.

Wanneer u een activiteit opnieuw probeert, u een vertraging en een voorwaarde instellen. De vertraging is de tijd (gemeten in seconden of minuten) dat de runbook wacht voordat de activiteit opnieuw wordt uitgevoerd. Als u geen vertraging opgeeft, wordt de activiteit onmiddellijk na het voltooien opnieuw uitgevoerd.

![Activiteit probeert vertraging opnieuw uit](media/automation-graphical-authoring-intro/retry-delay.png)

De voorwaarde opnieuw proberen is een PowerShell-expressie die wordt geëvalueerd na elke keer dat de activiteit wordt uitgevoerd. Als de expressie wordt opgelost in True, wordt de activiteit opnieuw uitgevoerd. Als de expressie wordt opgelost in False, wordt de activiteit niet opnieuw uitgevoerd en gaat de runbook door naar de volgende activiteit.

![Activiteit probeert vertraging opnieuw uit](media/automation-graphical-authoring-intro/retry-condition.png)

De voorwaarde voor opnieuw proberen `RetryData` kan een variabele met de naam gebruiken die toegang biedt tot informatie over de activiteitspogingen. Deze variabele heeft de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--- |:--- |
| `NumberOfAttempts` |Het aantal keren dat de activiteit is uitgevoerd. |
| `Output` |Uitvoer vanaf de laatste run van de activiteit. |
| `TotalDuration` |Getimede verstreken sinds de activiteit de eerste keer werd gestart. |
| `StartedAt` |Tijd (in UTC-indeling) toen de activiteit voor het eerst werd gestart. |

Hieronder volgen voorbeelden van voorwaarden voor het opnieuw proberen van activiteiten.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

Nadat u een voorwaarde voor een nieuwe poging voor een activiteit hebt geconfigureerd, bevat de activiteit twee visuele aanwijzingen om u eraan te herinneren. Een wordt weergegeven in de activiteit en de andere wordt weergegeven wanneer u de configuratie van de activiteit bekijkt.

![Visuele indicatoren voor activiteit opnieuw proberen](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Werkstroomscriptbesturingselement

Een werkstroomscriptbesturingselement is een speciale activiteit die PowerShell- of PowerShell-werkstroomscript accepteert, afhankelijk van het type grafisch runbook dat wordt geschreven. Dit besturingselement biedt functionaliteit die mogelijk niet op een andere manier beschikbaar is. Het kan geen parameters accepteren, maar het kan variabelen gebruiken voor activiteitsuitvoer en runbook-invoerparameters. Elke uitvoer van de activiteit wordt toegevoegd aan de databus. Een uitzondering is uitvoer zonder uitgaande koppeling, in welk geval de uitvoer wordt toegevoegd aan de uitvoer van het runbook.

De volgende code voert bijvoorbeeld datumberekeningen uit met `NumberOfDays`een regelvariabele voor runbook-invoer met de naam . Vervolgens wordt een berekende Datumtijdwaarde als uitvoer uitgevoerd die door latere activiteiten in het runbook moet worden gebruikt.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Koppelingen en werkstroom

Een koppeling in een grafisch runbook verbindt twee activiteiten. Het wordt weergegeven op het canvas als een pijl die van de bronactiviteit naar de doelactiviteit wijst. De activiteiten worden uitgevoerd in de richting van de pijl met de doelactiviteit die begint nadat de bronactiviteit is voltooid.

### <a name="link-creation"></a>Koppelingscreatie

U een koppeling maken tussen twee activiteiten door de bronactiviteit te selecteren en op de cirkel onder aan de vorm te klikken. Sleep de pijl naar de doelactiviteit en laat deze los.

![Een koppeling maken](media/automation-graphical-authoring-intro/create-link-options.png)

Selecteer de koppeling om de eigenschappen ervan in het configuratieblad te configureren. Eigenschappen omvatten het koppelingstype, dat wordt beschreven in de volgende tabel.

| Koppelingstype | Beschrijving |
|:--- |:--- |
| Pijplijn |De doelactiviteit wordt eenmaal uitgevoerd voor elke objectuitvoer vanuit de bronactiviteit. De doelactiviteit wordt niet uitgevoerd als de bronactiviteit geen uitvoer oplevert. Uitvoer van de bronactiviteit is beschikbaar als object. |
| Reeks |De doelactiviteit wordt slechts één keer uitgevoerd wanneer deze wordt uitgevoerd vanaf de bronactiviteit. Uitvoer van de bronactiviteit is beschikbaar als een array van objecten. |

### <a name="start-of-activity"></a>Begin van de activiteit

Een grafisch runbook begint met activiteiten die geen inkomende link hebben. Er is vaak slechts één activiteit die fungeert als startactiviteit voor het runbook. Als meerdere activiteiten geen inkomende koppeling hebben, begint het runbook met parallel. Het volgt de links om andere activiteiten uit te voeren als elk voltooit.

### <a name="link-conditions"></a>Koppelingsvoorwaarden

Wanneer u een voorwaarde opeengeeft op een koppeling, wordt de doelactiviteit alleen uitgevoerd als de voorwaarde true wordt opgelost. U gebruikt doorgaans `ActivityOutput` een variabele in een voorwaarde om de uitvoer uit de bronactiviteit op te halen.

Voor een pijplijnkoppeling moet u een voorwaarde opgeven voor één object. Het runbook evalueert de voorwaarde voor elke objectuitvoer op de bronactiviteit. Vervolgens wordt de doelactiviteit uitgevoerd voor elk object dat aan de voorwaarde voldoet. Met een bronactiviteit van `Get-AzVM`, u bijvoorbeeld de volgende syntaxis gebruiken voor een voorwaardelijke pijplijnkoppeling om alleen virtuele machines op te halen in de resourcegroep met de naam Group1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Voor een koppeling naar de reeks evalueert het runbook de voorwaarde slechts één keer, omdat één array met alle objecten uit de bronactiviteit wordt geretourneerd. Hierdoor kan het runbook geen sequentiekoppeling gebruiken voor het filteren, zoals het kan met een pijplijnkoppeling. De volgordekoppeling kan eenvoudig bepalen of de volgende activiteit wordt uitgevoerd.

Neem bijvoorbeeld de volgende reeks activiteiten in ons **Start VM-runbook:**

![Voorwaardelijke koppeling met reeksen](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Het runbook maakt gebruik van drie verschillende `VMName` reekskoppelingen die de waarden van de invoerparameters verifiëren en `ResourceGroupName` de juiste actie bepalen om te nemen. Mogelijke acties zijn het starten van één VM, het starten van alle VM's in de resourcegroep of het starten van alle VM's in een abonnement. Voor de volgorde `Connect to Azure` `Get single VM`link tussen en , hier is de voorwaarde logica:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

Wanneer u een voorwaardelijke koppeling gebruikt, worden de gegevens die beschikbaar zijn van de bronactiviteit naar andere activiteiten in die vertakking gefilterd op de voorwaarde. Als een activiteit de bron is voor meerdere koppelingen, zijn de gegevens die beschikbaar zijn voor activiteiten in elke branch afhankelijk van de voorwaarde in de koppeling die verbinding maakt met die branch.

De `Start-AzVM` activiteit in het onderstaande runbook start bijvoorbeeld alle virtuele machines. Het heeft twee voorwaardelijke links. De eerste voorwaardelijke koppeling `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` gebruikt de `Start-AzVM` expressie om te filteren als de activiteit is voltooid. De tweede voorwaardelijke koppeling `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` gebruikt de `Start-AzVm` expressie om te filteren als de activiteit de virtuele machine niet start.

![Voorbeeld van voorwaardelijke koppeling](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Elke activiteit die de eerste koppeling volgt `Get-AzureVM` en de activiteitsuitvoer gebruikt, haalt `Get-AzureVM` alleen de virtuele machines op die zijn gestart op het moment dat deze werd uitgevoerd. Elke activiteit die de tweede koppeling volgt, krijgt alleen `Get-AzureVM` de virtuele machines die zijn gestopt op het moment dat deze werd uitgevoerd. Elke activiteit na de derde koppeling krijgt alle virtuele machines, ongeacht hun bedrijfsstatus.

### <a name="junctions"></a>Kruispunten

Een knooppunt is een speciale activiteit die wacht tot alle inkomende takken zijn voltooid. Hierdoor kan het runbook meerdere activiteiten parallel uitvoeren en ervoor zorgen dat ze allemaal zijn voltooid voordat ze verder gaan.

Hoewel een knooppunt een onbeperkt aantal inkomende koppelingen kan hebben, kan slechts één van die koppelingen een pijplijn zijn. Het aantal inkomende reekskoppelingen is niet beperkt. U de verbinding met meerdere inkomende pijplijnkoppelingen maken en het runbook opslaan, maar deze mislukt wanneer deze wordt uitgevoerd.

Het onderstaande voorbeeld maakt deel uit van een runbook dat een set virtuele machines start en tegelijkertijd patches downloadt die op die machines moeten worden toegepast. Het maakt gebruik van een knooppunt om ervoor te zorgen dat beide processen worden voltooid voordat het runbook wordt voortgezet.

![Verbinding](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cycli

Een cyclus wordt gevormd wanneer een doelactiviteit wordt gekoppeld aan de bronactiviteit of naar een andere activiteit die uiteindelijk wordt teruggekoppeld naar de bron. Grafische ontwerpen ondersteunt momenteel geen cycli. Als uw runbook een cyclus heeft, wordt deze op de juiste manier opgeslagen, maar wordt er een foutmelding ontvangen wanneer deze wordt uitgevoerd.

![Cyclus](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>Het delen van gegevens tussen activiteiten

Alle gegevens die een activiteit uitvoert met een uitgaande koppeling, worden naar de databus voor het runbook geschreven. Elke activiteit in het runbook kan gegevens op de databus gebruiken om parameterwaarden in te vullen of in scriptcode op te nemen. Een activiteit heeft toegang tot de uitvoer van eerdere activiteiten in de werkstroom.

Hoe de gegevens naar de databus worden geschreven, is afhankelijk van het type koppeling op de activiteit. Voor een pijplijnkoppeling worden de gegevens uitgevoerd als meerdere objecten. Voor een reekskoppeling worden de gegevens uitgevoerd als een array. Als er slechts één waarde is, wordt deze uitgevoerd als een array met één element.

Uw runbook heeft twee manieren om toegang te krijgen tot gegevens op de databus: 
* Gebruik een gegevensbron voor activiteitsuitvoer.
* Gebruik een PowerShell-expressiegegevensbron.

Het eerste mechanisme gebruikt een gegevensbron voor activiteitsuitvoer om een parameter van een andere activiteit in te vullen. Als de uitvoer een object is, kan de runbook één eigenschap opgeven.

![activiteitsuitvoer](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Het tweede mechanisme voor gegevenstoegang haalt de uitvoer van een activiteit op in `ActivityOutput` een PowerShell-expressiegegevensbron of een werkstroomscriptactiviteit met een variabele, met behulp van de onderstaande syntaxis. Als de uitvoer een object is, kan uw runbook één eigenschap opgeven.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Controlepunten

U [controlepunten](automation-powershell-workflow.md#checkpoints) instellen in een grafische PowerShell-werkstroomrunboek door **controlepuntrunbook** voor elke activiteit te selecteren. Hierdoor wordt een controlepunt ingesteld nadat de activiteit is uitgevoerd.

![Controlepunt](media/automation-graphical-authoring-intro/set-checkpoint.png)

Controlepunten zijn alleen ingeschakeld in grafische PowerShell Workflow-runbooks en zijn niet beschikbaar in grafische runbooks. Als de runbook Azure-cmdlets gebruikt, moet deze `Connect-AzAccount` elke activiteit met een activiteit volgen. De verbindingsbewerking wordt gebruikt in het geval de runbook wordt opgeschort en moet opnieuw worden opgestart vanuit dit controlepunt op een andere werknemer.

## <a name="runbook-input-and-output"></a>Invoer en uitvoer van runbook

### <a name="runbook-input"></a>Runbook-invoer<a name="runbook-input"></a>

Een runbook vereist invoer van een gebruiker die de runbook start via de Azure-portal of van een andere runbook, als de huidige als onderliggend wordt gebruikt. Voor een runbook dat een virtuele machine maakt, moet de gebruiker bijvoorbeeld informatie opgeven zoals de naam van de virtuele machine en andere eigenschappen telkens wanneer het runbook wordt gestart.

Het runbook accepteert invoer door een of meer invoerparameters te definiëren. De gebruiker geeft waarden voor deze parameters elke keer dat het runbook wordt gestart. Wanneer de gebruiker de runbook start met de Azure-portal, wordt de gebruiker gevraagd waarden op te geven voor elke invoerparameter die wordt ondersteund door de runbook.

Wanneer u uw runbook maakt, hebt u toegang tot de invoerparameters door op **Invoer en uitvoer** op de werkbalk runbook te klikken. Hiermee wordt het besturingselement Invoer en uitvoer geopend waar u een bestaande invoerparameter bewerken of een nieuwe maken door op **Invoer toevoegen**te klikken.

![Invoer toevoegen](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Elke invoerparameter wordt gedefinieerd door de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--- |:--- |
| Name | Vereist. De naam van de parameter. De naam moet uniek zijn in het runbook. Het moet beginnen met een brief en kan alleen letters, cijfers en underscores bevatten. De naam mag geen spatie bevatten. |
| Beschrijving |Optioneel. Beschrijving van het doel voor de invoerparameter. |
| Type | Optioneel. Gegevenstype dat wordt verwacht voor de parameterwaarde. De Azure-portal biedt een geschikt besturingselement voor het gegevenstype voor elke parameter wanneer u om invoer vraagt. Ondersteunde parametertypen zijn String, Int32, Int64, Decimal, Boolean, DateTime en Object. Als een gegevenstype niet is geselecteerd, wordt dit standaard ingesteld op Tekenreeks.|
| Verplicht | Optioneel. Als u aangeeft of er een waarde moet worden opgegeven voor de parameter. Als u `yes`kiest, moet een waarde worden opgegeven wanneer het runbook wordt gestart. Als u `no`kiest, is een waarde niet vereist wanneer de runbook wordt gestart en kan een standaardwaarde worden gebruikt. De runbook kan niet worden gestart als u geen waarde opgeeft voor elke verplichte parameter waarvoor geen standaardwaarde is gedefinieerd. |
| Standaardwaarde | Optioneel. De waarde die voor een parameter wordt gebruikt als deze niet wordt doorgegeven wanneer het runbook wordt gestart. Als u een standaardwaarde wilt instellen, kiest u `Custom`. Selecteer `None` of u geen standaardwaarde wilt geven. |

### <a name="runbook-output"></a>Runbook-uitvoer

Grafische ontwerpen slaat gegevens op die zijn gemaakt door activiteiten die geen uitgaande koppeling hebben naar de [uitvoer van het runbook](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). De uitvoer wordt opgeslagen met de runbook-taak en is beschikbaar voor een bovenliggende runbook wanneer het runbook als onderliggend wordt gebruikt.

## <a name="powershell-expressions"></a>PowerShell-expressies

Een van de voordelen van grafische authoring is dat het u toelaat om een runbook te bouwen met minimale kennis van PowerShell. Op dit moment moet u echter wel een beetje PowerShell kennen voor het vullen van bepaalde [parameterwaarden](#activities) en voor het instellen van [koppelingsvoorwaarden.](#links-and-workflow) Deze sectie biedt een snelle inleiding tot PowerShell-expressies. Volledige details van PowerShell zijn beschikbaar bij [Scripting met Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Gegevensbron powershell-expressie

U een PowerShell-expressie als gegevensbron gebruiken om de waarde van een [activiteitsparameter](#activities) te vullen met de resultaten van PowerShell-code. De expressie kan een enkele regel code zijn die een eenvoudige functie of meerdere regels uitvoert die een complexe logica uitvoeren. Elke uitvoer van een opdracht die niet aan een variabele is toegewezen, is uitvoer naar de parameterwaarde.

Met de volgende opdracht wordt bijvoorbeeld de huidige datum uitgevoerd.

```powershell-interactive
Get-Date
```

Het volgende codefragment bouwt een tekenreeks vanaf de huidige datum en wijst deze toe aan een variabele. De code stuurt de inhoud van de variabele naar de uitvoer.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

In de volgende opdrachten worden de huidige datum geëvalueerd en wordt een tekenreeks die aangeeft of de huidige dag een weekend of een weekdag is, retourneren.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Activiteitsuitvoer

Als u de uitvoer van een vorige activiteit `ActivityOutput` in uw runbook wilt gebruiken, gebruikt u de variabele met de volgende syntaxis.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

U bijvoorbeeld een activiteit hebben met een eigenschap waarvoor de naam van een virtuele machine vereist is. In dit geval kan uw runbook de volgende expressie gebruiken.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Als de eigenschap het virtuele machineobject vereist in plaats van alleen een naam, retourneert de runbook het hele object met de volgende syntaxis.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Het runbook kan de uitvoer van een activiteit gebruiken in een complexere expressie, zoals het volgende. Deze expressie voegt tekst toe aan de naam van de virtuele machine.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>Voorwaarden

Gebruik [vergelijkingsoperatoren](https://technet.microsoft.com/library/hh847759.aspx) om waarden te vergelijken of te bepalen of een waarde overeenkomt met een opgegeven patroon. Een vergelijking retourneert een waarde van Waar of Onwaar.

De volgende voorwaarde bepaalt bijvoorbeeld of de virtuele `Get-AzureVM` machine van een activiteit met de naam momenteel is gestopt.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

De volgende voorwaarde bepaalt of dezelfde virtuele machine zich in een andere staat bevindt dan gestopt.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

U meerdere voorwaarden in uw runbook deelnemen `-and` `-or`met een [logische operator,](https://technet.microsoft.com/library/hh847789.aspx)zoals of. De volgende voorwaarde controleert bijvoorbeeld of de virtuele machine in het vorige voorbeeld zich in een staat van Gestopt of Stoppen bevindt.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Hashtabellen

[Hashtables](https://technet.microsoft.com/library/hh847780.aspx) zijn naam-waardeparen die handig zijn voor het retourneren van een set waarden. Mogelijk ziet u ook een hashtable die een woordenboek wordt genoemd. Eigenschappen voor bepaalde activiteiten verwachten een hashtable in plaats van een eenvoudige waarde.

Maak een hashtable met de volgende syntaxis. Het kan een willekeurig aantal vermeldingen bevatten, maar elk wordt gedefinieerd door een naam en waarde.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Met de volgende expressie wordt bijvoorbeeld een hashtable gemaakt die moet worden gebruikt als gegevensbron voor een activiteitsparameter die een hashtabel met waarden voor een zoekopdracht op internet verwacht.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

In het volgende voorbeeld wordt `Get Twitter Connection` uitvoer gebruikt van een activiteit die is aangeroepen om een hashtabel te vullen.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>Authenticeren naar Azure-bronnen

Voer boeken uit in Azure Automation waarvoor Azure-resources moeten worden gesommeerd voor Azure. Het [Run As-account](automation-create-runas-account.md), ook wel serviceprincipal genoemd, is het standaardmechanisme dat een runbook voor automatisering gebruikt om toegang te krijgen tot Azure Resource Manager-bronnen in uw abonnement. U deze functionaliteit toevoegen aan een `AzureRunAsConnection` grafisch runbook door het verbindingselement, dat gebruikmaakt van de cmdlet PowerShell [Get-AutomationConnection,](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) aan het canvas toe te voegen. U ook de [Connect-AzAccount-cmdlet](/powershell/module/az.accounts/connect-azaccount) toevoegen. Dit scenario wordt geïllustreerd in het volgende voorbeeld.

![Uitvoeren als verificatieactiviteiten](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

De `Get Run As Connection` activiteit `Get-AutomationConnection`of wordt geconfigureerd met een gegevensbron met constante waarde met de naam `AzureRunAsConnection`.

![Uitvoeren als verbindingsconfiguratie](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

De volgende `Connect-AzAccount`activiteit, , voegt de geverifieerde Run As-account voor gebruik in het runbook.

![Parameterset Connect-AzAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Voor `Add-AzAccount` PowerShell-runbooks `Add-AzureRMAccount` en zijn `Connect-AzAccount`aliassen voor . Houd er rekening mee dat deze aliassen niet beschikbaar zijn voor uw grafische runbooks. Een grafisch runbook `Connect-AzAccount` kan alleen zichzelf gebruiken.

Geef voor de parametervelden **APPLICATIONID**, **CERTIFICATETHUMBPRINT**en **TENANTID**de naam van de eigenschap voor het veldpad op, omdat de activiteit een object met meerdere eigenschappen uitbrengt. Als het runbook wordt uitgevoerd, mislukt het anders tijdens het verifiëren. Dit is wat je minimaal nodig hebt om je runbook te verifiëren met het Run As-account.

Sommige abonnees maken een Automatiseringsaccount met een [Azure AD-gebruikersaccount](automation-create-aduser-account.md) om de klassieke implementatie van Azure of azure resource manager-bronnen te beheren. Om achterwaartse compatibiliteit voor deze abonnees te behouden, is het `Add-AzureAccount` verificatiemechanisme dat u in uw runbook moet gebruiken de cmdlet met een [referentie-asset.](automation-credentials.md) Het item vertegenwoordigt een Active Directory-gebruiker met toegang tot het Azure-account.

U deze functionaliteit voor uw grafische runbook inschakelen door een `Add-AzureAccount` referentie-element toe te voegen aan het canvas, gevolgd door een activiteit die de referentie-asset voor de invoer gebruikt. Zie het volgende voorbeeld

![Verificatieactiviteiten](media/automation-graphical-authoring-intro/authentication-activities.png)

Het runbook moet zich verifiëren bij de start en na elk controlepunt. U moet dus `Add-AzureAccount` een `Checkpoint-Workflow` activiteit gebruiken na elke activiteit. U hoeft geen extra referentieactiviteit te gebruiken.

![Activiteitsuitvoer](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>Een grafisch runbook exporteren en importeren

U alleen de gepubliceerde versie van een grafisch runbook exporteren. Als het runbook nog niet is gepubliceerd, is de knop **Exporteren** uitgeschakeld. Wanneer u op de knop **Exporteren** klikt, wordt het runbook naar uw lokale computer gedownload. De naam van het bestand komt overeen met de naam van het runbook met een **.graphrunbook-extensie.**

U een grafisch of grafisch PowerShell-werkstroomrunboekbestand importeren door de optie **Importeren** te selecteren wanneer u een runbook toevoegt. Wanneer u het bestand selecteert dat u wilt importeren, u dezelfde naam behouden of een nieuw bestand opgeven. In het veld **Runbook Type** wordt het type runbook weergegeven nadat het geselecteerde bestand is beoordeeld. Als u een ander type probeert te selecteren dat niet correct is, presenteert de grafische editor een bericht waarin wordt gesteld dat er potentiële conflicten zijn en dat er syntaxisfouten kunnen optreden tijdens de conversie.

![Runbook importeren](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="testing-a-graphical-runbook"></a>Een grafisch runbook testen

Elk grafisch runbook in Azure Automation heeft een conceptversie en een gepubliceerde versie. U alleen de gepubliceerde versie uitvoeren, terwijl u alleen de conceptversie bewerken. De gepubliceerde versie wordt niet beïnvloed door wijzigingen in de conceptversie. Wanneer de conceptversie klaar is voor gebruik, publiceert u deze, die de huidige gepubliceerde versie overschrijft met uw conceptversie.

U de conceptversie van een runbook testen in de Azure-portal terwijl de gepubliceerde versie ongewijzigd blijft. U ook een nieuw runbook testen voordat het is gepubliceerd, zodat u controleren of de runbook correct werkt voordat de versie wordt vervangen. Als u een runbook test, wordt de conceptversie uitgevoerd en wordt ervoor zorgen dat alle acties die worden uitgevoerd, worden voltooid. Er wordt geen taakgeschiedenis gemaakt, maar in het deelvenster Uitvoer testen wordt de uitvoer weergegeven.

Open het besturingselement Test voor uw grafische runbook door het runbook te openen voor bewerken en vervolgens op **Het deelvenster Testen**te klikken . Het besturingselement Testen vraagt om invoerparameters en u het runbook starten door op **Start**te klikken.

## <a name="publishing-a-graphical-runbook"></a>Een grafisch runbook publiceren

Publiceer een grafisch loopboek door het runbook te openen voor bewerking en vervolgens op **Publiceren**te klikken. Mogelijke statussen voor het runbook zijn:

* Nieuw - het runbook is nog niet gepubliceerd. 
* Gepubliceerd - het runbook is gepubliceerd.
* In bewerken - het runbook is bewerkt nadat het is gepubliceerd, en de concept- en gepubliceerde versies zijn verschillend.

![Runbook-statussen](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

U terugkeren naar de gepubliceerde versie van een runbook. Deze bewerking gooit alle wijzigingen weg die zijn aangebracht sinds het runbook voor het laatst is gepubliceerd. Het vervangt de conceptversie van het runbook door de gepubliceerde versie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Mijn eerste PowerShell-werkstroomrunboek](automation-first-runbook-textual.md)om aan de slag te gaan met PowerShell Workflow-runbooks.
* Zie Mijn eerste grafische runbook om aan de slag te gaan met grafische [runbooks.](automation-first-runbook-graphical.md)
* Zie [Azure Automation runbook-typen](automation-runbook-types.md)voor meer informatie over runbook-typen en hun voordelen en beperkingen.
* Zie [Azure Run As-account configureren](automation-sec-configure-azure-runas-account.md)als u wilt weten hoe u verifiëren met het account Automation Run As.
