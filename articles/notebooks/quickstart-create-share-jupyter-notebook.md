---
title: Een Jupyter-notebook maken en delen in Azure Notebooks preview
description: U kunt snel een Jupyter-notebook maken en uitvoeren op Azure Notebooks preview en vervolgens die notebook delen met anderen.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 7b983d927bfd789cddb8d00675294cefc9f55e08
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647081"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Snelstartgids: een notitie blok maken en delen in Azure Notebooks preview

In deze Quick Start leert u hoe u snel een Jupyter-notebook kunt maken en uitvoeren op Azure Notebooks en dat notitie blok met anderen kunt delen.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-new-project-and-notebook"></a>Een nieuw project en een nieuwe notebook maken

1. Ga naar de [Azure notebooks-site (https://notebooks.azure.com)](https://notebooks.azure.com) en meld u aan. (Zie voor meer informatie, [-Snelstart: aanmelden bij Azure-notitieblokken](quickstart-sign-in-azure-notebooks.md)).

1. Selecteer in de pagina van uw openbare profiel **Mijn projecten** aan de bovenkant van de pagina:

    ![Mijn projecten koppeling boven aan het browservenster](media/quickstarts/my-projects-link.png)

1. Op de **Mijn projecten** weergeeft, schakelt **+ nieuw Project** (sneltoets: n); de knop wordt mogelijk weergegeven alleen als **+** als het browservenster smal is:

    ![Nieuw Project-opdracht op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. In de **nieuw Project maken** pop-upvenster dat wordt weergegeven, invoeren of stel de volgende details en selecteer vervolgens **maken**:

   - **Naam van het project**: Hello World in Python
   - **Project-ID**: hello-world-python
   - **Openbare project**: (uitgeschakeld)
   - **Maken van een README.md**: (uitgeschakeld)

     ![Nieuw Project met ingevulde details](media/quickstarts/new-project-popup.png)

1. Na enkele ogenblikken navigeert Azure notitieblokken u naar het nieuwe project. Een notitieblok toevoegen aan het project door het selecteren van de **+ nieuw** vervolgkeuzelijst (die mogelijk weergegeven als alleen **+** ), vervolgens de optie **Notebook**:

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. In de **nieuwe Notebook maken** pop-upvenster dat wordt weergegeven, Geef een bestandsnaam voor uw laptop, zoals *HelloWorldInPython.ipynb* ( *.ipynb* betekent dat de Notebook v Ironpythonu (Jupyter) ), en selecteer **Python 3.6** voor de taal (ook wel de *kernel*):

    ![Het pop-upvenster voor de nieuwe Notebook maken](media/quickstarts/new-notebook-popup.png)

1. Selecteer **nieuw** te maken van de notebook wordt vervolgens in de lijst met bestanden van uw project weergegeven voltooien:

    ![Nieuwe notebook wordt weergegeven in de lijst met bestanden van het project](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Het notitieblok uitvoeren

1. Selecteer de nieuwe notebook uit te voeren in de editor. de kernel die u hebt geselecteerd (Python 3.6 in dit voorbeeld) wordt automatisch geactiveerd voor dit notitieblok:

    ![Weergave van een nieuwe notebook in Azure-notitieblokken](media/quickstarts/create-notebook-first-open.png)

1. De notebook heeft standaard een lege codecel. Het celtype te wijzigen **Markdown**, de cel type vervolgkeuzelijst gebruiken om te selecteren **Markdown**:

    ![Wijzigen van het celtype in een nieuwe notebook](media/quickstarts/create-notebook-cell-type.png)

1. Typ of plak de volgende koptekst in de cel:

    ```markdown
    # Hello World in Python
    ```

1. Omdat het bewerken van Markdown, is de tekst wordt weergegeven als een header met de "#". Voor het renderen van de Markdown in HTML, selecteer de **uitvoeren** knop. Azure-notitieblokken automatisch maakt vervolgens een nieuwe codecel daarna:

    ![De knop uitvoeren voor een cel en het gerenderde Markdown](media/quickstarts/run-cell-markdown-render.png)

1. Voer de volgende Python-code in de codecel:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Selecteer **uitvoeren** (sneltoets: Shift + Enter) de code uit te voeren. U ziet onder de cel geslaagde uitvoer is vergelijkbaar met de volgende tekst:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Selecteer de opslaan pictogram van uw werk op te slaan:

    ![Pictogram opslaan op de werkbalk Jupyter-notebook](media/quickstarts/hello-results-save-icon.png)

1. Selecteer de menu opdracht **bestand** > **sluiten en stoppen** om de server te stoppen en het browser venster te sluiten.

## <a name="share-the-notebook"></a>Delen van de notebook

Als u wilt uw laptop delen, gaat u terug naar de projectpagina indien nodig, met de rechtermuisknop op de notebook-bestand, selecteer **koppeling kopiëren** (sneltoets: y), en plak de koppeling in een toepasselijk bericht (e-mail, IM, enz.).

Op de projectpagina ook kunt u de **Share** invoegcode menu Haal een koppeling, het maken van een e-mailbericht met de koppeling of het verkrijgen van HTML en Markdown:

![Opdracht voor project delen](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: een run maken een Jupyter-notebook te doen, lineaire regressie](tutorial-create-run-jupyter-notebook.md)
