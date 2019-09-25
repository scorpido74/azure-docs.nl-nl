---
title: Visual Studio code gebruiken voor machine learning
titleSuffix: Azure Machine Learning
description: Meer informatie over het installeren van Azure Machine Learning voor Visual Studio code en het maken van een experiment in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jimgries
author: greazer
ms.date: 09/20/2019
ms.custom: seodec18
ms.openlocfilehash: a93c71a97cdb1f6296919a248cf7ef545f7b307f
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269239"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Aan de slag met Azure Machine Learning voor Visual Studio code

In dit artikel leert u hoe u de **Azure machine learning voor Visual Studio code** extension kunt gebruiken om machine learning modellen te trainen en te implementeren.

De [Azure machine learning-service](overview-what-is-azure-ml.md) stroomlijnt het bouwen, trainen en implementeren van machine learning modellen.
+ Voor trainingen biedt het ondersteuning voor het lokaal of extern uitvoeren van experimenten. Voor elk experiment kunt u aangepaste metrische gegevens van meerdere uitvoeringen vastleggen om Hyper parameters te verfijnen
+ U kunt ook de Azure Machine Learning-service gebruiken om gemakkelijk machine learning modellen te implementeren voor uw test-en productie behoeften.

## <a name="prerequisites"></a>Vereisten

+ Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

