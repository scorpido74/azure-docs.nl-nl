---
title: Een Jupyter Notebook maken en uitvoeren in Azure Notebooks Preview
description: Snel een Jupyter Notebook maken en uitvoeren via Azure Notebooks Preview en dat notebook vervolgens delen met anderen.
ms.topic: quickstart
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: 77575b5b9e006ac9881d03dc95509d55c53c26d7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589330"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Quickstart: Een notebook maken en uitvoeren in Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

In deze quickstart gaat u een Jupyter Notebook maken en uitvoeren via Azure Notebooks en dat notebook vervolgens delen met anderen. Met Jupyter kunt u eenvoudig Markdown-tekst, uitvoerbare code, persistente gegevens, afbeeldingen en visualisaties combineren op één deelbaar canvas, het notebook. Azure Notebooks is een gratis gehoste service om Jupyter Notebooks te ontwikkelen en uit te voeren in de cloud, zonder installatie.

## <a name="prerequisites"></a>Vereisten
Geen.

## <a name="create-a-new-project-and-notebook"></a>Een nieuw project en notebook maken

1. Ga naar de [Azure Notebooks-site (https://notebooks.azure.com)](https://notebooks.azure.com) en meld u aan. Zie [Quickstart: Aanmelden bij Azure Notebooks](quickstart-sign-in-azure-notebooks.md) voor meer informatie.

1. Selecteer vanuit uw openbare profielpagina de optie **Mijn projecten** bovenaan de pagina:

    ![Koppeling naar Mijn projecten bovenaan het browservenster](media/quickstarts/my-projects-link.png)

1. Selecteer op de pagina **Mijn projecten** de optie **+ Nieuw project** (toetsenbordsneltoets: n). Als het browservenster smal is, wordt de knop mogelijk alleen weergegeven als **+** :

    ![De opdracht Nieuw project op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. Voer in het pop-upvenster **Nieuw project maken** dat wordt weergegeven de volgende gegevens in of stel deze in, en selecteer **Maken**:

   - **Projectnaam**: Hello World in Python
   - **Project-ID**: hello-world-python
   - **Openbaar project**: (uitgeschakeld)
   - **Een README.md maken** : (uitgeschakeld)

     ![Pop-up Nieuw project met ingevulde details](media/quickstarts/new-project-popup.png)

1. Na enkele ogenblikken gaat Azure Notebooks naar het nieuwe project. Voeg een notebook toe aan het project door het vervolgkeuzemenu **+ Nieuw** te selecteren (dat mogelijk alleen wordt weergegeven als **+** ) en selecteer vervolgens **Notebook**:

    :::image type="content" source="media/quickstarts/empty-project-new-notebook-button.png" alt-text="Een nieuw, leeg project en de opdracht om een notebook toe te voegen." lightbox="media/quickstarts/empty-project-new-notebook-button.png":::

1. Voer in de pop-up **Nieuw notebook maken** die wordt weergegeven een bestandsnaam in voor uw notebook, zoals *HelloWorldInPython.ipynb* ( *.ipynb* staat voor IronPython (Jupyter) Notebook), en selecteer **Python 3.6** als de taal (ook de *kernel* genoemd):

    ![De pop-up Nieuw notebook maken](media/quickstarts/new-notebook-popup.png)

1. Selecteer **Nieuw** om het maken van het notebook te voltooien, dat vervolgens in de bestandslijst van uw project wordt weergegeven:

    ![Nieuw notebook in de bestandslijst van het project](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Het notitieblok uitvoeren

1. Selecteer het nieuwe notebook om het uit te voeren in de editor; de door u geselecteerde kernel (Python 3.6 in dit voorbeeld) wordt automatisch geactiveerd voor dit notebook:

    ![Weergave van een nieuw notebook in Azure Notebooks](media/quickstarts/create-notebook-first-open.png)

1. Het notebook heeft standaard één lege codecel. Wijzig het celtype in **Markdown** door de vervolgkeuzelijst voor het celtype te gebruiken om **Markdown** te selecteren:

    ![Het celtype in een nieuw notebook wijzigen](media/quickstarts/create-notebook-cell-type.png)

1. Typ of plak de volgende koptekst in de cel:

    ```markdown
    # Hello World in Python
    ```

1. Omdat u Markdown bewerkt, wordt de tekst met het '#' weergegeven als een koptekst. Selecteer de knop **Uitvoeren** om de Markdown om te zetten in HTML. Vervolgens maakt Azure Notebooks automatisch een nieuwe codecel:

    ![De knop Uitvoeren voor een cel en gerenderde Markdown](media/quickstarts/run-cell-markdown-render.png)

1. Voer in de codecel de volgende Python-code in:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Selecteer **Uitvoeren** (toetsenbordsneltoets: Shift+Enter) om de code uit te voeren. Onder de cel zou u succesvolle uitvoer moeten zien die lijkt op de volgende tekst:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Selecteer het pictogram Opslaan om uw werk op te slaan:

    ![Pictogram Opslaan op de werkbalk van Jupyter Notebook](media/quickstarts/hello-results-save-icon.png)

1. Selecteer de menuopdracht **Bestand** > **Sluiten en stoppen** om de server te stoppen en het browservenster te sluiten.

## <a name="share-the-notebook"></a>Het notebook delen

Ga om uw notebook te delen indien nodig terug naar de projectpagina, klik met de rechtermuisknop op het notebook-bestand, selecteer **Koppeling kopiëren** (toetsenbordsneltoets: y) en plak die koppeling in een geschikt bericht (e-mail, sms, enzovoort).

Op de projectpagina kunt u ook het menu **Delen** gebruiken om een koppeling te verkrijgen, een e-mailbericht met de koppeling te maken of in te sluiten HTML- en Markdown-code te verkrijgen:

![Opdracht voor het delen van een project](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Een Jupyter Notebook maken en uitvoeren om lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
