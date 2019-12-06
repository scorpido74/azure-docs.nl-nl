---
title: Grafisch ontwerpen in Azure Automation
description: Met grafische ontwerpen kunt u runbooks maken voor Azure Automation zonder dat u code hoeft te gebruiken. Dit artikel bevat een inleiding tot het ontwerpen van afbeeldingen en alle informatie die nodig is om te beginnen met het maken van een grafisch runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 82a06510bd9d1e0de2b38260773cb4848156bf12
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850292"
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafisch ontwerpen in Azure Automation

Met grafische ontwerpen kunt u runbooks voor Azure Automation maken zonder de complexiteit van de onderliggende Windows Power shell-of Power shell-werk stroom code. U voegt activiteiten toe aan het canvas vanuit een bibliotheek met cmdlets en runbooks, koppel ze aan elkaar en configureren om een werk stroom te vormen. Als u ooit met System Center Orchestrator of Service Management Automation (SMA) hebt gewerkt, moet dit er bekend uitzien

Dit artikel bevat een inleiding tot grafische ontwerpen en de concepten die u nodig hebt om aan de slag te gaan met het maken van een grafisch runbook.

## <a name="graphical-runbooks"></a>Grafische runbooks

Alle runbooks in Azure Automation zijn Windows Power shell-werk stromen. Grafische en grafische runbooks in Power shell-werk stroom genereren Power shell-code die wordt uitgevoerd door de Automation-werk rollen, maar u kunt deze niet weer geven of rechtstreeks wijzigen. Een grafisch runbook kan worden geconverteerd naar een grafisch Power shell-werk stroom-runbook en vice versa, maar kan niet worden geconverteerd naar een tekst runbook. Een bestaand tekst runbook kan niet in de grafische editor worden geïmporteerd.

## <a name="overview-of-graphical-editor"></a>Overzicht van grafische editor

U kunt de grafische editor openen in de Azure Portal door een grafisch runbook te maken of te bewerken.

