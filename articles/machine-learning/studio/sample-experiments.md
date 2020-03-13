---
title: Kickstart experimenten van voor beelden
titleSuffix: ML Studio (classic) - Azure
description: Meer informatie over het gebruik van voor beelden machine learning experimenten om nieuwe experimenten te maken met Azure AI Gallery en Azure Machine Learning Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/05/2018
ms.openlocfilehash: 1a913e714565ffb99237357e331ef5cf0189c40f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204218"
---
# <a name="create-azure-machine-learning-studio-classic-experiments-from-working-examples-in-azure-ai-gallery"></a>Azure Machine Learning Studio (klassieke) experimenten maken op basis van de voor beelden in Azure AI Gallery

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Leer hoe u met voorbeeldexperimenten uit [Azure AI Gallery](https://gallery.azure.ai/) aan de slag gaat in plaats van experimenten voor Machine Learning helemaal opnieuw te maken. U kunt de voorbeelden gebruiken om uw eigen Machine Learning-oplossing te maken.

In de galerie vindt u voor beelden van experimenten door het Microsoft Azure Machine Learning Studio-Team (klassiek) en de voor delen van de Machine Learning Community. Hier kunt u ook vragen stellen of opmerkingen plaatsen over experimenten.

Bekijk de video [Copy other people's work to do data science](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) (Werk van anderen kopiëren om gegevenswetenschap uit te voeren) uit de serie [Data Science for Beginners](data-science-for-beginners-the-5-questions-data-science-answers.md) (Gegevenswetenschap voor beginners). Deze video duurt 3 minuten.



## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Een experiment zoeken om in Azure AI Gallery te kopiëren
Als u wilt zien welke experimenten beschikbaar zijn, gaat u naar [Gallery](https://gallery.azure.ai/) en klikt u boven aan de pagina op **Experiments**.

### <a name="find-the-newest-or-most-popular-experiments"></a>De nieuwste of meest populaire experimenten zoeken
Op deze pagina ziet u **onlangs toegevoegde** experimenten, of scroll omlaag om te zien **wat populair** is of wat de nieuwste, **populaire Microsoft-experimenten** zijn.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Naar een experiment zoeken dat aan specifieke vereisten voldoet
Door alle experimenten bladeren:

1. Klik op **Alles bekijken** boven aan de pagina.
2. Selecteer aan de linkerkant onder **Refine by**, in het gedeelte **Categories**, de optie **Experiment** om alle experimenten in de galerie weer te geven.
3. U kunt op verschillende manieren experimenten vinden die aan uw vereisten voldoen:
   * **Selecteer filters aan de linkerkant.** Als u bijvoorbeeld door experimenten wilt bladeren die gebruikmaken van een PCA-algoritme voor de detectie van afwijkingen, klikt u onder **Categories** op **Experiment**. Klik vervolgens onder **Algorithms Used** op **Show All** en kies in het dialoogvenster de optie **PCA-Based Anomaly Detection**. Misschien moet u scrollen om de optie te zien.<br></br>
     ![Filters selecteren](./media/sample-experiments/choose-an-algorithm.png)
   * **Het zoekvak gebruiken.** Als u bijvoorbeeld experimenten wilt vinden die door Microsoft zijn aangedragen en die betrekking hebben op cijferherkenning waarbij gebruik wordt gemaakt van een algoritme van twee klassen voor ondersteuningsvectormachines, voert u 'digit recognition' (cijferherkenning) in het zoekvak in. Selecteer vervolgens de filters **Experiment**, **Microsoft content only** (alleen inhoud van Microsoft) en **Two-Class Support Vector Machine** (algoritme van twee klassen voor ondersteuningsvectormachines):<br></br>
     ![Het zoekvak gebruiken](./media/sample-experiments/search-for-experiments.png)
4. Klik op een experiment voor meer informatie.
5. Als u het experiment wilt uitvoeren of wijzigen, klikt u op **Openen in Studio** op de pagina van het experiment. <br></br>

    ![Voorbeeldexperiment](./media/sample-experiments/example-experiment.png)

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Een nieuw experiment maken op basis van een sjabloon
U kunt ook een nieuw experiment in Machine Learning Studio (klassiek) maken met behulp van een galerie voorbeeld als een sjabloon.

1. Meld u bij [Studio](https://studio.azureml.net) aan met uw Microsoft-accountreferenties en klik vervolgens op **New** om een nieuw experiment te maken.
2. Blader door de voorbeelden en klik op een van de voorbeelden.

Er wordt een nieuw experiment gemaakt in de werk ruimte Machine Learning Studio (klassiek) met behulp van het voorbeeld experiment als sjabloon.

## <a name="next-steps"></a>Volgende stappen
* [Gegevens importeren uit diverse bronnen](import-data.md)
* [Snelstartzelfstudie voor de programmeertaal R in Machine Learning](r-quickstart.md)
* [Een Machine Learning-webservice implementeren](deploy-a-machine-learning-web-service.md)