+ Installeer [Visual Studio code](https://code.visualstudio.com/docs/setup/setup-overview), een licht gewicht code-editor die wordt uitgevoerd op Windows, Mac en Linux.

+ [Installeer Python 3,5 of hoger](https://www.anaconda.com/download/).


## <a name="install-the-extension"></a>De extensie installeren

Wanneer u de uitbrei ding Azure Machine Learning installeert, worden er automatisch nog twee extensies geïnstalleerd. Ze zijn de [Azure-account extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) en de [micro soft python-extensie](https://marketplace.visualstudio.com/items?itemName=ms-Python.Python). Voor meer informatie over het gebruik van de python-extensie voor het bewerken, uitvoeren en fout opsporing van python-code raadpleegt u de [python Hello-wereld zelf studie](https://code.visualstudio.com/docs/Python/Python-tutorial).

De Azure Machine Learning-extensie installeren:

1. Open Visual Studio Code.

1. Ga naar het tabblad Extensies en zoek naar "Azure Machine Learning".

1. Selecteer **installeren**op het tabblad uitbrei ding.

1. Er wordt een welkomst tabblad voor de uitbrei ding geopend in Visual Studio code en het Azure-symbool (rood gemarkeerd in de volgende scherm afbeelding) wordt toegevoegd aan de activiteiten balk.

   ![Azure-pictogram op de activiteiten balk van Visual Studio](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. Selecteer **Aanmelden** in het dialoog venster en volg de prompts om te verifiëren met Azure.

   De extensie van het Azure-account, dat samen met de Azure Machine Learning voor Visual Studio code-extensie is geïnstalleerd, helpt u bij het verifiëren van uw Azure-account. Zie de pagina voor de [Azure-account extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)voor een lijst met opdrachten.

> [!TIP]
> U kunt het installatie programma voor extensies ook rechtstreeks downloaden van [Azure machine learning voor Visual Studio code extension (preview)](https://aka.ms/vscodetoolsforai).

## <a name="quickstart-with-azure-machine-learning"></a>Snelstartgids met Azure Machine Learning
Er zijn meerdere manieren waarop u uw trainings scripts kunt uitvoeren met behulp van de Azure Machine Learning-service. Als u net aan de slag gaat, laten we eerst zien hoe u snel een trainings script kunt indienen om uit te voeren in Azure.

Als u al even vertrouwd bent met de Azure Machine Learning-concepten en meer informatie wilt over het beheren en gebruiken van de uitbrei ding, raadpleegt u [Azure machine learning uitgebreid met VS-code](./how-to-vscode-tools.md#azure-machine-learning-in-depth-with-vs-code) .

## <a name="run-an-existing-python-training-script-in-azure"></a>Een bestaand python-trainings script uitvoeren in azure
Als u een bestaand trainings script hebt, is de uitbrei ding van de Azure Machine Learning voor VS code niet alleen van toepassing op een uitstekende bewerkings-, fout opsporings-en bron beheer ervaring, maar u kunt ook eenvoudig metrische gegevens voor uw script in azure uitvoeren en opslaan.

Aan de slag. U kunt uw eigen trainings script gebruiken als u het hebt voor bereid, of de voor beeld- [vscode-hulpprogram ma's-voor-AI opslag plaats](https://github.com/microsoft/vscode-tools-for-ai)klonen. Dit is de open bare opslag plaats voor het archiveren van problemen met deze extensie. Het bevat ook een kleine **mnist** -voorbeeld map die we voor dit voor beeld gebruiken.

1. Open de map **mnist** in VS code.

1. Maak een nieuwe python-omgeving met behulp van uw favoriete virtuele omgevings pakket of Anaconda en installeer de tensor flow-en numpy-pakketten.

1. Selecteer de nieuwe omgeving die u hebt gemaakt als de Python-interpreter in de linkerbenedenhoek van de status balk VS-code.

1. Open **Train.py** en voer deze uit door het fout opsporingsprogramma te openen en op de knop uitvoeren te drukken (of druk op F5).

   [![MNIST-training uitvoeren](./media/vscode-tools-for-ai/run-mnist.gif)](./media/vscode-tools-for-ai/run-mnist.gif#lightbox)

Als alles correct is geïnstalleerd, wordt het script uitgevoerd en wordt er een tensor flow-model in de map outputs gemaakt.

[![Tensor flow model weer geven](./media/vscode-tools-for-ai/show-tensorflow-model.gif)](./media/vscode-tools-for-ai/show-tensorflow-model.gif#lightbox)

Nu u weet dat uw script correct wordt uitgevoerd, kunt u het uitvoeren in Azure.

Dit kan eenvoudig worden gedaan zonder extra aanpassing van **Train.py**. Met slechts enkele eenvoudige wijzigingen kunt u Azure Machine Learning gebruiken om automatisch belang rijke metrische gegevens van uw keuze over de uitvoering van elke training bij te houden.

### <a name="make-azure-aware-of-your-run-metrics"></a>Zorg dat u Azure weet wat uw uitvoerings metrieken zijn
Om uw project te wijzigen zodat Azure op de hoogte kan worden gesteld van belang rijke informatie in uw uitvoeringen:

1. Maak een bestand met de naam **amlrun.py** in dezelfde map als **Train.py**

    ```Python
    import azureml
    from azureml.core import Run

    # access the Azure ML run
    # init run param to check if running within AML
    def get_AMLRun():
        try:
            run = Run.get_submitted_run()
            return run
        except Exception as e:
            print("Caught = {}".format(e.message))
            return None
    ```

2. Importeer het amlrun-bestand in **Train.py**

    ```Python
    ...
    from utils import prepare_data
    from amlrun import get_AMLRun
    ...
    ```
3. Initialiseer het run-object in **Train.py**

    ```Python
    ...
    init = tf.global_variables_initializer()
    saver = tf.train.Saver()
    run = get_AMLRun()
    ...
    ```
4. Metrische gegevens vastleggen in azure met de functie run. log ():

    ```Python
    ...
            acc_val = acc_op.eval(feed_dict = {X: X_test, y: y_test})

            # log accuracies to AML logger if using AML
            if run != None:
                run.log('Validation Accuracy', np.float(acc_val))
                run.log('Training Accuracy', np.float(acc_train))

            print(epoch, '-- Training accuracy:', acc_train, '\b Validation
    ...
    ```
### <a name="run-the-script-in-azure"></a>Het script uitvoeren in azure
Dat is alles. Gebruik nu alleen de extensie om uw script uit te voeren in de Cloud. De volgende scenario video neemt de vrijheid van het comprimeren van de tijd die nodig is om een nieuwe Azure ML-werk ruimte en-berekeningen te maken, evenals de tijd die nodig is om het trainings script uit te voeren.

   [![Een Azure ML-experiment starten](./media/vscode-tools-for-ai/start-golden-path.gif)](./media/vscode-tools-for-ai/start-golden-path.gif#lightbox)

Nadat u op de knop experiment uitvoeren hebt geklikt, beantwoordt u de prompts als volgt:

1. Kies uw Azure-abonnement
1. Kiezen voor het maken van een *nieuwe* Azure ml-werk ruimte
1. Kies uit een reeks vooraf geconfigureerde sjablonen om de python-omgeving voor de uitvoeringen te initialiseren. De sjablonen bieden een start punt en bevatten instellingen voor:
    1. **PyTorch**, **tensor flow**of **Scikit-leer**
    1. **Enkele** of **gedistribueerde** Compute-training
    1. **Algemeen** voor aangepaste omgevingen
1. Zorg ervoor dat de lijst met PIP-en Conda-pakketten is voltooid voor uw script door pakketten toe te voegen die niet zijn opgenomen in de sjabloon.
1. Bekijk de standaard namen en-specificaties voor de uitvoering van het experiment en klik op de koppeling **verzenden experiment** in het JSON-bestand. Het JSON-bestand wordt niet opgeslagen, maar dit is alleen mogelijk als u de instellingen voor het experiment wilt controleren of wijzigen voordat u het document instuurt.
1. U kunt terug en even horen terwijl de uitbrei ding alles voor u instelt en uw script uitvoert.

    [![Trainen in Cloud](./media/vscode-tools-for-ai/run-golden-path.gif)](./media/vscode-tools-for-ai/run-golden-path.gif#lightbox)

Over een paar seconden wordt u gewaarschuwd dat het experiment is verzonden naar Azure, op welk moment u de voortgang kunt bekijken in de Azure Portal door te klikken op de koppeling **experiment-uitvoering weer geven** in de melding over de VS-code of in VS code door te vernieuwen op het tabblad Azure.

Op het moment wordt het weer geven van metrische uitvoerings gegevens alleen ondersteund in de Azure Portal. Met de koppeling voor het uitvoeren van het **experiment weer geven** hierboven gaat u naar de uitvoering waar u de metrische gegevens ziet die u hebt geregistreerd.
[![Experiment uitvoeren in de portal](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG)](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG#lightbox)

## <a name="azure-machine-learning-in-depth-with-vs-code"></a>Azure Machine Learning diep gaande met VS-code

In het bovenstaande scenario hebben we een experiment gestuurd volgens het eenvoudigste pad. Als u de uitbrei ding wellicht hebt gezien, worden de stappen die u moet beheren, geminimaliseerd om een experiment uit te voeren. In deze sectie wordt beschreven hoe u alle Azure Machine Learning-concepten afzonderlijk kunt beheren, waardoor u Maxi maal beheer hebt.

Voordat u begint met het trainen en implementeren van machine learning modellen in Visual Studio code, moet u een [Azure machine learning-werk ruimte](concept-workspace.md) maken in de Cloud. Deze werk ruimte bevat uw modellen en resources.

### <a name="create-a-workspace"></a>Een werkruimte maken

1. Op de activiteiten balk van Visual Studio, selecteert u het pictogram van Azure. De Azure Machine Learning zijbalk wordt weer gegeven.

    [![Een werk ruimte maken](./media/vscode-tools-for-ai/create-workspace.gif)](./media/vscode-tools-for-ai/create-workspace.gif#lightbox)


1. Klik met de rechter muisknop op uw Azure-abonnement en selecteer **werk ruimte maken**. Er wordt standaard een naam gegenereerd met de datum en tijd van het maken. Wijzig de naam in **TeamWorkspace** en druk op ENTER.

1. Selecteer een resource groep in de lijst als u wilt dat u een nieuwe selecteert of maakt. Als u een nieuw account maakt, kiest u een locatie die zich het dichtst bij de locatie bevindt die u wilt implementeren van uw model. In dit geval kiest u **VS West 2**.

1. Nadat u op ENTER hebt gedrukt, ontvangt Azure Machine Learning de aanvraag om uw werk ruimte te maken. U wordt op de hoogte gesteld van het proces in het systeemvak voor Visual Studio-code.

1. Vouw het knoop punt abonnement uit om de zojuist gemaakte werk ruimte te vinden.

### <a name="create-an-experiment"></a>Een experiment maken
Een of meer experimenten kunnen worden gemaakt in uw werk ruimte om de uitvoering van afzonderlijke model trainingen bij te houden en te analyseren. Uitvoeringen kunnen worden uitgevoerd in de Azure-Cloud of op uw lokale machine.

1. Vouw de **TeamWorkspace** -werk ruimte uit. Klik met de rechter muisknop op het knoop punt **experimenten** en kies **experiment maken** in het context menu.

1. Voer in de prompt een naam in voor uw experiment. In de scherm afbeeldingen voor beeld wordt het experiment **MNIST**genoemd.

1. Selecteer ENTER om het nieuwe experiment te maken. Het nieuwe experiment wordt weer gegeven in de boom structuur als onderliggend element van het knoop punt **experimenten** .

1. In een werk ruimte kunt u met de rechter muisknop op een experiment klikken om dit als het **actieve** experiment in te stellen. Het **actieve** experiment bevat koppelingen waarmee in de Cloud wordt geexperimenteerd naar de map die u momenteel hebt geopend in Visual Studio code. Deze map moet uw lokale python-scripts bevatten. Door een actief experiment in te stellen, worden belang rijke metrische gegevens voor alle trainings runs opgeslagen in het experiment, ongeacht waar ze worden uitgevoerd.

    [![Een experiment maken](./media/vscode-tools-for-ai/create-experiment.gif)](./media/vscode-tools-for-ai/create-experiment.gif#lightbox)


### <a name="create-and-manage-compute-targets"></a>Maken en beheren van compute-doelen

Met Azure Machine Learning voor Visual Studio code kunt u uw gegevens voorbereiden, modellen trainen en deze zowel lokaal als op externe Compute-doelen implementeren.

De extensie ondersteunt diverse externe Compute-doelen voor Azure Machine Learning. Zie de volledige lijst met ondersteunde COMPUTE- [doelen voor Azure machine learning](how-to-set-up-training-targets.md)voor meer informatie.

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Reken doelen voor Azure Machine Learning maken in Visual Studio code

Een reken doel maken:

1. Op de activiteiten balk van Visual Studio, selecteert u het pictogram van Azure. De Azure Machine Learning zijbalk wordt weer gegeven.

1. Vouw in de structuur weergave uw Azure-abonnement en Azure Machine Learning-werk ruimte uit.

1. Onder het werkruimteknooppunt met de rechtermuisknop op de **Compute** knooppunt en kies **maken Compute**.

1. Kies het doeltype compute in de lijst.

1. Selecteer de grootte van een virtuele machine in de opdracht prompt. U kunt de berekeningen filteren met tekst, zoals ' GPU '.

1. Voer in het opdracht palet prompt een naam in voor het berekenings doel.

1. Nadat u de naam hebt ingevoerd, wordt de berekening gemaakt met behulp van de standaard parameters. Als u de para meters wilt wijzigen, klikt u met de rechter muisknop op de nieuwe Compute en kiest u **Compute bewerken**.

1. Breng de gewenste wijzigingen aan in de JSON die wordt weer gegeven en klik vervolgens op ' opslaan en door gaan ' code lens (met behulp van het toetsen bord kunt u op **CTRL + SHIFT + p** drukken om het opdracht palet aan te roepen en **Azure ml uit te voeren: Opslaan en door** gaan opdracht)

Hier volgt een voor beeld van het maken en bewerken van een Azure Machine Learning Compute (AMLCompute):

[![AML-berekeningen maken in Visual Studio code](./media/vscode-tools-for-ai/create-remote-compute.gif)](./media/vscode-tools-for-ai/create-remote-compute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Het configuratie bestand voor de uitvoering

Als u een Azure Machine Learning experiment wilt uitvoeren op een compute, moet u de reken kracht op de juiste wijze configureren. Een configuratie bestand voor uitvoering is het mechanisme waarmee deze omgeving is opgegeven.

Hier volgt een voor beeld van het maken van een uitvoerings configuratie voor de AmlCompute die hierboven is gemaakt.

[![Een uitvoerings configuratie maken voor een compute](./media/vscode-tools-for-ai/create-runconfig.gif)](./media/vscode-tools-for-ai/create-runconfig.gif#lightbox)

Als u Azure ML experimenten op uw lokale computer wilt uitvoeren, moet u een configuratie bestand voor uitvoering nog steeds gebruiken. Wanneer u een lokale uitvoerings configuratie maakt, wordt de python-omgeving die wordt gebruikt, standaard ingesteld op het pad naar de interpreter die u hebt opgegeven in VS code.

### <a name="train-and-tune-models"></a>Modellen trainen en afstemmen

Met de uitbrei ding voor Azure ML van VS code zijn er meerdere manieren om een trainings script uit te voeren in een experiment.

1. Klik met de rechter muisknop op het trainings **script en kies Azure ml: Als experiment uitvoeren in azure**
1. Klik op het pictogram van de werk balk experiment uitvoeren.
1. Klik met de rechter muisknop op een knoop punt voor het uitvoeren van een configuratie.
1. Gebruik het opdracht palet VS code voor het **uitvoeren van Azure ml: Experiment uitvoeren**

Een Azure Machine Learning experiment uitvoeren:

1. Op de activiteiten balk van Visual Studio, selecteert u het pictogram van Azure.

1. Vouw in de structuur weergave uw Azure-abonnement en Azure Machine Learning-werk ruimte uit.

1. Vouw het knoop punt **experimenten** onder het knoop punt werk ruimte uit en klik met de rechter muisknop op het experiment dat u wilt uitvoeren.

1. Selecteer **Experiment uit te voeren**.

1. Kies de naam van het python-bestand dat u wilt uitvoeren om het model te trainen en druk op ENTER om de uitvoering te verzenden. Opmerking: Het gekozen bestand moet zich bevinden in de map die momenteel is geopend in VS code.

1. Nadat de uitvoering is ingediend, wordt er een **knoop punt uitvoeren** weer gegeven onder het experiment dat u hebt gekozen. Gebruik dit knoop punt om de status van uw uitvoeringen te controleren. Opmerking: Het kan nodig zijn om het venster regel matig te vernieuwen om de meest recente status weer te geven.

Hier volgt een voor beeld van hoe u een experiment uitvoert op de eerder gemaakte comput:

[![Een lokaal experiment uitvoeren](./media/vscode-tools-for-ai/run-experiment.gif)](./media/vscode-tools-for-ai/run-experiment.gif#lightbox)

### <a name="deploy-and-manage-models"></a>Modellen implementeren en beheren
In Azure Machine Learning kunt u uw machine learning modellen implementeren en beheren in de Cloud en aan de rand.

#### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Uw model registreren bij Azure Machine Learning van Visual Studio code

Nu u uw model hebt getraind, kunt u dit registreren in uw werk ruimte. U kunt geregistreerde modellen volgen en implementeren.

Uw model registreren:

1. Op de activiteiten balk van Visual Studio, selecteert u het pictogram van Azure. De Azure Machine Learning zijbalk wordt weer gegeven.

1. Vouw in de structuur weergave uw Azure-abonnement en Azure Machine Learning-werk ruimte uit.

1. Onder het werkruimteknooppunt met de rechtermuisknop op **modellen** en kies **Model registreren**.

1. Voer in het opdracht palet in het veld een model naam in.

1. Kies in de lijst of u een **model bestand** (voor één model) of een **modelsubsite** (voor modellen met meerdere bestanden, zoals tensor flow) wilt uploaden.

1. Selecteer uw map of bestand.

1. Wanneer u klaar bent met het configureren van de model eigenschappen, selecteert u in de rechter benedenhoek van het venster **verzenden**.

Hier volgt een voor beeld van het registreren van uw model bij Azure Machine Learning:

[![Een model registreren voor AML](./media/vscode-tools-for-ai/register-model.gif)](./media/vscode-tools-for-ai/register-model.gif#lightbox)


#### <a name="deploy-your-service-from-visual-studio-code"></a>Implementeer uw service vanuit Visual Studio code

In Visual Studio code kunt u uw webservice implementeren voor het volgende:
+ Azure Container Instances (ACI) voor het testen.
+ Azure Kubernetes service (AKS) voor productie.

U hoeft geen ACI-container te maken om vooraf te testen, omdat ACI-containers zo nodig worden gemaakt. U moet echter wel AKS-clusters vooraf configureren. Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie.

Een webservice implementeren:

1. Op de activiteiten balk van Visual Studio, selecteert u het pictogram van Azure. De Azure Machine Learning zijbalk wordt weer gegeven.

1. Vouw uw Azure-abonnement en uw Azure Machine Learning-werk ruimte uit in de structuur weergave.

1. Vouw onder het werkruimteknooppunt, de **modellen** knooppunt.

1. Klik met de rechter muisknop op het model dat u wilt implementeren en kies **service implementeren vanuit geregistreerd model** in het context menu.

1. Kies in het opdracht palet het berekenings doel waarnaar u wilt implementeren.

1. Voer in het opdracht palet in het veld een naam in voor deze service.

1. Selecteer in het opdracht palet de Enter-toets op het toetsen bord om het script bestand te zoeken en te selecteren.

1. Selecteer in het opdracht palet de Enter-toets op het toetsen bord om naar het Conda-afhankelijkheids bestand te bladeren en dit te selecteren.

1. Wanneer u klaar bent met het configureren van de service-eigenschappen, selecteert u in de rechter benedenhoek van het venster **verzenden** om te implementeren. In het eigenschappen bestand van de service kunt u een lokaal docker-bestand of een schema. JSON-bestand opgeven.

De webservice is nu geïmplementeerd.

Hier volgt een voor beeld van hoe u een webservice implementeert:

[![Een webservice implementeren](./media/vscode-tools-for-ai/create-image.gif)](./media/vscode-tools-for-ai/create-image.gif#lightbox)

### <a name="experiment-with-additional-features"></a>Experimenteren met aanvullende functies

U kunt het opdracht palet gebruiken om toegang te krijgen tot veel Azure Machine Learning functies in Visual Studio code. Om het opdracht palet type CTRL + SHIFT + P aan te roepen. Hier kunt u zoeken naar aanvullende Azure ML-functies van de uitbrei ding.

[![Sneltoetsen voor de Azure Machine Learning voor Visual Studio code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie voor een overzicht van het trainen van Azure machine learning buiten Visual Studio code: Train modellen met Azure Machine Learning](tutorial-train-models-with-aml.md).
* Zie de zelf studie over het gebruik van de [python Hello-wereld](https://code.visualstudio.com/docs/Python/Python-tutorial)voor een overzicht van het lokaal bewerken, uitvoeren en fout opsporing van code.