![Grafische werk ruimte](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

In de volgende secties worden de besturings elementen in de grafische editor beschreven.

### <a name="canvas"></a>Canvas

Op het canvas kunt u het runbook ontwerpen. U voegt activiteiten van de knoop punten in het besturings element bibliotheek toe aan het runbook en verbindt deze met koppelingen om de logica van het runbook te definiëren.

U kunt de besturings elementen aan de onderkant van het canvas gebruiken om in en uit te zoomen.

### <a name="library-control"></a>Besturings element voor bibliotheek

In het besturings element bibliotheek selecteert u [activiteiten](#activities) die u wilt toevoegen aan uw runbook. U voegt deze toe aan het canvas waar u ze verbindt met andere activiteiten. Het bevat vier secties die in de volgende tabel worden beschreven:

| Sectie | Beschrijving |
|:--- |:--- |
| Cmdlets |Bevat alle cmdlets die kunnen worden gebruikt in uw runbook. Cmdlets zijn ingedeeld op module. Alle modules die u hebt geïnstalleerd in uw Automation-account zijn beschikbaar. |
| Runbooks |Bevat de runbooks in uw Automation-account. Deze runbooks kunnen worden toegevoegd aan het canvas dat moet worden gebruikt als onderliggende runbooks. Alleen runbooks van hetzelfde kern type als het runbook dat wordt bewerkt, worden weer gegeven. voor grafische runbooks worden alleen op Power shell gebaseerde runbooks weer gegeven. voor grafische power shell-werk stroom-runbooks worden alleen Power shell-runbooks op basis van werk stromen weer gegeven. |
| Assets |Bevat de [Automation-assets](/previous-versions/azure/dn939988(v=azure.100)) in uw Automation-account dat kan worden gebruikt in uw runbook. Wanneer u een Asset aan een runbook toevoegt, wordt er een werk stroom activiteit toegevoegd waarmee de geselecteerde activa worden opgehaald. In het geval van variabele assets kunt u selecteren of u een activiteit wilt toevoegen om de variabele op te halen of de variabele in te stellen. |
| Runbook-besturings element |Bevat runbook Control-activiteiten die kunnen worden gebruikt in uw huidige runbook. Een *verbinding* neemt meerdere invoer en wacht totdat alle taken zijn voltooid voordat u doorgaat met de werk stroom. Een *code* activiteit voert een of meer regels Power shell-of Power shell-werk stroom code uit, afhankelijk van het type van het grafische runbook. U kunt deze activiteit gebruiken voor aangepaste code of voor functionaliteit die moeilijk te vergemakkelijkt is met andere activiteiten. |

### <a name="configuration-control"></a>Configuratie beheer

Het besturings element configuratie bevat Details voor een object dat is geselecteerd op het canvas. De eigenschappen die beschikbaar zijn in dit besturings element, zijn afhankelijk van het type object dat is geselecteerd. Wanneer u een optie in het configuratie besturings element selecteert, worden er aanvullende Blades geopend om aanvullende informatie te bieden.

### <a name="test-control"></a>Besturings element testen

Het besturings element test wordt niet weer gegeven wanneer de grafische editor voor het eerst wordt gestart. Het wordt geopend wanneer u interactief [een grafisch runbook test](#graphical-runbook-procedures).

## <a name="graphical-runbook-procedures"></a>Grafische runbook-procedures

### <a name="exporting-and-importing-a-graphical-runbook"></a>Een grafisch runbook exporteren en importeren

U kunt alleen de gepubliceerde versie van een grafisch runbook exporteren. Als het runbook nog niet is gepubliceerd, wordt de knop **exporteren** uitgeschakeld. Wanneer u op de knop **exporteren** klikt, wordt het runbook gedownload naar uw lokale computer. De naam van het bestand komt overeen met de naam van het runbook met een *graphrunbook* -extensie.

U kunt een grafisch of grafisch runbook van een Power shell-werk stroom bestand importeren door de optie **importeren** te selecteren bij het toevoegen van een runbook. Wanneer u het bestand selecteert dat u wilt importeren, kunt u dezelfde **naam** blijven of een nieuw item opgeven. In het veld type Runbook wordt het type runbook weer gegeven nadat het geselecteerde bestand is geëvalueerd. Als u probeert een ander type te selecteren dat niet juist is, wordt er een bericht weer gegeven met de mogelijke conflicten en tijdens de conversie, waardoor er mogelijk een syntaxis is bufferoverschrijdingsfouten.

![Runbook importeren](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Een grafisch runbook testen

U kunt de concept versie van een runbook testen in de Azure Portal terwijl de gepubliceerde versie van het runbook ongewijzigd blijft, of u kunt een nieuw runbook testen voordat het is gepubliceerd. Hiermee kunt u controleren of het runbook correct werkt voordat u de gepubliceerde versie vervangen. Wanneer u een runbook test, wordt het concept runbook wordt uitgevoerd en de acties die worden uitgevoerd worden voltooid. Er wordt geen taak geschiedenis gemaakt, maar de uitvoer wordt weer gegeven in het deel venster test uitvoer.

Open het besturings element test voor een runbook door het runbook te openen voor bewerken en vervolgens te klikken op de knop **test venster** .

De test controle vraagt naar alle invoer parameters en u kunt het runbook starten door te klikken op de knop **Start** .

### <a name="publishing-a-graphical-runbook"></a>Een grafisch runbook publiceren

Elk runbook in Azure Automation heeft een concept en een gepubliceerde versie. Alleen de gepubliceerde versie is beschikbaar om te worden uitgevoerd, en alleen de conceptversie kan worden bewerkt. De gepubliceerde versie wordt niet beïnvloed door wijzigingen in de conceptversie. Wanneer de concept versie gereed is om beschikbaar te zijn, publiceert u deze, waardoor de gepubliceerde versie wordt overschreven door de concept versie.

U kunt een grafisch runbook publiceren door het runbook te openen voor bewerken en vervolgens op de knop **publiceren** te klikken.

Wanneer een runbook nog niet is gepubliceerd, heeft het de status **Nieuw**. Wanneer het is gepubliceerd, heeft het de status **gepubliceerd**. Als u het runbook bewerkt nadat het is gepubliceerd en het concept en de gepubliceerde versies verschillend zijn, heeft het runbook de status **in Edit**.

![Runbook-statussen](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

U hebt ook de mogelijkheid om terug te keren naar de gepubliceerde versie van een runbook. Hiermee worden de wijzigingen die zijn aangebracht sinds het runbook voor het laatst is gepubliceerd, verwijderd en wordt de concept versie van het runbook vervangen door de gepubliceerde versie.

## <a name="activities"></a>Activiteiten

Activiteiten zijn de bouw stenen van een runbook. Een activiteit kan een Power shell-cmdlet, een onderliggend runbook of een werk stroom activiteit zijn. U voegt een activiteit aan het runbook toe door er met de rechter muisknop op te klikken in het besturings element bibliotheek en vervolgens **toevoegen aan canvas**te selecteren. Vervolgens klikt u op de activiteit en sleept u deze naar een wille keurige plek op het canvas dat u wilt. De locatie van de activiteit op het canvas heeft geen invloed op de werking van het runbook. U kunt uw runbook indelen, maar u vindt het het meest geschikt om de werking ervan te visualiseren.

![toevoegen aan canvas](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Selecteer de activiteit op het canvas om de eigenschappen en para meters te configureren op de Blade configuratie. U kunt het **Label** van de activiteit wijzigen in iets wat een beschrijving voor u is. De oorspronkelijke cmdlet wordt nog uitgevoerd. u hoeft alleen de weergave naam te wijzigen die wordt gebruikt in de grafische editor. Het label moet uniek zijn binnen het runbook.

### <a name="parameter-sets"></a>Parameter sets

Een parameterset definieert de verplichte en optionele para meters waarmee waarden voor een bepaalde cmdlet worden geaccepteerd. Alle cmdlets hebben ten minste één parameterset en andere hebben er meerdere. Als een cmdlet meerdere parameter sets heeft, moet u selecteren welke u gebruikt voordat u para meters kunt configureren. De para meters die u kunt configureren, zijn afhankelijk van de door u gekozen parameterset. U kunt de parameterset die door een activiteit wordt gebruikt, wijzigen door **para meters** in te stellen en een andere set te selecteren. In dit geval gaan alle parameter waarden die u hebt geconfigureerd verloren.

In het volgende voor beeld heeft de cmdlet Get-AzureRmVM drie parameter sets. U kunt parameter waarden pas configureren als u een van de parameter sets selecteert. De para meter ListVirtualMachineInResourceGroupParamSet is ingesteld voor het retour neren van alle virtuele machines in een resource groep en heeft één optionele para meter. De **GetVirtualMachineInResourceGroupParamSet** is voor het opgeven van de virtuele machine die u wilt retour neren en heeft twee verplichte en een optionele para meter.

![Parameterset](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Parameterwaarden

Wanneer u een waarde voor een para meter opgeeft, selecteert u een gegevens bron om te bepalen hoe de waarde is opgegeven. De gegevens bronnen die beschikbaar zijn voor een bepaalde para meter, zijn afhankelijk van de geldige waarden voor die para meter. Zo is null bijvoorbeeld geen beschik bare optie voor een para meter die geen Null-waarden toestaat.

| Gegevensbron | Beschrijving |
|:--- |:--- |
| Constante waarde |Typ een waarde voor de para meter. Dit is alleen beschikbaar voor de volgende gegevens typen: Int32, Int64, String, Boolean, DateTime, Switch. |
| Uitvoer van activiteit |Uitvoer van een activiteit die voorafgaat aan de huidige activiteit in de werk stroom. Alle geldige activiteiten worden weer gegeven. Selecteer alleen de activiteit voor het gebruik van de uitvoer voor de parameter waarde. Als met de activiteit een object met meerdere eigenschappen wordt uitgevoerd, kunt u de naam van de eigenschap typen nadat u de activiteit hebt geselecteerd. |
| Runbook-invoer |Selecteer een invoer parameter voor het runbook als invoer voor de para meter activiteit. |
| Variabel activum |Selecteer een Automation-variabele als invoer. |
| Referentie-Asset |Selecteer een Automation-referentie als invoer. |
| Certificaat Asset |Selecteer een Automation-certificaat als invoer. |
| Verbindings Asset |Selecteer een Automation-verbinding als invoer. |
| PowerShell-expressie |Geef een eenvoudige [Power shell-expressie](#powershell-expressions)op. De expressie wordt geëvalueerd vóór de activiteit en het resultaat dat wordt gebruikt voor de parameter waarde. U kunt variabelen gebruiken om te verwijzen naar de uitvoer van een activiteit of een invoer parameter van een runbook. |
| Niet geconfigureerd |Hiermee wist u alle eerder geconfigureerde waarden. |

#### <a name="optional-additional-parameters"></a>Optionele aanvullende para meters

Alle cmdlets hebben de mogelijkheid om extra para meters op te geven. Dit zijn algemene Power shell-para meters of andere aangepaste para meters. Er wordt een tekstvak weer gegeven waarin u para meters kunt opgeven met behulp van de Power shell-syntaxis. Als u bijvoorbeeld de para meter **uitgebreide** common wilt gebruiken, geeft u **'-verbose: $True '** op.

### <a name="retry-activity"></a>Activiteit voor opnieuw proberen

Met het **gedrag voor opnieuw proberen** kan een activiteit meerdere keren worden uitgevoerd tot aan een bepaalde voor waarde wordt voldaan, net als bij een lus. U kunt deze functie gebruiken voor activiteiten die meerdere keren worden uitgevoerd, fout gevoelig is en mogelijk meer dan één poging voor geslaagde pogingen hebben of de uitvoer gegevens van de activiteit testen op geldige gegevens.

Wanneer u opnieuw proberen voor een activiteit inschakelt, kunt u een vertraging en een voor waarde instellen. De vertraging is de tijd (gemeten in seconden of minuten) die het runbook wacht voordat de activiteit opnieuw wordt uitgevoerd. Als er geen vertraging is opgegeven, wordt de activiteit opnieuw uitgevoerd zodra deze is voltooid.

![Vertraging voor opnieuw proberen van activiteit](media/automation-graphical-authoring-intro/retry-delay.png)

De voor waarde voor opnieuw proberen is een Power shell-expressie die wordt geëvalueerd na elke keer dat de activiteit wordt uitgevoerd. Als de expressie wordt omgezet in True, wordt de activiteit opnieuw uitgevoerd. Als de expressie wordt omgezet in False, wordt de activiteit niet opnieuw uitgevoerd en wordt het runbook verplaatst naar de volgende activiteit.

![Vertraging voor opnieuw proberen van activiteit](media/automation-graphical-authoring-intro/retry-condition.png)

De voor waarde voor opnieuw proberen kan een variabele met de naam $RetryData gebruiken die toegang geeft tot informatie over de nieuwe pogingen van de activiteit. Deze variabele heeft de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--- |:--- |
| NumberOfAttempts |Aantal keren dat de activiteit is uitgevoerd. |
| Uitvoer |Uitvoer van de laatste uitvoering van de activiteit. |
| TotalDuration |De tijd die is verstreken sinds de activiteit voor het eerst is gestart. |
| StartedAt |Tijd in de UTC-notatie waarmee de activiteit voor het eerst werd gestart. |

Hieronder volgen enkele voor beelden van voor waarden voor een nieuwe activiteit.

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

Nadat u een voor waarde voor opnieuw proberen voor een activiteit hebt geconfigureerd, bevat de activiteit twee visuele hints om u eraan te herinneren. De ene wordt weer gegeven in de activiteit en de andere is wanneer u de configuratie van de activiteit bekijkt.

![Visuele indica toren voor activiteit opnieuw proberen](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Besturings element voor werk stroom script

Een code beheer is een speciale activiteit die een Power shell-of Power shell-werk stroom script accepteert, afhankelijk van het type grafisch runbook dat wordt ontworpen om functionaliteit te bieden die anders mogelijk niet beschikbaar is. Het kan geen para meters accepteren, maar kan wel variabelen gebruiken voor uitvoer van de activiteit en de invoer parameters voor het runbook. Een uitvoer van de activiteit wordt toegevoegd aan de gegevensbus, tenzij deze geen uitgaande koppeling heeft in welk geval deze wordt toegevoegd aan de uitvoer van het runbook.

Met de volgende code worden bijvoorbeeld datum berekeningen uitgevoerd met behulp van een runbook-invoer variabele met de naam $NumberOfDays. Vervolgens wordt een berekende datum en tijd als uitvoer verzonden die door volgende activiteiten in het runbook moet worden gebruikt.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Koppelingen en werk stroom

Een **koppeling** in een grafisch runbook verbindt twee activiteiten. Het wordt op het canvas weer gegeven als een pijl die van de bron activiteit naar de doel activiteit wijst. De activiteiten worden uitgevoerd in de richting van de pijl met de doel activiteit vanaf het volt ooien van de bron activiteit.

### <a name="create-a-link"></a>Een koppeling maken

Maak een koppeling tussen twee activiteiten door de bron activiteit te selecteren en op de cirkel aan de onderkant van de vorm te klikken. Sleep de pijl naar de doel activiteit en laat deze los.

![Een koppeling maken](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Selecteer de koppeling om de eigenschappen ervan te configureren in de Blade configuratie. Dit omvat het koppelings type, dat wordt beschreven in de volgende tabel:

| Koppelings type | Beschrijving |
|:--- |:--- |
| Pijplijn |De doel activiteit wordt eenmaal uitgevoerd voor elke object uitvoer van de bron activiteit. De doel activiteit wordt niet uitgevoerd als de bron activiteit resulteert in geen uitvoer. Uitvoer van de bron activiteit is beschikbaar als een object. |
| Volgorde |De doel activiteit wordt slechts één keer uitgevoerd. Het ontvangt een matrix van objecten van de bron activiteit. Uitvoer van de bron activiteit is beschikbaar als een matrix met objecten. |

### <a name="starting-activity"></a>Begin activiteit

Een grafisch runbook begint met alle activiteiten die geen binnenkomende koppeling hebben. Dit is vaak maar één activiteit, die als de begin activiteit voor het runbook zou optreden. Als meerdere activiteiten geen binnenkomende koppeling hebben, wordt het runbook gestart door deze parallel uit te voeren. De koppelingen worden gevolgd om andere activiteiten uit te voeren, aangezien elke bewerking is voltooid.

### <a name="conditions"></a>Voorwaarden

Wanneer u een voor waarde op een koppeling opgeeft, wordt de doel activiteit alleen uitgevoerd als de voor waarde wordt omgezet in True. Normaal gesp roken gebruikt u een $ActivityOutput variabele in een voor waarde om de uitvoer op te halen uit de bron activiteit

Voor een pijplijn koppeling geeft u een voor waarde voor één object op en de voor waarde wordt geëvalueerd voor elk object dat door de bron activiteit wordt uitgevoerd. De doel activiteit wordt vervolgens uitgevoerd voor elk object dat voldoet aan de voor waarde. Met een bron activiteit van Get-AzureRmVm kan bijvoorbeeld de volgende syntaxis worden gebruikt voor een voorwaardelijke pijplijn koppeling om alleen virtuele machines op te halen in de resource groep met de naam *Group1*.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Voor een reeks koppeling wordt de voor waarde slechts eenmaal geëvalueerd omdat één matrix met alle objecten uitvoer van de bron activiteit wordt geretourneerd. Daarom kan een sequentie koppeling niet worden gebruikt voor filteren zoals een pijplijn koppeling, maar u kunt gewoon bepalen of de volgende activiteit wordt uitgevoerd. Neem bijvoorbeeld de volgende serie activiteiten in het runbook start VM.

![Voorwaardelijke koppeling met reeksen](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Er zijn drie verschillende reeks koppelingen waarmee waarden worden gecontroleerd voor twee runbook-invoer parameters die de VM-naam en de naam van de resource groep vertegenwoordigen, zodat u kunt bepalen welk actie moet worden ondernomen om een enkele virtuele machine te starten. Start alle virtuele machines in de resource groep of alle virtuele machines in een abonnement. Voor de reeks koppeling tussen verbinding maken met Azure en het ophalen van één virtuele machine is dit de conditie logica:

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

Wanneer u een voorwaardelijke koppeling gebruikt, worden de gegevens die beschikbaar zijn van de bron activiteit naar andere activiteiten in die vertakking gefilterd op basis van de voor waarde. Als een activiteit de bron voor meerdere koppelingen is, zijn de gegevens die beschikbaar zijn voor activiteiten in elke vertakking afhankelijk van de voor waarde in de koppeling verbinding maken met die vertakking.

Bijvoorbeeld: met de activiteit **Start-AzureRmVm** in het onderstaande runbook worden alle virtuele machines gestart. Het heeft twee voorwaardelijke koppelingen. De eerste voorwaardelijke koppeling gebruikt de expressie *$ActivityOutput [' Start-AzureRmVM ']. IsSuccessStatusCode-EQ $true* om te filteren of de start-AzureRmVm-activiteit is voltooid. De tweede gebruikt de expressie *$ActivityOutput [' Start-AzureRmVM ']. IsSuccessStatusCode-ne $true* als u wilt filteren op het starten van de virtuele machine is mislukt met de start-AzureRmVm-activiteit.

![Voor beeld van voorwaardelijke koppeling](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Alle activiteiten die de eerste koppeling volgen en de uitvoer van de activiteit van Get-AzureVM gebruiken, krijgen alleen de virtuele machines die zijn gestart op het moment dat Get-AzureVM werd uitgevoerd. Elke activiteit die volgt op de tweede koppeling, haalt alleen de virtuele machines op die zijn gestopt op het moment dat Get-AzureVM werd uitgevoerd. Bij elke activiteit die volgt op de derde koppeling, worden alle virtuele machines opgehaald, ongeacht hun uitvoerings status.

### <a name="junctions"></a>Koppelingen

Een verbinding is een speciale activiteit die wacht totdat alle binnenkomende branches zijn voltooid. Hierdoor kunt u meerdere activiteiten parallel uitvoeren en ervoor zorgen dat alle taken zijn voltooid voordat u doorgaat.

Hoewel een verbinding een onbeperkt aantal binnenkomende koppelingen kan hebben, kan niet meer dan een van deze koppelingen een pijp lijn zijn. Het aantal binnenkomende reeks koppelingen is niet beperkt. U mag de verbinding met meerdere inkomende pijplijn koppelingen maken en het runbook opslaan, maar dit mislukt wanneer het wordt uitgevoerd.

Het onderstaande voor beeld maakt deel uit van een runbook dat een set virtuele machines start terwijl er tegelijkertijd patches worden gedownload die op deze computers moeten worden toegepast. Een verbinding wordt gebruikt om ervoor te zorgen dat beide processen zijn voltooid voordat het runbook wordt voortgezet.

![Punt](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cycli

Een cyclus wordt weer gegeven wanneer een doel activiteit is gekoppeld aan de bron activiteit of naar een andere activiteit die uiteindelijk is gekoppeld aan de bron. Cycli zijn momenteel niet toegestaan in grafische ontwerpen. Als uw runbook een cyclus heeft, wordt het programma op de juiste manier opgeslagen, maar ontvangt deze een fout melding wanneer deze wordt uitgevoerd.

![Cyclus](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Gegevens delen tussen activiteiten

Alle gegevens die worden uitgevoerd door een activiteit met een uitgaande koppeling, worden geschreven naar de *gegevensbus* voor het runbook. Elke activiteit in het runbook kan gegevens op de gegevensbus gebruiken om parameter waarden te vullen of in script code op te neemt. Een activiteit kan toegang krijgen tot de uitvoer van een vorige activiteit in de werk stroom.

Hoe de gegevens worden geschreven naar de gegevensbus, is afhankelijk van het type koppeling op de activiteit. Voor een **pijp lijn**worden de gegevens uitgevoerd als meerdere objecten. Voor een **sequentie** koppeling worden de gegevens uitgevoerd als een matrix. Als er slechts één waarde is, wordt deze uitgevoerd als een enkele element matrix.

U kunt met een van de volgende twee methoden toegang krijgen tot gegevens op de gegevensbus. Maakt eerst gebruik van een gegevens bron voor **activiteit uitvoer** om een para meter van een andere activiteit te vullen. Als de uitvoer een object is, kunt u één eigenschap opgeven.

![Uitvoer van activiteit](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

U kunt ook de uitvoer ophalen van een activiteit in een **Power shell-expressie** gegevens bron of vanuit een **werk stroom script** activiteit met een activity output-variabele. Als de uitvoer een object is, kunt u één eigenschap opgeven. Activity output-variabelen gebruiken de volgende syntaxis.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Controlepunten

U kunt [controle punten](automation-powershell-workflow.md#checkpoints) instellen in een grafisch Power shell-werk stroom-runbook door in een wille keurige activiteit een *controle punt runbook* te selecteren. Dit zorgt ervoor dat een controle punt wordt ingesteld nadat de activiteit is uitgevoerd.

![Check Point](media/automation-graphical-authoring-intro/set-checkpoint.png)

Controle punten worden alleen ingeschakeld in grafische runbooks voor Power shell-werk stromen en zijn niet beschikbaar in grafische runbooks. Als het runbook gebruikmaakt van Azure-cmdlets, moet u de activiteit van een controle punt volgen met een Connect-AzureRmAccount als het runbook wordt onderbroken en opnieuw wordt gestart vanuit dit controle punt op een andere werk nemer.

## <a name="authenticating-to-azure-resources"></a>Verifiëren met Azure-resources

Runbooks in Azure Automation die Azure-resources beheren, moeten worden geverifieerd voor Azure. Het [uitvoeren als-account](automation-create-runas-account.md) (ook wel een Service-Principal genoemd) is de standaard methode voor toegang tot Azure Resource Manager resources in uw abonnement met Automation-runbooks. U kunt deze functionaliteit toevoegen aan een grafisch runbook door de **AzureRunAsConnection** -verbindings Asset toe te voegen. dit wordt met behulp van de Power shell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) cmdlet en [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet op het canvas. Dit wordt geïllustreerd in het volgende voor beeld:

![Run as-verificatie activiteiten](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

De activiteit uitvoeren als-verbinding ophalen (dat wil zeggen, Get-AutomationConnection) is geconfigureerd met een gegevens bron met een constante waarde met de naam AzureRunAsConnection.

![Configuratie van run as-verbinding](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Met de volgende activiteit, Connect-AzureRmAccount, wordt het geverifieerde run as-account toegevoegd voor gebruik in het runbook.

![Connect-AzureRmAccount Parameter Set](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> **Add-AzureRmAccount** is nu een alias voor **Connect-AzureRmAccount**. Als u de bibliotheek items zoekt en u geen **Connect-AzureRMAccount**ziet, kunt u **add-AzureRMAccount**gebruiken, maar u kunt ook uw modules bijwerken in uw Automation-account.

Voor de para meters **APPLICATIONID**, **CERTIFICATETHUMBPRINT**en **TENANTID** moet u de naam van de eigenschap voor het pad naar het veld opgeven, omdat de activiteit een object met meerdere eigenschappen uitvoert. Anders wanneer u het runbook uitvoert, mislukt de verificatie. Dit is wat u Mini maal nodig hebt om uw runbook te verifiëren met het run as-account.

Als u achterwaartse compatibiliteit wilt behouden voor abonnees die een Automation-account hebben gemaakt met behulp van een [Azure AD-gebruikers account](automation-create-aduser-account.md) voor het beheren van de klassieke Azure-implementatie of voor Azure Resource Manager resources, is de verificatie methode de cmdlet Add-AzureAccount met een [referentie-element](automation-credentials.md) dat een Active Directory gebruiker met toegang tot het Azure-account vertegenwoordigt.

U kunt deze functionaliteit toevoegen aan een grafisch runbook door een referentie-element toe te voegen aan het canvas gevolgd door een add-AzureAccount-activiteit. Voor de invoer van add-AzureAccount wordt de referentie activiteit gebruikt. Dit wordt geïllustreerd in het volgende voor beeld:

![Verificatie activiteiten](media/automation-graphical-authoring-intro/authentication-activities.png)

U moet zich bij het begin van het runbook en na elk controle punt verifiëren. Dit betekent dat u een toevoeg add-AzureAccount-activiteit toevoegt na elk controle punt-werk stroom activiteit. U hebt geen aanvullende referentie activiteit nodig, want u kunt hetzelfde gebruiken

![Uitvoer van activiteit](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Invoer en uitvoer van Runbook

### <a name="runbook-input"></a>Runbook-invoer

Een runbook vereist mogelijk invoer van een gebruiker wanneer het runbook wordt gestart via de Azure Portal of van een ander runbook als de huidige wordt gebruikt als onderliggend element.
Als u bijvoorbeeld een runbook hebt dat een virtuele machine maakt, moet u mogelijk informatie opgeven, zoals de naam van de virtuele machine en andere eigenschappen telkens wanneer u het runbook start.

U accepteert invoer voor een runbook door een of meer invoer parameters te definiëren. Telkens wanneer het runbook wordt gestart, geeft u de waarden voor deze para meters op. Wanneer u een runbook start met de Azure Portal, wordt u gevraagd waarden op te geven voor elk van de invoer parameters van het runbook.

U kunt invoer parameters voor een runbook openen door te klikken op de knop **invoer en uitvoer** op de werk balk van runbook.

Hiermee opent u het besturings element voor **invoer en uitvoer** , waar u een bestaande invoer parameter kunt bewerken of een nieuw item maakt door te klikken op **invoer toevoegen**.

![Invoer toevoegen](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Elke invoer parameter wordt gedefinieerd door de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--- |:--- |
| Naam |De unieke naam van de para meter. Dit mag alleen alfanumerieke tekens bevatten en mag geen spatie bevatten. |
| Beschrijving |Een optionele beschrijving voor de invoer parameter. |
| Type |Er wordt een gegevens type verwacht voor de parameter waarde. De Azure Portal biedt een geschikt besturings element voor het gegevens type voor elke para meter bij het vragen om invoer. |
| Verplicht |Hiermee geeft u op of er een waarde moet worden opgegeven voor de para meter. Het runbook kan niet worden gestart als u geen waarde opgeeft voor elke verplichte para meter waarvoor geen standaard waarde is gedefinieerd. |
| Default Value |Hiermee geeft u op welke waarde voor de para meter wordt gebruikt als er geen is opgegeven. Dit kan null of een specifieke waarde zijn. |

### <a name="runbook-output"></a>Runbook-uitvoer

Gegevens die zijn gemaakt door een activiteit die geen uitgaande koppeling heeft, worden opgeslagen in de [uitvoer van het runbook](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). De uitvoer wordt opgeslagen met de runbook-taak en is beschikbaar voor een bovenliggend runbook wanneer het runbook als onderliggend wordt gebruikt.

## <a name="powershell-expressions"></a>Power shell-expressies

Een van de voor delen van het ontwerpen van grafische elementen biedt u de mogelijkheid om een runbook te bouwen met minimale kennis van Power shell. Op dit moment moet u een beetje van Power shell weten, ook als u bepaalde [parameter waarden](#activities) wilt invullen en voor het instellen van [koppelings voorwaarden](#links-and-workflow). In deze sectie vindt u een korte inleiding tot Power shell-expressies voor gebruikers die mogelijk niet bekend zijn. Volledige informatie over Power shell is beschikbaar op [Scripting met Windows Power shell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Gegevens bron voor Power shell-expressie
U kunt een Power shell-expressie als gegevens bron gebruiken om de waarde van een [activiteit parameter](#activities) in te vullen met de resultaten van een Power shell-code. Dit kan een enkele regel code zijn die een eenvoudige functie of meerdere regels uitvoert waarmee complexe logica wordt uitgevoerd. Uitvoer van een opdracht die niet is toegewezen aan een variabele wordt uitgevoerd naar de parameter waarde.

Met de volgende opdracht wordt bijvoorbeeld de huidige datum uitgevoerd.

Met de volgende opdracht wordt bijvoorbeeld de huidige datum uitgevoerd.

```powershell-interactive
Get-Date
```

Met de volgende opdrachten bouwt u een teken reeks vanaf de huidige datum en wijst u deze toe aan een variabele. De inhoud van de variabele wordt vervolgens naar de uitvoer verzonden

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

De volgende opdrachten evalueren de huidige datum en retour neren een teken reeks die aangeeft of de huidige dag een weekend of een weekdag is.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Uitvoer van activiteit

Als u de uitvoer van een vorige activiteit in het runbook wilt gebruiken, gebruikt u de variabele $ActivityOutput met de volgende syntaxis.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

U kunt bijvoorbeeld een activiteit hebben met een eigenschap waarvoor de naam van een virtuele machine is vereist. in dat geval kunt u de volgende expressie gebruiken:

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

Als de eigenschap die vereist is voor het virtuele-machine object in plaats van alleen een eigenschap, zou u het gehele object retour neren met de volgende syntaxis.

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

U kunt ook de uitvoer van een activiteit gebruiken in een complexere expressie, zoals het volgende dat tekst aan de naam van de virtuele machine samenvoegt.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>Voorwaarden

Gebruik [vergelijkings operatoren](https://technet.microsoft.com/library/hh847759.aspx) om waarden te vergelijken of te bepalen of een waarde overeenkomt met een opgegeven patroon. Een vergelijking retourneert een waarde van zowel $true als $false.

Met de volgende voor waarde wordt bijvoorbeeld bepaald of de virtuele machine van een activiteit met de naam *Get-AzureVM* momenteel is *gestopt*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Met de volgende voor waarde wordt gecontroleerd of dezelfde virtuele machine zich in een andere status bevindt dan *gestopt*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

U kunt meerdere voor waarden koppelen met behulp van een [logische operator](https://technet.microsoft.com/library/hh847789.aspx) zoals **-en** of **-of**. Met de volgende voor waarde wordt bijvoorbeeld *gecontroleerd of dezelfde*virtuele machine in het vorige voor beeld de status *beëindigd* of gestopt heeft.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Hashtabellen

[Hashtabellen](https://technet.microsoft.com/library/hh847780.aspx) zijn naam/waarde-paren die nuttig zijn voor het retour neren van een set waarden. Eigenschappen voor bepaalde activiteiten verwachten mogelijk een hashtabel in plaats van een eenvoudige waarde. U kunt ook zien als een woorden lijst.

U maakt een hashtabel met de volgende syntaxis. Een hashtabel kan een wille keurig aantal vermeldingen bevatten, maar deze worden gedefinieerd door een naam en een waarde.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Met de volgende expressie maakt u bijvoorbeeld een hashtabel die moet worden gebruikt in de gegevens bron voor een activiteit parameter waarbij een hashtabel werd verwacht met waarden voor een Internet zoekopdracht.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

In het volgende voor beeld wordt de uitvoer van een activiteit met de naam *Twitter-verbinding ophalen* gebruikt voor het vullen van een hashtabel.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Mijn eerste PowerShell Workflow-runbook](automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks
* Zie [Mijn eerste grafische runbook](automation-first-runbook-graphical.md) om aan de slag te gaan met grafische runbooks
* Zie [Azure Automation-runbooktypen](automation-runbook-types.md) voor meer informatie over runbooktypen, hun voordelen en beperkingen
* Zie [uitvoeren als-account voor Azure configureren](automation-sec-configure-azure-runas-account.md) voor meer informatie over het verifiëren met het uitvoeren als-account voor Automation.
