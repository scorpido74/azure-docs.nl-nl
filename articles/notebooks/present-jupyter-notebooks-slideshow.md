---
title: Een Jupyter-notebook worden weergegeven als een diavoorstelling op Azure
description: Over het configureren van cellen voor diavoorstellingsmodus in een Jupyter-notebook en Presenteer de diavoorstelling met de extensie leiden.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: d180eaf571fa57191e3b0856020b02f05d05e344
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277594"
---
# <a name="run-a-notebook-slideshow"></a>Voer een diavoorstelling notebook

Azure-laptops is vooraf geconfigureerd met de Jupyter/IPython diavoorstelling extensie (stijging) waarmee u een notitieblok rechtstreeks als een diavoorstelling presenteren. In een diavoorstelling zijn cellen doorgaans weergegeven één bewerking tegelijk met behulp van een grootte die geschikt is voor presenteren op grote schermen en u kunt de code nog steeds uitgevoerd in plaats van overschakelen naar een afzonderlijke demo-computer.

De volgende afbeelding ziet u de standaard-notebook-weergave, waarin u Markdown en de code cellen alles bij elkaar ziet:

![Een notitieblok in de standaardweergave](media/slideshow/slideshow-notebook-view.png)

Wanneer u een diavoorstelling start, wordt de eerste cel verg root om de browser te vullen, waarbij de **X** in de linkerbovenhoek de diavoorstelling afsluit **?** in het onderste links weergegeven navigeren sneltoetsen, en de pijlen in de rechterbenedenhoek tussen de dia's:

![Een notitieblok in de diavoorstellingsmodus](media/slideshow/slideshow-slide-view.png)

Een notitieblok voorbereiden voor een diavoorstelling bestaat uit twee primaire activiteiten:

1. Omdat Markdown cellen zijn opgebouwd met grote lettertypen, worden bepaalde inhoud niet meer zichtbaar in de diavoorstelling. Dus meestal beperkt u de hoeveelheid tekst in een cel; een koptekst met twee tot vier regels meestal werkt het beste. Hebt u meer tekst, die informatie in meerdere cellen splitsen

2. Het gedrag van elke cel in de diavoorstelling configureren met behulp van de werkbalk van de cel diavoorstelling. Cel typen bepalen het gedrag van de navigatieknoppen.

## <a name="the-anatomy-of-a-slideshow"></a>De anatomie van een diavoorstelling

Als u een willekeurige notebook nemen en deze voor een diavoorstelling gebruiken, merken u meestal dat alle cellen zijn samen elkaar, en veel van de inhoud van de onderkant van het browservenster wordt verborgen. Als u wilt maken van een doeltreffende presentatie, vervolgens moet u een type diavoorstelling toewijzen aan elke cel met de werkbalk van de cel diavoorstelling:

1. Selecteer in het menu **weer gave** de optie **werk balk cel** > **Diavoorstelling**:

    ![De cel diavoorstelling werkbalk inschakelen](media/slideshow/slideshow-view-cell-toolbar.png)

1. Er wordt een vervolg keuzelijst van het **type dia** weer gegeven in de rechter bovenhoek van elke cel in het notitie blok:

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

1. In eerste instantie is het handig om de **dia** voor elke cel te kiezen. Vervolgens kunt u de diavoorstelling uitvoeren en juiste aanpassingen.

### <a name="example-fragment-cells-for-bullet-items"></a>Voorbeeld: fragment cellen voor items met opsommingstekens

Als u wilt dat opsommings tekens op een dia één voor één worden weer gegeven, plaatst u de rijkop in een cel met een prijs opgave met het type **dia** en plaatst u elk opsommings teken in een afzonderlijke afkortings cel met het **fragment** type:

![Voorbeeld van het maken van meerdere Markdown cellen voor items met opsommingstekens](media/slideshow/slideshow-fragments.png)

Omdat de diavoorstelling fragmenten op basis van meer verticale afstand dan wanneer alle opsommingstekens zich in dezelfde cel wordt weergegeven, kunt u mogelijk niet zo veel items met opsommingstekens gebruiken.

## <a name="run-the-slideshow"></a>Voer de diavoorstelling

1. Als u verkortings cellen hebt bewerkt, moet u ervoor zorgen dat ze worden uitgevoerd om hun HTML weer te geven. anders worden ze weer gegeven *als* prijs verlaging in de diavoorstelling.

1. Zodra u het **type dia** voor elke cel hebt geconfigureerd, selecteert u de cel waarmee de diavoorstelling moet worden gestart en selecteert u de knop Diavoorstelling op het scherm **invoeren/afsluiten** op de hoofdwerk balk:

    ![Knop op de werkbalk van de belangrijkste/uitschakelen voor de STIJGEN diavoorstelling](media/slideshow/slideshow-start.png)

1. Als u wilt navigeren tussen de dia's, evenals fragmenten, gebruikt u de linker- en pijlen in de navigatiebesturingselement. De tekst in het besturings element bevat een getal dat de *dia. subslide*vertegenwoordigt.

    ![Diavoorstelling voor paginanavigatie](media/slideshow/slideshow-navigation-control.png)

1. Als u wilt navigeren tussen de dia's en onderliggende dia's, evenals fragmenten, gebruik de omhoog en omlaag pijlen, als ingeschakeld:

    ![Diavoorstelling besturingselementen voor paginanavigatie voor onderliggende dia 's](media/slideshow/slideshow-navigation-control-subslide.png)

1. Op een codecel, gebruikt u de knop afspelen om uit te voeren van de code. uitvoer wordt weergegeven op de dia:

    ![De knop afspelen om uit te voeren een codecel](media/slideshow/slideshow-run-code-cell.png)

    ![Code-cel-uitvoer wordt weergegeven in de diavoorstelling](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Uitvoer van de cel wordt beschouwd als onderdeel van de cel in een diavoorstelling. Als u een cel in het notitieblok of diavoorstelling uitvoert, wordt de uitvoer wordt weergegeven in de andere weergave ook. Als u de uitvoer wilt wissen, gebruikt u de **cel** > **huidige uitvoer** > opdracht **wissen** (voor de huidige cel) of de **cel** > **alle uitvoer** > **wissen** (voor alle cellen).

1. Wanneer u klaar bent met de diavoorstelling, gebruikt u de **X** om terug te keren naar de notebook weergave.

## <a name="next-steps"></a>Volgende stappen

- [Procedure: projecten configureren en beheren](configure-manage-azure-notebooks-projects.md)
- [Procedure: pakketten installeren vanuit een notitie blok](install-packages-jupyter-notebook.md)
- [Procedure: werken met gegevens bestanden](work-with-project-data-files.md)
- [Informatie over toegang tot gegevens bronnen](access-data-resources-jupyter-notebooks.md)
