---
title: Een Jupyter-notebook worden weergegeven als een diavoorstelling op Azure
description: Over het configureren van cellen voor diavoorstellingsmodus in een Jupyter-notebook en Presenteer de diavoorstelling met de extensie leiden.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: c372175b-beb5-4b45-b2f8-34cb06990117
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 04612dc8892a544397251580b3494badc84e2ad5
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973117"
---
# <a name="run-a-notebook-slideshow"></a>Voer een diavoorstelling notebook

Azure-laptops is vooraf geconfigureerd met de Jupyter/IPython diavoorstelling extensie (stijging) waarmee u een notitieblok rechtstreeks als een diavoorstelling presenteren. In een diavoorstelling zijn cellen doorgaans weergegeven één bewerking tegelijk met behulp van een grootte die geschikt is voor presenteren op grote schermen en u kunt de code nog steeds uitgevoerd in plaats van overschakelen naar een afzonderlijke demo-computer.

De volgende afbeelding ziet u de standaard-notebook-weergave, waarin u Markdown en de code cellen alles bij elkaar ziet:

![Een notitieblok in de standaardweergave](media/slideshow/slideshow-notebook-view.png)

Wanneer u een diavoorstelling start, de eerste cel is vergroot om op te vullen van de browser, waarbij de **X** in de linkerbovenhoek verlaat de diavoorstelling **?** in het onderste links weergegeven navigeren sneltoetsen, en de pijlen in de rechterbenedenhoek tussen de dia's:

![Een notitieblok in de diavoorstellingsmodus](media/slideshow/slideshow-slide-view.png)

Een notitieblok voorbereiden voor een diavoorstelling bestaat uit twee primaire activiteiten:

1. Omdat Markdown cellen zijn opgebouwd met grote lettertypen, worden bepaalde inhoud niet meer zichtbaar in de diavoorstelling. Dus meestal beperkt u de hoeveelheid tekst in een cel; een koptekst met twee tot vier regels meestal werkt het beste. Hebt u meer tekst, die informatie in meerdere cellen splitsen

2. Het gedrag van elke cel in de diavoorstelling configureren met behulp van de werkbalk van de cel diavoorstelling. Cel typen bepalen het gedrag van de navigatieknoppen.

## <a name="the-anatomy-of-a-slideshow"></a>De anatomie van een diavoorstelling

Als u een willekeurige notebook nemen en deze voor een diavoorstelling gebruiken, merken u meestal dat alle cellen zijn samen elkaar, en veel van de inhoud van de onderkant van het browservenster wordt verborgen. Als u wilt maken van een doeltreffende presentatie, vervolgens moet u een type diavoorstelling toewijzen aan elke cel met de werkbalk van de cel diavoorstelling:

1. Op de **weergave** in het menu **cel werkbalk** > **diavoorstelling**:

    ![De cel diavoorstelling werkbalk inschakelen](media/slideshow/slideshow-view-cell-toolbar.png)

1. Een **Type dia** vervolgkeuzelijst wordt weergegeven in de rechterbovenhoek van elke cel in het notitieblok:

    ![Cel diavoorstelling werkbalk](media/slideshow/slideshow-cell-toolbar.png)

1. Voor elke cel, selecteert u een van de vijf typen:

    ![Cel diavoorstelling typen](media/slideshow/slideshow-cell-slide-types.png)

    | Type dia | Gedrag |
    | --- | --- |
    | -(niet ingesteld) | Cel wordt weergegeven met de vorige cel, wat vaak niet het gewenste effect in een diavoorstelling. |
    | Dia | Cel is een primaire dia, met behulp van de pijlen links en rechts van het navigatiebesturingselement genavigeerd. |
    | Onderliggende dia | Cel is een primaire dia genavigeerd met behulp van de pijl-omlaag van het navigatiebesturingselement ' onder'. De pijl-omhoog retourneert aan de primaire dia. Onderliggende dia's worden gebruikt voor secundaire materiaal dat u in de belangrijkste pad van een presentatie kan overslaan, maar direct beschikbaar zijn als nodig is. |
    | Fragment | Inhoud van de cel wordt weergegeven in de context van de vorige dia of subquery dia bij het gebruik van de navigatiepijl-omlaag (een fragment wordt verwijderd wanneer u de pijl-omhoog). U kunt een fragment met een codecel gebruiken om te maken die worden weergegeven in een dia code of u kunt meerdere fragmenten gebruiken om tekst te opsommingstekens weergegeven één voor één (Zie het voorbeeld in de volgende sectie). Omdat de fragmenten maken op de huidige dia, zijn overtollige fragmenten alleen zichtbaar van de onderkant van het browservenster. |
    | Overslaan | Cel wordt niet weergegeven in de diavoorstelling. |
    | Opmerkingen | Cel bevat als sprekersnotities, worden niet in de diavoorstelling weergegeven. |

