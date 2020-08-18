---
title: 'Zelfstudie: Experimenteren in Jupyter-notebooks (Python)'
titleSuffix: Machine Learning - Azure
description: In deze zelfstudie gaat u aan de slag met de Python SDK voor Azure Machine Learning die wordt uitgevoerd in Jupyter-notebooks.  In deel 1 maakt u een werkruimte waarin u experimenten en ML-modellen beheert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: ff23a42d9b96b8411d8b2f82ab8303e2a8a69953
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852715"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Zelfstudie: Ga aan de slag met het maken van uw eerste ML-experiment met de Python SDK
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze zelfstudie doorloopt u de end-to-end stappen om aan de slag te gaan met de Python SDK voor Azure Machine Learning die wordt uitgevoerd in Jupyter-notebooks. Deze zelfstudie is **deel 1 van een 2-delige reeks zelfstudies** en dekt het voorbereiden en configureren van een Python-omgeving, evenals het maken van een werkruimte om uw experimenten en machine learning-modellen te beheren. [**Deel 2**](tutorial-1st-experiment-sdk-train.md) bouwt hierop voort om meerdere machine learning-modellen te trainen en het modelbeheerproces te introduceren met behulp van zowel Azure Machine Learning Studio als de SDK.

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Maak een [Azure Machine Learning-werkruimte](concept-workspace.md) om in de volgende zelfstudie te gebruiken.
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

In deze zelfstudie wordt de cloudnotebook-server in uw werkruimte gebruikt voor een installatieloze en vooraf geconfigureerde ervaring. Gebruik [uw eigen omgeving](how-to-configure-environment.md#local) als u liever controle over uw omgeving, pakketten en afhankelijkheden hebt.

 Volg deze video of gebruik de gedetailleerde stappen hieronder om de zelfstudie te klonen en uit te voeren vanuit uw werkruimte. 

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
    
1. Selecteer het bestand **tutorial-1st-experiment-sdk-train.ipynb** in uw map **tutorials/create-first-ml-experiment**.

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

In deze zelfstudie hebt u de volgende taken voltooid:

* Een Azure Machine Learning-werkruimte gemaakt.
* Een cloudnotebook-server in uw werkruimte gemaakt en geconfigureerd.

In **Deel 2** van de zelfstudie voert u de code in `tutorial-1st-experiment-sdk-train.ipynb` uit om een machine learning-model te trainen. 

> [!div class="nextstepaction"]
> [Zelfstudie: Uw eerste model trainen](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Als u niet van plan bent om Deel 2 van deze zelfstudie of andere zelfstudies te volgen, moet u [de VM van de cloudnotebook-server stoppen](tutorial-1st-experiment-sdk-train.md#clean-up-resources) wanneer u deze niet gebruikt, om de kosten te verlagen.


