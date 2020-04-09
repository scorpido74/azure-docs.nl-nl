---
title: 'Zelfstudie: Een labelproject maken voor afbeeldingsclassificatie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het beheren van het proces van het labelen van afbeeldingen, zodat ze kunnen worden gebruikt in classificatiemodellen van meerdere klassen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/02/2020
ms.openlocfilehash: fa33861d86ff8bee3e2a34fb3d93032ac6180880
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879719"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Zelfstudie: Een labelproject maken voor afbeeldingsclassificatie met meerdere klassen 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze zelfstudie ziet u hoe u het proces van labeling (ook wel tagging) afbeeldingen beheren die moeten worden gebruikt als gegevens voor het bouwen van machine learning-modellen. Gegevensetikettering in Azure Machine Learning is in openbare preview.

Als u een machine learning-model wilt trainen om afbeeldingen te classificeren, hebt u honderden of zelfs duizenden afbeeldingen nodig die correct zijn gelabeld.  Azure Machine Learning helpt u bij het beheren van de voortgang van uw privéteam van domeinexperts terwijl ze uw gegevens labelen.
 
In deze tutorial gebruik je afbeeldingen van katten en honden.  Aangezien elke afbeelding een kat of een hond is, is dit een *multi-class* labeling project. U leert het volgende:

> [!div class="checklist"]
>
> * Maak een Azure-opslagaccount aan en upload afbeeldingen naar het account.
> * Maak een Azure Machine Learning-werkruimte.
> * Maak een meerklassenimagelabelingproject.
> * Label uw gegevens.  U of uw labelers kunnen deze taak uitvoeren.
> * Voltooi het project door de gegevens te bekijken en te exporteren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree)aan.

## <a name="create-a-workspace"></a>Een werkruimte maken

Een Azure Machine Learning-werkruimte is een basisbron in de cloud die u gebruikt om machine learning-modellen te experimenteren, te trainen en te implementeren. Het koppelt uw Azure-abonnement en resourcegroep aan een gemakkelijk verbruikt object in de service.

U maakt een werkruimte via de Azure-portal, een webconsole voor het beheren van uw Azure-bronnen.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Een etiketteringsproject starten

Vervolgens beheert u het project voor het labelen van gegevens in Azure Machine Learning-studio, een geconsolideerde interface die machine learning-tools bevat om scenario's voor gegevenswetenschap uit te voeren voor beoefenaars van gegevenswetenschappen van alle vaardigheidsniveaus. De studio wordt niet ondersteund in internet explorer-browsers.

