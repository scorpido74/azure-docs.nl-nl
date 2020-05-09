---
title: De Azure Automation grafische runbook SDK gebruiken
description: In dit artikel wordt beschreven hoe u de Azure Automation grafische runbook SDK gebruikt.
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 886ce03b6e107d871879ff40bdc5de9ceb97c7c3
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690739"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>De Azure Automation grafische runbook SDK gebruiken

Met [grafische runbooks](automation-graphical-authoring-intro.md) kunt u de complexiteit van de onderliggende Windows Power shell-of Power shell-werk stroom code beheren. Met de Microsoft Azure Automation-SDK voor grafische elementen kunnen ontwikkel aars grafische runbooks maken en bewerken voor gebruik met Azure Automation. In dit artikel worden de basis stappen beschreven voor het maken van een grafisch runbook vanuit uw code.

## <a name="prerequisites"></a>Vereisten

Importeer het `Orchestrator.GraphRunbook.Model.dll` pakket in uw project.

## <a name="create-a-runbook-object-instance"></a>Een runbook-object instantie maken

Verwijs `Orchestrator.GraphRunbook.Model` naar de assembly en maak een `Orchestrator.GraphRunbook.Model.GraphRunbook` exemplaar van de klasse:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Runbook-para meters toevoegen

Objecten `Orchestrator.GraphRunbook.Model.Parameter` instantiëren en toevoegen aan het runbook:

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

Exemplaar van de juiste typen activiteiten en voeg deze toe aan het runbook:

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

Activiteiten worden geïmplementeerd door de volgende klassen in de `Orchestrator.GraphRunbook.Model` naam ruimte.

|Klasse  |Activiteit  |
|---------|---------|
|CommandActivity     | Hiermee wordt een Power shell-opdracht (cmdlet, function, enz.) aangeroepen.        |
|InvokeRunbookActivity     | Hiermee wordt een ander runbook inline aangeroepen.        |
|JunctionActivity     | Er wordt gewacht tot alle inkomende vertakkingen zijn voltooid.        |
|WorkflowScriptActivity     | Voert een blok met Power shell-of Power shell-werk stroom code uit (afhankelijk van het type runbook) in de context van het runbook. Dit is een krachtig hulp programma, maar niet te veel te gebruiken: de gebruikers interface zal dit script blok als tekst weer geven. de engine voor het uitvoeren van de uitvoering behandelt het meegeleverde blok als een zwart vak en maakt geen pogingen om de inhoud te analyseren, met uitzonde ring van een basis syntaxis controle. Als u slechts één Power shell-opdracht moet aanroepen, geeft u de voor keur aan CommandActivity.        |

> [!NOTE]
> U hoeft uw eigen activiteiten niet af te leiden van de beschik bare klassen. Azure Automation kan geen runbooks gebruiken met aangepaste typen activiteiten.

U moet `InvokeRunbookActivity` para `CommandActivity` meters opgeven als waarde-descriptors, niet directe waarden. Met Value-descriptors geeft u op hoe de werkelijke parameter waarden moeten worden geproduceerd. De volgende descriptors voor waarden zijn momenteel beschikbaar:


|Descriptor  |Definitie  |
|---------|---------|
|ConstantValueDescriptor     | Verwijst naar een in code vastgelegde constante waarde.        |
|RunbookParameterValueDescriptor     | Verwijst naar een runbook-para meter op naam.        |
|ActivityOutputValueDescriptor     | Verwijst naar de uitvoer van de upstream-activiteit, waardoor één activiteit kan worden geabonneerd op gegevens die door een andere activiteit worden geproduceerd.        |
|AutomationVariableValueDescriptor     | Verwijst naar een variabele van een Automation-Asset op naam.         |
|AutomationCredentialValueDescriptor     | Verwijst naar een Asset van het Automation-certificaat op naam.        |
|AutomationConnectionValueDescriptor     | Verwijst naar een Automation-verbindings Asset op naam.        |
|PowerShellExpressionValueDescriptor     | Hiermee geeft u een Power shell-expressie met een vrije vorm op die wordt geëvalueerd vlak voordat de activiteit wordt aangeroepen.  <br/>Dit is een krachtig hulp programma, maar niet te veel te gebruiken: de gebruikers interface geeft deze expressie weer als tekst. de engine voor het uitvoeren van de uitvoering behandelt het meegeleverde blok als een zwart vak en maakt geen pogingen om de inhoud te analyseren, met uitzonde ring van een basis syntaxis controle. Als dat mogelijk is, kunt u beter specifiekere waarden gebruiken.      |

> [!NOTE]
> U hebt geen eigen waarden voor descriptors van de beschik bare klassen afgeleid. Azure Automation kan geen runbooks gebruiken met typen descriptors van aangepaste waarden.

Instantie maken van koppelingen waarmee activiteiten worden verbonden en aan het runbook worden toegevoegd:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Het runbook opslaan in een bestand

Gebruiken `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` om een runbook te serialiseren naar een teken reeks:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

U kunt deze teken reeks opslaan in een bestand met de extensie **. graphrunbook** . Het bijbehorende runbook kan worden geïmporteerd in Azure Automation.
De geserialiseerde indeling kan worden gewijzigd in toekomstige versies van `Orchestrator.GraphRunbook.Model.dll`. We hebben achterwaartse compatibiliteit voor u: elk runbook dat is geserialiseerd met `Orchestrator.GraphRunbook.Model.dll` een oudere versie van kan worden gedeserialiseerd met een nieuwere versie. Voorwaartse compatibiliteit is niet gegarandeerd: een runbook dat is geserialiseerd met een nieuwere versie, mag niet worden gedeserialiseerd door oudere versies.

## <a name="next-steps"></a>Volgende stappen

Zie [Inleiding tot grafisch ontwerpen](automation-graphical-authoring-intro.md)voor meer informatie over grafische runbooks in azure Automation.