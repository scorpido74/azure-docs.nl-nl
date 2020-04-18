---
title: Een wachttaak maken in het Azure Automation-account
description: Meer informatie over het maken van een wachttaak in het Azure Automation-account om te kijken naar nieuwe bestanden die in een map zijn gemaakt.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617363"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Een Azure Automation-watchertaken maken om bestandswijzigingen op een lokale machine bij te houden

Azure Automation gebruikt een wachttaak om gebeurtenissen te zoeken en acties te activeren met PowerShell-runbooks. De watcher taak bestaat uit twee delen, de watcher en de actie. Een watcher runbook wordt uitgevoerd op een interval gedefinieerd in de watcher taak, en voert gegevens naar een actie runbook. 

Met deze zelfstudie u een wachttaak maken om te controleren wanneer een nieuw bestand aan een map wordt toegevoegd. Procedures voor:

> [!div class="checklist"]
> * Een watcher runbook importeren
> * Een variabele Automatisering maken
> * Een actierunbook maken
> * Een watcher-taak maken
> * Een watcher activeren
> * De uitvoer inspecteren

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om deze zelfstudie te voltooien:

* Azure-abonnement. Als je er nog geen hebt, kun je [je MSDN-abonneevoordeel activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of je aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Automatiseringsaccount](automation-offering-get-started.md) om de wacht- en actiebeheerboeken en de Watcher-taak te behouden.
* Een [hybride runbook-werknemer](automation-hybrid-runbook-worker.md) waar de wachttaak wordt uitgevoerd.
* PowerShell runbooks. PowerShell Workflow-runbooks worden niet ondersteund door watcher-taken.

> [!NOTE]
> Watcher-taken worden niet ondersteund in Azure China.

## <a name="import-a-watcher-runbook"></a>Een watcher runbook importeren

Deze zelfstudie maakt gebruik van een watcher runbook genaamd **Watch-NewFile** om te zoeken naar nieuwe bestanden in een directory. De watcher runbook haalt de laatst bekende schrijftijd naar de bestanden in een map en kijkt naar bestanden die nieuwer zijn dan dat watermerk.

