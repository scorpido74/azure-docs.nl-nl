---
title: Runbook-uitvoer en berichten in Azure Automation
description: Hierin wordt beschreven hoe u uitvoer-en fout berichten van runbooks maakt en ophaalt in Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: af199439fedddaef5b1bd3b219a60db697fb25ab
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849646"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Runbook-uitvoer en berichten in Azure Automation

De meeste Azure Automation runbooks hebben een vorm van uitvoer. Deze uitvoer kan een fout bericht zijn bij de gebruiker of een complex object dat u wilt gebruiken met een ander runbook. Windows Power shell biedt [meerdere streams](/powershell/module/microsoft.powershell.core/about/about_redirection) voor het verzenden van uitvoer van een script of werk stroom. Azure Automation werkt met elk van deze stromen anders. Wanneer u een runbook maakt, moet u aanbevolen procedures volgen.

De volgende tabel bevat een korte beschrijving van elk van de stromen en hun gedrag in de Azure Portal voor gepubliceerde runbooks en bij het [testen van een runbook](automation-testing-runbook.md). Meer informatie over elke stroom vindt u in latere secties.

| Stream | Beschrijving | Gepubliceerd | Testen |
|:--- |:--- |:--- |:--- |
| Uitvoer |Objecten die zijn bedoeld om te worden verbruikt door andere runbooks. |Naar de taakgeschiedenis geschreven. |In het deelvenster Testuitvoer weergegeven. |
| Waarschuwing |Waarschuwingsbericht bedoeld voor de gebruiker. |Naar de taakgeschiedenis geschreven. |In het deelvenster Testuitvoer weergegeven. |
| Fout |Foutbericht bedoeld voor de gebruiker. In tegenstelling tot een uitzondering blijft het runbook standaard na een foutbericht weergegeven. |Naar de taakgeschiedenis geschreven. |In het deelvenster Testuitvoer weergegeven. |
| Uitgebreid |Berichten die algemene informatie of fout opsporingsgegevens geven. |Naar de taakgeschiedenis geschreven alleen als uitgebreide logboekregistratie is ingeschakeld voor het runbook. |Wordt alleen weergegeven in het deelvenster Testuitvoer als $VerbosePreference is ingesteld op Doorgaan in het runbook. |
| Wordt uitgevoerd |Records automatisch worden gegenereerd voor en na elke activiteit in het runbook. Het runbook mag geen eigen voortgangs records maken, omdat deze zijn bedoeld voor een interactieve gebruiker. |Naar de taakgeschiedenis alleen geschreven als voortgangslogboekregistratie is ingeschakeld voor het runbook. |Niet weergegeven in het deelvenster Testuitvoer. |
| Foutopsporing |Berichten die zijn bedoeld voor een interactieve gebruiker. Mag niet worden gebruikt in runbooks. |Niet naar Taakgeschiedenis geschreven. |Niet naar het deelvenster Testuitvoer geschreven. |

