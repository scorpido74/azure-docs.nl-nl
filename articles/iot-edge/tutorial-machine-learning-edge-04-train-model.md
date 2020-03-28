---
title: 'Zelfstudie: Een model trainen en implementeren - Machine Learning op Azure IoT Edge'
description: In deze zelfstudie traint u een machine learning-model met Azure Machine Learning en verpakt u het model vervolgens als een containerafbeelding die kan worden geïmplementeerd als een Azure IoT Edge-module.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57630b789233dd23e61398f445b434e4ba08b48e
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80236027"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Zelfstudie: Een Azure Machine Learning-model trainen en implementeren

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks tot dit artikel bent gekomen, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de serie voor de beste resultaten.

In dit artikel doen we de volgende taken:

* Gebruik Azure Notebooks om een machine learning-model te trainen.
* Pak het getrainde model als containerafbeelding.
* Implementeer de containerafbeelding als een Azure IoT Edge-module.

De Azure-notitieblokken maken gebruik van een Azure Machine Learning-werkruimte, een basisblok dat wordt gebruikt om machine learning-modellen te experimenteren, te trainen en te implementeren.

De stappen in dit artikel kunnen doorgaans worden uitgevoerd door gegevenswetenschappers.

## <a name="set-up-azure-notebooks"></a>Azure-notitieblokken instellen

We gebruiken Azure Notebooks om de twee Jupyter-notitieblokken te hosten en ondersteunende bestanden. Hier maken en configureren we een Azure Notebooks-project. Als u Jupyter en/of Azure-notitieblokken niet hebt gebruikt, vindt u hier een paar inleidende documenten:

