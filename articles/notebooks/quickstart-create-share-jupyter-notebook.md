---
title: Een Jupyter-notitieblok maken en delen in De Preview van Azure-notitieblokken
description: Maak en voer snel een Jupyter-notitieblok uit in Azure Notebooks Preview en deel dat notitieblok vervolgens met anderen.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d3310444fa28240b8fb1344199514a9601a2c615
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064441"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Snelstart: een notitieblok maken en delen in de preview-proefversie van Azure Notebooks

In deze quickstart maakt en voert u een Jupyter-notitieblok uit op Azure-notitieblokken en deelt u dat notitieblok vervolgens met anderen. Met Jupyter u eenvoudig Markdown-tekst, uitvoerbare code, permanente gegevens, afbeeldingen en visualisaties combineren op één sharable canvas, het notitieblok. Azure Notebooks is een gratis gehoste service om Jupyter-notebooks te ontwikkelen en uitvoeren in de cloud, zonder installatie.

## <a name="prerequisites"></a>Vereisten
Geen.

## <a name="create-a-new-project-and-notebook"></a>Een nieuw project en notitieblok maken

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Ga naar de [sitehttps://notebooks.azure.com) Azure Notebooks (en](https://notebooks.azure.com) meld u aan. Zie [Snelstart - Aanmelden bij Azure-notitieblokken voor](quickstart-sign-in-azure-notebooks.md)meer informatie.

1. Selecteer op uw openbare profielpagina **Mijn projecten** boven aan de pagina:

    ![Koppeling Mijn projecten boven aan het browservenster](media/quickstarts/my-projects-link.png)

1. Selecteer **op** de pagina Mijn projecten **+ Nieuw project** (sneltoets: n). De knop mag **+** alleen worden weergegeven alsof het browservenster smal is:

    ![Opdracht Nieuw project op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. Selecteer in de pop-up **Nieuw project maken** dat wordt weergegeven, voer de volgende details in of stel u in en selecteer Vervolgens **Maken:**

   - **Projectnaam**: Hello World in Python
   - **Project ID**: hello-world-python
   - **Openbaar project**: (gewist)
   - **Een README.md maken**: (gewist)

     ![Nieuwe projectpop-up met bevolkte details](media/quickstarts/new-project-popup.png)

1. Na een paar ogenblikken navigeert Azure Notebooks u naar het nieuwe project. Voeg een notitieblok toe aan het project door de **vervolgkeuzelijst + Nieuw** (die kan worden weergegeven als alleen) **+** te selecteren en vervolgens **Notitieblok**te selecteren:

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. Voer in de pop-up **Nieuwe notitieblok maken** die wordt weergegeven een bestandsnaam voor uw notitieblok in, zoals *HelloWorldInPython.ipynb* (*.ipynb* betekent IronPython (Jupyter) Notitieblok) en selecteer **Python 3.6** voor de taal (ook wel de *kernel*genoemd):

    ![De pop-up Nieuw notitieblok maken](media/quickstarts/new-notebook-popup.png)

1. Selecteer **Nieuw** om het maken van het notitieblok te voltooien, dat vervolgens wordt weergegeven in de bestandslijst van uw project:

    ![Nieuw notitieblok dat wordt weergegeven in de bestandslijst van het project](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Het notitieblok uitvoeren

1. Selecteer het nieuwe notitieblok dat u in de editor wilt uitvoeren. de kernel die u hebt geselecteerd (Python 3.6 in dit voorbeeld) wordt automatisch geactiveerd voor dit notitieblok:

    ![Weergave van een nieuw notitieblok in Azure-notitieblokken](media/quickstarts/create-notebook-first-open.png)

1. Standaard heeft het notitieblok één lege codecel. Als u het celtype wilt wijzigen in **Markering,** gebruikt u de vervolgkeuzelijst voor celtype om **Markering**te selecteren:

    ![Het celtype wijzigen in een nieuw notitieblok](media/quickstarts/create-notebook-cell-type.png)

1. Voer de volgende koptekst in de cel in of plak deze:

    ```markdown
    # Hello World in Python
    ```

1. Omdat u Markdown bewerkt, wordt de tekst weergegeven als een koptekst met de '#'. Als u de markering wilt omzetten in HTML, selecteert u de knop **Uitvoeren.** Azure Notebooks maakt vervolgens automatisch een nieuwe codecel daarna:

    ![De knop Uitvoeren voor een cel en weergegeven markdown](media/quickstarts/run-cell-markdown-render.png)

1. Voer in de codecel de volgende Python-code in:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Selecteer **Uitvoeren** (sneltoets: Shift+Enter) om de code uit te voeren. Onder de cel ziet u een succesvolle uitvoer die vergelijkbaar is met de volgende tekst:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Selecteer het pictogram Opslaan om uw werk op te slaan:

    ![Pictogram Opslaan op de werkbalk Van het Jupyter-notitieblok](media/quickstarts/hello-results-save-icon.png)

1. Selecteer de menuopdracht **Bestand** > **sluiten en Stoppen** om de server te stoppen en het browservenster te sluiten.

## <a name="share-the-notebook"></a>Het notitieblok delen

Als u uw notitieblok wilt delen, schakelt u indien nodig terug naar de projectpagina, klikt u met de rechtermuisknop op het notitieblokbestand, selecteert u **Koppeling kopiëren** (sneltoets: y) en plakt u die koppeling in een geschikt bericht (e-mail, chat, enz.).

Op de projectpagina u ook het menu **Delen** gebruiken om een koppeling te verkrijgen, een e-mailbericht met de koppeling te maken of HTML- en Markdown-insluitcode te verkrijgen:

![Opdracht Projectdelen](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Een Jupyter-notitieblok maken en uitvoeren om lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
