---
title: Een model-Machine Learning trainen en implementeren op Azure IoT Edge | Microsoft Docs
description: Train een machine learning model met behulp van Azure Machine Learning en verpakt het model als een container installatie kopie die kan worden geïmplementeerd als een Azure IoT Edge-module.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6e1ee1fda658ef0884975e4055891f705c4f5058
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493986"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Zelf studie: een Azure Machine Learning model trainen en implementeren

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelf studie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks in dit artikel hebt gearriveerd, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks voor de beste resultaten.

In dit artikel gebruiken we Azure Notebooks eerst om een machine learning model met behulp van Azure Machine Learning te trainen en die model vervolgens te verpakken als een container installatie kopie die kan worden geïmplementeerd als een Azure IoT Edge-module. De Azure Notebooks profiteren van een Azure Machine Learning-werk ruimte, een basis blok dat wordt gebruikt om machine learning modellen te experimenteren, te trainen en te implementeren.

De activiteiten in dit deel van de zelf studie zijn verdeeld over twee notitie blokken.

* **01-turbofan\_regressie. ipynb:** In dit notitie blok worden de stappen beschreven voor het trainen en publiceren van een model met behulp van Azure Machine Learning. De volgende stappen zijn breed:

  1. De trainings gegevens downloaden, voorbereiden en verkennen
  2. De service werkruimte gebruiken om een machine learning experiment te maken en uit te voeren
  3. De model resultaten van het experiment evalueren
  4. Het beste model naar de service werkruimte publiceren

* **02-turbofan\_\_model implementeren. ipynb:** Dit notitie blok haalt het model op dat in het vorige notitie blok is gemaakt en gebruikt dit om een container installatie kopie te maken die kan worden geïmplementeerd op een Azure IoT Edge apparaat.

  1. Een score script maken voor het model
  2. De installatie kopie maken en publiceren
  3. De installatie kopie implementeren als webservice in azure container instance
  4. De webservice gebruiken om het model te valideren en de installatie kopie werkt zoals verwacht

De stappen in dit artikel worden mogelijk doorgaans uitgevoerd door gegevens wetenschappers.

## <a name="set-up-azure-notebooks"></a>Azure Notebooks instellen

We gebruiken Azure Notebooks om de twee Jupyter-notebooks en ondersteunende bestanden te hosten. Hier maken en configureren we een Azure Notebooks project. Als u Jupyter en/of Azure Notebooks niet hebt gebruikt, zijn hier enkele inleidende documenten:

