---
title: 'Zelfstudie: De visual studiocode-extensie instellen'
titleSuffix: Azure Machine Learning
description: Meer informatie over het instellen van de Azure Machine Learning-extensie van Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 02/24/2020
ms.openlocfilehash: 583071ee22e4fb9cffc741520b1583790002a5bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77604857"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Azure Machine Learning Visual Studio Code-extensie instellen

Meer informatie over het installeren en uitvoeren van scripts met de Azure Machine Learning Visual Studio Code-extensie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De Azure Machine Learning Visual Studio Code-extensie installeren
> * Aanmelden bij uw Azure-account vanuit Visual Studio Code
> * De Azure Machine Learning-extensie gebruiken om een voorbeeldscript uit te voeren

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement. Als u er geen hebt, meldt u zich aan om de gratis of betaalde versie van Azure Machine Learning uit te [proberen.](https://aka.ms/AMLFree)
- Visual Studio Code. Als je het niet hebt, [installeer het](https://code.visualstudio.com/docs/setup/setup-overview)dan.
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>De extensie installeren

1. Open Visual Studio Code.
1. Selecteer het pictogram **Extensies** in de **activiteitsbalk** om de weergave Extensies te openen.
1. Zoek in de weergave Extensies naar 'Azure Machine Learning'.
1. Selecteer **Installeren**.

    > [!div class="mx-imgBorder"]
    > ![Azure Machine Learning VS-codeextensie installeren](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> U de Azure Machine Learning-extensie ook installeren via de Visual Studio Marketplace door [het installatieprogramma rechtstreeks te downloaden.](https://aka.ms/vscodetoolsforai) 

De rest van de stappen in deze tutorial zijn getest met **versie 0.6.8** van de extensie.

## <a name="sign-in-to-your-azure-account"></a>Aanmelden bij uw Azure-account

Als u resources wilt inrichten en workloads op Azure wilt uitvoeren, moet u zich aanmelden met uw Azure-accountreferenties. Azure Machine Learning installeert automatisch de Azure-accountextensie om u te helpen bij accountbeheer. Ga naar de volgende site voor [meer informatie over de Azure-accountextensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Open het opdrachtpalet door **Weergave > opdrachtpalet** te selecteren op de menubalk. 
1. Voer de opdracht 'Azure: Aanmelden' in het opdrachtpalet in om het aanmeldingsproces te starten.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Een machine learning-modeltrainingsscript uitvoeren in Azure

Nu u zich met uw accountgegevens bij Azure hebt aangemeld, gebruikt u de stappen in deze sectie om te leren hoe u de extensie gebruiken om een machine learning-model te trainen.

1. Download en rits de [VS Code Tools for AI repository](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) overal op uw computer.
1. Open `mnist-vscode-docs-sample` de map in Visual Studio Code.
1. Selecteer het **Azure-pictogram** in de activiteitsbalk.
1. Selecteer het pictogram **Experiment uitvoeren** boven aan de Azure Machine Learning-weergave.

    > [!div class="mx-imgBorder"]
    > ![Experiment uitvoeren](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Wanneer het opdrachtpalet wordt uitgebreid, volgt u de aanwijzingen.

    1. Selecteer uw Azure-abonnement.
    1. Een **nieuwe Azure ML-werkruimte maken selecteren**
    1. Selecteer het taaktype **TensorFlow Single-Node Training.**
    1. Voer `train.py` in als het script om te trainen. Dit is het bestand dat code bevat voor een machine learning-model dat afbeeldingen van handgeschreven cijfers categoriseert.
    1. Geef de volgende pakketten op als vereisten die moeten worden uitgevoerd.

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. Op dit punt verschijnt een configuratiebestand dat vergelijkbaar is met het onderstaande bestand in de teksteditor. De configuratie bevat de informatie die nodig is om de trainingstaak uit te voeren, zoals het bestand dat de code bevat om het model te trainen en eventuele Python-afhankelijkheden die in de vorige stap zijn opgegeven.

    ```json
    {
        "workspace": "WS01311608",
        "resourceGroup": "WS01311608-rg1",
        "location": "South Central US",
        "experiment": "WS01311608-exp1",
        "compute": {
            "name": "WS01311608-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS01311608-com1-rc1",
            "condaDependencies": [
                "python=3.6.2",
                "tensorflow=1.15.0"
            ],
            "pipDependencies": [
                "azureml-defaults"
            ]
        }
    }
    ```

1. Zodra u tevreden bent met uw configuratie, dient u uw experiment in door het opdrachtpalet te openen en de volgende opdracht in te voeren:

    ```text
    Azure ML: Submit Experiment
    ```

    Hiermee wordt `train.py` het configuratiebestand en het configuratiebestand naar uw Azure Machine Learning-werkruimte verzonden. De trainingstaak wordt vervolgens gestart op een compute resource in Azure.

### <a name="track-the-progress-of-the-training-script"></a>De voortgang van het trainingsscript bijhouden

Het uitvoeren van uw script kan enkele minuten duren. Ga als volgt te werk om de voortgang ervan bij te houden:

1. Selecteer het **Azure-pictogram** op de activiteitenbalk.
1. Vouw uw abonnementsknooppunt uit.
1. Het knooppunt van het momenteel draaiende experiment uitbreiden. Dit bevindt `{workspace}/Experiments/{experiment}` zich in het knooppunt waar de waarden voor uw werkruimte en experiment dezelfde zijn als de eigenschappen die in het configuratiebestand zijn gedefinieerd.
1. Alle runs voor het experiment worden vermeld, evenals hun status. Als u de meest recente status wilt krijgen, klikt u op het vernieuwingspictogram boven aan de Azure Machine Learning-weergave.

    > [!div class="mx-imgBorder"]
    > ![Voortgang van het experiment bijhouden](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Download het getrainde model

Wanneer de experimentrun is voltooid, is de uitvoer een getraind model. Ga als een abonnement op de lokale downloadresultaten:

1. Klik met de rechtermuisknop op de meest recente run en selecteer **Uitvoer downloaden**.

    > [!div class="mx-imgBorder"]
    > ![Getraind model downloaden](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Selecteer een locatie waar u de uitvoer wilt opslaan.
1. Een map met de naam van uw run wordt lokaal gedownload. Navigeer naar die pagina.
1. De modelbestanden bevinden `outputs/outputs/model` zich in de map.

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Train en implementeer een TensorFlow-model voor afbeeldingsclassificatie met behulp van de Azure Machine Learning Visual Studio Code Extension](tutorial-train-deploy-image-classification-model-vscode.md).
