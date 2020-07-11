---
title: Azure Automation Wijzigingen bijhouden en voorraad implementatie bereik beperken
description: In dit artikel leest u hoe u met Scope configuraties kunt werken om het bereik van een Wijzigingen bijhouden-en inventaris implementatie te beperken.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 353f29d9b4c6599226619d40e4378e21618bcad4
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185905"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Het implementatie bereik voor Wijzigingen bijhouden en inventarisatie beperken

In dit artikel wordt beschreven hoe u met Scope configuraties kunt werken wanneer u de functie [Wijzigingen bijhouden en inventarisatie](change-tracking.md) gebruikt om wijzigingen in uw vm's te implementeren. Zie voor meer informatie [doel controle oplossingen in azure monitor (preview-versie)](../azure-monitor/insights/solution-targeting.md). 

## <a name="about-scope-configurations"></a>Over Scope configuraties

Een scope configuratie is een groep van een of meer opgeslagen Zoek opdrachten (query's) die worden gebruikt om het bereik van Wijzigingen bijhouden en inventaris te beperken tot specifieke computers. De scope configuratie wordt gebruikt binnen de Log Analytics-werk ruimte om de computers te richten die moeten worden ingeschakeld. Wanneer u een computer toevoegt aan wijzigingen van de functie, wordt de computer ook toegevoegd aan een opgeslagen zoek opdracht in de werk ruimte.

## <a name="set-the-scope-limit"></a>De scope limiet instellen

Het bereik voor uw Wijzigingen bijhouden-en inventaris implementatie beperken:

1. Selecteer in uw Automation-account **gekoppelde werk ruimte** onder **gerelateerde resources**.

2. Klik op **Ga naar werk ruimte**.

3. Selecteer **Scope configuraties (preview)** onder **gegevens bronnen van de werk ruimte**.

4. Selecteer het beletsel teken rechts van de `MicrosoftDefaultScopeConfig-ChangeTracking` Scope configuratie en klik op **bewerken**. 

5. Selecteer in het deel venster bewerken de optie **computer groepen selecteren**. In het deel venster computer groepen worden de opgeslagen Zoek opdrachten weer gegeven die worden gebruikt voor het maken van de scope configuratie. De opgeslagen zoek opdracht die wordt gebruikt door Wijzigingen bijhouden en inventaris is:

    |Naam     |Categorie  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  Change tracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

6. Selecteer de opgeslagen zoek opdracht om de query die wordt gebruikt om de groep te vullen, weer te geven en te bewerken. In de volgende afbeelding ziet u de query en de resultaten:

    ![Opgeslagen Zoek opdrachten](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Wijzigingen bijhouden en inventaris beheren](change-tracking-file-contents.md)als u wilt werken met wijzigingen bijhouden en inventarisatie.
* Zie [problemen met wijzigingen bijhouden en voorraad problemen oplossen](troubleshoot/change-tracking.md)voor informatie over het oplossen van algemene problemen met functies.
