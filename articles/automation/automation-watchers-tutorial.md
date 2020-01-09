---
title: Een Watcher-taak maken in het Azure Automation-account
description: Meer informatie over het maken van een Watcher-taak in het Azure Automation-account om te kijken naar nieuwe bestanden die in een map zijn gemaakt.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 5dc6145940883ff6f4446ad67c399cdf4931d38e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75419746"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Een Azure Automation Watcher-taken maken om bestands wijzigingen op een lokale computer bij te houden

Azure Automation Watcher-taken gebruikt om gebeurtenissen en trigger acties met Power shell-runbooks te bekijken. In deze zelf studie wordt u begeleid bij het maken van een Watcher-taak om te controleren wanneer een nieuw bestand wordt toegevoegd aan een map.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Watcher-runbook importeren
> * Een Automation-variabele maken
> * Een actie runbook maken
> * Een Watcher-taak maken
> * Een Watcher activeren
> * De uitvoer controleren

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om deze zelfstudie te voltooien:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) voor het opslaan van de Watcher-en actie-runbooks en de Watcher-taak.
* Een [hybride runbook worker](automation-hybrid-runbook-worker.md) waarbij de Watcher-taak wordt uitgevoerd.

> [!NOTE]
> Watcher-taken worden niet ondersteund in azure China.

## <a name="import-a-watcher-runbook"></a>Een Watcher-runbook importeren

In deze zelf studie wordt een Watcher-runbook met de naam **Watch-NewFile** gebruikt om te zoeken naar nieuwe bestanden in een map. Het Watcher-runbook haalt de laatst bekende schrijf tijd op voor de bestanden in een map en bekijkt alle bestanden die nieuwer zijn dan dat water merk.

