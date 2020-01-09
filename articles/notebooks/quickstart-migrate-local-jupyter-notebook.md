---
title: Een lokale Jupyter-notebook migreren naar Azure Notebooks preview
description: U kunt snel een Jupyter-notebook overdragen naar Azure Notebooks preview van uw lokale computer of een web-URL en deze vervolgens delen voor samen werking.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 9e5270c59a64f9510f9108bbe4d00b922178888c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647047"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Snelstartgids: een lokaal Jupyter-notitie blok migreren in Azure Notebooks preview

Jupyter-notitieblokken die u lokaal op uw eigen computer maakt, zijn alleen toegankelijk is voor u. U kunt uw bestanden via een groot aantal betekent, delen, maar vervolgens ontvangers hebben hun eigen lokale kopie van de notebook en is het moeilijk voor u om op te nemen van eventuele wijzigingen die ze ervoor kunnen zorgen. U kunt ook laptops opslaan in een gedeelde online opslagplaats, zoals GitHub, maar in dat geval nog steeds vereist dat elke samenwerker hun eigen lokale Jupyter-installatie met dezelfde configuratie als uw heeft.

Door uw lokale of op basis van een opslagplaats notitieblokken migreert naar Azure-laptops, opslaan u deze in de cloud van waaruit u direct met uw deelnemers delen kunt. De deelnemers moeten alleen een browser om te bekijken en uw laptop, uitvoeren en als ze [aanmelden](quickstart-sign-in-azure-notebooks.md) tot Azure-notitieblokken kunnen ze ook wijzigingen aanbrengen.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Deze snelstartgids ziet u het proces van de migratie van een laptop van uw lokale computer of een andere toegankelijk bestands-URL. Als u wilt migreren notitieblokken vanuit een GitHub-opslagplaats, Zie [Quick Start: een notitieblok klonen](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Een project maken op Azure-Notebooks

1. Ga naar [Azure notitieblokken](https://notebooks.azure.com) en meld u aan. (Zie voor meer informatie, [-Snelstart: aanmelden bij Azure-notitieblokken](quickstart-sign-in-azure-notebooks.md)).

1. Selecteer in de pagina van uw openbare profiel **Mijn projecten** aan de bovenkant van de pagina:

    ![Mijn projecten koppeling boven aan het browservenster](media/quickstarts/my-projects-link.png)

1. Op de **Mijn projecten** weergeeft, schakelt **+ nieuw Project** (sneltoets: n); de knop wordt mogelijk weergegeven alleen als **+** als het browservenster smal is:

    ![Nieuw Project-opdracht op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. In de **nieuw Project maken** pop-upvenster dat wordt weergegeven, geef de juiste waarden voor de notebook waarnaar u migreert in de **projectnaam** en **Project-ID** velden, schakel de Opties voor **openbare project** en **maken van een README.md**en selecteer vervolgens **maken**.

## <a name="upload-the-local-notebook"></a>De lokale laptop uploaden

1. Selecteer op de projectpagina **uploaden** (die kunnen worden weergegeven als een omhoog pijl alleen als het browservenster klein is), selecteert u 1. Selecteer in het pop-upvenster dat wordt weergegeven, **van computer** als uw laptop bevindt zich op uw lokale bestandssysteem of **van URL** als uw laptop online zich bevindt:

    ![Opdracht voor het uploaden van een laptop van een URL of de lokale computer](media/quickstarts/upload-from-computer-url-command.png)

   (Nogmaals, als uw laptop zich in een GitHub-opslagplaats, volg de stappen op [Snelstartgids: een notitieblok klonen](quickstart-clone-jupyter-notebook.md) in plaats daarvan.)

   - Als u **van Computer**, slepen en neerzetten uw *.ipynb* bestanden in het pop-upvenster of selecteer **bestanden kiezen**, en blader vervolgens naar en selecteer de bestanden die u wilt importeren. Selecteer vervolgens **uploaden**. De geüploade bestanden krijgt dezelfde naam als de lokale bestanden. (U hoeft niet te uploaden van de inhoud van een *.ipynb_checkpoints* mappen.)

     ![Uploaden vanaf computer pop-upvenster](media/quickstarts/upload-from-computer-popup.png)

   - Als u **van URL**, voert u de bronadres in de **bestands-URL** veld en de bestandsnaam om toe te wijzen aan de notebook in uw project in de **bestandsnaam** veld. Selecteer vervolgens **uploaden**. Als u meerdere bestanden met afzonderlijke URL's hebt, gebruikt u de **+ toevoegen bestand** opdracht om te controleren van de eerste URL die u hebt ingevoerd, waarna het pop-upvenster biedt nieuwe velden voor een ander bestand.

     ![Uploaden vanaf URL pop-upvenster](media/quickstarts/upload-from-url-popup.png)

1. Open en voer uw laptop onlangs geüpload om te controleren of de inhoud en de bewerking. Wanneer u klaar bent, selecteert u **bestand** > **stilstand komt en close** te sluiten van de notebook.

1. Als u wilt delen een koppeling naar uw geüploade laptop, met de rechtermuisknop op het bestand in het project en selecteer **koppeling kopiëren** (sneltoets: y), plakt u die koppeling in het juiste bericht. U kunt ook kunt u het project als een hele met delen de **delen** besturingselement op de projectpagina.

1. Als u wilt bewerken bestanden dan laptops, met de rechtermuisknop op het bestand in het project en selecteer **bestand bewerken** (sneltoets: ik). De standaardactie **uitvoeren** (sneltoets: r), alleen inhoud van het bestand bevat en niet toestaan.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: een run maken een Jupyter-notebook te doen, lineaire regressie](tutorial-create-run-jupyter-notebook.md)
