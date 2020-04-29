---
title: Een Azure Notebooks preview-project maken met een aangepaste omgeving
description: Maak een nieuw project in Azure Notebooks preview dat is geconfigureerd met een specifieke set geïnstalleerde pakketten en opstart scripts.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 6388cb7997cac5bef25975043a13c4e080f288d4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78196838"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Snelstartgids: een project met een aangepaste omgeving maken in Azure Notebooks preview

Een project in Azure Notebooks is een verzameling bestanden, zoals notebooks, gegevens bestanden, Documentatie, afbeeldingen enzovoort, samen met een omgeving die kan worden geconfigureerd met specifieke installatie opdrachten. Als u de omgeving definieert met het project, bevat iedereen die het project in hun eigen Azure Notebooks account kloont alle informatie die nodig is om de benodigde omgeving opnieuw te maken.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>Een project maken

1. Ga naar [Azure notebooks](https://notebooks.azure.com) en meld u aan. (Zie Quick Start ( [Aanmelden bij Azure notebooks](quickstart-sign-in-azure-notebooks.md)) voor meer informatie.

1. Selecteer op de pagina openbaar profiel **Mijn projecten** boven aan de pagina:

    ![De koppeling Mijn projecten bovenaan in het browser venster](media/quickstarts/my-projects-link.png)

1. Selecteer op de pagina **Mijn projecten** **+ Nieuw project** (sneltoets: n); de knop kan alleen worden weer **+** gegeven alsof het browser venster smal is:

    ![De opdracht Nieuw project op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. In de pop-up **Nieuw project maken** die wordt weer gegeven, voert u de volgende gegevens in of stelt u deze in en selecteert u **maken**:

    - **Project naam**: project met een aangepaste omgeving
    - **Project-id**: Project-Custom-Environment
    - **Openbaar project**: (uitgeschakeld)
    - **Een README.MD maken**: (uitgeschakeld)

1. Na enkele ogen blikken gaat Azure Notebooks naar het nieuwe project. Voeg een notitie blok toe aan het project door de vervolg keuzelijst **+ Nieuw** te selecteren (deze kan alleen **+** worden weer gegeven) en vervolgens **notebook**te selecteren.

1. Geef het notitie blok een naam zoals *aangepaste omgeving. ipynb*, selecteer **python 3,6** voor de taal en selecteer **Nieuw**.

## <a name="add-a-custom-setup-step"></a>Een aangepaste installatie stap toevoegen

1. Selecteer op de pagina project de optie **project instellingen**.

    ![Opdracht project instellingen](media/quickstarts/project-settings-command.png)

1. Selecteer in het pop-upvenster **project instellingen** het tabblad **omgeving** en selecteer vervolgens onder **Configuratie stappen**voor de omgeving de optie **+ toevoegen**:

    ![Opdracht voor het toevoegen van een nieuwe stap voor het instellen van een omgeving](media/quickstarts/environment-add-command.png)

1. Met de opdracht **+ add** wordt een stap gemaakt die wordt gedefinieerd door een bewerking en een doel bestand dat is geselecteerd in de bestanden in uw project. De volgende bewerkingen worden ondersteund:

   | Bewerking | Beschrijving |
   | --- | --- |
   | Requirements. txt | Python-projecten definiëren hun afhankelijkheden in het bestand requirements. txt. Met deze optie selecteert u het juiste bestand in de bestands lijst van het project en selecteert u ook de python-versie in de vervolg keuzelijst extra. Als dat nodig is, selecteert u **Annuleren** om terug te gaan naar het project, het bestand te uploaden of te maken en gaat u terug naar het tabblad **project instellingen** > **omgeving** en maakt u een nieuwe stap. Tijdens deze stap wordt het uitvoeren van een notitie blok in het project automatisch uitgevoerd`pip install -r <file>` |
   | Shell script | Gebruiken om een bash-shell script (meestal een bestand met de extensie *. sh* ) op te geven dat opdrachten bevat die u wilt uitvoeren om de omgeving te initialiseren. |
   | Environment. yml | Een python-project dat gebruikmaakt van Conda voor het beheren van een omgeving, gebruikt een environments *. yml* -bestand om afhankelijkheden te beschrijven. Met deze optie selecteert u het juiste bestand in de bestands lijst van het project. |

   > [!WARNING]
   > Aangezien dit een preview-service is die in ontwikkeling is, is er momenteel een bekend `Environment.yml` probleem waarbij de instelling niet op de verwachte manier wordt toegepast op het project. Het project en de Jupyter-notebooks in kunnen het opgegeven omgevings bestand momenteel niet laden.

1. Als u een wille keurige installatie stap wilt verwijderen, selecteert u de **X** rechts van de stap.

1. Wanneer alle installatie stappen zijn uitgevoerd, selecteert u **Opslaan**. (Selecteer **Annuleren** om de wijzigingen te negeren).

1. Als u uw omgeving wilt testen, maakt en voert u een nieuwe notebook uit en maakt u een code-cel met instructies die afhankelijk zijn van een pakket in de omgeving `import` , zoals het gebruik van een python-instructie. Als de instructie is geslaagd, is het benodigde pakket geïnstalleerd in de omgeving.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Projecten beheren en configureren in Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Zelf studie: een Jupyter-notitie blok maken om een lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
