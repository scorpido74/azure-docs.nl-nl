---
title: Klonen van een Jupyter-notebook vanuit GitHub met Azure-laptops
description: Snel een Jupyter-notebook vanuit een GitHub-opslagplaats te klonen en voer dit in uw Azure-notitieblokken-account.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 500bd3f85409bb2f5e7b73be0478694695cc9c00
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277526"
---
# <a name="quickstart-clone-a-notebook"></a>Snelstartgids: Een notitieblok klonen

Veel gegevens wetenschappers en ontwikkel aars slaan hun notebooks op in [github-opslag](https://github.com)plaatsen, een gratis service die opslag en versie beheer biedt voor veel verschillende project typen. GitHub wordt vaak gebruikt als middel om samen te werken op Jupyter-notebooks die lokaal worden uitgevoerd. In dergelijke gevallen kan elke samenwerker onderhoudt een lokale kopie van de opslagplaats en de notebooks op dat exemplaar wordt uitgevoerd.

Klonen wordt een kopie gemaakt van een GitHub-notebook in uw Azure-notitieblokken-account in plaats daarvan. De kloon is onafhankelijk van de oorspronkelijke opslagplaats; wijzigingen worden opgeslagen in alleen de account van uw Azure-notitieblokken en hebben geen invloed op de oorspronkelijke. Omdat de kloon in de cloud, kunt u het project delen met andere medewerkers die niet moeten een lokale kopieën te maken of zelfs automatisch laten Jupyter op hun eigen computers geïnstalleerd. U kunt een laptop ook klonen gewoon als een beginpunt voor een project van uw eigen of te downloaden van gegevensbestanden.

## <a name="clone-azure-cognitive-services-notebooks"></a>Klonen van Azure Cognitive Services-laptops

1. Ga naar [Azure notebooks](https://notebooks.azure.com) en meld u aan. (Zie Quick Start ( [Aanmelden bij Azure notebooks](quickstart-sign-in-azure-notebooks.md)) voor meer informatie.

1. Selecteer op de pagina openbaar profiel **Mijn projecten** boven aan de pagina:

    ![Mijn projecten koppeling boven aan het browservenster](media/quickstarts/my-projects-link.png)

1. Selecteer op de pagina **Mijn projecten** de pijl-omhoog (sneltoets: U; de knop wordt weer gegeven als **Upload github opslag plaats** wanneer het browser venster breed genoeg is):

    ![GitHub opslag plaats-opdracht uploaden op de pagina Mijn projecten](media/quickstarts/upload-github-repo-command.png)

1. In de **github-opslag plaats** die wordt weer gegeven, voert u de volgende gegevens in of stelt u deze in en selecteert u **importeren**:

   - **Github-opslag plaats**: micro soft/cognitieve-Services-notebooks (deze naam klont de Jupyter-notebooks voor Azure Cognitive Services op [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Recursief klonen**: (uitgeschakeld)
   - **Project naam**: klonen Cognitive Services
   - **Project-id**: cognitieve-Services-klonen
   - **Openbaar**: (uitgeschakeld)

     ![GitHub opslag plaats-pop uploaden om opslag plaats informatie te verzamelen](media/quickstarts/upload-github-repo-popup.png)

1. Niet beschikbaar is even geduld terwijl het proces is voltooid; een opslagplaats klonen, kan een paar minuten duren.

1. Als het klonen is voltooid, gaat-laptops Azure u naar het nieuwe project waar u kunt zien de kopieën van alle bestanden.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Een notitieblok delen

1. Als u uw kopie van het gekloonde project wilt delen, gebruikt u het besturings element **delen** of verkrijgt u een koppeling, haalt u een HTML-code op die de koppeling bevat of maakt u een e-mail bericht met de koppeling:

    ![Opdracht voor project delen](media/quickstarts/share-project-command.png)

1. Omdat u de **open bare** optie hebt uitgeschakeld tijdens het klonen van het project, is de kloon persoonlijk. Als u uw kopie openbaar wilt maken, selecteert u **project instellingen**, stelt u de optie **openbaar project** in het pop-upvenster in en selecteert u vervolgens **Opslaan**.

1. Selecteer een notitieblok in het project uit te voeren. Elk notitieblok in de opslagplaats voor Azure Cognitive Services, is bijvoorbeeld een eigen onafhankelijke Quick Start. De onderstaande afbeelding toont het resultaat van het gebruik van de notebook BingImageSearchAPI na het toevoegen van een abonnementssleutel Cognitive Services-API en wijzigen van de zoekterm 'Puppy's ' in 'bunnies':

    ![Jupyter-notebook uitgevoerd gekloond vanuit GitHub](media/quickstarts/clone-notebook-result.png)

1. Wanneer u klaar bent met het uitvoeren van het notitie blok, selecteert u **bestand** > **sluiten en stoppen** om het notitie blok en het browser venster te sluiten.

1. Als u een afzonderlijke notebook in het project wilt delen, klikt u met de rechter muisknop op het notitie blok en selecteert u **koppeling kopiëren** (sneltoets: y):

    ![Opdracht in het contextmenu een koppeling naar een afzonderlijke notitieblok kopiëren](media/quickstarts/copy-link-to-individual-notebook.png)

1. Als u andere bestanden dan notebooks wilt bewerken, klikt u met de rechter muisknop op het bestand in het project en selecteert u **bestand bewerken** (sneltoets: i). Met de standaard actie **(sneltoets** : r) wordt alleen de inhoud van het bestand weer gegeven en kan niet worden bewerkt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: een Jupyter-notitie blok maken om een lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
