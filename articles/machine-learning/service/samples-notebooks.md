---
title: Voorbeelden van Jupyter-notebooks
titleSuffix: Azure Machine Learning service
description: Zoek en gebruik voor beelden van Jupyter-notebooks om de Azure Machine Learning service python SDK te verkennen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: 14962b936d1c09a6c50daa7bec460ce11dbefe5d
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860390"
---
# <a name="explore-azure-machine-learning-service-with-jupyter-notebooks"></a>Azure Machine Learning-Service verkennen met Jupyter-notebooks

Het [voor beeld Azure machine learning-notitie blokken](https://github.com/azure/machinelearningnotebooks) bevat de meest recente Azure machine learning python SDK-voor beelden. Deze Juypter-notebooks zijn ontworpen om u te helpen de SDK te verkennen en als modellen te gebruiken voor uw eigen machine learning projecten.

In dit artikel wordt beschreven hoe u toegang kunt krijgen tot de opslag plaats vanuit de volgende omgevingen:

- [VM van Azure Machine Learning-notebook](#notebookvm)
- [Uw eigen notebook server meenemen](#byo)
- [Data Science Virtual Machine](#dsvm)

> [!NOTE]
> Zodra u de opslag plaats hebt gekloond, vindt u de notitie blokken voor zelf **studies** in de mappen map en specifieke notitie blokken in de map **How-to-use-azureml** .

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-notebook-vm"></a>Voor beelden van Azure Machine Learning notebook-VM ophalen

De eenvoudigste manier om aan de slag te gaan met de voor beelden [is om de zelf studie te volt ooien: Omgeving en werk ruimte](tutorial-1st-experiment-sdk-setup.md)instellen. Zodra u klaar bent, hebt u een vooraf geladen notebook server met de SDK en de voor beeld-opslag plaats. Geen down loads of installatie vereist.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Voor beelden ophalen van uw notebook server

Voer de volgende stappen uit als u uw eigen notebook server wilt maken voor lokale ontwikkeling:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

In deze instructies worden de basis-SDK-pakketten geïnstalleerd die nodig zijn voor de notebooks Snelstartgids en zelf studie. Voor andere voor beelden van notitie blokken moet u mogelijk extra onderdelen installeren. Zie [de Azure machine learning SDK voor python installeren](https://docs.microsoft.com/python/api/overview/azure/ml/install)voor meer informatie.

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Voor beelden ophalen op DSVM

De Data Science Virtual Machine (DSVM) is een aangepaste VM-installatie kopie die speciaal is gebouwd voor het uitvoeren van data Science. Als u [een DSVM maakt](how-to-configure-environment.md#dsvm), worden de SDK-en notebook server geïnstalleerd en geconfigureerd voor u. U moet echter nog steeds een werk ruimte maken en de voorbeeld opslagplaats klonen.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de [voorbeeld notitieblokken](https://aka.ms/aml-notebooks) om te ontdekken wat de Azure machine learning-service kan doen, of probeer deze zelf studies:

- [Een model voor de classificatie van afbeeldingen trainen en implementeren met MNIST](tutorial-train-models-with-aml.md)

- [Gegevens voorbereiden en geautomatiseerde machine learning gebruiken om een regressiemodel met de gegevensset voor taxi's in NYC te trainen](tutorial-auto-train-models.md)
