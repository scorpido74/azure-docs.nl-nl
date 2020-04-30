---
title: 'Zelf studie: een label project maken voor de classificatie van afbeeldingen'
titleSuffix: Azure Machine Learning
description: Meer informatie over het beheren van het proces voor het labelen van afbeeldingen, zodat deze kunnen worden gebruikt in een multi-class afbeeldings classificatie modellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.openlocfilehash: d960c8225cbb22c221ecaec1b7c49b5bb20c1429
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203638"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Zelf studie: een label project maken voor de classificatie van een afbeelding met meerdere klassen 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze zelf studie leert u hoe u het proces van labelen (ook wel Tags toevoegen genoemd) kunt beheren dat als gegevens moet worden gebruikt voor het bouwen van machine learning modellen. Gegevens labelen in Azure Machine Learning bevindt zich in de open bare preview.

Als u een machine learning model wilt trainen om afbeeldingen te classificeren, hebt u honderden of zelfs duizenden installatie kopieën met een juiste naam nodig.  Azure Machine Learning helpt u bij het beheren van de voortgang van uw persoonlijke team van domein experts wanneer ze een label hebben van uw gegevens.
 
In deze zelf studie gebruikt u installatie kopieën van katten en honden.  Omdat elke afbeelding een kat of hond is, is dit een project *met meerdere klassen* labelen. U leert het volgende:

> [!div class="checklist"]
>
> * Maak een Azure-opslag account en upload installatie kopieën naar het account.
> * Maak een Azure Machine Learning-werk ruimte.
> * Maak een afbeelding voor het labelen van meerdere klassen.
> * Voorzie uw gegevens van een label.  U of uw labelers kunnen deze taak uitvoeren.
> * Voltooi het project door de gegevens te controleren en te exporteren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Een werkruimte maken

Een Azure Machine Learning-werk ruimte is een Foundation-resource in de cloud die u gebruikt om machine learning modellen te experimenteren, te trainen en te implementeren. Uw Azure-abonnement en resource groep worden gebonden aan een eenvoudig verbruikte object in de service.

U maakt een werk ruimte via de Azure Portal, een webconsole voor het beheren van uw Azure-resources.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Een label project starten

Vervolgens beheert u het gegevens label project in Azure Machine Learning Studio, een geconsolideerde interface met machine learning-hulpprogram ma's voor het uitvoeren van data Science-scenario's voor data wetenschappen die deel uitmaakt van alle vaardigheids niveaus. De Studio wordt niet ondersteund in Internet Explorer-browsers.