1. Meld u aan bij [Azure Machine Learning studio](https://ml.azure.com).

1. Selecteer uw abonnement en de werkruimte die u hebt gemaakt.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Een gegevensarchief maken

Azure Machine Learning-gegevensarchieven worden gebruikt om verbindingsgegevens op te slaan, zoals uw abonnements-id en tokenautorisatie. Hier gebruikt u een datastore om verbinding te maken met het opslagaccount dat de afbeeldingen voor deze zelfstudie bevat.

1. Selecteer **Gegevensstores**aan de linkerkant van uw werkruimte.

1. Selecteer **+ Nieuw gegevensarchief**.

1. Vul het formulier in met de volgende instellingen:

    Veld|Beschrijving 
    ---|---
    Naam datastore | Geef de datastore een naam.  Hier gebruiken we **labeling_tutorial.**
    Gegevensarchieftype | Selecteer het type opslagruimte.  Hier gebruiken we **Azure Blob Storage**, de voorkeursopslag voor afbeeldingen.
    Methode voor accountselectie | Selecteer **Handmatig invoeren**.
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Verificatietype | Selecteer **SAS-token**.
    Accountsleutel | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Selecteer **Maken** om het gegevensarchief te maken.

### <a name="add-labelers-to-workspace"></a>Labelers toevoegen aan werkruimte

Stel uw werkruimte in met alle mensen die gegevens voor een van uw projecten labelen.  Later voegt u deze labelers toe aan uw specifieke labelingproject.

1. Selecteer aan de linkerkant **De labeling van gegevens**.

1. Selecteer **Labelers**boven aan de pagina .

1. Selecteer **Labeler toevoegen** om het e-mailadres van een labeler toe te voegen.

1. Blijf meer labelers toevoegen totdat je klaar bent.

### <a name="create-a-labeling-project"></a>Een labelproject maken

Nu u uw lijst met labelers en toegang tot de gegevens hebt die u wilt labelen, maakt u uw labelproject.

1. Selecteer Boven aan de pagina **Projecten**.

1. Selecteren **+ Project toevoegen**.

    ![Een project maken](media/tutorial-labeling/create-project.png)

### <a name="project-details"></a>Projectdetails

1. Gebruik de volgende invoer voor het formulier **Projectdetails:**

    Veld|Beschrijving 
    ---|---
    Projectnaam | Geef uw project een naam.  Hier zullen we gebruik maken **van tutorial-cats-n-honden**.
    Taaktype labelen | Selecteer **Afbeeldingsclassificatie meerdere klassen**.  
    
    Selecteer **Volgende** om het project verder te maken.

### <a name="select-or-create-a-dataset"></a>Een gegevensset selecteren of maken

1.   Selecteer in **het formulier Een gegevensset selecteren of maken** de tweede keuze, Een **gegevensset maken**en selecteer vervolgens de koppeling Uit **gegevensarchief**.

1. Gebruik de volgende invoer voor de **gegevensset Maken vanuit het gegevensarchiefformulier:**

    1. Op het **formulier Basisinformatie** voegt u een naam toe, hier gebruiken we **afbeeldingen voor de zelfstudie.**  Voeg desgewenst een beschrijving toe.  Selecteer **vervolgens Volgende**.
    1. Gebruik in het **selectieformulier van Datastore** de vervolgkeuzelijst om uw **eerder gemaakte gegevensarchief**te selecteren, bijvoorbeeld **tutorial_images (Azure Blob Storage)**
    1. Selecteer vervolgens nog steeds op het **gegevensarchiefselectieformulier** **Bladeren** en selecteer Vervolgens **MultiClass - DogsCats**.  Selecteer **Opslaan** om **/MultiClass - DogsCats** als pad te gebruiken.
    1. Selecteer **Volgende** om details te bevestigen en maak **vervolgens** om de gegevensset te maken.
    1. Selecteer de cirkel naast de naam van de gegevensset in de lijst, bijvoorbeeld **afbeeldingen voor zelfstudie**.

1. Selecteer **Volgende** om het project verder te maken.

### <a name="label-classes"></a>Labelklassen

1. Typ in het formulier **Labelklassen** een labelnaam en selecteer **+Label toevoegen** om het volgende label te typen.  Voor dit project zijn de labels **Cat**, **Dog**en **Uncertain**.

1. Selecteer **Volgende** wanneer alle labels zijn toegevoegd.

### <a name="labeling-instructions"></a>Etiketteringsinstructies

1. Op het formulier **Etiketteringsinstructies** u een link naar een website die gedetailleerde instructies voor uw labelers biedt.  We laten het leeg voor deze tutorial.

1. U ook een korte beschrijving van de taak direct op het formulier toevoegen.  Type **Labeling tutorial - Katten & Honden.**

1. Selecteer **Volgende**.

1. Laat het selectievakje op het **ml-gelabeldeformulier** onaangevinkt. Ml assisted labeling vereist meer gegevens dan u zult gebruiken in deze zelfstudie.

1. Selecteer **Project maken**.

Deze pagina wordt niet automatisch vernieuwd. Na een pauze wordt de pagina handmatig vernieuwd totdat de status van het project is gewijzigd in **Gemaakt.**

### <a name="add-labelers-to-your-project"></a>Labelers toevoegen aan uw project

Voeg een aantal of al uw labelers toe aan dit project.

1. Selecteer de projectnaam om het project te openen.  

1. Selecteer Boven aan de pagina **Teams**.

1. Selecteer de **koppeling labeling_tutorial Standaardteam.**

1. Gebruik nu **Labelers toewijzen** om de labelers toe te voegen waaraan u wilt deelnemen aan dit project. 

1. Kies in de lijst met labelers die u eerder hebt gemaakt.  Nadat u alle labelers hebt geselecteerd die u wilt gebruiken, selecteert u **Labelers toewijzen** om ze toe te voegen aan uw standaardprojectteam.

## <a name="start-labeling"></a>Beginnen met labelen

U hebt nu uw Azure-resources ingesteld en een project voor het labelen van gegevens geconfigureerd. Het is tijd om labels toe te voegen aan uw gegevens.

### <a name="notify-labelers"></a>Labelers op de hoogte stellen

Als je veel afbeeldingen hebt om te labelen, heb je hopelijk ook veel labelers om de taak te voltooien.  U wilt ze nu instructies sturen, zodat ze toegang hebben tot de gegevens en kunnen beginnen met labelen.

1. Selecteer [in Machine Learning-studio](https://ml.azure.com) **Gegevenslabeling** aan de linkerkant om uw project te vinden.  

1. Selecteer de koppeling projectnaam.

1. Selecteer boven aan de pagina **Details**.  U ziet een samenvatting van uw project.

    ![Projectdetails](media/tutorial-labeling/project-details.png)

1. Kopieer de **URL-koppeling labelportal** om naar uw labelers te verzenden.

1. Selecteer nu **Team** bovenaan om uw labelteam te vinden.  

1. Selecteer de koppeling teamnaam.

1. Selecteer boven aan de pagina **E-mailteam** om uw e-mail te starten.  Plak de URL van de labelportal die u zojuist hebt gekopieerd.  

Elke keer dat een labeler naar de URL van de portal gaat, krijgen ze meer afbeeldingen te zien om te labelen, totdat de wachtrij leeg is.  

### <a name="tag-the-images"></a>Tag de afbeeldingen

In dit deel van de zelfstudie schakelt u rollen over van de *projectbeheerder* naar die van een *labeler.*  Gebruik de URL die u naar het team hebt verzonden.  Deze URL brengt u naar de labeling portal voor uw project.  Als je instructies had toegevoegd, zou je ze hier zien wanneer je op de pagina aankomt.

1. Selecteer boven aan de pagina **Taken** om te beginnen met labelen.

1. Selecteer een miniatuurafbeelding aan de rechterkant om het aantal afbeeldingen weer te geven dat u in één keer wilt labelen. U moet al deze afbeeldingen labelen voordat u verder gaan. Wissel alleen van lay-out wanneer u een nieuwe pagina met niet-gelabelde gegevens hebt. Als u van indeling wisselt, wordt het werk voor het taggen van de pagina gewist.

1. Selecteer een of meer afbeeldingen en selecteer vervolgens een tag die u op de selectie wilt toepassen. De tag wordt onder de afbeelding weergegeven.  Ga door met het selecteren en taggen van alle afbeeldingen op de pagina.  Als u alle weergegeven afbeeldingen tegelijkertijd wilt selecteren, selecteert u **Alle selecteren**. Selecteer ten minste één afbeelding om een tag toe te passen.


    > [!TIP]
    > U de eerste negen tags selecteren met behulp van de nummertoetsen op uw toetsenbord.

1. Zodra alle afbeeldingen op de pagina zijn getagd, selecteert **u Verzenden** om deze labels in te dienen.

    ![Afbeeldingen taggen](media/tutorial-labeling/catsndogs.gif)

1. Nadat u tags voor de gegevens bij de hand hebt ingediend, vernieuwt Azure de pagina met een nieuwe set afbeeldingen uit de werkwachtrij.

## <a name="complete-the-project"></a>Het project voltooien

Nu schakelt u rollen terug naar de *projectbeheerder* voor het labelingproject.

Als manager u het werk van uw labeler bekijken.  

### <a name="review-labeled-data"></a>Gelabelde gegevens controleren

1. Selecteer [in Machine Learning-studio](https://ml.azure.com) **Gegevenslabeling** aan de linkerkant om uw project te vinden.  

1. Selecteer de koppeling projectnaam.

1. Het dashboard toont u de voortgang van uw project.

1. Selecteer boven aan de pagina **Gegevens**.

1. Selecteer aan de linkerkant **Gelabelde gegevens** om uw gelabelde afbeeldingen te zien.  

1. Als u het niet eens bent met een label, selecteert u de afbeelding en selecteert u **Vervolgens Weigeren** onder aan de pagina.  De tags worden verwijderd en de afbeelding wordt teruggezet in de wachtrij met niet-gelabelde afbeeldingen.

### <a name="export-labeled-data"></a>Gelabelde gegevens exporteren

U de labelgegevens voor Machine Learning-experimenten op elk gewenst moment exporteren. Gebruikers exporteren vaak meerdere keren en trainen verschillende modellen, in plaats van te wachten tot alle afbeeldingen worden gelabeld.

Afbeeldingslabels kunnen worden geëxporteerd in [COCO-indeling](http://cocodataset.org/#format-data) of als azure machine learning-gegevensset. De gegevenssetindeling maakt het eenvoudig om te trainen in Azure Machine Learning.  

1. Selecteer [in Machine Learning-studio](https://ml.azure.com) **Gegevenslabeling** aan de linkerkant om uw project te vinden.  

1. Selecteer de koppeling projectnaam.

1. Selecteer **Exporteren** en kies **Exporteren als Azure ML-gegevensset**. 

    De status van de export wordt net onder de knop **Exporteren** weergegeven. 

1. Zodra de labels zijn geëxporteerd, selecteert u **Gegevenssets** aan de linkerkant om de resultaten weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u afbeeldingen gelabeld.  Gebruik nu uw gelabelde gegevens:

> [!div class="nextstepaction"]
> [Train een machine learning beeldherkenningsmodel.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)
