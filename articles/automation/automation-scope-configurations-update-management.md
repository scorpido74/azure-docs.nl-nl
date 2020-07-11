---
title: Azure Automation Updatebeheer implementatie bereik beperken
description: In dit artikel wordt uitgelegd hoe u Scope configuraties kunt gebruiken om het bereik van een Updatebeheer-implementatie te beperken.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 8770762fa2d2ae6bc0584d75397829298a62e8c0
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185888"
---
# <a name="limit-update-management-deployment-scope"></a>Updatebeheer implementatie bereik beperken

In dit artikel wordt beschreven hoe u met Scope configuraties kunt werken wanneer u de functie [updatebeheer](automation-update-management.md) gebruikt voor het implementeren van updates en patches voor uw virtuele machines. Zie voor meer informatie [doel controle oplossingen in azure monitor (preview-versie)](../azure-monitor/insights/solution-targeting.md). 

## <a name="about-scope-configurations"></a>Over Scope configuraties

Een scope configuratie is een groep van een of meer opgeslagen Zoek opdrachten (query's) die worden gebruikt om het bereik van Updatebeheer te beperken tot specifieke computers. De scope configuratie wordt gebruikt binnen de Log Analytics-werk ruimte om de computers te richten die moeten worden ingeschakeld. Wanneer u een computer toevoegt voor het ontvangen van updates van Updatebeheer, wordt de computer ook toegevoegd aan een opgeslagen zoek opdracht in de werk ruimte.

## <a name="set-the-scope-limit"></a>De scope limiet instellen

Het bereik voor uw Updatebeheer-implementatie beperken:

1. Selecteer in uw Automation-account **gekoppelde werk ruimte** onder **gerelateerde resources**.

2. Klik op **Ga naar werk ruimte**.

3. Selecteer **Scope configuraties (preview)** onder **gegevens bronnen van de werk ruimte**.

4. Selecteer het beletsel teken rechts van de `MicrosoftDefaultScopeConfig-Updates` Scope configuratie en klik op **bewerken**. 

5. Vouw in het deel venster voor bewerken de **optie computer groepen selecteren**uit. In het deel venster computer groepen worden de opgeslagen Zoek opdrachten weer gegeven die worden gebruikt voor het maken van de scope configuratie. De opgeslagen zoek opdracht die door Updatebeheer wordt gebruikt, is:

    |Naam     |Categorie  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Updates        | Updates__MicrosoftDefaultComputerGroup         |

6. Selecteer de opgeslagen zoek opdracht om de query die wordt gebruikt om de groep te vullen, weer te geven en te bewerken. In de volgende afbeelding ziet u de query en de resultaten:

    ![Opgeslagen Zoek opdrachten](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [updates en patches voor uw virtuele Azure-machines beheren](automation-tutorial-update-management.md)voor meer informatie over het gebruik van de functie.
* Zie [problemen met updatebeheer oplossen](troubleshoot/update-management.md)voor informatie over het oplossen van functie fouten.
* Zie problemen [met Windows Update agent oplossen](troubleshoot/update-agent-issues.md)voor informatie over het oplossen van fouten in Windows Update Agent.
* Zie problemen [met de Linux-Update agent oplossen](troubleshoot/update-agent-issues-linux.md)voor informatie over het oplossen van problemen met Linux-update agenten.
