---
title: Een Azure-notitieblokken-project met een aangepaste omgeving maken
description: Een nieuw project maken in Azure-notitieblokken die geconfigureerd met een specifieke set geïnstalleerde pakketten en opstartscripts.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: f167d1bf6064cde535d17fa4a336ee13424baba7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277482"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>Snelstartgids: Een project met een aangepaste omgeving maken

Een project in notitieblokken van Azure is een verzameling van bestanden, zoals laptops, gegevensbestanden, documentatie, afbeeldingen, enzovoort, samen met een omgeving die kan worden geconfigureerd met specifieke setup-opdrachten. Met het definiëren van de omgeving met het project, heeft iedereen die het klonen van het project in hun eigen account Azure-notitieblokken alle informatie die ze nodig hebben om de noodzakelijke omgeving opnieuw te maken.

## <a name="create-a-project"></a>Een project maken

1. Ga naar [Azure notebooks](https://notebooks.azure.com) en meld u aan. (Zie Quick Start ( [Aanmelden bij Azure notebooks](quickstart-sign-in-azure-notebooks.md)) voor meer informatie.

1. Selecteer op de pagina openbaar profiel **Mijn projecten** boven aan de pagina:

    ![Mijn projecten koppeling boven aan het browservenster](media/quickstarts/my-projects-link.png)

1. Selecteer op de pagina **Mijn projecten** **+ Nieuw project** (sneltoets: n); de knop kan alleen worden weer gegeven als **+** als het browser venster smal is:

    ![Nieuw Project-opdracht op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. In de pop-up **Nieuw project maken** die wordt weer gegeven, voert u de volgende gegevens in of stelt u deze in en selecteert u **maken**:

    - **Project naam**: project met een aangepaste omgeving
    - **Project-id**: Project-Custom-Environment
    - **Openbaar project**: (uitgeschakeld)
    - **Een README.MD maken**: (uitgeschakeld)

1. Na enkele ogenblikken navigeert Azure notitieblokken u naar het nieuwe project. Voeg een notitie blok toe aan het project door de vervolg keuzelijst **+ Nieuw** te selecteren (dit kan worden weer gegeven als alleen **+** ) en selecteer vervolgens **notebook**.

1. Geef het notitie blok een naam zoals *aangepaste omgeving. ipynb*, selecteer **python 3,6** voor de taal en selecteer **Nieuw**.

## <a name="add-a-custom-setup-step"></a>Een aangepaste installatie-stap toevoegen

1. Selecteer op de pagina project de optie **project instellingen**.

    ![Opdracht van de instellingen voor project](media/quickstarts/project-settings-command.png)

1. Selecteer in het pop-upvenster **project instellingen** het tabblad **omgeving** en selecteer vervolgens onder **Configuratie stappen**voor de omgeving de optie **+ toevoegen**:

    ![Opdracht voor het toevoegen van een nieuwe omgeving setup stap](media/quickstarts/environment-add-command.png)

1. Met de opdracht **+ add** wordt een stap gemaakt die wordt gedefinieerd door een bewerking en een doel bestand dat is geselecteerd in de bestanden in uw project. De volgende bewerkingen worden ondersteund:

    | Bewerking | Beschrijving |
    | --- | --- |
    | Requirements.txt | Python-projecten definiëren hun afhankelijkheden in een requirements.txt-bestand. Met deze optie selecteert u het juiste bestand uit de lijst met bestanden van het project en selecteert u ook de Python-versie in de extra vervolgkeuzelijst die wordt weergegeven. Als dat nodig is, selecteert u **Annuleren** om terug te gaan naar het project, het bestand te uploaden of te maken en gaat u terug naar het tabblad **project instellingen** > **omgeving** en maakt u een nieuwe stap. Tijdens deze stap wordt het uitvoeren van een notitie blok in het project automatisch uitgevoerd `pip install -r <file>` |
    | Shell-script | Gebruiken om een bash-shell script (meestal een bestand met de extensie *. sh* ) op te geven dat opdrachten bevat die u wilt uitvoeren om de omgeving te initialiseren. |
    | Environment.yml | Een python-project dat gebruikmaakt van Conda voor het beheren van een omgeving, gebruikt een environments *. yml* -bestand om afhankelijkheden te beschrijven. Met deze optie selecteert u het juiste bestand uit de lijst met bestanden van het project. |

1. Als u een wille keurige installatie stap wilt verwijderen, selecteert u de **X** rechts van de stap.

1. Wanneer alle installatie stappen zijn uitgevoerd, selecteert u **Opslaan**. (Selecteer **Annuleren** om de wijzigingen te negeren).

1. Als u uw omgeving wilt testen, maakt en voert u een nieuwe notebook uit en maakt u een code-cel met instructies die afhankelijk zijn van een pakket in de omgeving, zoals het gebruik van een python `import`-instructie. Als de instructie is geslaagd, is klikt u vervolgens het benodigde pakket geïnstalleerd in de omgeving.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Projecten beheren en configureren in Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Zelf studie: een Jupyter-notitie blok maken om een lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
