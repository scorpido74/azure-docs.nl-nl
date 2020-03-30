---
title: Een lokaal Jupyter-notitieblok migreren naar Azure-notitieblok
description: Breng snel een Jupyter-notitieblok over naar Azure Notebooks Preview vanaf uw lokale computer of een web-URL en deel het vervolgens voor samenwerking.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: baf05d7adb1340d712ff0fc87436d5bbac51bc8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064322"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Snelstart: een lokaal Jupyter-notitieblok migreren in Azure-notitieblokkenproefversie

In deze quickstart migreert u een Jupyter-notitieblok van uw lokale computer of een andere toegankelijke bestands-URL naar Azure-notitieblokken. 

Jupyter notebooks op uw eigen computer zijn alleen voor u toegankelijk. U uw bestanden delen, maar ontvangers hebben dan hun eigen lokale kopieën van het notitieblok en het is moeilijk om hun wijzigingen op te nemen. Zelfs als u notitieblokken opslaat in een gedeelde onlineopslagplaats zoals GitHub, moet elke medewerker een lokale Jupyter-installatie hebben geconfigureerd zoals de uwe.

Door uw lokale of op opslagplaatsen gebaseerde notitieblokken te migreren naar Azure-notitieblokken, u ze direct delen met uw bijdragers, die alleen een browser nodig hebben om uw notitieblokken te bekijken en uit te voeren. Als ze zich aanmelden bij Azure Notebooks, kunnen ze ook wijzigingen aanbrengen.

## <a name="prerequisites"></a>Vereisten

- Een [Jupyter-notitieblok](https://jupyter-notebook.readthedocs.io) op uw lokale computer of op een andere toegankelijke bestands-URL. 

## <a name="create-a-project-on-azure-notebooks"></a>Een project maken op Azure-notitieblokken

Deze quickstart toont het migreren van een notitieblok vanaf uw lokale computer of een andere toegankelijke bestands-URL. Zie [Snelstart: Een notitieblok klonen](quickstart-clone-jupyter-notebook.md)als u notitieblokken wilt migreren vanuit een GitHub-opslagplaats.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Ga naar [Azure-notitieblokken](https://notebooks.azure.com) en meld u aan. (Zie [Snelstart - Aanmelden bij Azure-notitieblokken voor](quickstart-sign-in-azure-notebooks.md)meer informatie).

1. Selecteer op uw openbare profielpagina **Mijn projecten** boven aan de pagina:

    ![Koppeling Mijn projecten boven aan het browservenster](media/quickstarts/my-projects-link.png)

1. Selecteer **op** de pagina Mijn projecten de optie **Nieuw project** (sneltoets: n). De knop mag **+** alleen worden weergegeven alsof het browservenster smal is:

    ![Opdracht Nieuw project op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. Voer in de pop-up **Nieuw project maken** dat wordt weergegeven de juiste waarden in voor het notitieblok dat u migreert in de velden **Projectnaam** en **Project ID,** de opties voor **Openbaar project** wissen en een **README.md maken**en selecteer Vervolgens **Maken**.

## <a name="upload-the-local-notebook"></a>Het lokale notitieblok uploaden

1. Selecteer op de projectpagina **Uploaden** (die alleen als pijl-omhoog kan worden weergegeven als uw browservenster klein is) en selecteer 1. Selecteer in de pop-up die wordt weergegeven **van de computer** als uw notitieblok zich op uw lokale bestandssysteem bevindt of van **URL** als uw notitieblok zich online bevindt:

    ![Opdracht om een notitieblok te uploaden vanaf een URL of de lokale computer](media/quickstarts/upload-from-computer-url-command.png)

   Als uw notitieblok zich in een GitHub-opslagplaats bevindt, voert u de stappen uit op [Quickstart: Kloon in](quickstart-clone-jupyter-notebook.md) plaats daarvan een notitieblok.

   - Als u **Van computer**gebruikt, sleept u uw *.ipynb-bestanden* en zet u deze neer in de pop-up of selecteert u **Bestanden kiezen**en bladert u naar de bestanden die u wilt importeren. Selecteer vervolgens **Uploaden**. De geüploade bestanden krijgen dezelfde naam als de lokale bestanden. U hoeft de inhoud van *ipynb_checkpoints* mappen niet te uploaden.

     ![Uploaden vanaf computerpop-up](media/quickstarts/upload-from-computer-popup.png)

   - Als u **Van URL**gebruikt, voert u het bronadres in het veld **URL van** bestand en de bestandsnaam in die aan het notitieblok in uw project moet worden toegewezen in het veld **Bestandsnaam.** Selecteer vervolgens **Uploaden**. Als u meerdere bestanden met afzonderlijke URL's hebt, gebruikt u de opdracht **Bestand toevoegen** om de eerste URL te controleren die u hebt ingevoerd, waarna de pop-up nieuwe velden voor een ander bestand bevat.

     ![Uploaden vanuit URL pop-up](media/quickstarts/upload-from-url-popup.png)

1. Open en voer uw nieuw geüploade notitieblok uit om de inhoud en werking ervan te verifiëren. Wanneer u klaar bent, selecteert u **Bestand** > Halt en sluit u het**notitieblok.**

1. Als u een koppeling naar uw geüploade notitieblok wilt delen, klikt u met de rechtermuisknop op het bestand in het project en selecteert u **Koppeling kopiëren** (sneltoets: y) en plakt u die koppeling vervolgens in het juiste bericht. U het project als geheel delen met het besturingselement **Delen** op de projectpagina.

1. Als u andere bestanden dan notitieblokken wilt bewerken, klikt u met de rechtermuisknop op het bestand in het project en selecteert u **Bestand bewerken** (sneltoets: i). De **standaardactie, Uitvoeren** (sneltoets: r), toont alleen de bestandsinhoud en staat bewerking niet toe.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Een Jupyter-notitieblok maken en uitvoeren om lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