## <a name="output-stream"></a>Uitvoerstroom
De uitvoer stroom is bedoeld voor uitvoer van-objecten, die door een script of werk stroom worden gemaakt wanneer deze op de juiste wijze wordt uitgevoerd. In Azure Automation wordt deze stroom vooral gebruikt voor objecten die zijn bedoeld om te worden gebruikt door [bovenliggende runbooks die het huidige runbook aanroepen](automation-child-runbooks.md). Wanneer u [een runbook inline aanroept](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) van een bovenliggend runbook, retourneert deze gegevens uit de uitvoerstroom naar de bovenliggende. Gebruik de uitvoer stroom alleen om algemene informatie weer te geven aan de gebruiker als u weet dat het runbook nooit wordt aangeroepen door een ander runbook. Als een best practice, echter, moet u doorgaans gebruiken de [uitgebreide Stream](#verbose-stream) om algemene informatie voor de gebruiker te communiceren.

U kunt gegevens schrijven naar de uitvoer stream via [Write-Output](https://technet.microsoft.com/library/hh849921.aspx) of door het plaatsen van het object op een eigen regel in het runbook.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Uitvoer van een functie

Wanneer u naar de uitvoer stroom schrijft in een functie die is opgenomen in uw runbook, wordt de uitvoer teruggestuurd naar het runbook. Als het runbook die uitvoer aan een variabele toewijst, is het niet geschreven naar de uitvoerstroom. Schrijven naar andere stromen vanuit de functie schrijft naar de bijbehorende stroom voor het runbook.

Bekijk het volgende voor beeld-runbook:

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

De uitvoerstroom voor de runbooktaak zou zijn:

```output
Output inside of function
Output outside of function
```

De uitgebreide stroom voor de runbooktaak zou zijn:

```output
Verbose outside of function
Verbose inside of function
```

Nadat u het runbook hebt gepubliceerd en voordat u het hebt gestart, moet u ook uitgebreide logboek registratie inschakelen in de runbook-instellingen om de uitgebreide uitvoer van de stream te verkrijgen.

### <a name="declaring-output-data-type"></a>Type uitvoer gegevens declareren

Een werkstroom kan het gegevenstype van het gebruik van de uitvoer geeft de [OutputType kenmerk](https://technet.microsoft.com/library/hh847785.aspx). Dit kenmerk heeft geen effect tijdens runtime, maar biedt een indicatie voor de runbookauteur van het tijdens het ontwerpen van de verwachte uitvoer van het runbook. Als de toolset voor runbooks blijft ontwikkelen, neemt het belang van het declareren van uitvoer gegevens typen tijdens de ontwerp fase toe. Als gevolg hiervan is het een best practice om deze declaratie op te neemt in een runbooks die u maakt.

Hier volgt een lijst met voorbeeld uitvoer typen:

* System. String
* System. Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

Het volgende voorbeeldrunbook voert een tekenreeksobject en bevat een declaratie van het uitvoertype. Als uw runbook een matrix van een bepaald type, moet u nog steeds het type in plaats van een matrix van het type opgeven.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Als u een uitvoer type wilt declareren in grafische of grafische runbooks van Power shell-werk stromen, kunt u de menu optie **invoer en uitvoer** selecteren en de naam van het uitvoer type opgeven. U kunt het beste de volledige naam van een .NET-klasse gebruiken om het gemakkelijk te identificeren wanneer ernaar wordt verwezen vanuit een bovenliggend runbook. Hiermee worden alle eigenschappen van die klasse weer gegeven in de data bus in het runbook. Dit biedt veel flexibiliteit bij het gebruik van voorwaardelijke logica, logboek registratie en het verwijzen naar waarden voor andere activiteiten in het runbook.<br> ![-optie voor Runbook-invoer en-uitvoer](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

In het volgende voor beeld hebt u twee grafische runbooks om deze functie te demonstreren. Als u het modulaire runbook-ontwerp model toepast, hebt u één runbook, dat fungeert als de sjabloon voor het verifiëren van de *verificatie* met Azure met behulp van het uitvoeren als-account. Het tweede runbook, waarmee normaal gesp roken de kern logica wordt uitgevoerd om een bepaald scenario te automatiseren, wordt in dit geval de *sjabloon voor het authenticatie runbook* uitgevoerd en worden de resultaten weer gegeven in het deel venster **test** uitvoer. Onder normale omstandigheden zou u dit runbook iets doen tegen een resource door gebruik te maken van de uitvoer van het onderliggende runbook.

Dit is de basis logica van het **AuthenticateTo-Azure-** runbook.<br> ![het](media/automation-runbook-output-and-messages/runbook-authentication-template.png)voor beeld van Runbook-sjabloon te verifiëren.

Het bevat het uitvoer type *Microsoft. Azure. commands. profile. Models. PSAzureContext*, dat de eigenschappen van het verificatie profiel retourneert.<br> ![type voor beeld van Runbook-uitvoer](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Hoewel dit runbook direct kan worden aangeroepen, is er één configuratie-item om hier aan te roepen. De laatste activiteit voert de cmdlet **Write-output** uit en schrijft de profiel gegevens naar een $ _-variabele met behulp van een Power shell-expressie voor de para meter **input object** , die vereist is voor de cmdlet.

Voor het tweede runbook in dit voor beeld, met de naam *test-ChildOutputType*, hebt u slechts twee activiteiten.<br> ![voor beeld van het onderliggende uitvoer type Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Met de eerste activiteit wordt **het AuthenticateTo-Azure-** runbook aangeroepen en de tweede activiteit voert de **Write-verbose** cmdlet uit met de **gegevens bron** van de uitvoer van de **activiteit** en de waarde voor het pad van het **veld** **context. Subscription. subscriptionname**, waarmee de context uitvoer van het **AuthenticateTo-Azure-** runbook wordt opgegeven.<br> ![](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png) voor de cmdlet-gegevens bron voor Write-verbose

De resulterende uitvoer is de naam van het abonnement.<br> ![Test-ChildOutputType Runbook Results](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

> [!NOTE]
> Nadat u een waarde hebt ingevoerd in het vak **uitvoer type** in het deel venster **invoer-en uitvoer eigenschappen** , moet u buiten het besturings element klikken, zodat uw vermelding kan worden herkend door het besturings element.

## <a name="message-streams"></a>Berichtstromen

In tegenstelling tot de uitvoerstroom zijn berichtstromen bedoeld om informatie voor de gebruiker te communiceren. Er zijn meerdere berichten stromen voor verschillende soorten informatie, en elke gegevens worden op een andere manier verwerkt door Azure Automation.

### <a name="warning-and-error-streams"></a>Waarschuwings-en fout stromen

De waarschuwings- en foutstromen zijn bedoeld om aan te melden van problemen die optreden in een runbook. Deze worden naar de taak geschiedenis geschreven wanneer een runbook wordt uitgevoerd, en worden opgenomen in het deel venster test uitvoer in de Azure Portal wanneer een runbook wordt getest. Standaard kan het runbook zal worden uitgevoerd na een waarschuwing of fout. U kunt opgeven dat het runbook op een waarschuwing of fout moet worden onderbroken door in te stellen een [voorkeursvariabele](#preference-variables) in het runbook voordat het bericht wordt gemaakt. Als u bijvoorbeeld wilt dat een runbook wordt onderbroken bij een fout omdat het een uitzonde ring zou vormen, stelt u **$ErrorActionPreference** in om te stoppen.

Maken van een waarschuwing of een foutbericht met de [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) of [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) cmdlet. Activiteiten mogen ook naar deze stromen schrijven.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Uitgebreide stroom

De uitgebreide berichtenstroom is bedoeld voor algemene informatie over de runbookwerking. Omdat de [Stream voor fout opsporing](#debug-stream) niet beschikbaar is in een runbook, moeten uitgebreide berichten worden gebruikt voor fout opsporingsgegevens. Uitgebreide berichten van gepubliceerde runbooks worden standaard niet opgeslagen in de taak geschiedenis. Als u uitgebreide berichten wilt opslaan, configureert u gepubliceerde runbooks om uitgebreide records te registreren op het tabblad configureren van het runbook in de Azure Portal. In de meeste gevallen moet u de standaardinstelling niet registreren van uitgebreide records voor een runbook uit prestatieoverwegingen behouden. Schakel deze optie alleen voor probleemoplossing of foutopsporing van een runbook.

Bij het [testen van een runbook](automation-testing-runbook.md)worden uitgebreide berichten niet weer gegeven, zelfs niet als het runbook is geconfigureerd voor het registreren van uitgebreide records. Als u uitgebreide berichten wilt weer geven tijdens [het testen van een runbook](automation-testing-runbook.md), moet u de $VerbosePreference variabele instellen om door te gaan. Als deze variabele is ingesteld, worden uitgebreide berichten weer gegeven in het deel venster test uitvoer van de Azure Portal.

Maak een uitgebreid bericht met de [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) cmdlet.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Foutopsporingsstroom

De foutopsporingsstroom is bedoeld voor gebruik met een interactieve gebruiker en mag niet worden gebruikt in runbooks.

## <a name="progress-records"></a>Voortgangsrecords

Als u een runbook configureert om voortgangs records te registreren (op het tabblad configureren van het runbook in de Azure Portal), wordt een record naar de taak geschiedenis geschreven vóór en nadat elke activiteit is uitgevoerd. In de meeste gevallen moet u de standaardinstelling niet registreren van voortgangsrecords voor een runbook om prestaties te maximaliseren. Schakel deze optie alleen voor probleemoplossing of foutopsporing van een runbook. Bij het testen van een runbook worden voortgangsberichten niet weergegeven, zelfs als het runbook is geconfigureerd voor logboekregistratie van voortgangsrecords.

De cmdlet [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) is niet geldig in een runbook, omdat deze cmdlet is bedoeld voor gebruik met een interactieve gebruiker.

## <a name="preference-variables"></a>Voorkeursvariabelen

Maakt gebruik van Windows PowerShell [voorkeursvariabelen](https://technet.microsoft.com/library/hh847796.aspx) om te bepalen hoe om te reageren op gegevens die worden verzonden naar verschillende uitvoerstromen. U kunt deze variabelen instellen in een runbook om te bepalen hoe het reageert op gegevens die worden verzonden naar verschillende stromen.

De volgende tabel bevat de voorkeursvariabelen die kunnen worden gebruikt in runbooks met hun geldige en standaardwaarden. Deze tabel bevat alleen de waarden die geldig zijn in een runbook. Aanvullende waarden zijn geldig voor de voorkeurs variabelen wanneer deze worden gebruikt in Windows Power shell buiten Azure Automation.

| Variabele | Default Value | Geldige waarden |
|:--- |:--- |:--- |
| WarningPreference |Doorgaan |Stoppen<br>Doorgaan<br>SilentlyContinue |
| ErrorActionPreference |Doorgaan |Stoppen<br>Doorgaan<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Stoppen<br>Doorgaan<br>SilentlyContinue |

De volgende tabel geeft een lijst van het gedrag van de voorkeursvariabelewaarden die geldig in runbooks zijn.

| Waarde | Gedrag |
|:--- |:--- |
| Doorgaan |Registreert het bericht en blijft het runbook uitvoeren. |
| SilentlyContinue |Blijft het runbook uitvoeren zonder de logboekregistratie van het bericht. Deze waarde heeft als gevolg dat het bericht wordt genegeerd. |
| Stoppen |Registreert het bericht en het runbook wordt onderbroken. |

## <a name="runbook-output"></a>Runbook-uitvoer en-berichten ophalen

### <a name="azure-portal"></a>Azure Portal

U kunt de details van een runbook-taak weer geven in het Azure Portal op het tabblad taken van een runbook. De samen vatting van de taak geeft de invoer parameters en de [uitvoer stroom](#output-stream) weer, naast algemene informatie over de taak en eventuele uitzonde ringen. De geschiedenis bevat berichten van de [uitvoer stroom](#output-stream) en [waarschuwings-en fout stromen](#warning-and-error-streams) , evenals de [uitgebreide stroom](#verbose-stream) en [voortgangs records](#progress-records) als het runbook is geconfigureerd voor het registreren van uitgebreide en voortgangs records.

### <a name="windows-powershell"></a>Windows PowerShell

In Windows Power shell kunt u uitvoer en berichten ophalen uit een runbook met behulp van de cmdlet [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) . Deze cmdlet vereist de ID van de taak en heeft een parameter genaamd Stroom waarin u opgeeft welke stroom moet worden geretourneerd. U kunt opgeven **eventuele** om te retourneren van alle stromen voor de taak.

Het volgende voorbeeld wordt een voorbeeldrunbook gestart en wacht dan tot deze is voltooid. Zodra het is voltooid, wordt de uitvoerstroom verzameld van de taak.

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

### <a name="graphical-authoring"></a>Grafisch ontwerpen

Voor grafische runbooks is extra logboek registratie beschikbaar in de vorm van tracering op activiteit niveau. Er zijn twee tracerings niveaus: basis en gedetailleerd. In basis tracering ziet u de begin-en eind tijd van elke activiteit in het runbook plus informatie met betrekking tot een nieuwe poging van een activiteit. Enkele voor beelden zijn, het aantal pogingen en de start tijd van de activiteit. In gedetailleerde tracering krijgt u elementaire tracering plus invoer-en uitvoer gegevens voor elke activiteit. De tracerings records worden momenteel geschreven met behulp van de uitgebreide stroom, dus u moet uitgebreide logboek registratie inschakelen wanneer tracering is ingeschakeld. Voor grafische runbooks waarvoor tracering is ingeschakeld, hoeft u geen voortgangs records te registreren. Basis tracering heeft hetzelfde doel en is meer informatie.

![Werk stroom voor het maken van een grafische taak](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

U kunt op de vorige scherm afbeelding zien dat wanneer u uitgebreide logboek registratie en tracering voor grafische runbooks inschakelt, veel meer informatie beschikbaar is in de weer gave productie taak stromen. Deze extra informatie kan essentieel zijn voor het oplossen van problemen met de productie met een runbook en daarom moet u deze alleen voor dat doel inschakelen en niet als algemene procedure. De tracerings records kunnen zeer groot zijn. Met grafische runbook-tracering kunt u twee tot vier records per activiteit verkrijgen, afhankelijk van of u basis-of gedetailleerde tracering hebt geconfigureerd. Tenzij u deze informatie nodig hebt om de voortgang van een runbook bij te houden voor het oplossen van problemen, wilt u de tracering mogelijk uitgeschakeld houden.

**Als u tracering op activiteit niveau wilt inschakelen, voert u de volgende stappen uit:**

1. Open uw Automation-account in Azure Portal.
2. Selecteer **runbooks** onder **proces automatisering**om de lijst met Runbooks te openen.
3. Klik op de pagina Runbooks om een grafisch runbook te selecteren in de lijst met Runbooks.
4. Klik onder **instellingen**op **logboek registratie en tracering**.
5. Klik op de pagina logboek registratie en tracering onder uitgebreide records vastleggen op **ingeschakeld** om uitgebreide logboek registratie in te scha kelen en wijzig onder tracering op activiteit niveau het tracerings niveau in **Basic** of **gedetailleerd** op basis van het tracerings niveau dat u nodig hebt.<br>

   ![De pagina logboek registratie en tracering voor grafisch ontwerpen](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure controle logboeken

Automation kan de taak status van een runbook en taak stromen verzenden naar uw Log Analytics-werk ruimte. Met Azure Monitor-Logboeken kunt u

* Krijg inzicht in uw Automation-taken
* Een e-mail of waarschuwing activeren op basis van de status van uw runbook-taak (bijvoorbeeld mislukt of opgeschort)
* Geavanceerde query's schrijven in uw taak stromen
* Taken correleren over Automation-accounts
* Uw taak geschiedenis gedurende een periode visualiseren

Zie voor meer informatie over het configureren van de integratie met Azure Monitor logboeken voor het verzamelen, correleren en uitvoeren van taak gegevens, de [taak status door sturen en taak stromen van automatisering naar Azure monitor-logboeken](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Volgende stappen

* Zie [Track a runbook job (Runbooktaken bijhouden)](automation-runbook-execution.md) voor meer informatie over runbookuitvoering, het bewaken van runbooktaken en andere technische details
* Zie [onderliggende runbooks in azure Automation](automation-child-runbooks.md) voor meer informatie over het ontwerpen en gebruiken van onderliggende runbooks
* Raadpleeg de [Power shell-documenten](/powershell/scripting/overview)voor meer informatie over Power shell, inclusief taal referentie-en leer modules.
