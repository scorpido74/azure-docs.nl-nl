---
title: Een Jupyter-notitieblok presenteren als een diavoorstelling in Azure Notebooks Preview
description: Meer informatie over het configureren van cellen voor diavoorstellingsmodus in een Jupyter-notitieblok en vervolgens de diavoorstelling presenteren met behulp van de RISE-extensie.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 05dd3d9c5580e208ecf6f9e6d762476b0b493a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647115"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>Een notitieblokdiavoorstelling uitvoeren in de proefversie van Azure Notebooks

Azure Notebooks is vooraf geconfigureerd met de Jupyter/IPython Slideshow Extension (RISE) waarmee u een notitieblok rechtstreeks als diavoorstelling presenteren. In een diavoorstelling worden cellen meestal één voor één weergegeven met behulp van een tekengrootte die geschikt is voor presentatie op grote schermen, en u de code nog steeds uitvoeren in plaats van over te schakelen naar een afzonderlijke democomputer.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

In de volgende afbeelding wordt de standaardnotitieblokweergave weergegeven, waarin u Markdown en codecellen samen zien:

![Een notitieblok in de standaardweergave](media/slideshow/slideshow-notebook-view.png)

Wanneer u een diavoorstelling start, wordt de eerste cel vergroot om de browser te vullen, waarbij de **X** linksboven de diavoorstelling **verlaat?** Linksonder worden sneltoetsen weergegeven en de pijlen rechtsonder navigeren tussen dia's:

![Een notitieblok in diavoorstellingsmodus](media/slideshow/slideshow-slide-view.png)

Het voorbereiden van een notitieblok voor een diavoorstelling omvat twee primaire activiteiten:

1. Omdat markeringscellen worden weergegeven met grote lettertypen, is sommige inhoud mogelijk niet zichtbaar in de diavoorstelling. U beperkt dus meestal de hoeveelheid tekst in een bepaalde cel; een header met vier tot zes lijnen werkt meestal het beste. Als u meer tekst hebt, splitst u die informatie op in meerdere cellen.

2. Configureer het gedrag van elke cel in de diavoorstelling met behulp van de werkbalk diavoorstellingcel. Celtypen bepalen het gedrag van de navigatieknoppen.

## <a name="the-anatomy-of-a-slideshow"></a>De anatomie van een diavoorstelling

Als u een willekeurig notitieblok neemt en het gebruikt voor een diavoorstelling, vindt u meestal dat alle cellen door elkaar zijn gegooid en dat een groot deel van de inhoud is verborgen onder in het browservenster. Als u een effectieve presentatie wilt maken, moet u vervolgens een diavoorstellingstype aan elke cel toewijzen met behulp van de werkbalk Diavoorstellingscel:

1. Selecteer in het menu **Weergave** de optie **Diavoorstelling van de celwerkbalk:** > **Slideshow**

    ![De werkbalk van de celdiavoorstelling inschakelen](media/slideshow/slideshow-view-cell-toolbar.png)

1. Rechtsboven in elke cel in het notitieblok verschijnt een vervolgkeuzelijst **Diatype:**

    ![Werkbalk Celdiavoorstelling](media/slideshow/slideshow-cell-toolbar.png)

1. Selecteer voor elke cel een van de vijf typen:

    ![Celdiavoorstellingstypen](media/slideshow/slideshow-cell-slide-types.png)

    | Diatype | Gedrag |
    | --- | --- |
    | - (niet ingesteld) | Cel wordt weergegeven met de vorige cel, wat vaak geen gewenst effect is in een diavoorstelling. |
    | Dia | Cel is een primaire dia, genavigeerd met de linker- en rechterpijlen van het navigatiebesturingselement. |
    | Subdia | Cel is "onder" een primaire dia, genavigeerd naar het gebruik van de pijl-omlaag van het navigatiebesturingselement. De pijl-omhoog keert terug naar de primaire dia. Subdia's worden gebruikt voor secundair materiaal dat u in het hoofdpad van een presentatie overslaan, maar die indien nodig direct beschikbaar is. |
    | Fragment | Celinhoud wordt weergegeven in de context van de vorige dia of subdia wanneer u de pijl-omlaag gebruikt (een fragment wordt verwijderd wanneer u de pijl-omhoog gebruikt). U een fragment met een codecel gebruiken om die code in een dia weer te geven, of u meerdere fragmenten gebruiken om tekstopsommingstekens één voor één te laten verschijnen (zie voorbeeld in de volgende sectie). Omdat fragmenten op de huidige dia worden gebouwd, zijn overtollige fragmenten niet zichtbaar aan de onderkant van het browservenster. |
    | Overslaan | Cel wordt niet weergegeven in de diavoorstelling. |
    | Opmerkingen | Cel bevat als sprekersnotities, die niet worden weergegeven in de diavoorstelling. |

