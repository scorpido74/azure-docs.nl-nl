---
title: Een Jupyter-notebook van GitHub klonen met Azure Notebooks Preview
description: Kloon snel een Jupyter-notebook van een GitHub-opslagplaats en voer deze uit in uw Azure Notebooks-account.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 35352e74a8db071a2f85bd71857d47f3af1953e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85833083"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Quickstart: Een notebook klonen in Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

In deze quickstart kopieert u een Jupyter-notebook die is opgeslagen in GitHub naar een Azure Notebooks-account. 

GitHub-opslagplaatsen bieden opslag en versiebeheer voor Jupyter-notebooks. Samenwerkers behouden lokale kopieën van de opslagplaatsen en voeren de notebooks vanuit die kopieën. Als u een Jupyter Notebook van GitHub naar uw Azure Notebooks-account kloont, wordt er een onafhankelijke kopie van de notebook gemaakt. Wijzigingen worden alleen in uw Azure Notebooks-account opgeslagen en hebben geen invloed op de oorspronkelijke GitHub-opslagplaats. 

Omdat de kloon van uw Azure Notebooks zich in de cloud bevindt, kunt u deze delen met samenwerkers die geen lokale kopieën hoeven te maken of Jupyter op hun computers hebben geïnstalleerd. U kunt een notebook ook gewoon als uitgangspunt voor een eigen project klonen of om gegevensbestand ophalen. 

## <a name="prerequisites"></a>Vereisten
Geen.

## <a name="clone-azure-cognitive-services-notebooks"></a>Azure Cognitive Services klonen

1. Ga naar [Azure Notebooks](https://notebooks.azure.com) en meld u aan. Zie [Quickstart: Aanmelden bij Azure Notebooks](quickstart-sign-in-azure-notebooks.md) voor meer informatie.

1. Selecteer vanuit uw openbare profielpagina de optie **Mijn projecten** bovenaan de pagina:

    ![Koppeling naar Mijn projecten bovenaan het browservenster](media/quickstarts/my-projects-link.png)

1. Selecteer op de pagina **Mijn projecten** de pijl-omhoog (toetsenbordsneltoets: U; de knop wordt weergegeven als **GitHub-opslagplaats uploaden** wanneer het browservenster breed genoeg is):

    ![De opdracht GitHub-opslagplaats uploaden op de pagina Mijn projecten](media/quickstarts/upload-github-repo-command.png)

1. Voer of stel in het venster **GitHub-opslagplaats uploaden** de volgende gegevens in en selecteer vervolgens **Importeren**:

   - **GitHub-opslagplaats**: Microsoft/cognitive-services-notebooks (deze naam kloont de Jupyter Notebooks voor Azure Cognitive Services op [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Recursief klonen**: (gewist)
   - **Projectnaam**: Cognitive Services-kloon
   - **Project-id**: cognitive-services-clone
   - **Openbaar**: (gewist)

     ![Pop-upvenster GitHub-opslagplaats uploaden om informatie over de opslagplaats te verzamelen](media/quickstarts/upload-github-repo-popup.png)

1. Een ogen blik geduld. Het proces wordt voltooid. Het klonen van een opslagplaats kan een paar minuten duren.

1. Zodra het klonen is voltooid, wordt het nieuwe project in Azure Notebooks weergegeven, waarin u de kopieën van alle bestanden kunt zien.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Een notebook delen

1. Als u uw kopie van het gekloonde project wilt delen, gebruikt u het besturingselement **Delen** of haalt u een koppeling op, haalt u de HTML- of Markdown-code op die de koppeling bevat of maakt u e-mailbericht met de koppeling:

    ![Opdracht voor het delen van een project](media/quickstarts/share-project-command.png)

1. Aangezien u de optie **Openbaar** tijdens het maken van het project hebt uitgeschakeld, is de kloon privé. Als u uw kopie openbaar wilt maken, selecteert u **Projectinstellingen**, stelt u in het pop-upvenster de optie **Openbaar project** in en selecteert u **Opslaan**.

1. Selecteer een notebook in het project om deze uit te voeren. Elke notebook in de Azure Cognitive Services-opslagplaats is bijvoorbeeld zijn eigen, ingesloten quickstart. In de onderstaande afbeelding ziet u het resultaat van het gebruik van de BingImageSearchAPI-notebook, na het toevoegen van een Cognitive Services API-abonnementssleutel en het wijzigen van de zoekterm puppies in bunnies:

    ![Een Jupyter Notebook uitvoeren die is gekloond in GitHub](media/quickstarts/clone-notebook-result.png)

1. Wanneer u klaar bent met het uitvoeren van de notebook, selecteert u **Bestand** > **Sluiten en stoppen** om de notebook en het bijbehorende browservenster te sluiten.

1. Als u een afzonderlijke notebook in het project wilt delen, klikt u met de rechtermuisknop op de notebook en selecteert u **Koppeling kopiëren** (toetsenbordsneltoets: y):

    ![Opdracht in het contextmenu om een koppeling naar een afzonderlijke notebook te kopiëren](media/quickstarts/copy-link-to-individual-notebook.png)

1. Als u andere bestanden dan notebooks wilt bewerken, klikt u met de rechtermuisknop op het bestand in het project en selecteert u **Bestand bewerken** (toetsenbordsneltoets: i). De standaardactie **Uitvoeren** (toetsenbordsneltoets: r) geeft alleen de inhoud van het bestand weer en biedt niet de mogelijkheid om deze te bewerken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: een Jupyter-notebook maken en uitvoeren om lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
