---
title: Een Jupyter-notebook maken en delen in Azure Notebooks preview
description: U kunt snel een Jupyter-notebook maken en uitvoeren op Azure Notebooks preview en vervolgens die notebook delen met anderen.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d3310444fa28240b8fb1344199514a9601a2c615
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77064441"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Snelstartgids: een notitie blok maken en delen in Azure Notebooks preview

In deze Quick Start maakt u een Jupyter-notitie blok en voert u dit uit op Azure Notebooks en deelt u dat notitie blok met anderen. Met Jupyter kunt u eenvoudig geprijsde tekst, uitvoer bare code, permanente gegevens, afbeeldingen en visualisaties op één deelbaar canvas, de notebook combi neren. Azure Notebooks is een gratis gehoste service om Jupyter-notebooks te ontwikkelen en uitvoeren in de cloud, zonder installatie.

## <a name="prerequisites"></a>Vereisten
Geen.

## <a name="create-a-new-project-and-notebook"></a>Een nieuw project en een nieuwe notebook maken

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Ga naar de [Azure notebooks-sitehttps://notebooks.azure.com) (](https://notebooks.azure.com) en meld u aan. Zie [Quick Start (aanmelden bij Azure notebooks](quickstart-sign-in-azure-notebooks.md)) voor meer informatie.

1. Selecteer op de pagina openbaar profiel **Mijn projecten** boven aan de pagina:

    ![De koppeling Mijn projecten bovenaan in het browser venster](media/quickstarts/my-projects-link.png)

1. Selecteer op de pagina **Mijn projecten** **+ Nieuw project** (sneltoets: n). De knop kan alleen worden weer **+** gegeven alsof het browser venster smal is:

    ![De opdracht Nieuw project op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. In de pop-up **Nieuw project maken** die wordt weer gegeven, voert u de volgende gegevens in of stelt u deze in en selecteert u **maken**:

   - **Project naam**: Hallo wereld in python
   - **Project-id**: Hallo-wereld-python
   - **Openbaar project**: (uitgeschakeld)
   - **Een README.MD maken**: (uitgeschakeld)

     ![Pop-up nieuw project met gevulde Details](media/quickstarts/new-project-popup.png)

1. Na enkele ogen blikken gaat Azure Notebooks naar het nieuwe project. Voeg een notitie blok toe aan het project door de vervolg keuzelijst **+ Nieuw** te selecteren (deze kan alleen **+** worden weer gegeven) en vervolgens **notebook**te selecteren:

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. Voer in de pop-up **Nieuw notitie blok maken** die verschijnt een bestands naam in voor uw notitie blok, zoals *HelloWorldInPython. ipynb* (*. ipynb* betekent ironpython (Jupyter) notebook) en selecteer **python 3,6** voor de taal (ook wel de *kernel*genoemd):

    ![De pop-up nieuw notitie blok maken](media/quickstarts/new-notebook-popup.png)

1. Selecteer **Nieuw** om het notitie blok te maken dat vervolgens wordt weer gegeven in de lijst met bestanden van uw project:

    ![Nieuw notitie blok dat wordt weer gegeven in de bestands lijst van het project](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Het notitieblok uitvoeren

1. Selecteer het nieuwe notitie blok om dit uit te voeren in de editor. de kernel die u hebt geselecteerd (python 3,6 in dit voor beeld) wordt automatisch geactiveerd voor dit notitie blok:

    ![Weer gave van een nieuw notitie blok in Azure Notebooks](media/quickstarts/create-notebook-first-open.png)

1. Standaard bevat het notitie blok één lege code-cel. Als u het type van de cel wilt wijzigen in **verlaagd, gebruikt**u de vervolg keuzelijst cellen type om **prijs verlaging**te selecteren:

    ![Wijzigen van het type cel in een nieuw notitie blok](media/quickstarts/create-notebook-cell-type.png)

1. Voer de volgende koptekst in of plak de tekst in de cel:

    ```markdown
    # Hello World in Python
    ```

1. Omdat u de prijs verlaging bewerkt, wordt de tekst weer gegeven als een kop met de "#". Selecteer de knop **uitvoeren** om de prijs verlaging in HTML weer te geven. Azure Notebooks wordt daarna automatisch een nieuwe code-cel gemaakt:

    ![De knop uitvoeren voor een cel en gerenderde prijs verlaging](media/quickstarts/run-cell-markdown-render.png)

1. Voer in de cel code de volgende python-code in:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Selecteer **uitvoeren** (sneltoets: Shift + Enter) om de code uit te voeren. Onder de cel ziet u een geslaagde uitvoer zoals de volgende tekst:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Selecteer het pictogram opslaan om uw werk op te slaan:

    ![Pictogram opslaan op de werk balk van de Jupyter-notebook](media/quickstarts/hello-results-save-icon.png)

1. Selecteer de menu opdracht **bestand** > **sluiten en stoppen** om de server te stoppen en het browser venster te sluiten.

## <a name="share-the-notebook"></a>Het notitie blok delen

Als u uw notitie blok wilt delen, gaat u terug naar de pagina project, klikt u met de rechter muisknop op het notitie blok, selecteert u **koppeling kopiëren** (sneltoets: y) en plakt u die koppeling in een geschikt bericht (e-mail adres, im, enzovoort).

Op de pagina project kunt u ook het menu **delen** gebruiken om een koppeling te verkrijgen, een e-mail bericht met de koppeling te maken of HTML-code op te nemen en in te sluiten:

![De opdracht project share](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: een Jupyter-notebook maken en uitvoeren om lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
