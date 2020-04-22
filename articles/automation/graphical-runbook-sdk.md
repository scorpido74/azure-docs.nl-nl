---
title: De Azure Automation grafische runbook SDK gebruiken
description: In dit artikel wordt beschreven hoe u de Azure Automation grafische runbook SDK gebruiken.
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 21f6ff8078d5a1db88b2fde33c9063a56b3ee43a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682911"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>De Azure Automation grafische runbook SDK gebruiken

[Grafische runbooks](automation-graphical-authoring-intro.md) helpen bij het beheren van de complexiteit van de onderliggende Windows PowerShell- of PowerShell-werkstroomcode. Met de Microsoft Azure Automation grafische ontwerpSDK kunnen ontwikkelaars grafische runbooks maken en bewerken voor gebruik met Azure Automation. In dit artikel worden basisstappen beschreven bij het maken van een grafisch runbook van uw code.

## <a name="prerequisites"></a>Vereisten

Importeer `Microsoft.Azure.Automation.GraphicalRunbook.Model` het pakket in uw project.

## <a name="create-a-runbook-object-instance"></a>Een voorbeeld van een runbook-object maken

Verwijs `Orchestrator.GraphRunbook.Model` naar de verzameling en `Orchestrator.GraphRunbook.Model.GraphRunbook` maak een instantie van de klasse:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Runbook-parameters toevoegen

Maak objecten `Orchestrator.GraphRunbook.Model.Parameter` instantiate en voeg ze toe aan het runbook:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Activiteiten en koppelingen toevoegen

Instantiate activiteiten van de juiste soorten en voeg ze toe aan het runbook:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

Activiteiten worden uitgevoerd door de volgende `Orchestrator.GraphRunbook.Model` klassen in de naamruimte.

|Klasse  |Activiteit  |
|---------|---------|
|CommandActiviteit     | Beroept zich op een PowerShell-opdracht (cmdlet, functie, enz.).        |
|Activiteit van aanroepenRunbook     | Roept een andere runbook inline.        |
|Activiteit van knooppunten     | Wacht tot alle inkomende takken zijn voltooid.        |
|WorkflowScriptActiviteit     | Hiermee wordt een blok PowerShell- of PowerShell-werkstroomcode (afhankelijk van het runbook-type) uitgevoerd in de context van het runbook. Dit is een krachtig hulpmiddel, maar gebruik het niet te veel: de gebruikersinterface toont dit scriptblok als tekst; de uitvoeringsengine behandelt het opgegeven blok als een zwart vak en doet geen pogingen om de inhoud ervan te analyseren, behalve een basissyntaxiscontrole. Als u slechts één PowerShell-opdracht hoeft aan te roepen, geeft u de voorkeur aan CommandActivity.        |

> [!NOTE]
> Ontlenen uw eigen activiteiten niet aan de meegeleverde klassen. Azure Automation kan geen runbooks gebruiken met aangepaste activiteitstypen.

U moet `CommandActivity` `InvokeRunbookActivity` parameters opgeven en als waardebeschrijvingen, niet als directe waarden. Waardebeschrijvingen geven aan hoe de werkelijke parameterwaarden moeten worden geproduceerd. De volgende waardebeschrijvingen worden momenteel verstrekt:


|Descriptor  |Definitie  |
|---------|---------|
|ConstantValueDescriptor     | Verwijst naar een hard-gecodeerde constante waarde.        |
|RunbookParameterValueDescriptor     | Verwijst naar een runbook parameter op naam.        |
|ActivityOutputValueDescriptor     | Verwijst naar een upstream-activiteit output, waardoor een activiteit te "abonneren" op gegevens die door een andere activiteit.        |
|AutomationVariableValueDescriptor     | Verwijst naar een automatiseringsvariabele-asset op naam.         |
|AutomationCredentialValueDescriptor     | Verwijst naar een asset automatiseringscertificaat op naam.        |
|AutomationConnectionValueDescriptor     | Verwijst naar een asset Automation Connection op naam.        |
|PowerShellExpressionValueDescriptor     | Hiermee geeft u een PowerShell-expressie in vrije vorm op die vlak voor een beroep op de activiteit wordt geëvalueerd.  <br/>Dit is een krachtig hulpmiddel, maar gebruik het niet te veel: de gebruikersinterface zal deze expressie als tekst weergeven; de uitvoeringsengine behandelt het opgegeven blok als een zwart vak en doet geen pogingen om de inhoud ervan te analyseren, behalve een basissyntaxiscontrole. Indien mogelijk, liever meer specifieke waarde beschrijvingen.      |

> [!NOTE]
> Ontlenen uw eigen waarde beschrijvingen niet van de verstrekte klassen. Azure Automation kan geen runbooks gebruiken met aangepaste waardebeschrijvingstypen.

Instantiate koppelt verbindingsactiviteiten en voeg ze toe aan het runbook:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>De runbook opslaan in een bestand

Met `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` als beste degelijk een runbook op een tekenreeks te seriseren:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

U deze tekenreeks opslaan in een bestand met de extensie **.graphrunbook.** De bijbehorende runbook kan worden geïmporteerd in Azure Automation.
De geserialiseerde indeling kan veranderen `Orchestrator.GraphRunbook.Model.dll`in de toekomstige versies van . We beloven achterwaartse compatibiliteit: elke runbook geserialiseerd met een oudere versie van `Orchestrator.GraphRunbook.Model.dll` kan worden gedeserialiseerd door een nieuwere versie. Forward compatibiliteit is niet gegarandeerd: een runbook geserialiseerd met een nieuwere versie kan niet worden deserializable door oudere versies.

## <a name="next-steps"></a>Volgende stappen

Zie [Inleiding voor](automation-graphical-authoring-intro.md)grafische ontwerpen voor meer informatie over grafische runbooks in Azure Automation.