1. Meld u aan bij [Azure machine learning Studio](https://ml.azure.com).

1. Selecteer uw abonnement en de werk ruimte die u hebt gemaakt.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Een gegevens opslag maken

Azure Machine Learning gegevens opslag worden gebruikt voor het opslaan van verbindings gegevens, zoals uw abonnements-ID en Token autorisatie. Hier gebruikt u een gegevens opslag om verbinding te maken met het opslag account dat de installatie kopieën bevat voor deze zelf studie.

1. Selecteer aan de linkerkant van uw werk ruimte de optie **gegevens opslag**.

1. Selecteer **+ nieuwe gegevens opslag**.

1. Vul het formulier in met de volgende instellingen:

    Veld|Beschrijving 
    ---|---
    Naam van gegevens archief | Geef een naam op voor het gegevens archief.  Hier gebruiken we **labeling_tutorial**.
    Gegevens opslag type | Selecteer het type opslag.  Hier gebruiken we **Azure Blob Storage**, de voorkeurs opslag voor installatie kopieën.
    Methode voor het selecteren van accounts | Selecteer **hand matig invoeren**.
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Verificatietype | Selecteer **SAS-token**.
    Accountsleutel | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Selecteer **maken** om het gegevens archief te maken.

### <a name="create-a-labeling-project"></a>Een label project maken

Nu u de lijst met labelers hebt en toegang hebt tot de gegevens die u wilt voorzien van een label, maakt u het label project.

1. Selecteer aan de bovenkant van de pagina **projecten**.

1. Selecteer **+ project toevoegen**.

    ![Een project maken](media/tutorial-labeling/create-project.png)

### <a name="project-details"></a>Project Details

1. Gebruik de volgende invoer voor het formulier **Project Details** :

    Veld|Beschrijving 
    ---|---
    Projectnaam | Geef uw project een naam.  Hier gebruiken we **zelf studie-katten-n-honden**.
    Het taak type labelen | Selecteer **afbeeldings classificatie met meerdere klassen**.  
    
    Selecteer **volgende** om door te gaan met het maken van het project.

### <a name="select-or-create-a-dataset"></a>Een gegevensset selecteren of maken

1.   Selecteer in het formulier **een gegevensset selecteren of maken** de tweede keuze, **Maak een gegevensset**en selecteer vervolgens de koppeling **van gegevens opslag**.

1. Gebruik de volgende invoer voor het formulier **gegevensset maken op basis van Data Store** :

    1. Voeg op het formulier **basis informatie** een naam toe, hier gaan we **afbeeldingen voor zelf studie**gebruiken.  Voeg indien gewenst een beschrijving toe.  Selecteer **volgende**.
    1. Gebruik in het formulier **Data Store selecteren** de vervolg keuzelijst om uw **eerder gemaakte gegevens opslag**te selecteren, bijvoorbeeld **tutorial_images (Azure Blob Storage)**
    1. Selecteer vervolgens in het formulier **gegevens opslag** selecteren de optie **Bladeren** en selecteer vervolgens multi **Class-DogsCats**.  Selecteer **Opslaan** om **/MultiClass-DogsCats** als pad te gebruiken.
    1. Selecteer **volgende** om de details te bevestigen en vervolgens te **maken** om de gegevensset te maken.
    1. Selecteer de cirkel naast de naam van de gegevensset in de lijst, bijvoorbeeld **afbeeldingen-voor-zelf studie**.

1. Selecteer **volgende** om door te gaan met het maken van het project.

### <a name="label-classes"></a>Label klassen

1. Typ op het formulier **Label klassen** een label naam en selecteer **+ label toevoegen** om het volgende label te typen.  Voor dit project zijn de labels **kat**, **hond**en **onzeker**.

1. Selecteer **volgende** wanneer alle labels zijn toegevoegd.

### <a name="labeling-instructions"></a>Instructies voor labelen

1. In het formulier **Label instructies** kunt u een koppeling naar een website opgeven die gedetailleerde instructies biedt voor uw labelers.  Voor deze zelf studie laten we dit leeg.

1. U kunt ook een korte beschrijving van de taak rechtstreeks op het formulier toevoegen.  Typ een label voor de **zelf studie-katten & honden.**

1. Selecteer **Next**.

1. Laat het selectie vakje ingeschakeld op het label formulier voor het maken van **ondersteuning** . Voor het labelen van ML worden meer gegevens nodig dan in deze zelf studie wordt gebruikt.

1. Selecteer **Project maken**.

Deze pagina wordt niet automatisch vernieuwd. Vernieuw de pagina na een onderbreking hand matig om de status van het project te wijzigen in **gemaakt**.

## <a name="start-labeling"></a>Labelen starten

U hebt nu uw Azure-resources ingesteld en een project voor gegevens labeling geconfigureerd. Het is tijd om labels toe te voegen aan uw gegevens.

### <a name="tag-the-images"></a>De afbeeldingen labelen

In dit gedeelte van de zelf studie gaat u rollen van de *Project beheerder* over naar die van een *Labeler*.  Iedereen met Inzender toegang tot uw werk ruimte kan een Labeler worden.

1. Selecteer in [machine learning Studio](https://ml.azure.com) **gegevens labelen** aan de linkerkant om het project te zoeken.  

1. Selecteer de project naam in de lijst.

1. Selecteer onder de project naam **Label gegevens**.

1. Lees de instructies en selecteer vervolgens **taken**.

1. Selecteer een miniatuur afbeelding aan de rechter kant om het aantal installatie kopieën dat u wilt labelen in één slag weer te geven. U moet al deze installatie kopieën labelen voordat u kunt door gaan. Schakel alleen indelingen in als u een nieuwe pagina hebt met niet-gelabelde gegevens. Als u inschakelt, wordt de code ring van de pagina in uitvoering gewist.

1. Selecteer een of meer afbeeldingen en selecteer vervolgens een label om toe te passen op de selectie. Het label wordt onder de afbeelding weer gegeven.  Ga door met het selecteren en labelen van alle installatie kopieën op de pagina.  Als u alle weer gegeven afbeeldingen tegelijk wilt selecteren, selecteert u **Alles selecteren**. Selecteer ten minste één afbeelding om een tag toe te passen.


    > [!TIP]
    > U kunt de eerste negen tags selecteren met behulp van de cijfer toetsen op het toetsen bord.

1. Zodra alle installatie kopieën op de pagina zijn gelabeld, selecteert u **verzenden** om deze labels te verzenden.

    ![Afbeeldingen taggen](media/tutorial-labeling/catsndogs.gif)

1. Nadat u labels voor de gegevens hebt verzonden, vernieuwt Azure de pagina met een nieuwe set installatie kopieën uit de werk wachtrij.

## <a name="complete-the-project"></a>Het project volt ooien

Nu gaat u naar de *Project beheerder* voor het labelen van het project.

Als Manager wilt u mogelijk het werk van uw Labeler bekijken.  

### <a name="review-labeled-data"></a>Gelabelde gegevens controleren

1. Selecteer in [machine learning Studio](https://ml.azure.com) **gegevens labelen** aan de linkerkant om het project te zoeken.  

1. Selecteer de koppeling project naam.

1. In het dash board ziet u de voortgang van het project.

1. Selecteer boven aan de pagina **gegevens**.

1. Selecteer aan de linkerkant de optie **gelabelde gegevens** om uw gemarkeerde afbeeldingen weer te geven.  

1. Wanneer u het voor beeld van een label hebt, selecteert u de afbeelding en selecteert u vervolgens onder aan de pagina **weigeren** .  De tags worden verwijderd en de installatie kopie wordt terug geplaatst in de wachtrij met niet-gelabelde afbeeldingen.

### <a name="export-labeled-data"></a>Gelabelde gegevens exporteren

U kunt de label gegevens voor Machine Learning experimenten op elk gewenst moment exporteren. Gebruikers exporteren vaak meerdere keren en kunnen verschillende modellen trainen, in plaats van te wachten tot alle installatie kopieën worden gelabeld.

Labels van afbeeldingen kunnen worden geëxporteerd in [Coco-indeling](http://cocodataset.org/#format-data) of als een Azure machine learning-gegevensset. Met de indeling gegevensset kunt u eenvoudig oefenen in Azure Machine Learning.  

1. Selecteer in [machine learning Studio](https://ml.azure.com) **gegevens labelen** aan de linkerkant om het project te zoeken.  

1. Selecteer de koppeling project naam.

1. Selecteer **exporteren** en kies **exporteren als Azure ml-gegevensset**. 

    De status van de export wordt weer gegeven onder de knop **exporteren** . 

1. Zodra de labels zijn geëxporteerd, selecteert u **gegevens sets** aan de linkerkant om de resultaten weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u afbeeldingen gelabeld.  Gebruik nu uw gelabelde gegevens:

> [!div class="nextstepaction"]
> [Train een machine learning voor het herkennen van een afbeeldings model](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb).
