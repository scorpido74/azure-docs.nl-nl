---
title: Een Jupyter-notebook presen teren als een diapresentatie op Azure Notebooks preview
description: Meer informatie over het configureren van cellen voor de diavoorstellings modus in een Jupyter-notebook en de diavoorstelling vervolgens presen teren met de toename extensie.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 2fe337361436ecfc8eabf2855ad633b891db69d8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85834043"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>Een diapresentatie van een notebook uitvoeren in Azure Notebooks preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks is vooraf geconfigureerd met de Jupyter/IPython-extensie (toename) waarmee u een notitie blok rechtstreeks als een diapresentatie kunt weer geven. In een diapresentatie worden cellen doorgaans een voor een weer gegeven met behulp van een teken grootte die geschikt is voor het presen teren op grote schermen, en u kunt de code nog steeds uitvoeren in plaats van naar een afzonderlijke demo computer te scha kelen.

In de volgende afbeelding ziet u de standaard notitieblok weergave, waarin u prijs-en code cellen tegelijk kunt zien:

![Een notitie blok in de standaard weergave](media/slideshow/slideshow-notebook-view.png)

Wanneer u een diavoorstelling start, wordt de eerste cel verg root om de browser te vullen, waarbij de **X** in de linkerbovenhoek de diavoorstelling afsluit **?** in de linkerbenedenhoek worden sneltoetsen weer gegeven en de pijlen aan de rechter kant scha kelen tussen dia's:

![Een notitie blok in de modus Diavoorstelling](media/slideshow/slideshow-slide-view.png)

Voor het voorbereiden van een notitie blok voor een diapresentatie zijn twee primaire activiteiten vereist:

1. Omdat de geprijsde cellen worden gerenderd met grote letter typen, is bepaalde inhoud mogelijk niet zichtbaar in de diavoorstelling. Doorgaans beperkt u de hoeveelheid tekst in een bepaalde cel. een koptekst met vier tot zes regels werkt meestal het beste. Als u meer tekst hebt, splitst u die informatie in meerdere cellen.

2. Het gedrag van elke cel in de diavoorstelling configureren met behulp van de werk balk van de diavoorstelling. Cellen bepalen het gedrag van de navigatie knoppen.

## <a name="the-anatomy-of-a-slideshow"></a>De anatomie van een diapresentatie

Als u een wille keurige notebook gebruikt voor een diavoorstelling, weet u doorgaans dat alle cellen onsamenhangend zijn en dat veel van de inhoud wordt verborgen onder in het browser venster. Als u een doel treffende presentatie wilt maken, moet u aan elke cel een type diavoorstelling toewijzen met behulp van de werk balk van de diavoorstelling:

1. Selecteer in het menu **weer gave** de optie diavoorstelling op **werk balk van cel**  >  **Slideshow**:

    ![De werk balk van de diavoorstelling met cellen inschakelen](media/slideshow/slideshow-view-cell-toolbar.png)

1. Er wordt een vervolg keuzelijst van het **type dia** weer gegeven in de rechter bovenhoek van elke cel in het notitie blok:

    ![Werk balk voor cel met diavoorstelling](media/slideshow/slideshow-cell-toolbar.png)

1. Selecteer een van de volgende vijf typen voor elke cel:

    ![Typen diapresentatie-cellen](media/slideshow/slideshow-cell-slide-types.png)

    | Type dia | Gedrag |
    | --- | --- |
    | -(niet ingesteld) | Cel wordt weer gegeven met de vorige cel, wat vaak niet het gewenste effect is in een diavoorstelling. |
    | Diazoeker | Cel is een primaire dia, met behulp van de pijl naar links en naar rechts van het besturings element navigatie. |
    | Subdia | Cel is "onder" een primaire dia, navigeert naar met behulp van de pijl-omlaag van het besturings element navigatie. De pijl-omhoog wordt weer gegeven op de primaire dia. Subdiavoorstellingen worden gebruikt voor secundair materiaal dat u kunt overs laan in het hoofd pad van een presentatie, maar is zo nodig direct beschikbaar. |
    | Fragment | Celinhoud wordt weer gegeven in de context van de vorige dia of subdia wanneer u de navigatie pijl omlaag gebruikt (een fragment wordt verwijderd wanneer u de pijl-omhoog gebruikt). U kunt een fragment met een code-cel gebruiken om ervoor te zorgen dat de code wordt weer gegeven in een dia of u kunt meerdere fragmenten gebruiken om tekst opsommings tekens één voor één weer te geven (Zie voor beeld in de volgende sectie). Omdat fragmenten op de huidige dia worden gebouwd, worden overtollige fragmenten niet weer gegeven in de onderkant van het browser venster. |
    | Overslaan | Cel wordt niet weer gegeven in de diavoorstelling. |
    | Notities | Cel bevat sprekers notities die niet worden weer gegeven in de diavoorstelling. |