Dit import proces kan worden uitgevoerd via de [PowerShell Gallery](https://www.powershellgallery.com).

1. Ga naar de galerie pagina voor [Watch-newFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Klik op het tabblad **Azure Automation** op **implementeren naar Azure Automation**.

U kunt dit runbook ook vanuit de Portal importeren in uw Automation-account door de volgende stappen uit te voeren.

1. Open uw Automation-account en klik op de pagina **Runbooks** .
2. Klik op de knop **Bladeren in Galerie** .
3. Zoek naar ' Watcher-runbook ', selecteer **Watcher runbook dat zoekt naar nieuwe bestanden in een map** en selecteer **importeren**.
  ![Automation-runbook importeren vanuit de gebruikers interface](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Geef een naam en beschrijving op voor het runbook en selecteer **OK** om het runbook te importeren in uw Automation-account.
1. Selecteer **bewerken** en klik vervolgens op **publiceren**. Wanneer u wordt gevraagd, selecteert u **Ja** om het runbook te publiceren.

## <a name="create-an-automation-variable"></a>Een Automation-variabele maken

Een [Automation-variabele](automation-variables.md) wordt gebruikt voor het opslaan van de tijds tempels die het voor gaande runbook leest en opslaat uit elk bestand.

1. Selecteer **variabelen** onder **gedeelde resources** en selecteer **+ een variabele toevoegen**.
1. Voer ' Watch-NewFileTimestamp ' in voor de naam
1. Selecteer datum/tijd voor type.
1. Klik op de knop **maken** . Hiermee maakt u de Automation-variabele.

## <a name="create-an-action-runbook"></a>Een actie runbook maken

Een actie runbook wordt in een Watcher-taak gebruikt om te reageren op de gegevens die worden door gegeven vanuit een Watcher-runbook. Power shell workflow-runbooks worden niet ondersteund door Watcher-taken. u moet Power shell-runbooks gebruiken. U moet een vooraf gedefinieerd actie runbook met de naam **process-NewFile**importeren.

Dit import proces kan worden uitgevoerd via de [PowerShell Gallery](https://www.powershellgallery.com).

1. Ga naar de galerie pagina voor [process-newFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Klik op het tabblad **Azure Automation** op **implementeren naar Azure Automation**.

U kunt dit runbook ook vanuit de Portal importeren in uw Automation-account door de volgende stappen uit te voeren.

1. Navigeer naar uw Automation-account en selecteer **Runbooks** in de categorie **proces automatisering** .
1. Klik op de knop **Bladeren in Galerie** .
1. Zoek naar ' Watcher actie ' en selecteer **Watcher actie waarmee gebeurtenissen worden verwerkt die zijn geactiveerd door een Watcher-runbook** en selecteer **importeren**.
  ![het importeren van een actie runbook uit de gebruikers interface](media/automation-watchers-tutorial/importsourceaction.png)
1. Geef een naam en beschrijving op voor het runbook en selecteer **OK** om het runbook te importeren in uw Automation-account.
1. Selecteer **bewerken** en klik vervolgens op **publiceren**. Wanneer u wordt gevraagd, selecteert u **Ja** om het runbook te publiceren.

## <a name="create-a-watcher-task"></a>Een Watcher-taak maken

De Watcher-taak bevat twee delen. De Watcher en de actie. De Watcher wordt uitgevoerd met een interval dat is gedefinieerd in de Watcher-taak. Gegevens van het Watcher-runbook worden door gegeven aan het actie runbook. In deze stap configureert u de Watcher-taak die verwijst naar de Watcher en actie-runbooks die in de voor gaande stappen zijn gedefinieerd.

1. Navigeer naar uw Automation-account en selecteer **Watcher-taken** onder de categorie **proces automatisering** .
1. Selecteer de pagina Watcher-taken en klik op **+ een Watcher-taak knop toevoegen** .
1. Geef ' WatchMyFolder ' op als de naam.

1. Selecteer **Watcher configureren** en selecteer vervolgens het runbook **Watch-NewFile** .

1. Voer de volgende waarden in voor de para meters:

   * **FOLDERPATH** : een map op de Hybrid worker waar nieuwe bestanden worden gemaakt. d:\examplefiles
   * **Extensie** : laat deze leeg om alle bestands extensies te verwerken.
   * **Recursief** : verlaat deze waarde als standaard.
   * **Instellingen uitvoeren** : Kies de Hybrid Worker.

1. Klik op OK en selecteer vervolgens om terug te keren naar de Watcher-pagina.
1. Selecteer **actie configureren** en selecteer ' proces-NewFile ' runbook.
1. Voer de volgende waarden in voor de para meters:

   * **Event Data** : laat dit leeg. Gegevens worden door gegeven vanuit het Watcher-runbook.
   * **Instellingen uitvoeren** : verlaat als Azure omdat dit runbook wordt uitgevoerd in de Automation-Service.

1. Klik op **OK**en selecteer vervolgens om terug te keren naar de Watcher-pagina.
1. Klik op **OK** om de Watcher-taak te maken.

![Watcher-actie configureren vanuit de gebruikers interface](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Een Watcher activeren

Als u wilt testen of de Watcher werkt zoals verwacht, moet u een test bestand maken.

Extern naar de Hybrid Worker. Open **Power shell** en maak een test bestand in de map.

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

1. Navigeer naar uw Automation-account en selecteer **Watcher-taken** onder de categorie **proces automatisering** .
1. Selecteer de Watcher-taak ' WatchMyFolder '.
1. Klik op **Watcher-stromen weer geven** onder **streams** om te zien dat de Watcher het nieuwe bestand heeft gevonden en het actie runbook heeft gestart.
1. Als u de actie runbook-taken wilt zien, klikt u op de **actie taken van Watcher weer geven**. Elke taak kan worden geselecteerd om de details van de taak weer te geven.

   ![Watcher-actie taken vanuit de gebruikers interface](media/automation-watchers-tutorial/WatcherActionJobs.png)

De verwachte uitvoer wanneer het nieuwe bestand wordt gevonden, kan in het volgende voor beeld worden weer gegeven:

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Watcher-runbook importeren
> * Een Automation-variabele maken
> * Een actie runbook maken
> * Een Watcher-taak maken
> * Een Watcher activeren
> * De uitvoer controleren

Volg deze koppeling voor meer informatie over het ontwerpen van uw eigen runbook.

> [!div class="nextstepaction"]
> [Mijn eerste Power shell-runbook](automation-first-runbook-textual-powershell.md).

