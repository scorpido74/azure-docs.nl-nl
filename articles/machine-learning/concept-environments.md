---
title: Wat zijn ML-omgevingen?
titleSuffix: Azure Machine Learning
description: In dit artikel vindt u meer informatie over de voor delen van machine learning omgevingen, waarmee u reproduceer bare, audit bare en Portable machine learning afhankelijkheids definities voor verschillende reken doelen kunt gebruiken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 3216248943ccc0dba788816cdba38732f9e43e14
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930751"
---
# <a name="what-are-azure-machine-learning-environments"></a>Wat zijn Azure Machine Learning omgevingen?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In omgevingen worden de Python-pakketten, omgevings variabelen en software-instellingen voor uw trainings-en Score scripts en uitvoerings tijden (python, Spark of docker) opgegeven. Ze zijn beheerde en geversiede entiteiten in uw Azure Machine Learning-werk ruimte waarmee reproduceer bare, audit bare en Portable machine learning werk stromen kunnen worden toegepast op verschillende Compute-doelen.

U kunt een omgevings object gebruiken op uw lokale Compute voor het ontwikkelen van uw trainings script, het hergebruiken van dezelfde omgeving op Azure Machine Learning Compute for model-training op schaal en het implementeren van uw model zelfs met diezelfde omgeving.

In het volgende voor beeld ziet u dat hetzelfde omgevings object kan worden gebruikt in zowel de configuratie voor training als voor de implementatie van de webservice.

![Diagram van de omgeving in machine learning werk stroom](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Typen omgevingen

Omgevingen kunnen breed worden onderverdeeld in drie categorieën: met **curator**, door de **gebruiker beheerd** en door **het systeem beheerd**.

Gehoste omgevingen worden verzorgd door Azure Machine Learning en zijn standaard beschikbaar in uw werk ruimte. Ze bevatten verzamelingen van Python-pakketten en instellingen waarmee u verschillende machine learning Frameworks kunt starten. 

Voor een door een gebruiker beheerde omgeving bent u verantwoordelijk voor het instellen van uw omgeving en het installeren van elk pakket dat nodig is voor uw trainings script voor het Compute-doel. Conda controleert uw omgeving niet en installeert niets voor u. Houd er rekening mee dat als u uw eigen omgeving definieert, u `azureml-defaults` moet vermelden met versie `>= 1.0.45` als een PIP-afhankelijkheid. Dit pakket bevat de functionaliteit die nodig is om het model als een webservice te hosten.

Door systeem beheerde omgevingen worden gebruikt wanneer u wilt dat [Conda](https://conda.io/docs/) de python-omgeving en de script afhankelijkheden voor u beheert. De service gaat ervan uit dat type omgeving standaard, vanwege de bruikbaarheid op externe Compute-doelen die niet hand matig kunnen worden geconfigureerd.

## <a name="creating-and-managing-environments"></a>Omgevingen maken en beheren

Omgevingen kunnen worden gemaakt door:

* Het definiëren van nieuwe `Environment`-objecten, het gebruik van een gecuratore omgeving of het definiëren van uw eigen afhankelijkheden
* Bestaande `Environment`-objecten uit uw werk ruimte gebruiken. Dit maakt consistentie en reproduceer baarheid mogelijk met uw afhankelijkheden
* Importeren uit een bestaande Anaconda-omgevings definitie.
* De Azure Machine Learning CLI gebruiken

Zie de [voor beelden voor specifieke](how-to-use-environments.md#create-an-environment) code. Omgevingen worden ook eenvoudig beheerd via uw werk ruimte en bevatten de volgende functionaliteit:

* Omgevingen worden automatisch bij uw werk ruimte geregistreerd wanneer u een experiment verzendt. Ze kunnen ook hand matig worden geregistreerd
* Omgevingen ophalen uit uw werk ruimte en deze gebruiken voor het trainen, implementeren of het maken van wijzigingen in de omgevings definitie
* Met versie beheer kunt u de wijzigingen in uw omgevingen na verloop van tijd bekijken en de reproduceer baarheid garanderen
* Docker-installatie kopieën automatisch bouwen vanuit uw omgevingen

Zie de sectie [omgevingen beheren](how-to-use-environments.md#manage-environments) van de procedures voor code voorbeelden.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken en gebruiken van omgevingen](how-to-use-environments.md) in azure machine learning
* Zie de documentatie van python SDK voor de [omgevings klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Zie de naslag documenten R SDK voor [omgevingen](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).