1. In eerste instantie is het handig om **dia** voor elke cel te kiezen. U de diavoorstelling vervolgens uitvoeren en de juiste aanpassingen aanbrengen.

### <a name="example-fragment-cells-for-bullet-items"></a>Voorbeeld: fragmentcellen voor opsommingstekens

Als u opsommingstekens op een dia één voor één wilt laten verschijnen, plaatst u de diakopin een markdowncel met het **diatype** en plaatst u elke opsommingsteken in een afzonderlijke markeringscel met het **fragmenttype:**

![Voorbeeld van het maken van meerdere markeringscellen voor opsommingstekens](media/slideshow/slideshow-fragments.png)

Omdat de diavoorstelling fragmenten met meer verticale afstand weergeeft dan wanneer alle opsommingstekens zich in dezelfde cel bevinden, u mogelijk niet zoveel opsommingstekens gebruiken.

## <a name="run-the-slideshow"></a>De diavoorstelling uitvoeren

1. Als u markdowncellen hebt bewerkt, moet u deze uitvoeren om hun HTML weer te geven, anders worden ze weergegeven *als* Markdown in de diavoorstelling.

1. Nadat u het **diatype** voor elke cel hebt geconfigureerd, selecteert u de cel waarmee u de diavoorstelling wilt starten en selecteert u de knop **RISE-diavoorstelling invoeren/afsluiten** op de hoofdwerkbalk:

    ![Knop RISE-diavoorstelling invoeren/afsluiten op de hoofdwerkbalk](media/slideshow/slideshow-start.png)

1. Als u tussen dia's en fragmenten wilt navigeren, gebruikt u de pijl-links en -rechts in het navigatiebesturingselement. De tekst in het besturingselement toont een getal dat *slide.sub-slide weergeeft*.

    ![Navigatiebesturingselement voor diavoorstellingen](media/slideshow/slideshow-navigation-control.png)

1. Als u wilt navigeren tussen dia's en subdia's en fragmenten, gebruikt u de pijl-omhoog en pijl-omlaag, indien ingeschakeld:

    ![Navigatiebesturingselementen voor subdia's](media/slideshow/slideshow-navigation-control-subslide.png)

1. Gebruik in een codecel de afspeelknop om de code uit te voeren. uitvoer wordt weergegeven op de dia:

    ![Knop Afspelen om een codecel uit te voeren](media/slideshow/slideshow-run-code-cell.png)

    ![Codeceluitvoer wordt weergegeven in de diavoorstelling](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Celuitvoer wordt beschouwd als onderdeel van de cel in een diavoorstelling. Als u een cel uitvoert in de weergave Notitieblok of diavoorstelling, wordt de uitvoer ook in de andere weergave weergegeven. Als u de uitvoer wilt wissen, gebruikt u **de** > opdracht**Celstroomuitvoer** > **wissen** (voor de huidige cel) of **Celalle** > **uitvoerwissen** > **Clear** (voor alle cellen).

1. Wanneer u klaar bent met de diavoorstelling, gebruikt u de **X** om terug te keren naar de notitieblokweergave.

## <a name="next-steps"></a>Volgende stappen

- [Hoe: Projecten configureren en beheren](configure-manage-azure-notebooks-projects.md)
- [Hoe: pakketten installeren vanuit een notitieblok](install-packages-jupyter-notebook.md)
- [Hoe: Werken met gegevensbestanden](work-with-project-data-files.md)
- [Hoe: Toegang tot gegevensbronnen](access-data-resources-jupyter-notebooks.md)
