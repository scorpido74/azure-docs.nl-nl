---
title: Werken met Scope configuraties voor Azure Automation-Wijzigingen bijhouden en-inventaris
description: In dit artikel leest u hoe u met Scope configuraties kunt werken wanneer u Wijzigingen bijhouden en inventaris gebruikt.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c30bd8a3bb4fa1085e56dd93c66c016c3612e352
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749135"
---
# <a name="work-with-scope-configurations-for-change-tracking-and-inventory"></a>Werken met Scope configuraties voor Wijzigingen bijhouden en inventaris

In dit artikel wordt beschreven hoe u kunt werken met Scope configuraties bij het inschakelen van de functie [updatebeheer](automation-update-management.md) op vm's. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>De scope configuratie controleren

Updatebeheer gebruikt een scope configuratie binnen de Log Analytics-werk ruimte om de computers te richten die voor Updatebeheer moeten worden ingeschakeld. De scope configuratie is een groep van een of meer opgeslagen Zoek opdrachten die wordt gebruikt om het bereik van de functie te beperken tot specifieke computers. Voor toegang tot de scope configuraties:

1. In uw Automation-account onder **gerelateerde resources**, selecteert u **werk ruimte**. 

2. Kies de werk ruimte onder **gegevens bronnen voor de werk ruimte**en selecteer **Scope configuraties**.

3. Als de functie Updatebeheer nog niet is ingeschakeld voor de geselecteerde werk ruimte, wordt de `MicrosoftDefaultScopeConfig-ChangeTracking` Scope configuratie gemaakt. 

4. Als de functie al is ingeschakeld voor de geselecteerde werk ruimte, wordt deze niet opnieuw geïmplementeerd en wordt de scope configuratie niet toegevoegd. 

5. Selecteer het weglatings teken in een van de scope configuraties en klik vervolgens op **bewerken**. 

6. Selecteer in het deel venster bewerken de optie **computer groepen selecteren**. In het deel venster computer groepen worden de opgeslagen Zoek opdrachten weer gegeven die worden gebruikt voor het maken van de scope configuratie.

## <a name="view-a-saved-search"></a>Een opgeslagen zoek opdracht weer geven

Wanneer een computer wordt toegevoegd aan Wijzigingen bijhouden en inventaris, wordt deze ook toegevoegd aan een opgeslagen zoek opdracht in uw werk ruimte. De opgeslagen zoek opdracht is een query die de doel computers bevat.

1. Navigeer naar uw Log Analytics-werk ruimte en selecteer **opgeslagen Zoek opdrachten** onder **Algemeen**. De opgeslagen zoek opdracht die door Updatebeheer wordt gebruikt, is:

    |Name     |Categorie  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  Change tracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

2. Selecteer de opgeslagen zoek opdracht om de query weer te geven die wordt gebruikt om de groep te vullen. In de volgende afbeelding ziet u de query en de resultaten:

    ![Opgeslagen Zoek opdrachten](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van wijzigingen bijhouden en inventaris](change-tracking.md)voor algemene informatie over de functie.
* Zie [Wijzigingen bijhouden en inventaris beheren](change-tracking-file-contents.md)als u de functie wilt gebruiken.
* Als u een Automation-account wilt gebruiken om de functie in te scha kelen, raadpleegt u [Wijzigingen bijhouden en inventaris inschakelen vanuit een Automation-account](automation-enable-changes-from-auto-acct.md).
* Als u de Azure Portal wilt gebruiken om de functie in te scha kelen, raadpleegt u [Wijzigingen bijhouden en inventaris inschakelen in de Azure Portal](automation-enable-changes-from-browse.md).
* Als u een runbook wilt gebruiken om de functie in te scha kelen, raadpleegt u [Wijzigingen bijhouden en inventaris inschakelen vanuit een runbook](automation-enable-changes-from-runbook.md).
* Als u een virtuele machine van Azure wilt gebruiken om de functie in te scha kelen, raadpleegt u [Wijzigingen bijhouden en inventaris inschakelen op een virtuele Azure-machine](automation-enable-changes-from-vm.md).
* Zie [problemen met wijzigingen in een virtuele Azure-machine oplossen](automation-tutorial-troubleshoot-changes.md)voor informatie over het oplossen van problemen met wijzigings-en inventarisatie op een VM.
* Zie [problemen met wijzigingen bijhouden-en inventaris problemen oplossen](troubleshoot/change-tracking.md)voor informatie over het oplossen van functie fouten.