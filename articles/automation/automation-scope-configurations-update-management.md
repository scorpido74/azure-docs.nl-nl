---
title: Werken met Scope configuraties voor Azure Automation Updatebeheer
description: In dit artikel leest u hoe u met Scope configuraties kunt werken wanneer u Updatebeheer gebruikt.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 95563e816fa4f0931e547b6bd163dce23c338a44
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749121"
---
# <a name="work-with-scope-configurations-for-update-management"></a>Werken met Scope configuraties voor Updatebeheer

In dit artikel wordt beschreven hoe u met Scope configuraties kunt werken wanneer u de functie [updatebeheer](automation-update-management.md) op vm's gebruikt. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>De scope configuratie controleren

Updatebeheer gebruikt een scope configuratie binnen de Log Analytics-werk ruimte om de computers te richten op de functie in te scha kelen. De scope configuratie is een groep van een of meer opgeslagen Zoek opdrachten die wordt gebruikt om het bereik van de functie te beperken tot specifieke computers. Voor toegang tot de scope configuraties:

1. In uw Automation-account onder **gerelateerde resources**, selecteert u **werk ruimte**. 

2. Kies de werk ruimte onder **gegevens bronnen voor de werk ruimte**en selecteer **Scope configuraties**.

3. Als de functie Updatebeheer nog niet is ingeschakeld voor de geselecteerde werk ruimte, wordt de `MicrosoftDefaultScopeConfig-Updates` Scope configuratie gemaakt. 

4. Als de functie al is ingeschakeld voor de geselecteerde werk ruimte, wordt deze niet opnieuw geïmplementeerd en wordt de scope configuratie niet toegevoegd. 

5. Selecteer het weglatings teken in een van de scope configuraties en klik vervolgens op **bewerken**. 

6. Selecteer in het deel venster bewerken de optie **computer groepen selecteren**. In het deel venster computer groepen worden de opgeslagen Zoek opdrachten weer gegeven die worden gebruikt voor het maken van de scope configuratie.

## <a name="view-a-saved-search"></a>Een opgeslagen zoek opdracht weer geven

Wanneer een computer wordt toegevoegd aan Updatebeheer, wordt deze ook toegevoegd aan een opgeslagen zoek opdracht in uw werk ruimte. De opgeslagen zoek opdracht is een query die de doel computers bevat.

1. Navigeer naar uw Log Analytics-werk ruimte en selecteer **opgeslagen Zoek opdrachten** onder **Algemeen**. De opgeslagen zoek opdracht die door Updatebeheer wordt gebruikt, is:

|Name     |Categorie  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     | Updates        | Updates__MicrosoftDefaultComputerGroup         |

2. Selecteer de opgeslagen zoek opdracht om de query weer te geven die wordt gebruikt om de groep te vullen. In de volgende afbeelding ziet u de query en de resultaten:

    ![Opgeslagen Zoek opdrachten](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van updatebeheer](automation-update-management.md)voor algemene informatie over de functie.
* Zie [updates en patches voor uw virtuele Azure-machines beheren](automation-tutorial-update-management.md)voor meer informatie over het gebruik van de functie.
* Als u een Automation-account wilt gebruiken om de functie in te scha kelen, raadpleegt u [updatebeheer inschakelen vanuit een Automation-account](automation-onboard-solutions-from-automation-account.md).
* Als u wilt bladeren door de Azure Portal om de functie in te scha kelen, raadpleegt u [updatebeheer inschakelen in de Azure Portal](automation-onboard-solutions-from-browse.md).
* Als u een runbook wilt gebruiken om de functie in te scha kelen, raadpleegt u [updatebeheer inschakelen vanuit een runbook](automation-onboard-solutions.md).
* Als u een virtuele machine van Azure wilt gebruiken om de functie in te scha kelen, raadpleegt u [updatebeheer inschakelen op een virtuele machine van Azure](automation-onboard-solutions-from-vm.md).
* Zie [problemen met updatebeheer oplossen](troubleshoot/update-management.md)voor informatie over het oplossen van functie fouten.
* Zie problemen [met Windows Update agent oplossen](troubleshoot/update-agent-issues.md)voor informatie over het oplossen van fouten in Windows Update Agent.
* Zie problemen [met de Linux-Update agent oplossen](troubleshoot/update-agent-issues-linux.md)voor informatie over het oplossen van problemen met Linux-update agenten.