---
title: Een Azure Notebooks-project (preview-versie) maken met een aangepaste omgeving
description: Maak een nieuw project in Azure Notebooks (preview-versie) dat is geconfigureerd met een specifieke set geïnstalleerde pakketten en opstartscripts.
ms.topic: quickstart
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: 655c016b55abdcf4b6f546a1fe16348ec4c83724
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853361"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Quickstart: Een project met een aangepaste omgeving maken in Azure Notebooks (preview-versie)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Een project in Azure Notebooks is een verzameling bestanden, zoals notebooks, gegevensbestanden, documentatie, afbeeldingen, enzovoort, naast een omgeving die kan worden geconfigureerd met specifieke installatieopdrachten. Door de omgeving bij het project te definiëren, beschikt iedereen die het project naar zijn of haar eigen Azure Notebooks-account kloont, over alle benodigde informatie om de benodigde omgeving na te maken.

## <a name="create-a-project"></a>Een project maken

1. Ga naar [Azure Notebooks](https://notebooks.azure.com) en meld u aan. (Zie [Quickstart: Aanmelden bij Azure Notebooks](quickstart-sign-in-azure-notebooks.md) voor meer informatie).

1. Selecteer vanuit uw openbare profielpagina de optie **Mijn projecten** bovenaan de pagina:

    ![Koppeling naar Mijn projecten bovenaan het browservenster](media/quickstarts/my-projects-link.png)

1. Selecteer op de pagina **Mijn projecten** de optie **+ Nieuw project** (sneltoets: n). Als het browservenster smal is, wordt de knop mogelijk alleen weergegeven als **+** :

    ![De opdracht Nieuw project op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. Voer in het pop-upvenster **Nieuw project maken** dat wordt weergegeven de volgende gegevens in of stel deze in, en selecteer **Maken**:

    - **Projectnaam**: project met een aangepaste omgeving
    - **Project-id**: project-custom-environment
    - **Openbaar project**: (uitgeschakeld)
    - **Een README.md maken** : (uitgeschakeld)

1. Na enkele ogenblikken gaat Azure Notebooks naar het nieuwe project. Voeg een notebook toe aan het project door het vervolgkeuzemenu **+ Nieuw** te selecteren (die mogelijk alleen wordt weergegeven als **+** ) en selecteer vervolgens **Notebook**.

1. Geef de notebook een naam zoals *Aangepaste-omgeving.ipynb*, selecteer **Python 3.6** als de programmeertaal en selecteer **Nieuw**.

## <a name="add-a-custom-setup-step"></a>Een aangepaste installatiestap toevoegen

1. Selecteer **Projectinstellingen** op de projectpagina.

    ![De opdracht Projectinstellingen](media/quickstarts/project-settings-command.png)

1. In het pop-upvenster **Projectinstellingen** selecteer u het tabblad **Omgeving**. Selecteer vervolgens onder **Installatiestappen voor omgeving** de optie **+ Toevoegen**:

    ![Opdracht om een nieuwe omgevingsinstallatiestap toe te voegen](media/quickstarts/environment-add-command.png)

1. Met de opdracht **+ Toevoegen** maakt u een stap die wordt gedefinieerd door een bewerking en een doelbestand dat uit de bestanden in uw project wordt geselecteerd. De volgende bewerkingen worden ondersteund:

   | Bewerking | Beschrijving |
   | --- | --- |
   | Requirements.txt | Voor Python-projecten worden hun afhankelijkheden in het bestand requirements.txt gedefinieerd. Bij deze optie selecteert u het juiste bestand uit de lijst met projectbestanden en kiest u de Python-versie in de aanvullende vervolgkeuzelijst die wordt weergegeven. Indien nodig selecteert u **Annuleren** om naar het project terug te keren, upload of maak het bestand en keer vervolgens terug naar het tabblad **Projectinstellingen** > **Omgeving** en maak een nieuwe stap. Wanneer deze stap is ingesteld, wordt door het uitvoeren van een notebook in het project ook automatisch `pip install -r <file>` uitgevoerd |
   | Shellscript | Wordt gebruikt om een bash-shellscript aan te duiden (doorgaans een bestand met de extensie *.sh*) dat opdrachten bevat die u wilt uitvoeren om de omgeving te initialiseren. |
   | Environment.yml | Voor een Python-project waarvoor conda wordt gebruikt voor het beheren van een omgeving, wordt het bestand *environments.yml* gebruikt om afhankelijkheden te beschrijven. Met deze optie selecteert u het juiste bestand in de bestandenlijst van het project. |

   > [!WARNING]
   > Omdat dit een service in preview is die nog wordt ontwikkeld, is er momenteel een bekend probleem waarbij de instelling `Environment.yml` niet zoals verwacht op uw project wordt toegepast. In het project en de Jupyter-notebooks in dat project wordt het opgegeven omgevingsbestand momenteel niet geladen.

1. Als u een installatiestap wilt verwijderen, selecteert u de **X** rechts naast de stap.

1. Wanneer alle installatiestappen zijn doorgevoerd, selecteert u **Opslaan**. (Selecteer **Annuleren** om wijzigingen te verwijderen).

1. Als u uw omgeving wilt testen, moet u een nieuwe notebook maken en uitvoeren en vervolgens een codecel maken met instructies die afhankelijk zijn van een pakket in de omgeving, zoals het gebruik van een Python `import`-instructie. Als de instructie is gelukt, is het benodigde pakket geïnstalleerd in de omgeving.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Projecten beheren en configureren in Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Zelfstudie: een Jupyter-notebook maken en uitvoeren om lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
