---
title: Oplossingen voor onboard-updatebeheer, wijzigingstracking en voorraad van een Azure-vm
description: Meer informatie over het aan boord gaan van een virtuele Azure-machine met updatebeheer, wijzigingstracking en voorraadoplossingen die deel uitmaken van Azure Automation.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: f33f829b6cb86cb01c848e5fc48e1618a3e00a2c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537029"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Oplossingen voor onboard-updatebeheer, wijzigingstracking en voorraad van een virtuele Azure-machine

Azure Automation biedt oplossingen waarmee u beveiligingsupdates voor besturingssystemen beheren, wijzigingen bijhouden en inventariseren wat er op uw computers is geïnstalleerd. Er zijn meerdere manieren om machines aan boord te gaan. U de oplossing aan boord nemen vanaf een virtuele machine, [vanaf uw Automation-account,](automation-onboard-solutions-from-automation-account.md) [van browsen op meerdere machines](automation-onboard-solutions-from-browse.md)of met behulp van een [runbook.](automation-onboard-solutions.md) In dit artikel wordt deze oplossingen vanaf een virtuele Azure-machine onboarding.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="enable-the-solutions"></a>De oplossingen inschakelen

Schakel eerst een of alle drie de oplossingen op uw VM in:

1. Selecteer **virtuele machines** in de [Azure-portal](https://portal.azure.com)of zoek **naar virtuele machines** op de startpagina en selecteer deze.
2. Selecteer de VM waarvoor u een oplossing wilt inschakelen.
3. Selecteer op de vm-pagina onder **Bewerkingen**de optie **Beheer**bijwerken , **Voorraad**of **Bijhouden wijzigen**. De virtuele machine kan in elke regio bestaan, ongeacht de locatie van uw Automatiseringsaccount. Wanneer u een oplossing van een virtuele `Microsoft.OperationalInsights/workspaces/read` machine instelt, moet u de toestemming hebben om te bepalen of de VM aan boord is van een werkruimte. Zie machtigingen die nodig zijn [voor machines aan boord](automation-role-based-access-control.md#onboarding-permissions)voor meer informatie over aanvullende machtigingen. Zie [Onboard Update Management, Change Tracking en Inventory-oplossingen](automation-onboard-solutions-from-automation-account.md)voor meer informatie over het aan boord gaan van meerdere machines.

4. Selecteer de azure log Analytics-werkruimte en automatiseringsaccount en klik op **Inschakelen** om de oplossing in te schakelen. Het duurt maximaal 15 minuten om de oplossing in te schakelen.

![Aan boord van de updatebeheeroplossing](media/automation-tutorial-update-management/manageupdates-update-enable.png)

5. Ga naar de andere oplossingen en selecteer **Inschakelen**. De vervolgkeuzelijsten voor logboekanalyses en het automatiseringsaccount zijn uitgeschakeld omdat deze oplossingen dezelfde werkruimte en automatiseringsaccount gebruiken als de eerder ingeschakelde oplossing.

> [!NOTE]
> Bij het bijhouden van wijzigingen en voorraad gebruiken dezelfde oplossing. Wanneer een van deze oplossingen is ingeschakeld, is de andere ook ingeschakeld.

## <a name="scope-configuration"></a>Scope-configuratie

Elke oplossing maakt gebruik van een scopeconfiguratie in de werkruimte om de computers te targeten die de oplossing krijgen. De scopeconfiguratie is een groep van een of meer opgeslagen zoekopdrachten die worden gebruikt om het bereik van de oplossing te beperken tot specifieke computers. Ga als u de bereikconfiguraties openen:

1. Selecteer **werkruimte**in uw automatiseringsaccount onder **Gerelateerde resources**. 
2. Selecteer in de werkruimte onder **Gegevensbronnen werkruimte**de optie **Bereikconfiguraties**.
3. Als de geselecteerde werkruimte nog niet over de oplossing Updatebeheer of Wijzigingstracking beschikt, worden de volgende scopeconfiguraties gemaakt:

    * `MicrosoftDefaultScopeConfig-ChangeTracking`
    * `MicrosoftDefaultScopeConfig-Updates`

    Als de geselecteerde werkruimte de oplossing al heeft, wordt de oplossing niet opnieuw geïmplementeerd en wordt de scopeconfiguratie niet toegevoegd.

4. Selecteer de ellipsen (**...**) op een van de configuraties en klik op **Bewerken**. 
5. Selecteer **computergroepen selecteren**in het **configuratievenster bereik bewerken** . In het deelvenster **Computergroepen** worden de opgeslagen zoekopdrachten weergegeven die worden gebruikt om de bereikconfiguratie te maken.

## <a name="saved-searches"></a>Opgeslagen zoekopdrachten

Wanneer een computer wordt toegevoegd aan de oplossing Updatebeheer, Wijzigingstracking of Voorraad, wordt de computer toegevoegd aan een van de twee opgeslagen zoekopdrachten in uw werkruimte. De opgeslagen zoekopdrachten zijn query's die de computers bevatten die zijn gericht op deze oplossingen.

Ga naar uw werkruimte. Selecteer **Onder Algemeen**de optie Opgeslagen **zoekopdrachten**. De twee opgeslagen zoekopdrachten die door deze oplossingen worden gebruikt, worden weergegeven in de volgende tabel:

|Naam     |Categorie  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking (ChangeTracking)       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Updates        | Updates__MicrosoftDefaultComputerGroup         |

Selecteer een van de opgeslagen zoekopdrachten om de query weer te geven die wordt gebruikt om de groep te vullen. In de volgende afbeelding worden de query en de resultaten weergegeven:

![Opgeslagen zoekopdrachten](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Werkruimte ontkoppelen

De volgende oplossingen zijn afhankelijk van een Log Analytics-werkruimte:

* [Updatebeheer](automation-update-management.md)
* [Tracering wijzigen](automation-change-tracking.md)
* [Vm's starten/stoppen tijdens kantooruren](automation-solution-vm-management.md)

Als u besluit uw Automatiseringsaccount niet langer te willen integreren in een Log Analytics-werkruimte, u uw account rechtstreeks loskoppelen van de Azure-portal.  Voordat u verder gaat, moet u eerst de eerder genoemde oplossingen verwijderen, anders wordt dit proces verhinderd. Bekijk het artikel voor de specifieke oplossing die u hebt geïmporteerd om inzicht te krijgen in de stappen die nodig zijn om de oplossing te verwijderen.

Nadat u deze oplossingen hebt verwijderd, u de volgende stappen uitvoeren om de koppeling van uw Automatiseringsaccount te ontkoppelen.

> [!NOTE]
> Sommige oplossingen, waaronder eerdere versies van de Azure SQL-bewakingsoplossing, hebben mogelijk automatiseringselementen gemaakt en moeten mogelijk ook worden verwijderd voordat de werkruimte wordt ontkoppeld.

1. Open in de Azure-portal uw Automatiseringsaccount en selecteer **Gekoppelde werkruimte** onder de sectie **Gerelateerde resources** aan de linkerkant.

2. Klik op de pagina Werkruimte ontkoppelen op **Werkruimte ontkoppelen**.

   ![Pagina Werkruimte ontkoppelen](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   U ontvangt een prompt waarin u wordt gevraagd of u wilt doorgaan.

3. Terwijl Azure Automation probeert de koppeling van het account van uw Log Analytics-werkruimte te ontkoppelen, u de voortgang bijhouden onder **Meldingen** in het menu.

Als u de oplossing Updatebeheer hebt gebruikt, u optioneel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

* Updateschema's - Elk heeft namen die overeenkomen met de update-implementaties die u hebt gemaakt.

* Hybride werknemersgroepen die voor de oplossing zijn gemaakt - Elk wordt op dezelfde manier genoemd als machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Als u de vm's start/stop tijdens de oplossing buiten kantooruren hebt gebruikt, u optioneel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

* Vm-runbookschema's starten en stoppen
* VM-runbooks starten en stoppen
* Variabelen

U ook de koppeling van uw werkruimte loskoppelen van uw automatiseringsaccount vanuit uw Log Analytics-werkruimte. Selecteer op uw werkruimte **automatiseringsaccount** onder **Gerelateerde resources**. Selecteer op de pagina Automatiseringsaccount de optie **Account ontkoppelen**.

## <a name="clean-up-resources"></a>Resources opschonen

Ga als lid van het werk als u een vm verwijdert uit Updatebeheer:

* Verwijder de VM in uw werkruimte Log Analytics uit `MicrosoftDefaultScopeConfig-Updates`de opgeslagen zoekopdracht naar de scopeconfiguratie. Opgeslagen zoekopdrachten zijn te vinden onder **Algemeen** in uw werkruimte.
* Verwijder de [agent Log Analytics voor Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) of de agent Log Analytics voor [Linux.](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

Ga verder naar de tutorials voor de oplossingen om te leren hoe ze te gebruiken:

* [Zelfstudie - Updates voor uw vm beheren](automation-tutorial-update-management.md)

* [Zelfstudie - Software identificeren op een virtuele machine](automation-tutorial-installed-software.md)

* [Zelfstudie - Problemen met wijzigingen op een virtuele machine oplossen](automation-tutorial-troubleshoot-changes.md)