Dit importproces kan worden uitgevoerd via de [PowerShell Gallery](https://www.powershellgallery.com).

1. Navigeer naar de galeriepagina voor [Watch-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Klik onder het tabblad **Azure Automation** op Implementeren voor **Azure Automation**.

U dit runbook ook importeren in uw automatiseringsaccount vanuit de portal met behulp van de volgende stappen.

1. Open uw Automatiseringsaccount en klik op de pagina Runbooks.
2. Klik **op Galerie bladeren**.
3. Zoek naar **Watcher runbook,** selecteer **Watcher runbook dat zoekt naar nieuwe bestanden in een map**en klik op **Importeren**.
  ![Automatiseringsrunbook importeren vanuit de gebruikersinterface](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Geef het runbook een naam en beschrijving en klik op **OK** om het runbook te importeren in uw Automatiseringsaccount.
5. Selecteer **Bewerken** en klik op **Publiceren**. Klik op **Ja** om het runbook te publiceren wanneer u daarom wordt gevraagd.

## <a name="create-an-automation-variable"></a>Een variabele Automatisering maken

Een [automatiseringsvariabele](automation-variables.md) wordt gebruikt om de tijdstempels op te slaan die de voorgaande runbook uit elk bestand leest en opslaat.

1. Selecteer **Variabelen** onder **Gedeelde bronnen** en klik op + Voeg een variabele **toe**.
1. Voer Watch-NewFileTimestamp in voor de naam.
1. Selecteer Datumtijd voor het type.
1. Klik **op Maken** om de variabele Automatisering te maken.

## <a name="create-an-action-runbook"></a>Een actierunbook maken

Een actie runbook wordt gebruikt in een watcher taak om te handelen op de gegevens doorgegeven aan het van een watcher runbook. U moet een vooraf gedefinieerde actierunbook met de naam **Process-NewFile** importeren uit de [PowerShell-galerie](https://www.powershellgallery.com). 

Ga als een actie-runbook:

1. Navigeer naar de galeriepagina voor [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Klik onder het tabblad **Azure Automation** op Implementeren voor **Azure Automation**.

U deze runbook ook importeren in uw Automatiseringsaccount vanuit de Azure-portal:

1. Navigeer naar uw automatiseringsaccount en selecteer **Runbooks** onder **Procesautomatisering**.
1. Klik **op Galerie bladeren**.
1. Zoek naar **watcher-actie,** selecteer **de actie Watcher die gebeurtenissen verwerkt die worden geactiveerd door een logboek voor wachtkijkers**en klik op **Importeren**.
  ![Actierunbook importeren vanuit de gebruikersinterface](media/automation-watchers-tutorial/importsourceaction.png)
1. Geef het runbook een naam en beschrijving en klik op **OK** om het runbook te importeren in uw Automatiseringsaccount.
1. Selecteer **Bewerken** en klik op **Publiceren**. Klik op **Ja** om het runbook te publiceren wanneer u daarom wordt gevraagd.

## <a name="create-a-watcher-task"></a>Een watcher-taak maken

In deze stap configureert u de watcher-taak die verwijst naar de wachter- en actierunbooks die in de voorgaande secties zijn gedefinieerd.

1. Navigeer naar uw automatiseringsaccount en selecteer **Watcher-taken** onder **Procesautomatisering**.
1. Selecteer de pagina Wachttaken en klik op **+ Een wachttaak toevoegen**.
1. Voer **WatchMyFolder** in als naam.

1. Selecteer **Watcher configureren** en kies de **runbook Watch-NewFile.**

1. Voer de volgende waarden voor de parameters in:

   * **FOLDERPAD** - Een map op de hybride runbookworker waar nieuwe bestanden worden gemaakt, bijvoorbeeld **d:\examplefiles**.
   * **UITBREIDING** - Uitbreiding voor de configuratie. Laat leeg om alle bestandsextensies te verwerken.
   * **RECURSE** - Recursieve werking. Laat deze waarde als standaardwaarde.
   * **UITVOEREN INSTELLINGEN** - Instellen voor het uitvoeren van het runbook. Kies de hybride werknemer.

1. Klik op **OK**en **selecteer deze** om terug te keren naar de pagina Watcher.
1. Selecteer **Actie configureren** en kies de **runbook Process-NewFile.**
1. Voer de volgende waarden voor de parameters in:

   * **EVENTDATA** - Gebeurtenisgegevens. Leeg laten. Gegevens worden doorgegeven uit de watcher runbook.
   * **Instellingen uitvoeren** - Instellen voor het uitvoeren van de runbook. Laat als Azure, want deze runbook wordt uitgevoerd in Azure Automation.

1. Klik op **OK**en **selecteer deze** om terug te keren naar de pagina Watcher.
1. Klik op **OK** om de wachttaak te maken.

![Watcher-actie configureren vanuit de gebruikersinterface](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Een watcher activeren

U moet een test uitvoeren zoals hieronder beschreven om ervoor te zorgen dat de watcher-taak werkt zoals verwacht. 

1. Afstandsbediening in de hybride runbookworker. 
2. Open **PowerShell** en maak een testbestand in de map.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

In het volgende voorbeeld wordt de verwachte uitvoer weergegeven.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>De uitvoer inspecteren

1. Navigeer naar uw automatiseringsaccount en selecteer **Watcher-taken** onder **Procesautomatisering**.
1. Selecteer de watcher-taak **WatchMyFolder**.
1. Klik op **View watcher streams** onder **Streams** om te zien dat de watcher het nieuwe bestand heeft gevonden en het actie-runbook is gestart.
1. Als u de actierunbook-taken wilt bekijken, klikt u op **Actietaken voor kijkers weergeven**. Elke taak kan worden geselecteerd om de details van de taak te bekijken.

   ![Watcher actie banen van UI](media/automation-watchers-tutorial/WatcherActionJobs.png)

De verwachte uitvoer wanneer het nieuwe bestand wordt gevonden, is te zien in het volgende voorbeeld:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een watcher runbook importeren
> * Een variabele Automatisering maken
> * Een actierunbook maken
> * Een watcher-taak maken
> * Een watcher activeren
> * De uitvoer inspecteren

Volg deze link voor meer informatie over het maken van je eigen runbook.

> [!div class="nextstepaction"]
> [Mijn eerste PowerShell runbook](automation-first-runbook-textual-powershell.md).

