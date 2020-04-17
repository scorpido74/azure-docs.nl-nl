---
title: Uitvoer van runbook en berichten in Azure Automation
description: Beschrijft hoe u uitvoer- en foutberichten maken en ophalen uit runbooks in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 92b6378b00e12f618d07798b5ce789cbd9971544
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535533"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Uitvoer van runbook en berichten in Azure Automation

De meeste Azure Automation-runbooks hebben een of andere vorm van uitvoer. Deze uitvoer kan een foutbericht zijn voor de gebruiker of een complex object dat bedoeld is om te worden gebruikt met een ander runbook. Windows PowerShell biedt [meerdere streams](/powershell/module/microsoft.powershell.core/about/about_redirection) om uitvoer vanuit een script of werkstroom te verzenden. Azure Automation werkt met elk van deze streams anders. Volg aanbevolen procedures voor het gebruik van de streams wanneer u een runbook maakt.

In de volgende tabel wordt elke stream kort beschreven met zijn gedrag in de Azure-portal voor gepubliceerde runbooks en tijdens [het testen van een runbook.](automation-testing-runbook.md) De uitvoerstroom is de hoofdstroom die wordt gebruikt voor communicatie tussen runbooks. De andere streams worden geclassificeerd als berichtstromen, bedoeld om informatie aan de gebruiker door te geven. 

| Streamen | Beschrijving | Gepubliceerd | Testen |
|:--- |:--- |:--- |:--- |
| Fout |Foutbericht bedoeld voor de gebruiker. In tegenstelling tot een uitzondering gaat het runbook standaard verder na een foutbericht. |Geschreven naar werkgeschiedenis |Weergegeven in het deelvenster Uitvoer van de test |
| Fouten opsporen |Berichten die zijn bedoeld voor een interactieve gebruiker. Mag niet worden gebruikt in runbooks. |Niet geschreven naar werkgeschiedenis |Niet weergegeven in deelvenster Uitvoer testen |
| Uitvoer |Objecten die zijn bedoeld om te worden verbruikt door andere runbooks. |Geschreven naar werkgeschiedenis |Weergegeven in het deelvenster Uitvoer van de test |
| Voortgang |Records die automatisch worden gegenereerd voor en na elke activiteit in het runbook. Het runbook moet niet proberen om zijn eigen voortgangsrecords te maken, omdat ze bedoeld zijn voor een interactieve gebruiker. |Alleen naar taakgeschiedenis geschreven als voortgangslogboekregistratie is ingeschakeld voor het runbook |Niet weergegeven in deelvenster Uitvoer testen |
| Verbose |Berichten die algemene of foutopsporingsinformatie geven. |Alleen naar de functiegeschiedenis geschreven als verbose logging is ingeschakeld voor het runbook |Alleen weergegeven in het `VerbosePreference` uitvoerdeelvenster van de test als variabele is ingesteld op Doorgaan in runbook |
| Waarschuwing |Waarschuwingsbericht bedoeld voor de gebruiker. |Geschreven naar werkgeschiedenis |Weergegeven in het deelvenster Uitvoer van de test |

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="output-stream"></a>Uitvoerstroom

De uitvoerstroom wordt gebruikt voor de uitvoer van objecten die zijn gemaakt door een script of werkstroom wanneer deze correct wordt uitgevoerd. Azure Automation gebruikt deze stream voornamelijk voor objecten die moeten worden verbruikt door bovenliggende runbooks die de [huidige runbook](automation-child-runbooks.md)aanroepen. Wanneer een ouder [een runbook-inline aanroept,](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)retourneert het kind gegevens uit de uitvoerstroom naar de bovenliggende bron. 