1. In eerste instantie is het handig om de **dia** voor elke cel te kiezen. U kunt vervolgens de diavoorstelling uitvoeren en de juiste aanpassingen aanbrengen.

### <a name="example-fragment-cells-for-bullet-items"></a>Voor beeld: cellen fragmenteren voor items met opsommings tekens

Als u wilt dat opsommings tekens op een dia één voor één worden weer gegeven, plaatst u de rijkop in een cel met een prijs opgave met het type **dia** en plaatst u elk opsommings teken in een afzonderlijke afkortings cel met het **fragment** type:

![Voor beeld van het maken van meerdere prijs opgaven voor items met opsommings tekens](media/slideshow/slideshow-fragments.png)

Omdat de diavoorstelling fragmenten weergeeft met meer verticale ruimte dan wanneer alle opsommings tekens zich in dezelfde cel bevinden, kunt u mogelijk niet net zoveel items voor opsommings tekens gebruiken.

## <a name="run-the-slideshow"></a>De diavoorstelling uitvoeren

1. Als u verkortings cellen hebt bewerkt, moet u ervoor zorgen dat ze worden uitgevoerd om hun HTML weer te geven. anders worden ze weer gegeven *als* prijs verlaging in de diavoorstelling.

1. Zodra u het **type dia** voor elke cel hebt geconfigureerd, selecteert u de cel waarmee de diavoorstelling moet worden gestart en selecteert u de knop Diavoorstelling op het scherm **invoeren/afsluiten** op de hoofdwerk balk:

    ![Knop voor oplopende diavoorstelling op de hoofdwerk balk invoeren/afsluiten](media/slideshow/slideshow-start.png)

1. Als u wilt navigeren tussen dia's en fragmenten, gebruikt u de pijlen links en rechts in het besturings element navigatie. De tekst in het besturings element bevat een getal dat de *dia. subslide*vertegenwoordigt.

    ![Besturings element voor diavoorstellings navigatie](media/slideshow/slideshow-navigation-control.png)

1. Als u wilt navigeren tussen dia's en subdia's, evenals fragmenten, gebruikt u de pijl-omhoog-en-omlaag als deze functie is ingeschakeld:

    ![Besturings elementen voor diavoorstellings navigatie voor subdia's](media/slideshow/slideshow-navigation-control-subslide.png)

1. Op een code-cel gebruikt u de knop afspelen om de code uit te voeren. uitvoer wordt weer gegeven op de dia:

    ![Knop afspelen voor het uitvoeren van een code-cel](media/slideshow/slideshow-run-code-cell.png)

    ![De uitvoer van de code-cel wordt weer gegeven in de diavoorstelling](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > De cel-uitvoer wordt beschouwd als onderdeel van de cel in een diavoorstelling. Als u een cel in een van de notebook-of diavoorstellings weergave uitvoert, wordt de uitvoer ook weer gegeven in de andere weer gave. Als u de uitvoer wilt wissen, gebruikt u de **cel**  >  **huidige uitvoer**  >  **wissen** opdracht (voor de huidige cel **Cell**) of  >  **alle uitvoer**  >  **Clear** van een cel (voor alle cellen).

1. Wanneer u klaar bent met de diavoorstelling, gebruikt u de **X** om terug te keren naar de notebook weergave.

## <a name="next-steps"></a>Volgende stappen

- [Procedure: projecten configureren en beheren](configure-manage-azure-notebooks-projects.md)
- [Procedure: pakketten installeren vanuit een notitie blok](install-packages-jupyter-notebook.md)
- [Procedure: werken met gegevens bestanden](work-with-project-data-files.md)
- [Informatie over toegang tot gegevens bronnen](access-data-resources-jupyter-notebooks.md)
