---
title: Een lokale Jupyter-notebook migreren naar Azure Notebooks preview
description: U kunt snel een Jupyter-notebook overdragen naar Azure Notebooks preview van uw lokale computer of een web-URL en deze vervolgens delen voor samen werking.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: baf05d7adb1340d712ff0fc87436d5bbac51bc8f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77064322"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Snelstartgids: een lokaal Jupyter-notitie blok migreren in Azure Notebooks preview

In deze Snelstartgids migreert u een Jupyter-notebook van uw lokale computer of een andere toegankelijke bestands-URL naar Azure Notebooks. 

Jupyter-notebooks op uw eigen computer zijn alleen toegankelijk voor u. U kunt uw bestanden delen, maar ontvangers hebben vervolgens hun eigen lokale kopieën van het notitie blok, en het is moeilijk om hun wijzigingen op te nemen. Zelfs als u notitie blokken opslaat in een gedeelde online opslag plaats zoals GitHub, moet elke samen werker een lokale Jupyter-installatie configureren, zoals u dat wilt.

Door uw lokale of opslagplaats notitieblokken te migreren naar Azure Notebooks, kunt u ze direct delen met uw mede werkers, die alleen een browser nodig hebben om uw notitie blokken weer te geven en uit te voeren. Als de gebruiker zich aanmeldt bij Azure Notebooks, kunnen ze ook wijzigingen aanbrengen.

## <a name="prerequisites"></a>Vereisten

- Een [Jupyter-notebook](https://jupyter-notebook.readthedocs.io) op uw lokale computer of op een andere toegankelijke BESTANDS-URL. 

## <a name="create-a-project-on-azure-notebooks"></a>Een project maken op Azure Notebooks

In deze Quick Start wordt gedemonstreerd hoe u een notebook migreert van uw lokale computer of een andere toegankelijke bestands-URL. Voor het migreren van notitie blokken vanuit een GitHub-opslag plaats raadpleegt u [Quick Start: een notitie blok klonen](quickstart-clone-jupyter-notebook.md).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Ga naar [Azure notebooks](https://notebooks.azure.com) en meld u aan. (Zie Quick Start ( [Aanmelden bij Azure notebooks](quickstart-sign-in-azure-notebooks.md)) voor meer informatie.

1. Selecteer op de pagina openbaar profiel **Mijn projecten** boven aan de pagina:

    ![De koppeling Mijn projecten bovenaan in het browser venster](media/quickstarts/my-projects-link.png)

1. Op de pagina **Mijn projecten** selecteert u **Nieuw project** (sneltoets: n). De knop kan alleen worden weer **+** gegeven alsof het browser venster smal is:

    ![De opdracht Nieuw project op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. Voer in het pop-upvenster **Nieuw project maken** dat wordt weer gegeven de juiste waarden in voor het notitie blok dat u wilt migreren in de velden **project naam** en **project-id** , Wis de opties voor het **open bare project** en **Maak een README.MD**en selecteer vervolgens **maken**.

## <a name="upload-the-local-notebook"></a>Het lokale notitie blok uploaden

1. Selecteer op de pagina project de optie **uploaden** (die kan worden weer gegeven als een pijl-omhoog als uw browser venster klein is) en selecteer vervolgens 1. Selecteer in het pop-upvenster dat wordt weer gegeven **van computer** als uw notitie blok zich op het lokale bestands systeem bevindt, of **van URL** als uw notitie blok online is:

    ![Opdracht voor het uploaden van een notitie blok vanuit een URL of de lokale computer](media/quickstarts/upload-from-computer-url-command.png)

   Als uw notebook zich in een GitHub-opslag plaats bevindt, volgt u de stappen op [Quick Start: een notebook klonen](quickstart-clone-jupyter-notebook.md) .

   - Als u **vanaf computer**gebruikt, sleept u uw *. ipynb* -bestanden naar de pop-up of selecteert u **bestanden kiezen**, bladert u naar en selecteert u de bestanden die u wilt importeren. Selecteer vervolgens **Uploaden**. De geüploade bestanden krijgen dezelfde naam als de lokale bestanden. Het is niet nodig om de inhoud van een *ipynb_checkpoints* -map te uploaden.

     ![Van de computer uploaden](media/quickstarts/upload-from-computer-popup.png)

   - Als u **vanaf URL**gebruikt, voert u het bron adres in het veld **bestands-URL** in en de bestands naam die u wilt toewijzen aan het notitie blok in het project in het veld **Bestands naam** . Selecteer vervolgens **Uploaden**. Als u meerdere bestanden met afzonderlijke Url's hebt, gebruikt u de opdracht **bestand toevoegen** om de eerste URL die u hebt ingevoerd te controleren, waarna de pop-up nieuwe velden bevat voor een ander bestand.

     ![Uploaden via URL-pop-up](media/quickstarts/upload-from-url-popup.png)

1. Open de zojuist geüploade notitie blok en voer deze uit om de inhoud en bewerking te controleren. Wanneer u klaar bent, selecteert u **bestand** > **stoppen en sluiten** om het notitie blok te sluiten.

1. Als u een koppeling naar uw geüploade notitie blok wilt delen, klikt u met de rechter muisknop op het bestand in het project en selecteert u **koppeling kopiëren** (sneltoets: y) en plakt u die koppeling in het juiste bericht. U kunt het project ook als geheel delen met behulp van het besturings element **delen** op de pagina project.

1. Als u andere bestanden dan notebooks wilt bewerken, klikt u met de rechter muisknop op het bestand in het project en selecteert u **bestand bewerken** (sneltoets: i). Met de standaard actie **(sneltoets** : r) wordt alleen de inhoud van het bestand weer gegeven en kan niet worden bewerkt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: een Jupyter-notebook maken en uitvoeren om lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
