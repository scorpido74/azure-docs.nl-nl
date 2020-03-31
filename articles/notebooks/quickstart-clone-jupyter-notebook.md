---
title: Een Jupyter-notitieblok klonen vanuit GitHub met Azure-notitieblok
description: Kloon snel een Jupyter-notitieblok uit een GitHub-opslagplaats en voer het uit in uw Azure Notebooks-account.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d0f3a12ff04e115074c3821c5e29652484710bca
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064594"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Snelstart: een notitieblok klonen in de preview-proefversie van Azure Notebooks

In deze quickstart kopieert u een Jupyter-notitieblok dat is opgeslagen in GitHub naar een Azure Notebooks-account. 

GitHub-repositories bieden opslag- en versiebeheer voor Jupyter-laptops. Bijdragers onderhouden lokale kopieën van de opslagplaatsen en voeren de notitieblokken uit die kopieën uit. Als u een Jupyter-notitieblok van GitHub in uw Azure Notebooks-account vastmaakt, wordt een onafhankelijke kopie van het notitieblok gemaakt. Wijzigingen worden alleen opgeslagen in uw Azure Notebooks-account en hebben geen invloed op de oorspronkelijke GitHub-opslagplaats. 

Omdat uw Azure Notebooks-kloon zich in de cloud bevindt, u deze delen met bijdragers, die geen lokale kopieën hoeven te maken of Jupyter op hun computers hoeven te laten installeren. U een notitieblok ook gewoon klonen als uitgangspunt voor een eigen project, of om gegevensbestanden te verkrijgen. 

## <a name="prerequisites"></a>Vereisten
Geen.

## <a name="clone-azure-cognitive-services-notebooks"></a>Azure Cognitive Services-notitieblokken klonen

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Ga naar [Azure-notitieblokken](https://notebooks.azure.com) en meld u aan. Zie [Snelstart - Aanmelden bij Azure-notitieblokken voor](quickstart-sign-in-azure-notebooks.md)meer informatie.

1. Selecteer op uw openbare profielpagina **Mijn projecten** boven aan de pagina:

    ![Koppeling Mijn projecten boven aan het browservenster](media/quickstarts/my-projects-link.png)

1. Selecteer **op** de pagina Mijn projecten de pijl-omhoog -knop (sneltoets: U; de knop wordt weergegeven als **GitHub Repo uploaden** wanneer het browservenster breed genoeg is):

    ![GitHub Repo-opdracht uploaden op de pagina Mijn projecten](media/quickstarts/upload-github-repo-command.png)

1. Selecteer **Upload GitHub Repository** vervolgens **Importeren:**

   - **GitHub repository**: Microsoft/cognitive-services-notebooks (met deze naam worden de Jupyter-notitieblokken voor Azure Cognitive Services bij [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Kloon recursief**: (gewist)
   - **Projectnaam**: Cognitive Services Clone
   - **Project ID**: cognitive-services-clone
   - **Publiek**: (gewist)

     ![GitHub Repo pop-up uploaden om repo-informatie te verzamelen](media/quickstarts/upload-github-repo-popup.png)

1. Wees geduldig terwijl het proces is voltooid; het klonen van een opslagplaats kan een paar minuten duren.

1. Zodra het klonen is voltooid, neemt Azure Notebooks u mee naar het nieuwe project waar u de kopieën van alle bestanden zien.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Een notitieblok delen

1. Als u uw kopie van het gekloonde project wilt delen, gebruikt u het besturingselement **Delen** of een koppeling verkrijgen, ontvangt u HTML- of Markdown-code die de koppeling bevat of maakt u een e-mailbericht met de koppeling:

    ![Opdracht Projectdelen](media/quickstarts/share-project-command.png)

1. Omdat je de openbare optie **hebt** goedgekeurd bij het klonen van het project, is de kloon privé. Als u uw exemplaar openbaar wilt maken, selecteert u **Projectinstellingen,** stelt u de optie **Openbaar project** in de pop-up in en selecteert u **Opslaan**.

1. Selecteer een notitieblok in het project om het uit te voeren. Elk notitieblok in de Azure Cognitive Services-repository is bijvoorbeeld zijn eigen, op zichzelf staande Quickstart. De afbeelding hieronder toont het resultaat van het gebruik van de BingImageSearchAPI notebook, na het toevoegen van een Cognitive Services API-abonnementssleutel en het wijzigen van de zoekterm "puppies" in "konijnen":

    ![Jupyter-laptop dat is gekloond van GitHub](media/quickstarts/clone-notebook-result.png)

1. Wanneer u klaar bent met het uitvoeren van het notitieblok, selecteert u **Bestand** > **sluiten en stoppen** om het notitieblok en het browservenster te sluiten.

1. Als u een afzonderlijk notitieblok in het project wilt delen, klikt u met de rechtermuisknop op het notitieblok en selecteert **u Koppeling kopiëren** (sneltoets: y):

    ![Opdracht Contextmenu om een koppeling naar een afzonderlijk notitieblok te kopiëren](media/quickstarts/copy-link-to-individual-notebook.png)

1. Als u andere bestanden dan notitieblokken wilt bewerken, klikt u met de rechtermuisknop op het bestand in het project en selecteert u **Bestand bewerken** (sneltoets: i). De **standaardactie, Uitvoeren** (sneltoets: r), toont alleen de bestandsinhoud en staat bewerking niet toe.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Een Jupyter-notitieblok maken en uitvoeren om lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