* **Snelstart:** [een notitieblok maken en delen](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Zelfstudie:** [Een Jupyter-notitieblok maken en uitvoeren met Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Het gebruik van Azure-notitieblokken zorgt voor een consistente omgeving voor de oefening.

> [!NOTE]
> Eenmaal ingesteld, kan de Azure Notebooks-service vanaf elke machine worden geopend. Tijdens de installatie moet u de ontwikkel-VM gebruiken, die alle bestanden heeft die u nodig hebt.

### <a name="create-an-azure-notebooks-account"></a>Een Azure Notebooks-account maken

Als u Azure-notitieblokken wilt gebruiken, moet u een account maken. Azure Notebook-accounts zijn onafhankelijk van Azure-abonnementen.

1. Navigeer naar [Azure-notitieblokken](https://notebooks.azure.com).

1. Klik **op Aanmelden** in de rechterbovenhoek van de pagina.

1. Meld u aan met uw werk- of schoolaccount (Azure Active Directory) of uw persoonlijke account (Microsoft-account).

1. Als u Azure Notebooks nog niet eerder hebt gebruikt, wordt u gevraagd om toegang te verlenen voor de Azure Notebooks-app.

1. Maak een gebruikersnaam voor Azure-notitieblokken.

### <a name="upload-jupyter-notebook-files"></a>Jupyter-notitieblokbestanden uploaden

We uploaden voorbeeldnotitieblokbestanden naar een nieuw Azure Notebooks-project.

1. Selecteer **Mijn projecten op** de gebruikerspagina van uw nieuwe account in de bovenste menubalk.

1. Voeg een nieuw project **+** toe door de knop te selecteren.

1. Geef in het dialoogvenster **Nieuw project maken** een **projectnaam op**. 

1. Laat **Openbaar** en **README** ongecontroleerd omdat het project niet openbaar hoeft te zijn of een readme hoeft te hebben.

1. Selecteer **Maken**.

1. Selecteer **Uploaden** (het pictogram pijl-omhoog) en kies **Van computer**.

1. Selecteer **Bestanden kiezen**.

1. Navigeer naar **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecteer alle bestanden in de lijst en klik op **Openen**.

1. Schakel het **vakje Ik vertrouw de inhoud van deze bestanden** in.

1. Selecteer **Uploaden** om te beginnen met uploaden en selecteer **Gereed** zodra het proces is voltooid.

### <a name="azure-notebook-files"></a>Azure-notitieblokbestanden

Bekijk de bestanden die u hebt geüpload naar uw Azure Notebooks-project. De activiteiten in dit gedeelte van de zelfstudie omvatten twee notitieblokbestanden, die een paar ondersteunende bestanden gebruiken.

* **01-turbofan\_regression.ipynb:** Dit notitieblok maakt gebruik van de Machine Learning-servicewerkruimte om een machine learning-experiment te maken en uit te voeren. In grote lijnen doet het notitieblok de volgende stappen:

  1. Downloadt gegevens van het Azure Storage-account dat is gegenereerd door het apparaatharnas.
  1. Verkent en bereidt de gegevens voor en gebruikt de gegevens om het classificatiemodel te trainen.
  1. Evalueer het model uit het experiment\_met behulp van een testgegevensset (Test FD003.txt).
  1. Publiceert het beste classificatiemodel in de Machine Learning-servicewerkruimte.

* **02-turbofan\_\_deploy model.ipynb:** Dit notitieblok neemt het model dat in het vorige notitieblok is gemaakt en gebruikt het om een containerafbeelding te maken die klaar is om te worden geïmplementeerd op een Azure IoT Edge-apparaat. Het notitieblok voert de volgende stappen uit:

  1. Hiermee maakt u een scorescript voor het model.
  1. Produceert een containerafbeelding met behulp van het classificatiemodel dat is opgeslagen in de machine learning-servicewerkruimte.
  1. Implementeert de afbeelding als een webservice op Azure Container Instance.
  1. Gebruikt de webservice om het model te valideren en het beeld werkt zoals verwacht. De gevalideerde afbeelding wordt geïmplementeerd op ons IoT Edge-apparaat in het gedeelte Van deze zelfstudie [voor IE-edge-modules maken en implementeren.](tutorial-machine-learning-edge-06-custom-modules.md)

* **Test\_FD003.txt:** Dit bestand bevat de gegevens die we zullen gebruiken als onze testset bij het valideren van onze getrainde classificatie. We kozen ervoor om de testgegevens te gebruiken, zoals voorzien voor de oorspronkelijke wedstrijd, als onze testset voor zijn eenvoud.

* **RUL\_FD003.txt:** Dit bestand bevat de resterende nuttige levensduur (RUL)\_voor de laatste cyclus van elk apparaat in het bestand Test FD003.txt. Zie de readme.txt en de Damage Propagation Modeling.pdf-bestanden in\\de\\C:\\bron\\IoTEdgeAndMlSample data Turbofan voor een gedetailleerde uitleg van de gegevens.

* **Utils.py:** Bevat een set Python-hulpprogrammafuncties voor het werken met gegevens. Het eerste notitieblok bevat een gedetailleerde uitleg van de functies.

* **README.md:** Readme beschrijft het gebruik van de notitieblokken.  

## <a name="run-azure-notebooks"></a>Azure-notitieblokken uitvoeren

Nu het project is gemaakt, u de notitieblokken uitvoeren. 

1. Selecteer op uw projectpagina **01-turbofan\_regression.ipynb**.

    ![Eerste notitieblok selecteren dat wordt uitgevoerd](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Als het notitieblok wordt vermeld als **Niet vertrouwd,** klikt u op het object **Niet vertrouwd** in de rechterbovenhoek van het notitieblok. Wanneer het dialoogvenster verschijnt, selecteert u **Vertrouwen**.

1. Voor de beste resultaten leest u de documentatie voor elke cel en voert u deze afzonderlijk uit. Selecteer **Uitvoeren** op de werkbalk. Later vindt u het opportuun om meerdere cellen uit te voeren. U upgrade- en afschrijvingswaarschuwingen negeren.

    Wanneer een cel wordt uitgevoerd, wordt een sterretje weergegeven\*tussen de vierkante haakjes ([]. Wanneer de bewerking van de cel is voltooid, wordt het sterretje vervangen door een getal en kan relevante uitvoer worden weergegeven. De cellen in een notitieblok bouwen opeenvolgend en slechts een kan worden uitgevoerd op een moment.

    U ook runopties gebruiken `Ctrl`  +  `Enter` in het menu `Shift`  +  `Enter` **Cel,** om een cel uit te voeren en een cel uit te voeren en door te gaan naar de volgende cel.

    > [!TIP]
    > Voor consistente celbewerkingen moet u voorkomen dat hetzelfde notitieblok wordt uitgevoerd vanaf meerdere tabbladen in uw browser.

1. Schrijf in de cel die volgt op de instructies **voor globale eigenschappen instellen** in de waarden voor uw Azure-abonnement, -instellingen en -bronnen. Laat dan de cel lopen.

    ![Globale eigenschappen instellen in het notitieblok](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Zoek in de cel voorafgaand aan **werkruimtedetails,** nadat deze is uitgevoerd, naar de koppeling die u opdraagt zich aan te melden om te verifiëren:

    ![Aanmeldingsprompt voor apparaatverificatie](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Open de koppeling en voer de opgegeven code in. Met deze aanmeldingsprocedure wordt het Jupyter-notitieblok geverifieerd om toegang te krijgen tot Azure-bronnen met behulp van de Microsoft Azure Cross-Platform Command Line Interface.  

    ![Toepassing verifiëren op apparaatbevestiging](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Kopieer in de cel die voorafgaat **De resultaten verkennen,** kopieer de waarde van de run-id en plak deze voor de run-id in de cel die volgt **Een run reconstrueren**.

   ![De run-id tussen cellen kopiëren](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Voer de resterende cellen in het notitieblok uit.

1. Sla het notitieblok op en keer terug naar uw projectpagina.

1. Open **\_02-turbofan\_deploy model.ipynb** en voer elke cel uit. U moet zich aanmelden om te verifiëren in de cel die volgt **werkruimte configureren.**

1. Sla het notitieblok op en keer terug naar uw projectpagina.

### <a name="verify-success"></a>Succes verifiëren

Als u wilt controleren of de notitieblokken zijn voltooid, controleert u of er een paar items zijn gemaakt.

1. Selecteer op uw projectpagina azure notebooks de optie **Verborgen items weergeven** zodat itemnamen die beginnen met een periode worden weergegeven.

1. Controleer of de volgende bestanden zijn gemaakt:

    | File | Beschrijving |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Configuratiebestand dat wordt gebruikt om de Azure Machine Learning Workspace te maken. |
    | ./aml_config/model_config.json | Configuratiebestand dat we nodig hebben om het model te implementeren in de **turbofanDemo** Machine Learning-werkruimte in Azure. |
    | myenv.yml| Biedt informatie over de afhankelijkheden voor het geïmplementeerde Machine Learning-model.|

1. Controleer of de volgende Azure-resources zijn gemaakt. Sommige resources namen worden toegevoegd met willekeurige tekens.

    | Azure-bron | Name |
    | --- | --- |
    | Machine Learning-werkruimte | turborfanDemo |
    | Container Registry | turbofandemoxxxxxxxx |
    | Inzichten in toepassingen | turbofaninsightxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxx |
    | Storage | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Foutopsporing

U Python-instructies in het notitieblok invoegen `print()` voor foutopsporing, zoals de opdracht om waarden weer te geven. Als u variabelen of objecten ziet die niet zijn gedefinieerd, voert u de cellen uit waar ze voor het eerst worden gedeclareerd of geinstantieerd.

Mogelijk moet u eerder gemaakte bestanden en Azure-bronnen verwijderen als u de notitieblokken opnieuw moet gebruiken.

## <a name="next-steps"></a>Volgende stappen

In dit artikel gebruikten we twee Jupyter-notitieblokken die in Azure-notitieblokken worden uitgevoerd om de gegevens van de turbofanapparaten te gebruiken om een resterende nuttige levensduur (RUL) te trainen, om de classificatie als model op te slaan, om een containerafbeelding te maken en om de afbeelding te implementeren en te testen als een web Service.

Ga verder naar het volgende artikel om een IoT Edge-apparaat te maken.

> [!div class="nextstepaction"]
> [Een IoT Edge-apparaat configureren](tutorial-machine-learning-edge-05-configure-edge-device.md)
