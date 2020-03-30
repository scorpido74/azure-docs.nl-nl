---
title: Foutafhandeling in grafische Azure Automation-runbooks
description: In dit artikel wordt beschreven hoe u foutafhandelingslogica kunt implementeren in grafische Azure Automation-runbooks.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: f1aa605b3e6f32b260ea4a9eee9c056277fcd12d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367071"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Foutafhandeling in grafische Azure Automation-runbooks

Een belangrijk ontwerpprincipe waarmee rekening moet worden gehouden voor uw grafische runbook azure automation is de identificatie van problemen die het runbook kan ondervinden tijdens de uitvoering. Deze problemen kunnen geslaagde pogingen, verwachte foutstatussen en onverwachte foutvoorwaarden zijn.

Als er vaak een niet-beëindigende fout optreedt bij een runbook-activiteit, verwerkt Windows PowerShell de activiteit door alle activiteiten te verwerken die volgen, ongeacht de fout. De fout genereert waarschijnlijk een uitzondering, maar de volgende activiteit mag wel worden uitgevoerd.

Uw grafische runbook moet foutafhandelingscode bevatten om uitvoeringsproblemen op te lossen. Als u de uitvoer van een activiteit wilt valideren of een fout wilt verwerken, u een PowerShell-codeactiviteit gebruiken, voorwaardelijke logica definiëren op de uitvoerkoppeling van de activiteit of een andere methode toepassen.

Grafische Azure Automation-runbooks zijn verbeterd en bieden nu de mogelijkheid om foutafhandeling op te nemen. U kunt nu van uitzonderingen niet-afsluitfouten maken en foutkoppelingen tussen activiteiten maken. Het verbeterde proces stelt uw runbook in staat om fouten op te vangen en gerealiseerde of onverwachte omstandigheden te beheren. 

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="powershell-error-types"></a>PowerShell-fouttypen

De typen PowerShell-fouten die kunnen optreden tijdens de uitvoering van het runbook, zijn beëindigingsfouten en niet-beëindigende fouten.
 
### <a name="terminating-error"></a>Beëindigingsfout

Een beëindigingsfout is een ernstige fout tijdens de uitvoering die een opdracht- of scriptuitvoering volledig stopt. Voorbeelden hiervan zijn niet-bestaande cmdlets, syntaxisfouten die voorkomen dat een cmdlet wordt uitgevoerd en andere fatale fouten.

### <a name="non-terminating-error"></a>Niet-beëindigingsfout

Een niet-beëindigende fout is een niet-ernstige fout die het mogelijk maakt de uitvoering door te zetten ondanks de foutvoorwaarde. Voorbeelden hiervan zijn operationele fouten, zoals bestand niet gevonden fouten en machtigingen problemen.

## <a name="when-to-use-error-handling"></a>Wanneer foutafhandeling gebruiken

Gebruik foutafhandeling in uw runbook wanneer een kritieke activiteit een fout of uitzondering genereert. Het is belangrijk om te voorkomen dat de volgende activiteit in het runbook wordt verwerkt en dat de fout op de juiste manier wordt verwerkt. Het afhandelen van de fout is vooral essentieel wanneer uw runbooks een bedrijfs- of servicebewerkingsproces ondersteunen.

Voor elke activiteit die een fout kan veroorzaken, u een foutkoppeling toevoegen die naar een andere activiteit wijst. De bestemmingsactiviteit kan van elk type zijn, inclusief codeactiviteit, aanroepen van een cmdlet, aanroepen van een ander runbook, enzovoort. De bestemmingsactiviteit kan ook uitgaande koppelingen hebben, gewone of foutkoppelingen. Met de koppelingen kan het runbook complexe logica voor foutafhandeling implementeren zonder gebruik te maken van een codeactiviteit.

De aanbevolen praktijk is het maken van een speciale foutafhandelingsrunbook met algemene functionaliteit, maar deze praktijk is niet verplicht. Denk bijvoorbeeld aan een runbook dat een virtuele machine probeert te starten en er een toepassing op installeert. Als de VM niet correct wordt gestart, gaat het als het gaat om de

1. Hiermee stuurt u een melding over dit probleem.
2. Hiermee start u een ander runbook dat automatisch een nieuwe virtuele machine indient.

Een oplossing is om een foutkoppeling in het runbook te hebben die wijst op een activiteit die stap één verwerkt. Het runbook kan de `Write-Warning` cmdlet bijvoorbeeld verbinden met een activiteit voor stap twee, zoals de cmdlet [Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0)

U dit gedrag ook generaliseren voor gebruik in veel runbooks door deze twee activiteiten in een afzonderlijke runbook voor foutafhandeling te plaatsen. Voordat uw oorspronkelijke runbook deze foutafhandelingsrunbook aanroept, kan het een aangepast bericht uit de gegevens construeren en het vervolgens als parameter doorgeven aan de runbook voor foutafhandeling.

## <a name="how-to-use-error-handling"></a>Hoe foutafhandeling te gebruiken

Elke activiteit in uw runbook heeft een configuratie-instelling die uitzonderingen omtoontin niet-beëindigende fouten. Deze instelling is standaard uitgeschakeld. We raden u aan deze instelling in te schakelen voor elke activiteit waarbij uw runbook fouten verwerkt. Deze instelling zorgt ervoor dat het runbook zowel beëindigings- als niet-beëindigingsfouten in de activiteit verwerkt als niet-beëindigende fouten, met behulp van een foutkoppeling.  

Nadat u de configuratie-instelling hebt ingemaakt, maakt u een activiteit die de fout verwerkt. Als de activiteit een fout oplevert, worden de uitgaande foutkoppelingen gevolgd. De reguliere links worden niet gevolgd, zelfs als de activiteit ook regelmatig output oplevert.<br><br> ![Voorbeeld van foutkoppeling voor Automation-runbook](media/automation-runbook-graphical-error-handling/error-link-example.png)

In het volgende voorbeeld haalt een runbook een variabele op die de computernaam van een vm bevat. Vervolgens wordt geprobeerd om de VM te starten met de volgende activiteit.<br><br> ![Voorbeeld van het uitvoeren van runbook-foutafhandeling voor automatisering](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

De `Get-AutomationVariable` activiteit en de [Cmdlet Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) zijn geconfigureerd om uitzonderingen om te zetten in fouten. Als er problemen zijn met het verkrijgen van de variabele of het starten van de VM, genereert de code fouten.<br><br> ![Activiteitsinstellingen](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)voor foutafhandeling van de automatiseringsrunboek .

Foutkoppelingen vloeien van deze `error management` activiteiten over naar één codeactiviteit. Deze activiteit is geconfigureerd met een eenvoudige `throw` PowerShell-expressie die `$Error.Exception.Message` het trefwoord gebruikt om de verwerking te stoppen, samen met het bericht dat de huidige uitzondering beschrijft.<br><br> ![Voorbeeld van het uitvoeren van runbook-foutverwerkingscode voor automatisering](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Grafisch ontwerpen in Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow) voor meer informatie over koppelingen en koppelingstypen in grafische runbooks.

* Zie [Runbook-uitvoering in Azure Automation](automation-runbook-execution.md)voor meer informatie over runbook-uitvoering, het bewaken van runbook-taken en andere technische details.