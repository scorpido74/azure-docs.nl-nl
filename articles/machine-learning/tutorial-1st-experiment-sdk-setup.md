---
title: 'Zelfstudie: Aan de slag in Jupyter Notebooks (Python)'
titleSuffix: Azure Machine Learning
description: Installatie voor Jupyter Notebook-zelfstudies.  Maak een Azure Machine Learning-werkruimte, kloon Jupyter-notebooks in de werkruimte en maak een rekenproces waarin u de notebooks uitvoert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: de52013628f5d02bedcf72a99e0fad25cabe5d8f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896893"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Zelfstudie: Aan de slag met de Azure Machine Learning in Jupyter Notebooks

In deze zelfstudie voert u de stappen uit om aan de slag te gaan met de Azure Machine Learning met behulp van Jupyter Notebooks op een [beheerd werkstation in de cloud (rekenproces)](concept-compute-instance.md). Deze zelfstudie is een voorloper van alle andere Jupyter Notebook-zelfstudies.

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Maak een [Azure Machine Learning-werkruimte](concept-workspace.md) om in andere Jupyter Notebook-zelfstudies te gebruiken.
> * Kloon het zelfstudie-notebook naar uw map in de werkruimte.
> * Maak een cloudgebaseerd rekenproces met de Python SDK voor Azure Machine Learning geïnstalleerd en vooraf geconfigureerd.

Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Een werkruimte maken

Een Azure Machine Learning-werkruimte is een basisblok in de cloud dat u gebruikt voor het experimenteren, trainen en implementeren van machine learning-modellen. De klasse bindt uw Azure-abonnement en resourcegroep aan een eenvoudig te verbruiken object in de service.

U maakt een werkruimte via de Azure-portal, een webconsole om uw Azure-resources te beheren.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Noteer uw **werkruimte** en **abonnement**. U hebt deze nodig om ervoor te zorgen dat u uw experiment op de juiste plek aanmaakt. 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>Notebook uitvoeren in uw werkruimte

Azure Machine Learning bevat een cloudnotebook-server in uw werkruimte voor een installatieloze en vooraf geconfigureerde ervaring. Gebruik [uw eigen omgeving](tutorial-1st-experiment-sdk-setup-local.md) als u liever controle over uw omgeving, pakketten en afhankelijkheden hebt.

 Volg deze video of gebruik de gedetailleerde stappen hieronder om de zelfstudienotebook te klonen en uit te voeren vanuit uw werkruimte.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a>Een notebook-map klonen

U doorloopt de volgende stappen voor het voorbereiden en uitvoeren van het experiment in Azure Machine Learning Studio, een geconsolideerde interface met hulpmiddelen voor machine learning waar gegevenswetenschappers, ongeacht hun vaardigheidsniveaus, scenario's kunnen uitvoeren.

1. Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com/).

1. Selecteer uw abonnement en de werkruimte die u heeft gemaakt.

1. Selecteer **Notebooks** aan de linkerkant.

1. Selecteer het tabblad **Voorbeelden** bovenaan.

1. Open de map **Python**.

1. Open de map met een versienummer erop.  Dit nummer vertegenwoordigt de huidige release voor de Python SDK.

1. Selecteer **...** rechts van de map **zelfstudies** en selecteer vervolgens **Klonen**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Map Zelfstudies klonen":::

1. Er wordt een lijst met mappen weergegeven, die elke gebruiker toont die toegang tot de werkruimte heeft.  Selecteer uw map om de map **Zelfstudies**  daar te klonen.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Het gekloonde notebook openen

1. Open de map **Zelfstudies** die zojuist naar uw sectie **Gebruikersbestanden** is gekloond.

    > [!IMPORTANT]
    > U kunt notebooks bekijken in de map **Voorbeelden**, maar u kunt een notebook niet uitvoeren vandaaruit.  Als u een notebook wilt uitvoeren, moet u de gekloonde versie van het notebook in de sectie **Gebruikersbestanden** openen.
    
1. Selecteer het bestand **tutorial-1st-experiment-sdk-train.ipynb** in de map **tutorials/image-classification-mnist-data**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Map Zelfstudies openen":::

1. Selecteer in de bovenste balk een rekenproces om te gebruiken voor het uitvoeren van het notebook. Deze VM’s zijn vooraf geconfigureerd met [alles wat u nodig hebt om Azure Machine Learning uit te voeren](concept-compute-instance.md#contents).

1. Als er geen VM’s zijn gevonden, selecteert u **+ Toevoegen** om de rekenproces-VM te maken. 

    1. Volg deze regels wanneer u een VM maakt:  
        + De naam is verplicht en mag niet leeg zijn.
        + De naam moet uniek zijn (en is hoofdlettergevoelig) voor alle bestaande rekenprocessen in de Azure-regio van de werkruimte/het rekenproces. U krijgt een waarschuwing als de naam die u kiest niet uniek is.
        + Geldige tekens zijn hoofdletters, kleine letters, cijfers (0 t/m 9) en een streepje (-).
        + De naam moet tussen de 3 en 24 tekens lang zijn.
        + De naam moet met een letter beginnen (niet met een cijfer of streepje).
        + Als er een streepje wordt gebruikt, moet dit worden gevolgd door ten minste één letter. Voorbeeld: Test-, test-0 en test-01 zijn ongeldig, maar test-a0 en test-0a zijn geldig.

    1.  Selecteer de Virtual Machine-grootte in de beschikbare keuzes. Voor de zelfstudies is de standaard-VM een goede keuze.

    1. Selecteer vervolgens **Maken**. Het kan ongeveer 5 minuten duren voordat uw VM is voorbereid.

1. Zodra de VM beschikbaar is, wordt deze in de bovenste werkbalk weergegeven.  U kunt het notebook nu uitvoeren door **Alles uitvoeren** op de werkbalk te gebruiken of door **Shift+Enter** in de codecellen van het notebook te gebruiken.

Als u aangepaste widgets hebt of liever Jupyter/JupyterLab gebruikt, selecteert u de vervolgkeuzelijst **Jupyter** helemaal rechts en selecteert u vervolgens **Jupyter** of **JupyterLab**. Het nieuwe browservenster wordt geopend.

## <a name="next-steps"></a>Volgende stappen

Nu u een ontwikkelomgeving hebt ingesteld, gaat u verder met het trainen van een model in een Jupyter Notebook:

> [!div class="nextstepaction"]
> [Zelfstudie: Modellen voor de classificatie van afbeeldingen trainen met MNIST-gegevens en scikit-learn](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Als u nu niet van plan bent om andere zelfstudies te volgen, moet u de VM van de cloudnotebook-server stoppen wanneer u deze niet gebruikt, om de kosten te verlagen:

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
