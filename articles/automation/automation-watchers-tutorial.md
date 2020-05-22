---
title: Bijgewerkte bestanden bijhouden met een Azure Automation Watcher-taak
description: In dit artikel leest u hoe u een Watcher-taak maakt in het Azure Automation-account om te controleren of er nieuwe bestanden in een map zijn gemaakt.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1c41437773cf45e51e90dc55ad37e198c77f4373
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744152"
---
# <a name="track-updated-files-with-a-watcher-task"></a>Bijgewerkte bestanden bijhouden met een Watcher-taak

Azure Automation een Watcher-taak gebruikt om te zoeken naar gebeurtenissen en trigger acties met Power shell-runbooks. De Watcher-taak bevat twee delen: de Watcher en de actie. Een Watcher-runbook wordt uitgevoerd met een interval dat is gedefinieerd in de Watcher-taak en voert gegevens uit naar een actie runbook. 

> [!NOTE]
> Watcher-taken worden niet ondersteund in azure China ViaNet 21.

> [!IMPORTANT]
> Vanaf mei 2020 is het gebruik van Azure Logic Apps de ondersteunde manier om gebeurtenissen te controleren, terugkerende taken te plannen en acties te activeren. Zie [terugkerende geautomatiseerde taken, processen en werk stromen plannen en uitvoeren met Azure Logic apps](https://docs.microsoft.com/azure/logic-apps/concepts-schedule-automated-recurring-tasks-workflows).

In deze zelf studie wordt u begeleid bij het maken van een Watcher-taak om te controleren wanneer een nieuw bestand wordt toegevoegd aan een map. Procedures voor:

> [!div class="checklist"]
> * Een Watcher-runbook importeren
> * Een Automation-variabele maken
> * Een actie runbook maken
> * Een watcher-taak maken
> * Een Watcher activeren
> * De uitvoer controleren

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om deze zelfstudie te voltooien:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voor delen van uw MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) voor het opslaan van de Watcher-en actie-runbooks en de Watcher-taak.
* Een [hybride runbook worker](automation-hybrid-runbook-worker.md) waarbij de Watcher-taak wordt uitgevoerd.
* Power shell-runbooks. Power shell workflow-runbooks worden niet ondersteund door Watcher-taken.

## <a name="import-a-watcher-runbook"></a>Een Watcher-runbook importeren

In deze zelf studie wordt een Watcher-runbook met de naam **Watch-NewFile** gebruikt om te zoeken naar nieuwe bestanden in een map. Het Watcher-runbook haalt de laatst bekende schrijf tijd op voor de bestanden in een map en bekijkt alle bestanden die nieuwer zijn dan dat water merk.

