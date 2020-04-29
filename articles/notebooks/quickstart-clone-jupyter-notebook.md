---
title: Een Jupyter-notebook klonen vanuit GitHub met Azure Notebooks preview
description: Kloon snel een Jupyter-notebook vanuit een GitHub-opslag plaats en voer deze uit in uw Azure Notebooks-account.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d0f3a12ff04e115074c3821c5e29652484710bca
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77064594"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Snelstartgids: een notitie blok klonen in Azure Notebooks preview

In deze Snelstartgids kopieert u een Jupyter-notebook die is opgeslagen in GitHub naar een Azure Notebooks-account. 

GitHub-opslag plaatsen bieden opslag-en versie beheer voor Jupyter-notebooks. Mede werkers behouden lokale kopieën van de opslag plaatsen en voeren de notitie blokken uit die kopieën. Als u een Jupyter-notebook van GitHub naar uw Azure Notebooks-account kloont, wordt er een onafhankelijke kopie van het notitie blok gemaakt. Wijzigingen worden alleen in uw Azure Notebooks-account opgeslagen en hebben geen invloed op de oorspronkelijke GitHub-opslag plaats. 

Omdat uw Azure Notebooks kloon zich in de Cloud bevindt, kunt u deze delen met samen werkers, die geen lokale kopieën hoeven te maken of Jupyter op hun computers hebben geïnstalleerd. U kunt een notitie blok ook gewoon als uitgangs punt voor een eigen project klonen of gegevens bestanden ophalen. 

## <a name="prerequisites"></a>Vereisten
Geen.

## <a name="clone-azure-cognitive-services-notebooks"></a>Azure Cognitive Services-notebooks klonen

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Ga naar [Azure notebooks](https://notebooks.azure.com) en meld u aan. Zie [Quick Start (aanmelden bij Azure notebooks](quickstart-sign-in-azure-notebooks.md)) voor meer informatie.

1. Selecteer op de pagina openbaar profiel **Mijn projecten** boven aan de pagina:

    ![De koppeling Mijn projecten bovenaan in het browser venster](media/quickstarts/my-projects-link.png)

1. Selecteer op de pagina **Mijn projecten** de pijl-omhoog (sneltoets: U; de knop wordt weer gegeven als **Upload github opslag plaats** wanneer het browser venster breed genoeg is):

    ![GitHub opslag plaats-opdracht uploaden op de pagina Mijn projecten](media/quickstarts/upload-github-repo-command.png)

1. In de **github-opslag plaats** die wordt weer gegeven, voert u de volgende gegevens in of stelt u deze in en selecteert u **importeren**:

   - **Github-opslag plaats**: micro soft/cognitieve-Services-notebooks (deze naam klont de Jupyter- [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)notebooks voor Azure Cognitive Services op).
   - **Recursief klonen**: (uitgeschakeld)
   - **Project naam**: klonen Cognitive Services
   - **Project-id**: cognitieve-Services-klonen
   - **Openbaar**: (uitgeschakeld)

     ![GitHub opslag plaats-pop uploaden om opslag plaats informatie te verzamelen](media/quickstarts/upload-github-repo-popup.png)

1. Een ogen blik geduld. het proces wordt voltooid. het klonen van een opslag plaats kan een paar minuten duren.

1. Wanneer het klonen is voltooid, gaat u Azure Notebooks naar het nieuwe project waarin u de kopieën van alle bestanden kunt zien.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Een notitie blok delen

1. Als u uw kopie van het gekloonde project wilt delen, gebruikt u het besturings element **delen** of verkrijgt u een koppeling, haalt u een HTML-code op die de koppeling bevat of maakt u een e-mail bericht met de koppeling:

    ![De opdracht project share](media/quickstarts/share-project-command.png)

1. Omdat u de **open bare** optie hebt uitgeschakeld tijdens het klonen van het project, is de kloon persoonlijk. Als u uw kopie openbaar wilt maken, selecteert u **project instellingen**, stelt u de optie **openbaar project** in het pop-upvenster in en selecteert u vervolgens **Opslaan**.

1. Selecteer een notitie blok in het project om het uit te voeren. Elk notebook in de Azure Cognitive Services-opslag plaats is bijvoorbeeld een eigen eigen Snelstartgids. In de onderstaande afbeelding ziet u het resultaat van het gebruik van de BingImageSearchAPI-notebook, na het toevoegen van een Cognitive Services API-abonnements sleutel en het wijzigen van de zoek term ' Puppies ' in ' Bunnies ':

    ![Uitvoeren van Jupyter-notebook gekloond vanuit GitHub](media/quickstarts/clone-notebook-result.png)

1. Wanneer u klaar bent met het uitvoeren van het notitie blok, selecteert u **bestand** > **sluiten en stoppen** om het notitie blok en het browser venster te sluiten.

1. Als u een afzonderlijke notebook in het project wilt delen, klikt u met de rechter muisknop op het notitie blok en selecteert u **koppeling kopiëren** (sneltoets: y):

    ![Opdracht context menu om een koppeling naar een afzonderlijke notebook te kopiëren](media/quickstarts/copy-link-to-individual-notebook.png)

1. Als u andere bestanden dan notebooks wilt bewerken, klikt u met de rechter muisknop op het bestand in het project en selecteert u **bestand bewerken** (sneltoets: i). Met de standaard actie **(sneltoets** : r) wordt alleen de inhoud van het bestand weer gegeven en kan niet worden bewerkt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: een Jupyter-notebook maken en uitvoeren om lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