Uw runbook gebruikt de uitvoerstroom om algemene informatie alleen aan de client door te geven als deze nooit door een ander runbook wordt aangeroepen. Als aanbevolen praktijk moeten u echter meestal de [Verbose-stream](#verbose-stream) gebruiken om algemene informatie aan de gebruiker te communiceren.

Laat uw runbook gegevens naar de uitvoerstroom schrijven met [Schrijfuitvoer](https://technet.microsoft.com/library/hh849921.aspx). U het object ook op een eigen regel in het script plaatsen.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>Afhandeling van uitvoer vanuit een functie

Wanneer een runbook-functie naar de uitvoerstroom schrijft, wordt de uitvoer teruggezet naar het runbook. Als de runbook die uitvoer aan een variabele toewijst, wordt de uitvoer niet naar de uitvoerstroom geschreven. Het schrijven naar andere streams vanuit de functie schrijft naar de bijbehorende stream voor het runbook. Overweeg het volgende voorbeeld PowerShell-werkstroomrunboek.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

De uitvoerstream voor de taak runbook is:

```output
Output inside of function
Output outside of function
```

De Verbose-stream voor de runbook-taak is:

```output
Verbose outside of function
Verbose inside of function
```

Zodra u het runbook hebt gepubliceerd en voordat u het start, moet u ook verbose-logboekregistratie inschakelen in de runbook-instellingen om de Verbose-streamuitvoer te krijgen.

### <a name="declaring-output-data-type"></a>Uitvoergegevenstype declareren

Hieronder volgen voorbeelden van uitvoergegevenstypen:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Uitvoergegevenstype in een werkstroom declareren

Een werkstroom geeft het gegevenstype van de uitvoer op met het [kenmerk OutputType](https://technet.microsoft.com/library/hh847785.aspx). Dit kenmerk heeft geen effect tijdens runtime, maar geeft u een indicatie op het ontwerptijd van de verwachte uitvoer van het runbook. Naarmate de toolset voor runbooks blijft evolueren, neemt het belang van het declareren van uitvoergegevenstypen tijdens de ontwerptijd toe. Daarom is het een goede gewoonte om deze verklaring op te nemen in alle runbooks die u maakt.

Het volgende voorbeeldrunbook voert een tekenreeksobject uit en bevat een declaratie van het uitvoertype. Als uw runbook een matrix van een bepaald type uitvoert, moet u nog steeds het type opgeven in tegenstelling tot een matrix van het type.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Uitvoergegevenstype declareren in een grafisch runbook

Als u een uitvoertype wilt declareren in een grafisch of grafisch PowerShell-werkstroomwerkboek, u de **menuoptie Invoer en uitvoer** selecteren en het uitvoertype invoeren. Het wordt aanbevolen om de volledige klasse naam .NET te gebruiken om het type gemakkelijk identificeerbaar te maken wanneer een bovenliggend runbook ernaar verwijst. Als u de volledige naam gebruikt, worden alle eigenschappen van de klasse blootgesteld aan de gegevensbus in het runbook en wordt de flexibiliteit vergroot wanneer de eigenschappen worden gebruikt voor voorwaardelijke logica, logboekregistratie en verwijzingen naar waarden voor andere runbook-activiteiten.<br> ![Invoer en uitvoer van runbook, optie](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Nadat u een waarde hebt ingevoerd in het veld **Uitvoertype** in het deelvenster Eigenschappen van invoer en uitvoer, moet u buiten het besturingselement klikken zodat het uw vermelding herkent.

In het volgende voorbeeld worden twee grafische runbooks weergegeven om de functie Invoer en uitvoer weer te geven. Als u het modulaire ontwerpmodel voor runbook toepast, hebt u één runbook als de verificatie van de Runbook-sjabloon verifiëren met Azure met behulp van het Run As-account. Het tweede runbook, dat normaal gesproken kernlogica uitvoert om een bepaald scenario te automatiseren, voert in dit geval de sjabloon Runbook verifiëren uit. De resultaten worden weergegeven in het uitvoerdeelvenster Testen. Onder normale omstandigheden zou u dit runbook iets laten doen tegen een resource die gebruikmaakt van de uitvoer uit het onderliggende runbook.

Hier is de basislogica van de **Runbook AuthenticTo-Azure.**<br> ![Voorbeeld van runbook-sjabloon](media/automation-runbook-output-and-messages/runbook-authentication-template.png)verifiëren .

Het runbook bevat `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`het uitvoertype , dat de eigenschappen van het verificatieprofiel retourneert.<br> ![Voorbeeld van uitvoertype runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Hoewel dit runbook eenvoudig is, is er een configuratie-item om hier te roepen. Bij de laatste `Write-Output` activiteit wordt de cmdlet uitgevoerd om profielgegevens `Inputobject` naar een variabele te schrijven met behulp van een PowerShell-expressie voor de parameter. Deze parameter is `Write-Output`vereist voor .

Het tweede runbook in dit voorbeeld, genaamd **Test-ChildOutputType,** definieert eenvoudig twee activiteiten.<br> ![Voorbeeld van runbook voor onderliggend uitvoertype](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

De eerste activiteit roept de **Runbook AuthenticateTo-Azure** aan. De tweede activiteit `Write-Verbose` voert de cmdlet uit met **gegevensbron** ingesteld op **Activiteitsuitvoer**. **Veldpad** is ook ingesteld op **Context.Subscription.SubscriptionName**, de contextuitvoer uit de **runbook VerifieTo-Azure.**<br> ![Gegevensbron voor cmdlet-cmdlet-schrijfverbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

De resulterende uitvoer is de naam van het abonnement.<br> ![Test-ChildOutputType Runbook Resultaten](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>Berichtstromen

In tegenstelling tot de uitvoerstream communiceren berichtstromen informatie naar de gebruiker. Er zijn meerdere berichtstromen voor verschillende soorten informatie en Azure Automation verwerkt elke stream anders.

### <a name="warning-and-error-streams"></a>Waarschuwings- en foutstromen

De waarschuwings- en foutstromen logboekproblemen die zich voordoen in een runbook. Azure Automation schrijft deze streams naar de taakgeschiedenis bij het uitvoeren van een runbook. Automatisering omvat de streams in het deelvenster Uitvoer testen in de Azure-portal wanneer een runbook wordt getest. 

Standaard wordt een runbook uitgevoerd na een waarschuwing of fout. U opgeven dat uw runbook moet worden opgeschort op een waarschuwing of fout door de runbook een [voorkeursvariabele](#preference-variables) te laten instellen voordat u het bericht maakt. Als u bijvoorbeeld wilt zorgen dat de runbook op een fout `ErrorActionPreference` wordt opgeschort, zoals bij een uitzondering, stelt u de variabele in op Stoppen.

Maak een waarschuwings- of foutbericht met de cmdlet [Schrijfwaarschuwing](https://technet.microsoft.com/library/hh849931.aspx) of [Schrijffout.](https://technet.microsoft.com/library/hh849962.aspx) Activiteiten kunnen ook schrijven naar de waarschuwings- en foutstreams.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>Foutopsporingsstroom

Azure Automation gebruikt de berichtstream Debug voor interactieve gebruikers. Het mag niet worden gebruikt in runbooks.

### <a name="verbose-stream"></a>Verbose stroom

De Verbose-berichtenstroom ondersteunt algemene informatie over de bewerking van runbook. Aangezien de Foutopsporingsstream niet beschikbaar is voor een runbook, moet uw runbook uitgebreide berichten gebruiken voor foutopsporingsgegevens. 

Standaard slaat de taakgeschiedenis om prestatieredenen geen uitgebreide berichten uit gepubliceerde runbooks op. Als u verbose-berichten wilt opslaan, gebruikt u het tabblad **Configureer configureren** met de instelling **Logboekverbose Records** om uw gepubliceerde runbooks te configureren om verbose-berichten aan te melden. Schakel deze optie alleen in voor probleemoplossing of foutopsporing van een runbook. In de meeste gevallen moet u de standaardinstelling van het niet registreren van verboserecords behouden.

Bij [het testen van een runbook](automation-testing-runbook.md)worden verbose-berichten niet weergegeven, zelfs niet als het runbook is geconfigureerd om verboserecords te registreren. Als u veelboseberichten wilt weergeven tijdens het `VerbosePreference` testen van een [runbook,](automation-testing-runbook.md)moet u de variabele instellen op Doorgaan. Met die variabele set worden verbose-berichten weergegeven in het deelvenster Uitvoer testen van de Azure-portal.

Met de volgende code wordt een verbosebericht gemaakt met de [cmdlet Write-Verbose.](https://technet.microsoft.com/library/hh849951.aspx)

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>Voortgangsrecords

U het tabblad **Configureren van** de Azure-portal gebruiken om een runbook te configureren om voortgangsrecords te registreren. De standaardinstelling is om de records niet te registreren, om de prestaties te maximaliseren. In de meeste gevallen moet u de standaardinstelling behouden. Schakel deze optie alleen in voor probleemoplossing of foutopsporing van een runbook. 

Als u logboekregistratie inschakelt, schrijft uw runbook een record naar taakgeschiedenis voor en na elke activiteit. Als u een runbook test, worden voortgangsberichten niet weergegeven, zelfs niet als de runbook is geconfigureerd om voortgangsrecords te registreren.

>[!NOTE]
>De [write-progress](https://technet.microsoft.com/library/hh849902.aspx) cmdlet is niet geldig in een runbook, omdat deze cmdlet is bedoeld voor gebruik met een interactieve gebruiker.

## <a name="preference-variables"></a>Voorkeursvariabelen

U bepaalde Windows [PowerShell-voorkeursvariabelen](https://technet.microsoft.com/library/hh847796.aspx) in uw runbooks instellen om het antwoord op gegevens die naar verschillende uitvoerstromen worden verzonden, te beheren. In de volgende tabel worden de voorkeursvariabelen weergegeven die in runbooks kunnen worden gebruikt, met hun standaardwaarden en geldige waarden. Er zijn extra waarden beschikbaar voor de voorkeursvariabelen die worden gebruikt in Windows PowerShell buiten Azure Automation.

| Variabele | Standaardwaarde | Geldige waarden |
|:--- |:--- |:--- |
| `WarningPreference` |Doorgaan |Stoppen<br>Doorgaan<br>SilentlyContinue |
| `ErrorActionPreference` |Doorgaan |Stoppen<br>Doorgaan<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stoppen<br>Doorgaan<br>SilentlyContinue |

In de volgende tabel wordt het gedrag weergegeven voor de voorkeursvariabelewaarden die geldig zijn in runbooks.

| Waarde | Gedrag |
|:--- |:--- |
| Doorgaan |Registreert het bericht en blijft het runbook uitvoeren. |
| SilentlyContinue |Blijft het runbook uitvoeren zonder het bericht te registreren. Deze waarde heeft het effect van het negeren van het bericht. |
| Stoppen |Het bericht wordt geregistreerd en het runbook wordt onderbroken. |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>Uitvoer van runbook en berichten ophalen

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Runbook-uitvoer en -berichten ophalen in Azure-portal

U de details van een runbook-taak in de Azure-portal bekijken via het tabblad **Taken** voor de runbook. In het overzicht van de taak worden de invoerparameters en de [uitvoerstroom](#output-stream)weergegeven, naast algemene informatie over de taak en eventuele uitzonderingen die zijn opgetreden. De taakgeschiedenis bevat berichten uit de uitvoerstroom [en waarschuwings- en foutstromen](#warning-and-error-streams). Het bevat ook berichten uit de [Verbose-stream](#verbose-stream) en [voortgangsrecords](#progress-records) als het runbook is geconfigureerd om verbose- en voortgangsrecords te registreren.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Runbook-uitvoer en -berichten ophalen in Windows PowerShell

In Windows PowerShell u uitvoer en berichten uit een runbook ophalen met de cmdlet [Get-AzAutomationJobOutput.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) Deze cmdlet vereist de ID van de `Stream` taak en heeft een parameter aangeroepen om de stream op te halen. U een waarde opgeven van Welke voor deze parameter om alle streams voor de taak op te halen.

In het volgende voorbeeld wordt een voorbeeldrunbook gestart en wordt gewacht tot dit is voltooid. Zodra het runbook de uitvoering heeft voltooid, verzamelt het script de uitvoerstroom van runbook van de taak.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Uitvoer van runbook en berichten ophalen in grafische runbooks

Voor grafische runbooks is extra logboekregistratie van uitvoer en berichten beschikbaar in de vorm van tracering op activiteitsniveau. Er zijn twee niveaus van tracering: Basic en Gedetailleerd. Bij basistracering wordt de begin- en eindtijd voor elke activiteit in het runbook weergegeven, plus informatie over eventuele activiteitspogingen. Enkele voorbeelden zijn het aantal pogingen en de begintijd van de activiteit. Gedetailleerde tracering omvat basistraceringsfuncties plus logboekregistratie van invoer- en uitvoergegevens voor elke activiteit. 

Momenteel schrijft activity-level tracing records met behulp van de Verbose stream. Daarom moet u verbose logging inschakelen wanneer u tracering inschakelt. Voor grafische runbooks waarvoor tracering is ingeschakeld, is het niet nodig om voortgangsrecords in te loggen. Basic tracing dient hetzelfde doel en is informatiever.

![Grafische weergave taakstromen voor grafisch ontwerpen](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

U op de afbeelding zien dat het inschakelen van verbose logboekregistratie en tracering voor grafische runbooks veel meer informatie beschikbaar maakt in de weergave **Taakstromen** voor productie. Deze extra informatie kan essentieel zijn voor het oplossen van productieproblemen met een runbook. 

Tenzij u deze informatie echter nodig hebt om de voortgang van een runbook voor het oplossen van problemen bij te houden, wilt u mogelijk dat tracering wordt uitgeschakeld als algemene praktijk. De traceerrecords kunnen bijzonder talrijk zijn. Met grafische runbook tracing u twee tot vier records per activiteit krijgen, afhankelijk van uw configuratie van Basic of Gedetailleerde tracering.

**Ga als volgt te werk om tracering op activiteitenniveau in te schakelen:**

1. Open uw Automation-account in Azure Portal.
2. Selecteer **Runbooks** onder **Procesautomatisering** om de lijst met runbooks te openen.
3. Selecteer op de pagina Runbooks een grafisch runbook in uw lijst met runbooks.
4. Klik **onder Instellingen**op **Logboekregistratie en tracering**.
5. Klik op de pagina Logboekregistratie en Tracering onder **Logboeken records**op **Om** verbose logging in te schakelen.
6. Wijzig onder Tracering op **activiteitsniveau**het traceerniveau in **Basic** of **Detailed**, op basis van het niveau van tracering dat u nodig hebt.<br>

   ![Pagina Logboekregistratie en tracering van grafische ontwerpen](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Uitvoer van runbook en berichten ophalen in Microsoft Azure Monitor-logboeken

Azure Automation kan de status van runbook-taken en taakstromen naar uw Log Analytics-werkruimte verzenden. Azure Monitor ondersteunt logboeken waarmee u:

* Krijg inzicht in uw automatiseringstaken.
* Activeer een e-mail of waarschuwing op basis van de status van uw runbook-taak, bijvoorbeeld Mislukt of Opgeschort.
* Schrijf geavanceerde query's in taakstromen.
* Correleer taken tussen automatiseringsaccounts.
* Visualiseer werkgeschiedenis.

Zie [Taakstatus en taakstromen van Automatisering naar Azure Monitor-logboeken doorsturen](automation-manage-send-joblogs-log-analytics.md)voor meer informatie over het configureren van integratie met Azure Monitor-logboeken om taakgegevens te verzamelen, te correleren en erop te reageren.

## <a name="next-steps"></a>Volgende stappen

* Zie [Een runbook-taak bijhouden](automation-runbook-execution.md)voor meer informatie over runbook-uitvoering, het bewaken van runbook-taken en andere technische details.
* Zie [Onderliggende runbooks in Azure Automation](automation-child-runbooks.md)voor meer informatie over het ontwerpen en gebruiken van onderliggende runbooks.
* Zie de [PowerShell-documenten](/powershell/scripting/overview)voor meer informatie over PowerShell, inclusief taalverwijzing en leermodules.
