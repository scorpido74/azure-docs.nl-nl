---
title: Een Azure Notebooks preview-project maken met een aangepaste omgeving
description: Maak een nieuw project in Azure Notebooks preview dat is geconfigureerd met een specifieke set geïnstalleerde pakketten en opstart scripts.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 999133dd7d9d792956f9a2c93ec218e458c921e8
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647064"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Snelstartgids: een project met een aangepaste omgeving maken in Azure Notebooks preview

Een project in notitieblokken van Azure is een verzameling van bestanden, zoals laptops, gegevensbestanden, documentatie, afbeeldingen, enzovoort, samen met een omgeving die kan worden geconfigureerd met specifieke setup-opdrachten. Met het definiëren van de omgeving met het project, heeft iedereen die het klonen van het project in hun eigen account Azure-notitieblokken alle informatie die ze nodig hebben om de noodzakelijke omgeving opnieuw te maken.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>Een project maken

1. Ga naar [Azure notitieblokken](https://notebooks.azure.com) en meld u aan. (Zie voor meer informatie, [-Snelstart: aanmelden bij Azure-notitieblokken](quickstart-sign-in-azure-notebooks.md)).

1. Selecteer in de pagina van uw openbare profiel **Mijn projecten** aan de bovenkant van de pagina:

    ![Mijn projecten koppeling boven aan het browservenster](media/quickstarts/my-projects-link.png)

1. Op de **Mijn projecten** weergeeft, schakelt **+ nieuw Project** (sneltoets: n); de knop wordt mogelijk weergegeven alleen als **+** als het browservenster smal is:

    ![Nieuw Project-opdracht op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. In de **nieuw Project maken** pop-upvenster dat wordt weergegeven, invoeren of stel de volgende details en selecteer vervolgens **maken**:

    - **Naam van het project**: Project met een aangepaste omgeving
    - **Project-ID**: project-aangepaste-omgeving
    - **Openbare project**: (uitgeschakeld)
    - **Maken van een README.md**: (uitgeschakeld)

1. Na enkele ogenblikken navigeert Azure notitieblokken u naar het nieuwe project. Een notitieblok toevoegen aan het project door het selecteren van de **+ nieuw** vervolgkeuzelijst (die mogelijk weergegeven als alleen **+** ), vervolgens de optie **Notebook**.

1. Geef een naam, zoals op de notebook *aangepaste environment.ipynb*, selecteer **Python 3.6** voor de taal en selecteer **nieuw**.

## <a name="add-a-custom-setup-step"></a>Een aangepaste installatie-stap toevoegen

1. Selecteer op de projectpagina **projectinstellingen**.

    ![Opdracht van de instellingen voor project](media/quickstarts/project-settings-command.png)

1. In de **projectinstellingen** pop-upvenster, selecteer de **omgeving** tabblad vervolgens onder **omgeving installatiestappen uit**, selecteer **+ toevoegen**:

    ![Opdracht voor het toevoegen van een nieuwe omgeving setup stap](media/quickstarts/environment-add-command.png)

1. De **+ toevoegen** opdracht maakt u een stap die gedefinieerd door een bewerking en een doelbestand dat geselecteerd in de bestanden in uw project. De volgende bewerkingen worden ondersteund:

    | Bewerking | Beschrijving |
    | --- | --- |
    | Requirements.txt | Python-projecten definiëren hun afhankelijkheden in een requirements.txt-bestand. Met deze optie selecteert u het juiste bestand uit de lijst met bestanden van het project en selecteert u ook de Python-versie in de extra vervolgkeuzelijst die wordt weergegeven. Selecteer indien nodig, **annuleren** uploaden als u wilt terugkeren naar het project, of maak het bestand en vervolgens gaat u terug naar de **projectinstellingen** > **omgeving** tabblad en maak een nieuwe stap. Met deze stap in de plaats, met een notitieblok in het project automatisch wordt uitgevoerd `pip install -r <file>` |
    | Shell-script | Gebruiken om aan te geven van een bash-shell-script (meestal een bestand met de *.sh* extensie) die alle opdrachten die u uitvoeren wilt voor het initialiseren van de omgeving bevat. |
    | Environment.yml | Een Python-project dat conda wordt gebruikt voor het beheren van een omgeving gebruikmaakt van een *environments.yml* bestand om te beschrijven van afhankelijkheden. Met deze optie selecteert u het juiste bestand uit de lijst met bestanden van het project. |

1. Als u wilt een setup-stap verwijderen, selecteert u de **X** aan de rechterkant van de stap.

1. Wanneer alle stappen van de setup gemaakt zijn, selecteert u **opslaan**. (Selecteer **annuleren** om wijzigingen te verwijderen).

1. Als u wilt testen van uw omgeving, maken en uitvoeren op een nieuwe notebook, waarna een codecel maken met de instructies die afhankelijk van een pakket in de omgeving zijn, zoals het gebruik van een Python `import` instructie. Als de instructie is geslaagd, is klikt u vervolgens het benodigde pakket geïnstalleerd in de omgeving.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Beheren en configureren van projecten in Azure-notitieblokken](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Zelfstudie: een run maken een Jupyter-notebook te doen, lineaire regressie](tutorial-create-run-jupyter-notebook.md)
