---
title: 'Zelf studie: een model-Machine Learning trainen en implementeren op Azure IoT Edge'
description: In deze zelf studie traint u een machine learning model met behulp van Azure Machine Learning en verpakken u het model als een container installatie kopie die kan worden geïmplementeerd als een Azure IoT Edge-module.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a5c754373ba9437c631e62acbb5d6d246db4c862
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239474"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Zelf studie: een Azure Machine Learning model trainen en implementeren

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelf studie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks in dit artikel hebt gearriveerd, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks voor de beste resultaten.

In dit artikel worden de volgende taken uitgevoerd:

* Gebruik Azure Notebooks om een machine learning model te trainen.
* Verpakken het getrainde model als een container installatie kopie.
* Implementeer de container installatie kopie als een Azure IoT Edge module.

De Azure Notebooks profiteren van een Azure Machine Learning-werk ruimte, een basis blok dat wordt gebruikt om machine learning modellen te experimenteren, te trainen en te implementeren.

De stappen in dit artikel worden mogelijk doorgaans uitgevoerd door gegevens wetenschappers.

## <a name="set-up-azure-notebooks"></a>Azure Notebooks instellen

We gebruiken Azure Notebooks om de twee Jupyter-notebooks en ondersteunende bestanden te hosten. Hier maken en configureren we een Azure Notebooks project. Als u Jupyter en/of Azure Notebooks niet hebt gebruikt, zijn hier enkele inleidende documenten:

