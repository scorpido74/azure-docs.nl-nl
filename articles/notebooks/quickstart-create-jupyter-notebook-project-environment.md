---
title: Een Voorbeeldproject voor Azure Notebooks maken met een aangepaste omgeving
description: Maak een nieuw project in Azure Notebooks Preview dat is geconfigureerd met een specifieke set geïnstalleerde pakketten en opstartscripts.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 6388cb7997cac5bef25975043a13c4e080f288d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78196838"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Snelstart: een project maken met een aangepaste omgeving in azure-notitieblokkenvoorbeeld

Een project in Azure Notebooks is een verzameling bestanden, zoals notitieblokken, gegevensbestanden, documentatie, afbeeldingen enzovoort, samen met een omgeving die kan worden geconfigureerd met specifieke installatieopdrachten. Door de omgeving met het project te definiëren, heeft iedereen die het project kloont in zijn eigen Azure Notebooks-account alle informatie die ze nodig hebben om de benodigde omgeving opnieuw te maken.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>Een project maken

1. Ga naar [Azure-notitieblokken](https://notebooks.azure.com) en meld u aan. (Zie [Snelstart - Aanmelden bij Azure-notitieblokken voor](quickstart-sign-in-azure-notebooks.md)meer informatie).

1. Selecteer op uw openbare profielpagina **Mijn projecten** boven aan de pagina:

    ![Koppeling Mijn projecten boven aan het browservenster](media/quickstarts/my-projects-link.png)

1. Selecteer **op** de pagina Mijn projecten **+ Nieuw project** (sneltoets: n); de knop mag **+** alleen worden weergegeven alsof het browservenster smal is:

    ![Opdracht Nieuw project op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. Selecteer in de pop-up **Nieuw project maken** dat wordt weergegeven, voer de volgende details in of stel u in en selecteer Vervolgens **Maken:**

    - **Projectnaam**: Project met een aangepaste omgeving
    - **Project ID**: project-custom-omgeving
    - **Openbaar project**: (gewist)
    - **Een README.md maken**: (gewist)

1. Na een paar ogenblikken navigeert Azure Notebooks u naar het nieuwe project. Voeg een notitieblok toe aan het project door de vervolgkeuzelijst **+ Nieuw** (die kan worden weergegeven als alleen) **+** te selecteren en vervolgens **Notitieblok**te selecteren .

1. Geef het notitieblok een naam zoals *Custom environment.ipynb,* selecteer **Python 3.6** voor de taal en selecteer **Nieuw**.

## <a name="add-a-custom-setup-step"></a>Een aangepaste installatiestap toevoegen

1. Selecteer **projectinstellingen**op de projectpagina .

    ![Projectinstellingen, opdracht](media/quickstarts/project-settings-command.png)

1. Selecteer in de pop-up **Projectinstellingen** het tabblad **Omgeving** en selecteer onder **Stappen voor omgevingsinstellingen** **+ Toevoegen**:

    ![Opdracht om een nieuwe stap voor het instellen van omgevingen toe te voegen](media/quickstarts/environment-add-command.png)

1. Met de opdracht **+ Toevoegen** wordt een stap gemaakt die wordt gedefinieerd door een bewerking en een doelbestand dat is geselecteerd uit de bestanden in uw project. De volgende bewerkingen worden ondersteund:

   | Bewerking | Beschrijving |
   | --- | --- |
   | Requirements.txt | Python-projecten definiëren hun afhankelijkheden in een requirements.txt-bestand. Selecteer met deze optie het juiste bestand in de bestandslijst van het project en selecteer ook de Python-versie in de extra vervolgkeuzelijst die wordt weergegeven. Selecteer indien nodig **Annuleren** om terug te keren naar het project, upload of maak het bestand, ga vervolgens terug naar het tabblad **Projectinstellingen** > **omgeving** en maak een nieuwe stap. Met deze stap wordt het uitvoeren van een notitieblok in het project automatisch uitgevoerd`pip install -r <file>` |
   | Shell-script | Gebruik om een script van de bashshell aan te geven (meestal een bestand met de *.sh-extensie)* dat alle opdrachten bevat die u wilt uitvoeren om de omgeving te initialiseren. |
   | Milieu.yml | Een Python-project dat conda gebruikt voor het beheren van een omgeving, gebruikt een *bestand 'environments.yml* om afhankelijkheden te beschrijven. Selecteer met deze optie het juiste bestand in de bestandslijst van het project. |

   > [!WARNING]
   > Aangezien dit een voorbeeldservice in ontwikkeling is, is `Environment.yml` er momenteel een bekend probleem waarbij de instelling niet wordt toegepast op uw project zoals verwacht. Het project en de Jupyter-notitieblokken binnen laden momenteel het opgegeven omgevingsbestand niet.

1. Als u een installatiestap wilt verwijderen, selecteert u de **X** rechts van de stap.

1. Wanneer alle installatiestappen zijn ingesteld, selecteert u **Opslaan**. (Selecteer **Annuleren** om wijzigingen te verwijderen).

1. Als u uw omgeving wilt testen, maakt en voert u een nieuw notitieblok uit en maakt `import` u vervolgens een codecel met instructies die afhankelijk zijn van een pakket in de omgeving, zoals het gebruik van een Python-instructie. Als de instructie slaagt, is het benodigde pakket met succes geïnstalleerd in de omgeving.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Projecten beheren en configureren in Azure-notitieblokken](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Zelfstudie: maak een run een Jupyter-notitieblok om lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
