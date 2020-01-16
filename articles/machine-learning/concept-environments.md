---
title: Over Azure Machine Learning omgevingen
titleSuffix: Azure Machine Learning
description: In dit artikel vindt u meer informatie over de voor delen van machine learning omgevingen, waarmee u reproduceer bare, audit bare en Portable machine learning afhankelijkheids definities kunt gebruiken in verschillende reken doelen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 8906299cc9e2c000dab2ac9d2a345d9aaf238260
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045859"
---
# <a name="what-are-azure-machine-learning-environments"></a>Wat zijn Azure Machine Learning omgevingen?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In Azure Machine Learning omgevingen worden de Python-pakketten, omgevings variabelen en software-instellingen voor uw trainings-en Score scripts opgegeven. Ze specificeren ook uitvoerings tijden (python, Spark of docker). Deze entiteiten worden beheerd en genoteerd in uw Machine Learning-werk ruimte, waarmee reproduceer bare, audit bare en Portable machine learning werk stromen kunnen worden toegepast op diverse reken doelen.

U kunt een `Environment`-object op uw lokale Compute gebruiken om het volgende te doen:
* Ontwikkel uw trainings script.
* Gebruik dezelfde omgeving op Azure Machine Learning Compute for model-training op schaal.
* Implementeer uw model met diezelfde omgeving.

In het volgende diagram ziet u hoe u een enkel `Environment`-object kunt gebruiken in zowel uw uitvoerings configuratie, voor training, als uw detrain-en implementatie configuratie voor webservice-implementaties.

![Diagram van een omgeving in machine learning werk stroom](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Typen omgevingen

Omgevingen kunnen breed worden onderverdeeld in drie categorieën: met *curator*, door de *gebruiker beheerd*en door *het systeem beheerd*.

Gehoste omgevingen worden verzorgd door Azure Machine Learning en zijn standaard beschikbaar in uw werk ruimte. Ze bevatten verzamelingen van Python-pakketten en instellingen waarmee u aan de slag kunt met diverse machine learning frameworks. 

In door gebruikers beheerde omgevingen bent u verantwoordelijk voor het instellen van uw omgeving en het installeren van elk pakket dat uw trainings script nodig heeft voor het Compute-doel. Conda controleert uw omgeving niet en installeert niets voor u. Als u uw eigen omgeving definieert, moet u `azureml-defaults` met versie `>= 1.0.45` als een PIP-afhankelijkheid vermelden. Dit pakket bevat de functionaliteit die nodig is om het model als een webservice te hosten.

U gebruikt door het systeem beheerde omgevingen als u wilt dat [Conda](https://conda.io/docs/) de python-omgeving en de script afhankelijkheden voor u beheert. De service gaat ervan uit dat type omgeving standaard, vanwege de bruikbaarheid op externe Compute-doelen die niet hand matig kunnen worden geconfigureerd.

## <a name="create-and-manage-environments"></a>Omgevingen maken en beheren

U kunt omgevingen maken op:

* Het definiëren van nieuwe `Environment`-objecten, hetzij door gebruik te maken van een netwerk met een curator of het definiëren van uw eigen afhankelijkheden.
* Bestaande `Environment`-objecten uit uw werk ruimte gebruiken. Deze benadering maakt consistentie en reproduceer baarheid mogelijk met uw afhankelijkheden.
* Importeren uit een bestaande Anaconda-omgevings definitie.
* De Azure Machine Learning CLI gebruiken

Zie voor voor beelden van specifieke code de sectie ' een omgeving maken ' voor het [hergebruik van omgevingen voor training en implementatie](how-to-use-environments.md#create-an-environment). Omgevingen worden ook eenvoudig beheerd via uw werk ruimte. Ze bevatten de volgende functionaliteit:

* Omgevingen worden automatisch bij uw werk ruimte geregistreerd wanneer u een experiment verzendt. Ze kunnen ook hand matig worden geregistreerd.
* U kunt in uw werk ruimte omgevingen ophalen die u wilt gebruiken voor training of implementatie, of om wijzigingen aan te brengen in de omgevings definitie.
* Met versie beheer kunt u in de loop der tijd wijzigingen in uw omgevingen zien, waardoor de reproduceer baarheid gewaarborgd is.
* U kunt docker-installatie kopieën automatisch bouwen vanuit uw omgevingen.

Voor code voorbeelden, zie de sectie omgevingen beheren van [omgevingen hergebruiken voor training en implementatie](how-to-use-environments.md#manage-environments).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken en gebruiken van omgevingen](how-to-use-environments.md) in azure machine learning.
* Raadpleeg de documentatie van python SDK voor de [omgevings klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Raadpleeg de naslag documentatie over R SDK voor [omgevingen](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
