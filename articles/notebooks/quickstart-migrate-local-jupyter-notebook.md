---
title: Een lokaal Jupyter-notitieblok migreren naar Azure-laptops
description: Snel een Jupyter-notebook overbrengen naar Azure-laptops van uw lokale computer of een web-URL, vervolgens te delen om samen te werken.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 3b7d5aab05923fb4356e0d45aaf21a77076a870a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277472"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Snelstartgids: Een lokaal Jupyter-notitieblok migreren

Jupyter-notitieblokken die u lokaal op uw eigen computer maakt, zijn alleen toegankelijk is voor u. U kunt uw bestanden via een groot aantal betekent, delen, maar vervolgens ontvangers hebben hun eigen lokale kopie van de notebook en is het moeilijk voor u om op te nemen van eventuele wijzigingen die ze ervoor kunnen zorgen. U kunt ook laptops opslaan in een gedeelde online opslagplaats, zoals GitHub, maar in dat geval nog steeds vereist dat elke samenwerker hun eigen lokale Jupyter-installatie met dezelfde configuratie als uw heeft.

Door uw lokale of op basis van een opslagplaats notitieblokken migreert naar Azure-laptops, opslaan u deze in de cloud van waaruit u direct met uw deelnemers delen kunt. Deze mede werkers hebben alleen een browser nodig om uw notitie blok te bekijken en uit te voeren, en als ze [zich aanmelden](quickstart-sign-in-azure-notebooks.md) bij Azure notebooks, kunnen ze ook wijzigingen aanbrengen.

Deze snelstartgids ziet u het proces van de migratie van een laptop van uw lokale computer of een andere toegankelijk bestands-URL. Voor het migreren van notitie blokken vanuit een GitHub-opslag plaats raadpleegt u [Quick Start: een notitie blok klonen](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Een project maken op Azure-Notebooks

1. Ga naar [Azure notebooks](https://notebooks.azure.com) en meld u aan. (Zie Quick Start ( [Aanmelden bij Azure notebooks](quickstart-sign-in-azure-notebooks.md)) voor meer informatie.

1. Selecteer op de pagina openbaar profiel **Mijn projecten** boven aan de pagina:

    ![Mijn projecten koppeling boven aan het browservenster](media/quickstarts/my-projects-link.png)

1. Selecteer op de pagina **Mijn projecten** **+ Nieuw project** (sneltoets: n); de knop kan alleen worden weer gegeven als **+** als het browser venster smal is:

    ![Nieuw Project-opdracht op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. Voer in het pop-upvenster **Nieuw project maken** dat wordt weer gegeven de juiste waarden in voor het notitie blok dat u wilt migreren in de velden **project naam** en **project-id** , Wis de opties voor het **open bare project** en **Maak een README.MD**en selecteer vervolgens **maken**.

## <a name="upload-the-local-notebook"></a>De lokale laptop uploaden

1. Selecteer op de pagina project de optie **uploaden** (die kan worden weer gegeven als een pijl-omhoog als uw browser venster klein is) en selecteer vervolgens 1. Selecteer in het pop-upvenster dat wordt weer gegeven **van computer** als uw notitie blok zich op het lokale bestands systeem bevindt, of **van URL** als uw notitie blok online is:

    ![Opdracht voor het uploaden van een laptop van een URL of de lokale computer](media/quickstarts/upload-from-computer-url-command.png)

   (Als uw notitie blok zich in een GitHub-opslag plaats bevindt, volgt u de stappen op [Quick Start: een notitie blok klonen](quickstart-clone-jupyter-notebook.md) .)

   - Als u **vanaf computer**gebruikt, sleept u uw *. ipynb* -bestanden naar de pop-up of selecteert u **bestanden kiezen**, bladert u naar en selecteert u de bestanden die u wilt importeren. Selecteer vervolgens **uploaden**. De geüploade bestanden krijgt dezelfde naam als de lokale bestanden. (Het is niet nodig om de inhoud van een *ipynb_checkpoints* -map te uploaden.)

     ![Uploaden vanaf computer pop-upvenster](media/quickstarts/upload-from-computer-popup.png)

   - Als u **vanaf URL**gebruikt, voert u het bron adres in het veld **bestands-URL** in en de bestands naam die u wilt toewijzen aan het notitie blok in het project in het veld **Bestands naam** . Selecteer vervolgens **uploaden**. Als u meerdere bestanden met afzonderlijke Url's hebt, gebruikt u de **+ bestand toevoegen** opdracht om de eerste URL te controleren die u hebt ingevoerd, waarna de pop-up nieuwe velden voor een ander bestand bevat.

     ![Uploaden vanaf URL pop-upvenster](media/quickstarts/upload-from-url-popup.png)

1. Open en voer uw laptop onlangs geüpload om te controleren of de inhoud en de bewerking. Wanneer u klaar bent, selecteert u **bestand** > **stoppen en sluiten** om het notitie blok te sluiten.

1. Als u een koppeling naar uw geüploade notitie blok wilt delen, klikt u met de rechter muisknop op het bestand in het project en selecteert u **koppeling kopiëren** (sneltoets: y) en plakt u die koppeling in het juiste bericht. U kunt het project ook als geheel delen met behulp van het besturings element **delen** op de pagina project.

1. Als u andere bestanden dan notebooks wilt bewerken, klikt u met de rechter muisknop op het bestand in het project en selecteert u **bestand bewerken** (sneltoets: i). Met de standaard actie **(sneltoets** : r) wordt alleen de inhoud van het bestand weer gegeven en kan niet worden bewerkt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: een Jupyter-notitie blok maken om een lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
