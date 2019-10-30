---
title: 'Zelf studie: uw eerste ML-experiment maken: instellen'
titleSuffix: Azure Machine Learning
description: In deze zelf studie gaat u aan de slag met de Azure Machine Learning python SDK die wordt uitgevoerd in Jupyter-notebooks.  In deel 1 maakt u een werk ruimte waarin u experimenten en ML-modellen gaat beheren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/25/2019
ms.openlocfilehash: fc26b224a2af6ab4f1f6bf5551381d4739831351
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053875"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Zelf studie: aan de slag met het maken van uw eerste ML-experiment met de python-SDK

In deze zelf studie voltooit u de end-to-end-stappen om aan de slag te gaan met de Azure Machine Learning python SDK die wordt uitgevoerd in Jupyter-notebooks. Deze zelf studie maakt **deel uit van een tweedelige zelf studie reeks**, en omvat het instellen en configureren van python-omgevingen, en het maken van een werk ruimte voor het beheren van uw experimenten en machine learning modellen. [**Deel twee**](tutorial-1st-experiment-sdk-train.md) bouwt hier mee om meerdere machine learning modellen te trainen en het model beheer proces uit te voeren met behulp van zowel de Azure portal als de SDK.

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Maak een [Azure machine learning-werkruimte](concept-workspace.md) voor gebruik in de volgende zelf studie.
> * Kopieer de notebooks van de zelf studies naar uw map in de werk ruimte.
> * Maak een cloud-gebaseerde Jupyter-notebook-VM met Azure Machine Learning python SDK geïnstalleerd en vooraf geconfigureerd.

Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

## <a name="create-a-workspace"></a>Een werkruimte maken

Een Azure Machine Learning-werk ruimte is een Foundation-resource in de cloud die u gebruikt om machine learning modellen te experimenteren, te trainen en te implementeren. Uw Azure-abonnement en resource groep worden gebonden aan een eenvoudig verbruikte object in de service. 

U maakt een werk ruimte via de Azure Portal, een webconsole voor het beheren van uw Azure-resources. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Noteer uw **werk ruimte** en **abonnement**. U hebt deze nodig om ervoor te zorgen dat u op de juiste plaats uw experiment kunt maken. 


## <a name="azure"></a>Een notitieblokmap-map klonen

In dit voor beeld wordt de Cloud notebook server in uw werk ruimte gebruikt voor een installatie zonder een vooraf geconfigureerde ervaring. Gebruik [uw eigen omgeving](how-to-configure-environment.md#local) als u de controle wilt over uw omgeving, pakketten en afhankelijkheden.

U voert de volgende proef versie uit en voert de stappen uit op de pagina voor het land van de werk ruimte (preview), een geconsolideerde interface met machine learning-hulpprogram ma's voor het uitvoeren van data Science-scenario's voor data technologie van alle vaardigheids niveaus.

1. Meld u aan bij de pagina voor het land van de [werk ruimte](https://ml.azure.com/).

1. Selecteer uw abonnement en de werk ruimte die u hebt gemaakt.

1. Selecteer **notebooks en bestanden** aan de linkerkant.

1. Open de map met voor **beelden** .

1. Selecteer **'... '** aan de rechter kant van de map **zelf studies** en selecteer vervolgens **klonen**.

    ![Map klonen](media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. Er wordt een map weer gegeven voor elke gebruiker die toegang heeft tot de werk ruimte.  Selecteer de map waarin u de map **zelf studie** wilt klonen.

## <a name="a-nameopenselect-a-vm-to-run-the-notebook"></a><a name="open">een VM selecteren om het notitie blok uit te voeren

1. Open de map onder **gebruikers bestanden** en open vervolgens de map gekloonde **zelf studies** .

    ![Map met zelf studies openen](media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > U kunt notitie blokken weer geven in de map **samples** , maar u kunt geen notitie blok van daar uitvoeren.  Als u een notitie blok wilt uitvoeren, moet u ervoor zorgen dat u de gekloonde versie van het notitie blok opent in de sectie **gebruikers bestanden** .
    
1. Selecteer de **zelf studie-1ste experiment-SDK-Train. ipynb-** bestand in de map **zelf studies** .

1. Selecteer op de bovenste balk een werk blad-VM die u wilt gebruiken om het notitie blok uit te voeren. Deze Vm's zijn vooraf geconfigureerd met alles wat u nodig hebt om Azure Machine Learning uit te voeren. U kunt een virtuele machine selecteren die wordt gemaakt door een gebruiker van uw werk ruimte. 

1. Als er geen Vm's worden gevonden, selecteert u **+ nieuwe virtuele machine** om de virtuele machine te maken.

    ![Een VM maken](media/tutorial-1st-experiment-sdk-setup/no-vm.png)

    1. Wanneer u een virtuele machine maakt, moet u een naam opgeven.  De naam moet tussen 2 en 16 tekens lang zijn. Geldige tekens zijn letters, cijfers en het-teken, en moeten ook uniek zijn binnen uw Azure-abonnement.

    1. Selecteer vervolgens **Maken**. Het kan ongeveer 5 minuten duren om uw virtuele machine in te stellen.

1. Zodra de virtuele machine beschikbaar is, wordt deze weer gegeven op de bovenste werk balk.  U kunt het notitie blok nu uitvoeren met de optie **alle uitvoeren** op de werk balk of door **SHIFT + ENTER** te gebruiken in de code cellen van het notitie blok.


## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u de volgende taken uitgevoerd:

* Er is een Azure Machine Learning-werk ruimte gemaakt.
* Een Cloud notebook server in uw werk ruimte gemaakt en geconfigureerd.

In **deel twee** van de zelf studie voert u de code in `tutorial-1st-experiment-sdk-train.ipynb` om een machine learning model te trainen. 

> [!div class="nextstepaction"]
> [Zelf studie: uw eerste model trainen](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Als u niet van plan bent om deel 2 van deze zelf studie of andere zelf studies te plannen, moet u [de VM van de Cloud notebook server stoppen](tutorial-1st-experiment-sdk-train.md#clean-up-resources) wanneer u deze niet gebruikt om de kosten te verlagen.


