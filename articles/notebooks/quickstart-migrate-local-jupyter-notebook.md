---
title: Een lokale Jupyter-notebook migreren naar Azure Notebooks Preview
description: Zet snel een Jupyter-notebook over naar Azure Notebooks Preview vanaf uw lokale computer of een web-URL en deel dit vervolgens voor samenwerking.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 9c961ad67260cee2809e0bba1e79f2c709183dea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85832114"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Quickstart: Een lokale Jupyter-notebook migreren naar Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

In deze quickstart migreert u een Jupyter-notebook van uw lokale computer of een andere toegankelijke bestands-URL naar Azure Notebooks. 

Jupyter-notebooks op uw eigen computer zijn alleen toegankelijk voor u. U kunt uw bestanden delen, maar ontvangers hebben vervolgens hun eigen lokale kopieën van de notebook en het is moeilijk om hun wijzigingen hierin op te nemen. Zelfs als u notebooks opslaat in een gedeelde online opslagplaats zoals GitHub, moet elke medewerker over een lokale Jupyter-installatie beschikken die is geconfigureerd als de uwe.

Door uw lokale of op een opslagplaats gebaseerde notebook te migreren naar Azure Notebooks, kunt u ze direct delen met uw medewerkers, die alleen een browser nodig hebben om uw notebooks weer te geven en uit te voeren. Als zij zich aanmelden bij Azure Notebooks, kunnen ze ook wijzigingen aanbrengen.

## <a name="prerequisites"></a>Vereisten

- Een [Jupyter-notebook](https://jupyter-notebook.readthedocs.io) op uw lokale computer of op een andere toegankelijke bestands-URL. 

## <a name="create-a-project-on-azure-notebooks"></a>Een project maken op Azure Notebooks

In deze quickstart ziet u hoe u een notebook migreert van uw lokale computer of een andere toegankelijke bestands-URL. Raadpleeg voor het migreren van notebooks vanuit een GitHub-opslagplaats [Quickstart: Een notebook klonen](quickstart-clone-jupyter-notebook.md).

1. Ga naar [Azure Notebooks](https://notebooks.azure.com) en meld u aan. (Zie [Quickstart: Aanmelden bij Azure Notebooks](quickstart-sign-in-azure-notebooks.md) voor meer informatie).

1. Selecteer vanuit uw openbare profielpagina de optie **Mijn projecten** bovenaan de pagina:

    ![Koppeling naar Mijn projecten bovenaan het browservenster](media/quickstarts/my-projects-link.png)

1. Selecteer op de pagina **Mijn projecten** de optie **Nieuw project** (toetsenbordsneltoets: n). Als het browservenster smal is, wordt de knop mogelijk alleen weergegeven als **+** :

    ![De opdracht Nieuw project op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. In de pop-up **Nieuw project maken** die wordt weergegeven, voert u de juiste waarden in voor de notebook dat u migreert in de velden **Projectnaam** en **Project-id**, wist u de opties voor **Openbaar project** en **Een README.md maken**, en selecteert u vervolgens **Maken**.

## <a name="upload-the-local-notebook"></a>De lokale notebook uploaden

1. Selecteer op de projectpagina **Uploaden** (die alleen als pijl-omhoog kan worden weergegeven als uw browservenster klein is), en selecteer vervolgens 1. In de pop-up die wordt weergegeven, selecteert u **Van computer** als uw notebook zich op uw lokale bestandssysteem bevindt, of **Van URL** als uw notebook online is:

    ![Opdracht voor het uploaden van een notebook vanuit een URL of de lokale computer](media/quickstarts/upload-from-computer-url-command.png)

   Vergeet niet dat, als uw notebook zich in een GitHub-opslagplaats bevindt, u in plaats daarvan de stappen volgt in [Quickstart: Een notebook klonen](quickstart-clone-jupyter-notebook.md).

   - Als u **Van computer** gebruikt, sleept u uw *.ipynb*-bestanden naar de pop-up of selecteert u **Bestanden kiezen**, en bladert u naar de bestanden die u wilt importeren en selecteert u deze. Selecteer vervolgens **Uploaden**. De geüploade bestanden krijgen dezelfde naam als de lokale bestanden. Het is niet nodig om de inhoud van *.ipynb_checkpoints*-mappen te uploaden.

     ![Uploaden vanaf een pop-up op de computer](media/quickstarts/upload-from-computer-popup.png)

   - Als u **Van URL**gebruikt, voert u het bronadres in het veld **Bestands-URL** in en de bestandsnaam die u wilt toewijzen aan de notebook in uw project in het veld **Bestandsnaam**. Selecteer vervolgens **Uploaden**. Als u meerdere bestanden met afzonderlijke URL's hebt, gebruikt u de opdracht **Bestand toevoegen** om de eerste URL die u hebt ingevoerd te controleren, waarna de pop-up nieuwe velden opgeeft voor een ander bestand.

     ![Uploaden via URL-pop-up](media/quickstarts/upload-from-url-popup.png)

1. Open de zojuist geüploade notebook en voer deze uit om de inhoud en werking te controleren. Wanneer u klaar bent, selecteert u **Bestand** > **Stoppen en sluiten** om de notebook te sluiten.

1. Als u een koppeling naar uw geüploade notebook wilt delen, klikt u met de rechtermuisknop op het bestand in het project en selecteert u **Koppeling kopiëren** (toetsenbordsneltoets: y). Plak vervolgens die koppeling in het juiste bericht. U kunt het project ook als geheel delen met behulp van het besturingselement **Delen** op de projectpagina.

1. Als u andere bestanden dan notebooks wilt bewerken, klikt u met de rechtermuisknop op het bestand in het project en selecteert u **Bestand bewerken** (toetsenbordsneltoets: i). De standaardactie **Uitvoeren** (toetsenbordsneltoets: r) geeft alleen de inhoud van het bestand weer en biedt niet de mogelijkheid om deze te bewerken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: een Jupyter-notebook maken en uitvoeren om lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
