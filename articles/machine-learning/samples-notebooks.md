---
title: Voorbeelden van Jupyter-notebooks
titleSuffix: Azure Machine Learning
description: Zoek en gebruik voorbeeld Jupyter-notitieblokken om de Azure Machine Learning Python voor SDK te verkennen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 7242b82ee5c43878a33731bd1f02b685020f22b0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78673622"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Azure Machine Learning verkennen met Jupyter-notitieblokken

Het [voorbeeld Azure Machine Learning Notebooks repository](https://github.com/azure/machinelearningnotebooks) bevat de nieuwste Azure Machine Learning Python SDK-voorbeelden. Deze Juypter-notebooks zijn ontworpen om u te helpen de SDK te verkennen en als model te dienen voor uw eigen machine learning-projecten.

In dit artikel ziet u hoe u toegang krijgt tot de repository vanuit de volgende omgevingen:

- [Azure Machine Learning-rekeninstantie](#notebookvm)
- [Neem uw eigen notebookserver mee](#byo)
- [Data Science Virtual Machine](#dsvm)

> [!NOTE]
> Zodra u de opslagplaats hebt gekloond, vindt u zelfstudienotitieblokken in de map **met zelfstudies** en functiespecifieke notitieblokken in de map **how-to-use-azureml.**

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>Voorbeelden van gegevens over de rekeninstantie Azure Machine Learning downloaden

De eenvoudigste manier om met de voorbeelden aan de slag te gaan, is door de [omgeving Zelfstudie: Setup en werkruimte](tutorial-1st-experiment-sdk-setup.md)te voltooien. Zodra u klaar bent, beschikt u over een speciale notebookserver die vooraf is geladen met de SDK en de voorbeeldopslagplaats. Geen downloads of installatie nodig.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Voorbeelden op uw notitieblokserver downloaden

Als u uw eigen notebookserver mee wilt nemen voor lokale ontwikkeling, voert u de volgende stappen uit:

[!INCLUDE [aml-your-server](../../includes/aml-your-server.md)]

Deze instructies installeren de basis SDK pakketten die nodig zijn voor de quickstart en tutorial notebooks. Voor andere voorbeeldnotitieblokken moet u mogelijk extra onderdelen installeren. Zie [De Azure Machine Learning SDK voor Python installeren voor](https://docs.microsoft.com/python/api/overview/azure/ml/install)meer informatie.

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Voorbeelden downloaden op DSVM

De Data Science Virtual Machine (DSVM) is een aangepaste VM-afbeelding die speciaal is gebouwd voor het doen van data science. Als u [een DSVM maakt,](how-to-configure-environment.md#dsvm)worden de SDK- en notebookserver voor u geïnstalleerd en geconfigureerd. U moet echter nog steeds een werkruimte maken en de voorbeeldopslagplaats klonen.

[!INCLUDE [aml-dsvm-server](../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de [voorbeeldnotitieblokken](https://aka.ms/aml-notebooks) om te ontdekken wat Azure Machine Learning kan doen of probeer deze zelfstudies:

- [Een model voor de classificatie van afbeeldingen trainen en implementeren met MNIST](tutorial-train-models-with-aml.md)

- [Gegevens voorbereiden en geautomatiseerde machine learning gebruiken om een regressiemodel met de gegevensset voor taxi's in NYC te trainen](tutorial-auto-train-models.md)