* **Snelstartgids:** [een notitie blok maken en delen](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Zelf studie:** [een Jupyter-notebook maken en uitvoeren met python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Net als bij de ontwikkel aars van de virtuele machine zorgt Azure-notebooks voor een consistente omgeving voor de oefening.

> [!NOTE]
> Zodra de Azure Notebooks-service is ingesteld, kan deze vanaf elke computer worden geopend. Tijdens de installatie moet u de virtuele machine voor ontwikkeling gebruiken, die alle bestanden bevat die u nodig hebt.

### <a name="create-an-azure-notebooks-account"></a>Een Azure Notebooks-account maken

Azure notebook-accounts zijn onafhankelijk van Azure-abonnementen. Als u Azure Notebooks wilt gebruiken, moet u een account maken.

1. Navigeer naar [Azure-notebooks](https://notebooks.azure.com).

2. Klik op **Aanmelden** in de rechter bovenhoek van de pagina.

3. Meld u aan met uw werk-of school account (Azure Active Directory) of uw persoonlijke account (micro soft-account).

4. Als u Azure Notebooks nog niet eerder hebt gebruikt, wordt u gevraagd toegang te verlenen voor de Azure Notebooks-app.

5. Maak een gebruikers-ID voor Azure Notebooks.

### <a name="upload-jupyter-notebooks-files"></a>Jupyter-notitieblok bestanden uploaden

In deze stap maken we een nieuw Azure Notebooks project en uploaden ze bestanden. De bestanden die we uploaden, zijn met name:

* **01-turbofan\_regressie. ipynb**: Jupyter notebook-bestand dat het proces voor het downloaden van de gegevens die zijn gegenereerd door de apparaat-harnas vanuit het Azure-opslag account. het verkennen en voorbereiden van de gegevens voor het trainen van de classificatie; het model trainen; testen van de gegevens met behulp van de gegevensset test die is gevonden in de test\_FD003. txt-bestand. en ten slotte het classificatie model op te slaan in de werk ruimte Machine Learning service.

* **02-turbofan\_\_model implementeren. ipynb:** Jupyter notebook dat u helpt bij het proces van het gebruik van het classificatie model dat is opgeslagen in de werk ruimte van de Machine Learning-service voor het produceren van een container installatie kopie. Zodra de installatie kopie is gemaakt, wordt u door het proces van de implementatie van de installatie kopie als een webservice geleid, zodat u kunt valideren dat deze werkt zoals verwacht. De gevalideerde installatie kopie wordt geïmplementeerd op het IoT Edge apparaat in het gedeelte [aangepaste IOT Edge modules maken en implementeren](tutorial-machine-learning-edge-06-custom-modules.md) in deze zelf studie.

* **Test\_FD003. txt:** Dit bestand bevat de gegevens die we als onze testset gebruiken bij het valideren van onze getrainde classificatie. We hebben ervoor gekozen om de test gegevens te gebruiken zoals is opgegeven voor de oorspronkelijke wedstrijd als onze test die is ingesteld voor het gemak van het voor beeld.

* **Resterende levens duur\_FD003. txt:** Dit bestand bevat de resterende levens duur voor de laatste fase van elk apparaat in de test\_het bestand FD003. txt. Raadpleeg het **bestand README. txt** en het model voor het **door geven van beschadigingen. PDF-** bestanden in de C:\\source\\IoTEdgeAndMlSample\\data\\turbofan voor een gedetailleerde uitleg van de gegevens.

* **Utils.py:** Bevat een set python-hulp programma functies voor het werken met gegevens. Het eerste notitie blok bevat een gedetailleerde uitleg van de functies.

* **README.MD:** Leesmij-bestand met een beschrijving van het gebruik van de notitie blokken.

Maak een nieuw project en upload de bestanden naar uw notitie blok.

1. Selecteer **Mijn projecten** in de bovenste menu balk.

1. Selecteer **+ Nieuw project**. Geef een naam en een ID op. Het is niet nodig om het project openbaar te maken of een Leesmij te hebben.

1. Selecteer **uploaden** en kies **van computer**.

1. Selecteer **bestanden kiezen**.

1. Navigeer naar **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecteer alle bestanden en klik op **openen**.

1. Selecteer **uploaden** om te beginnen met uploaden en selecteer vervolgens **gereed** zodra het proces is voltooid.

## <a name="run-azure-notebooks"></a>Azure Notebooks uitvoeren

Nu het project is gemaakt, voert u het **turbofan van 01-\_regressie. ipynb** uit.

1. Selecteer op de pagina turbofan-project **01-turbofan\_regressie. ipynb**.

    ![Selecteer het eerste notitie blok dat moet worden uitgevoerd](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. Als hierom wordt gevraagd, kiest u de python 3,6-kernel in het dialoog venster en selecteert u **kernel instellen**.

3. Als het notitie blok niet wordt **vertrouwd**, klikt u op het **niet-vertrouwde** object in de rechter bovenhoek van het notitie blok. Wanneer het dialoog venster wordt geopend, selecteert u **vertrouwen**.

4. Volg de instructies in het notitie blok.

    * `Ctrl + Enter` een cel wordt uitgevoerd.
    * `Shift + Enter` wordt een cel uitgevoerd en wordt naar de volgende cel genavigeerd.
    * Wanneer een cel wordt uitgevoerd, heeft deze een sterretje tussen de vier Kante haken, zoals **[\*]** . Wanneer de asterisk is voltooid, wordt het sterretje vervangen door een aantal en de relevante uitvoer kan hieronder worden weer gegeven. Omdat cellen vaak worden gebaseerd op het werk van de voor gaande, kan er slechts één cel tegelijk worden uitgevoerd.

5. Wanneer u klaar bent met het uitvoeren van de **01-turbofan\_regressie. ipynb** -notebook, gaat u terug naar de pagina van het project.

6. Open **02-turbofan\_implementeer\_model. ipynb** en herhaal de stappen in deze sectie om de tweede notebook uit te voeren.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we twee Jupyter-notebooks gebruikt die in Azure Notebooks worden uitgevoerd om de gegevens van de turbofan-apparaten te gebruiken om een resterende bruikbare levens duur (resterende levens duur) te trainen, om de classificatie als een model op te slaan, een container installatie kopie te maken en de installatie kopie te implementeren en te testen als een web-se rvice.

Ga door naar het volgende artikel om een IoT Edge apparaat te maken.

> [!div class="nextstepaction"]
> [Een IoT Edge apparaat configureren](tutorial-machine-learning-edge-05-configure-edge-device.md)