Dit import proces kan worden uitgevoerd via de [PowerShell Gallery](https://www.powershellgallery.com).

1. Ga naar de galerie pagina voor [Watch-newFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Klik op het tabblad **Azure Automation** op **implementeren naar Azure Automation**.

U kunt dit runbook ook vanuit de Portal importeren in uw Automation-account door de volgende stappen uit te voeren.

1. Open uw Automation-account en klik op de pagina Runbooks.
2. Klik op **Bladeren galerie**.
3. Zoek naar **Watcher-runbook**, selecteer **Watcher runbook dat zoekt naar nieuwe bestanden in een map**en klik op **importeren**.
  ![Automation-runbook importeren vanuit de gebruikers interface](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Geef een naam en beschrijving voor het runbook op en klik op **OK** om het runbook te importeren in uw Automation-account.
5. Selecteer **bewerken** en klik vervolgens op **publiceren**. Wanneer u hierom wordt gevraagd, klikt u op **Ja** om het runbook te publiceren.

## <a name="create-an-automation-variable"></a>Een Automation-variabele maken

Een [Automation-variabele](automation-variables.md) wordt gebruikt voor het opslaan van de tijds tempels die het voor gaande runbook leest en opslaat uit elk bestand.

1. Selecteer **variabelen** onder **gedeelde resources** en klik op **+ een variabele toevoegen**.
1. Voer Watch-NewFileTimestamp in als naam.
1. Selecteer DateTime voor het type.
1. Klik op **maken** om de Automation-variabele te maken.

## <a name="create-an-action-runbook"></a>Een actie runbook maken

Een actie runbook wordt in een Watcher-taak gebruikt om te reageren op de gegevens die worden door gegeven vanuit een Watcher-runbook. U moet een vooraf gedefinieerd actie runbook met de naam **process-NewFile** van de [PowerShell Gallery](https://www.powershellgallery.com)importeren. 

Een actie runbook maken:

1. Ga naar de galerie pagina voor [process-newFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Klik op het tabblad **Azure Automation** op **implementeren naar Azure Automation**.

U kunt dit runbook ook importeren in uw Automation-account vanuit de Azure Portal:

1. Navigeer naar uw Automation-account en selecteer **Runbooks** onder **proces automatisering**.
1. Klik op **Bladeren galerie**.
1. Zoek naar **Watcher actie**, selecteer **Watcher actie waarmee gebeurtenissen worden verwerkt die worden geactiveerd door een Watcher-runbook**en klik op **importeren**.
  ![Actie runbook importeren uit gebruikers interface](media/automation-watchers-tutorial/importsourceaction.png)
1. Geef een naam en beschrijving voor het runbook op en klik op **OK** om het runbook te importeren in uw Automation-account.
1. Selecteer **bewerken** en klik vervolgens op **publiceren**. Wanneer u hierom wordt gevraagd, klikt u op **Ja** om het runbook te publiceren.

## <a name="create-a-watcher-task"></a>Een watcher-taak maken

In deze stap configureert u de Watcher-taak die verwijst naar de Watcher en actie-runbooks die in de voor gaande secties zijn gedefinieerd.

1. Navigeer naar uw Automation-account en selecteer **Watcher-taken** onder **proces automatisering**.
1. Selecteer de pagina Watcher-taken en klik op **+ een Watcher-taak toevoegen**.
1. Voer **WatchMyFolder** in als de naam.

1. Selecteer **Watcher configureren** en kies het **horloge-NewFile** runbook.

1. Voer de volgende waarden in voor de para meters:

   * **FOLDERPATH** : een map op het Hybrid Runbook worker waar nieuwe bestanden worden gemaakt, bijvoorbeeld **d:\examplefiles**.
   * **Extensie** -extensie voor de configuratie. Laat het veld leeg om alle bestands extensies te verwerken.
   * **Recursieve bewerking** . Wijzig deze waarde als standaard.
   * **Instellingen uitvoeren** : instelling voor het uitvoeren van het runbook. Kies de Hybrid Worker.

1. Klik op **OK**en **Selecteer** vervolgens om terug te keren naar de Watcher-pagina.
1. Selecteer **actie configureren** en kies het **proces-NewFile** runbook.
1. Voer de volgende waarden in voor de para meters:

   * **Event Data** -gebeurtenis gegevens. Leeg laten. Gegevens worden door gegeven vanuit het Watcher-runbook.
   * **Instellingen uitvoeren** : instelling voor het uitvoeren van het runbook. Verlaat als Azure, omdat dit runbook wordt uitgevoerd in Azure Automation.

1. Klik op **OK**en **Selecteer** vervolgens om terug te keren naar de Watcher-pagina.
1. Klik op **OK** om de Watcher-taak te maken.

![Watcher-actie configureren vanuit de gebruikers interface](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Een Watcher activeren

U moet een test uitvoeren zoals hieronder wordt beschreven om ervoor te zorgen dat de Watcher-taak werkt zoals verwacht. 

1. Extern in het Hybrid Runbook Worker. 
2. Open **Power shell** en maak een test bestand in de map.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

In het volgende voor beeld wordt de verwachte uitvoer weer gegeven.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>De uitvoer controleren

1. Navigeer naar uw Automation-account en selecteer **Watcher-taken** onder **proces automatisering**.
1. Selecteer de Watcher-taak **WatchMyFolder**.
1. Klik op **Watcher-stromen weer geven** onder **streams** om te zien dat de Watcher het nieuwe bestand heeft gevonden en het actie runbook heeft gestart.
1. Als u de actie runbook-taken wilt zien, klikt u op **Watcher-actie taken weer geven**. Elke taak kan worden geselecteerd om de details van de taak weer te geven.

   ![Watcher-actie taken vanuit de gebruikers interface](media/automation-watchers-tutorial/WatcherActionJobs.png)

De verwachte uitvoer wanneer het nieuwe bestand wordt gevonden, kan in het volgende voor beeld worden weer gegeven:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een Watcher-runbook importeren
> * Een Automation-variabele maken
> * Een actie runbook maken
> * Een watcher-taak maken
> * Een Watcher activeren
> * De uitvoer controleren

Volg deze koppeling voor meer informatie over het ontwerpen van uw eigen runbook.

> [!div class="nextstepaction"]
> [Een PowerShell-runbook maken](learn/automation-tutorial-runbook-textual-powershell.md)