1. In eerste instantie is het handig om te kiezen **dia** voor elke cel. Vervolgens kunt u de diavoorstelling uitvoeren en juiste aanpassingen.

### <a name="example-fragment-cells-for-bullet-items"></a>Voorbeeld: fragment cellen voor items met opsommingstekens

Om te maken van opsommingstekens in een dia die worden weergegeven door één, plaats de dia-header in een Markdown-cel met de **dia** en typ vervolgens elk opsommingsteken plaatsen in een afzonderlijke Markdown cel met de **Fragment** type:

![Voorbeeld van het maken van meerdere Markdown cellen voor items met opsommingstekens](media/slideshow/slideshow-fragments.png)

Omdat de diavoorstelling fragmenten op basis van meer verticale afstand dan wanneer alle opsommingstekens zich in dezelfde cel wordt weergegeven, kunt u mogelijk niet zo veel items met opsommingstekens gebruiken.

## <a name="run-the-slideshow"></a>Voer de diavoorstelling

1. Als u een Markdown-cellen hebt bewerkt, zorg ervoor dat u ze voor het renderen van de HTML-code, anders wordt deze weergegeven uit te voeren *als* Markdown in de diavoorstelling.

1. Wanneer u hebt geconfigureerd de **Type dia** voor elke cel, selecteert u de cel waarmee u de diavoorstelling te starten, en selecteer vervolgens de **Enter/afsluiten STIJGEN diavoorstelling** op de werkbalk van de belangrijkste:

    ![Knop op de werkbalk van de belangrijkste/uitschakelen voor de STIJGEN diavoorstelling](media/slideshow/slideshow-start.png)

1. Als u wilt navigeren tussen de dia's, evenals fragmenten, gebruikt u de linker- en pijlen in de navigatiebesturingselement. De tekst in het besturingselement bevat een *slide.sub dia*.

    ![Diavoorstelling voor paginanavigatie](media/slideshow/slideshow-navigation-control.png)

1. Als u wilt navigeren tussen de dia's en onderliggende dia's, evenals fragmenten, gebruik de omhoog en omlaag pijlen, als ingeschakeld:

    ![Diavoorstelling besturingselementen voor paginanavigatie voor onderliggende dia 's](media/slideshow/slideshow-navigation-control-subslide.png)

1. Op een codecel, gebruikt u de knop afspelen om uit te voeren van de code. uitvoer wordt weergegeven op de dia:

    ![De knop afspelen om uit te voeren een codecel](media/slideshow/slideshow-run-code-cell.png)

    ![Code-cel-uitvoer wordt weergegeven in de diavoorstelling](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Uitvoer van de cel wordt beschouwd als onderdeel van de cel in een diavoorstelling. Als u een cel in het notitieblok of diavoorstelling uitvoert, wordt de uitvoer wordt weergegeven in de andere weergave ook. Schakelt u de uitvoer, gebruikt u de **cel** > **huidige uitvoer** > **wissen** opdracht (voor de huidige cel) of **cel**  >  **Alle uitvoer** > **wissen** (voor alle cellen).

1. Wanneer u klaar bent met de diavoorstelling, gebruikt u de **X** om terug te keren naar de weergave van de notebook.

## <a name="next-steps"></a>Volgende stappen

- [Procedure: Projecten configureren en beheren @ no__t-0
- [Procedure: Pakketten installeren vanuit een notitie blok @ no__t-0
- [Procedure: Werken met gegevens bestanden @ no__t-0
- [Procedure: Toegang tot gegevens bronnen @ no__t-0
