---
title: 'Zelfstudie: Maak uw eerste ML-experiment: Instellen'
titleSuffix: Azure Machine Learning
description: In deze zelfstudie reeks voltooit u de end-to-end-stappen om aan de slag te gaan met de Azure Machine Learning python SDK die wordt uitgevoerd in Jupyter-notebooks.  Deel één voor het maken van een Cloud notebook server-omgeving en het maken van een werk ruimte voor het beheren van uw experimenten en machine learning modellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 08/28/2019
ms.openlocfilehash: 9bc5b9688a8cd568b47fe2dad88d6d007ceca0c4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004059"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Zelfstudie: Aan de slag met het maken van uw eerste ML-experiment met de python-SDK

In deze zelf studie voltooit u de end-to-end-stappen om aan de slag te gaan met de Azure Machine Learning python SDK die wordt uitgevoerd in Jupyter-notebooks. Deze zelf studie maakt **deel uit van een tweedelige zelf studie reeks**, en omvat het instellen en configureren van python-omgevingen, en het maken van een werk ruimte voor het beheren van uw experimenten en machine learning modellen. [**Deel twee**](tutorial-1st-experiment-sdk-train.md) bouwt hier mee om meerdere machine learning modellen te trainen en het model beheer proces uit te voeren met behulp van zowel de Azure portal als de SDK.

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Maak een [Azure machine learning-werkruimte](concept-workspace.md) voor gebruik in de volgende zelf studie.
> * Maak een cloud-gebaseerde Jupyter-notebook-VM met Azure Machine Learning python SDK geïnstalleerd en vooraf geconfigureerd.

Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

## <a name="create-a-workspace"></a>Een werkruimte maken

Een Azure Machine Learning-werk ruimte is een Foundation-resource in de cloud die u gebruikt om machine learning modellen te experimenteren, te trainen en te implementeren. Uw Azure-abonnement en resource groep worden gebonden aan een eenvoudig verbruikte object in de SDK. Als u al een Azure Machine Learning-werk ruimte hebt, gaat u verder met de [volgende sectie](#azure). Anders maakt u er nu een.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>Een Cloud notebook server maken

In dit voor beeld wordt de Cloud notebook server in uw werk ruimte gebruikt voor een installatie zonder een vooraf geconfigureerde ervaring. Gebruik [uw eigen omgeving](how-to-configure-environment.md#local) als u de controle wilt over uw omgeving, pakketten en afhankelijkheden.

Vanuit uw werk ruimte maakt u een Cloud resource om aan de slag te gaan met Jupyter-notebooks. Deze resource is een virtuele machine in de cloud die vooraf is geconfigureerd met alles wat u nodig hebt om Azure Machine Learning uit te voeren.

1. Open uw werk ruimte in de [Azure Portal](https://portal.azure.com/).  Als u niet zeker weet hoe u uw werk ruimte in de portal kunt vinden, raadpleegt u [uw werk ruimte zoeken](how-to-manage-workspace.md#view).

1. Selecteer op de pagina werk ruimte in de Azure Portal **laptop-vm's** aan de linkerkant.

1. Selecteer **+ Nieuw** om een VM van een notebook te maken.

     ![Nieuwe VM selecteren](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. Geef een naam op voor de virtuele machine. 
   + De naam van de VM van uw notebook moet tussen 2 en 16 tekens lang zijn. Geldige tekens zijn letters, cijfers en het teken.  
   + De naam moet ook uniek zijn binnen uw Azure-abonnement.

1. Selecteer vervolgens **Maken**. Het kan even duren voordat u uw VM hebt ingesteld.

1. Wacht totdat de status wordt gewijzigd in **wordt uitgevoerd**.
   Nadat de virtuele machine is uitgevoerd, gebruikt u de sectie voor **laptop-vm's** om de Jupyter-webinterface te starten.

1. Selecteer **Jupyter** in de kolom **URI** voor uw VM.

    ![De Jupyter-notebook server starten](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

   Met de koppeling wordt uw notebook server gestart en wordt de Jupyter-notebook-webpagina geopend in een nieuw browser tabblad.  Deze koppeling werkt alleen voor de persoon die de virtuele machine heeft gemaakt. Elke gebruiker van de werk ruimte moet een eigen VM maken.


## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u de volgende taken uitgevoerd:

* Er is een Azure Machine Learning-werk ruimte gemaakt.
* Een Cloud notebook server in uw werk ruimte gemaakt en geconfigureerd.

In **deel twee** van de zelf studie voert u de code `tutorial-1st-experiment-sdk-train.ipynb` in om een machine learning model te trainen. 

> [!div class="nextstepaction"]
> [Zelfstudie: Uw eerste model trainen](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Als u niet van plan bent om deel 2 van deze zelf studie of andere zelf studies te plannen, moet u [de VM van de Cloud notebook server stoppen](tutorial-1st-experiment-sdk-train.md#clean-up-resources) wanneer u deze niet gebruikt om de kosten te verlagen.