* **Snelstartgids:** [een notitie blok maken en delen](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Zelf studie:** [een Jupyter-notebook maken en uitvoeren met python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Het gebruik van Azure-notebooks zorgt voor een consistente omgeving voor de oefening.

> [!NOTE]
> Zodra de Azure Notebooks-service is ingesteld, kan deze vanaf elke computer worden geopend. Tijdens de installatie moet u de ontwikkelings-VM gebruiken. Deze bevat alle bestanden die u nodig hebt.

### <a name="create-an-azure-notebooks-account"></a>Een Azure Notebooks-account maken

Als u Azure Notebooks wilt gebruiken, moet u een account maken. Azure notebook-accounts zijn onafhankelijk van Azure-abonnementen.

1. Navigeer naar [Azure-notebooks](https://notebooks.azure.com).

1. Klik op **Aanmelden** in de rechter bovenhoek van de pagina.

1. Meld u aan met uw werk-of school account (Azure Active Directory) of uw persoonlijke account (micro soft-account).

1. Als u Azure Notebooks nog niet eerder hebt gebruikt, wordt u gevraagd toegang te verlenen voor de Azure Notebooks-app.

1. Maak een gebruikers-ID voor Azure Notebooks.

### <a name="upload-jupyter-notebook-files"></a>Jupyter-notebook bestanden uploaden

Er worden voorbeeld notitieblok bestanden geüpload naar een nieuw Azure Notebooks-project.

1. Selecteer op de pagina gebruiker van uw nieuwe account de optie **Mijn projecten** in de bovenste menu balk.

1. Geef in het dialoog venster **Nieuw project maken** een **project naam** op die ook automatisch de **project-id**vormt.

1. Laat het **open bare** en **Leesmij-bestand** uitgeschakeld omdat het project niet openbaar moet zijn of een Leesmij moet hebben.

1. Selecteer **Maken**.

1. Selecteer **uploaden** (het pictogram pijl-omhoog) en kies **van computer**.

1. Selecteer **bestanden kiezen**.

1. Navigeer naar **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecteer alle bestanden in de lijst en klik op **openen**.

1. Selecteer **uploaden** om te beginnen met uploaden en selecteer vervolgens **gereed** zodra het proces is voltooid.

### <a name="azure-notebook-files"></a>Azure notebook-bestanden

Laten we eens kijken welke bestanden u hebt geüpload naar uw Azure Notebooks-project. De activiteiten in dit deel van de zelf studie beslaan over twee notebook-bestanden, die enkele ondersteunende bestanden gebruiken.

* **01-turbofan\_regressie. ipynb:** In dit notitie blok wordt gebruikgemaakt van de Machine Learning service-werk ruimte om een machine learning experiment te maken en uit te voeren. In ruime mate voert het notitie blok de volgende stappen uit:

  1. Hiermee worden gegevens gedownload van het Azure Storage-account dat is gegenereerd door de apparaat-harnas.
  1. De gegevens worden verkend en voor bereid, waarna de gegevens worden gebruikt om het classificatie model te trainen.
  1. Evalueer het model vanuit het experiment met een test gegevensset (test\_FD003. txt).
  1. Hiermee wordt het beste classificatie model gepubliceerd in de werk ruimte Machine Learning service.

* **02-turbofan\_\_model implementeren. ipynb:** Dit notitie blok haalt het model op dat in het vorige notitie blok is gemaakt en gebruikt dit om een container installatie kopie te maken die kan worden geïmplementeerd op een Azure IoT Edge apparaat. Het notitie blok voert de volgende stappen uit:

  1. Hiermee maakt u een score script voor het model.
  1. Produceert een container installatie kopie met behulp van het classificatie model dat is opgeslagen in de werk ruimte Machine Learning service.
  1. Implementeert de installatie kopie als een webservice op Azure container instance.
  1. Maakt gebruik van de webservice om het model te valideren en de installatie kopie werkt zoals verwacht. De gevalideerde installatie kopie wordt geïmplementeerd op het IoT Edge apparaat in het gedeelte [aangepaste IOT Edge modules maken en implementeren](tutorial-machine-learning-edge-06-custom-modules.md) in deze zelf studie.

* **Test\_FD003. txt:** Dit bestand bevat de gegevens die we als onze testset gebruiken bij het valideren van onze getrainde classificatie. We hebben ervoor gekozen om de test gegevens te gebruiken, zoals is opgegeven voor de oorspronkelijke wedstrijd, terwijl onze test is ingesteld voor de eenvoud.

* **Resterende levens duur\_FD003. txt:** Dit bestand bevat de resterende levens duur voor de laatste fase van elk apparaat in de test\_het bestand FD003. txt. Raadpleeg het bestand README. txt en het model voor het door geven van beschadigingen. PDF-bestanden in de C:\\source\\IoTEdgeAndMlSample\\data\\turbofan voor een gedetailleerde uitleg van de gegevens.

* **Utils.py:** Bevat een set python-hulp programma functies voor het werken met gegevens. Het eerste notitie blok bevat een gedetailleerde uitleg van de functies.

* **README.MD:** Leesmij-bestand met een beschrijving van het gebruik van de notitie blokken.  

## <a name="run-azure-notebooks"></a>Azure Notebooks uitvoeren

Nu het project is gemaakt, kunt u de notitie blokken uitvoeren. 

1. Selecteer op uw project pagina **01-turbofan\_regressie. ipynb**.

    ![Selecteer het eerste notitie blok dat moet worden uitgevoerd](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Als hierom wordt gevraagd, kiest u de python 3,6-kernel in het dialoog venster en selecteert u **kernel instellen**.

1. Als het notitie blok niet wordt **vertrouwd**, klikt u op het **niet-vertrouwde** object in de rechter bovenhoek van het notitie blok. Wanneer het dialoog venster wordt geopend, selecteert u **vertrouwen**.

1. Schuif in het notitie blok omlaag naar de cel die volgt op de instructies **set global Properties instellen** en die begint met de code `AZURE_SUBSCRIPTION_ID =` en vul de waarden in voor uw Azure-abonnement, instellingen en resources.

    ![Algemene eigenschappen instellen in het notitie blok](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Voer die cel uit door **uitvoeren** op de werk balk te selecteren.

    Wanneer een cel wordt uitgevoerd, wordt een sterretje tussen de vier Kante haken ([\*]) weer gegeven. Wanneer de bewerking van de cel is voltooid, wordt het sterretje vervangen door een getal en wordt de relevante uitvoer weer gegeven. De cellen in een notitie blok maken opeenvolgend en er kan maar één tegelijk worden uitgevoerd.

    Volg de instructies in het notitie blok. U kunt ook uitvoer opties gebruiken in het menu **cel** , `Ctrl` + `Enter` om een cel uit te voeren en `Shift` + `Enter` om een cel uit te voeren en door te gaan naar de volgende cel.

    > [!TIP]
    > Vermijd hetzelfde notitie blok op meerdere tabbladen in uw browser voor consistente bewerkingen van cellen.

1. Schuif omlaag naar de cel die direct volgt op de overzichts tekst voor het **maken van een werk ruimte** en voer die cel uit. Zoek in de uitvoer van de cel naar de koppeling waarmee u zich aanmeldt om u aan te melden bij de verificatie. 

    ![Aanmeldings prompt voor verificatie van apparaat](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Open de koppeling en voer de opgegeven code in. Met deze aanmeldings procedure wordt de Jupyter-notebook geverifieerd voor toegang tot Azure-resources met behulp van de platformoverschrijdende opdracht regel interface van Microsoft Azure.  

    ![Toepassing verifiëren bij bevestiging van apparaat](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Op dit moment kunt u de rest van de cellen uitvoeren. Het is optimaal om alle cellen uit te voeren, zodat de code in de cellen opeenvolgend wordt uitgevoerd. Selecteer **alle uitvoeren** in het menu van de **cel** . Ga terug in het notitie blok en Bekijk hoe de bewerkingen in de cel zijn voltooid.

    In de sectie **gegevens verkennen** kunt u de cellen bekijken in de Subsectie **sensor aflezingen en resterende levens duur** die scatterplots van sensor metingen renderen.

    ![Scatterplots voor sensor aflezingen](media/tutorial-machine-learning-edge-04-train-model/sensor-readings.png)

1. Sla het notitie blok op en ga terug naar uw project pagina door te klikken op de naam van het project in de rechter bovenhoek van het notitie blok of terug te gaan in uw browser.

1. Open **02-turbofan\_implementeer\_model. ipynb** en herhaal de stappen in deze procedure om de tweede notebook uit te voeren.

1. Sla het notitie blok op en ga terug naar uw project pagina door te klikken op de naam van het project in de rechter bovenhoek van het notitie blok of terug te gaan in uw browser.

### <a name="verify-success"></a>Controleren geslaagd

Controleer of er enkele items zijn gemaakt om te controleren of de notitie blokken zijn voltooid.

1. Op de pagina Azure Notebooks project selecteert u **verborgen items weer geven** zodat item namen die met een punt beginnen, worden weer gegeven.

1. Controleer of de volgende bestanden zijn gemaakt:

    | File | Beschrijving |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Het configuratie bestand dat wordt gebruikt om de Azure Machine Learning-werkruimte te maken. |
    | ./aml_config/model_config. json | Configuratie bestand dat we nodig hebben om het model in de **turbofanDemo** machine learning-werk ruimte in azure te implementeren. |
    | myenv. yml| Bevat informatie over de afhankelijkheden voor het geïmplementeerde Machine Learning model.|

1. Controleer in de Azure Portal of de werk ruimte **turboFanDemo** machine learning bestaat in de resource groep.

### <a name="debugging"></a>Foutopsporing

U kunt python-instructies in het notitie blok inzet voor het opsporen van fouten, voornamelijk de `print()` opdracht. Als er variabelen of objecten worden weer geven die niet zijn gedefinieerd, voert u de cellen uit waar ze voor het eerst zijn gedeclareerd of geïnstantieerd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we twee Jupyter-notebooks gebruikt die in Azure Notebooks worden uitgevoerd om de gegevens van de turbofan-apparaten te gebruiken om een resterende bruikbare levens duur (resterende levens duur) te trainen, om de classificatie als een model op te slaan, een container installatie kopie te maken en de installatie kopie te implementeren en te testen als een web-se rvice.

Ga door naar het volgende artikel om een IoT Edge apparaat te maken.

> [!div class="nextstepaction"]
> [Een IoT Edge apparaat configureren](tutorial-machine-learning-edge-05-configure-edge-device.md)
