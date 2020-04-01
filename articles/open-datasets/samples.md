---
title: Voorbeeld Jupyter-notitieblokken met NOAA-gegevens
titleSuffix: Azure Open Datasets
description: Gebruik bijvoorbeeld Jupyter-notitieblokken voor Azure Open Datasets voor meer informatie over het laden van geopende gegevenssets en deze gebruiken om demogegevens te verrijken. Technieken omvatten het gebruik van Spark en Panda's om gegevens te verwerken.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: 8b96a35db91a282be1fb5e4c6143e6bd0a0203f2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73606144"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Voorbeeld Jupyter-notitieblokken laten zien hoe u gegevens verrijken met Gegevenssets openen 
In het voorbeeld Jupyter-notitieblokken voor Azure Open Datasets ziet u hoe u geopende gegevenssets laadt en deze gebruikt om demogegevens te verrijken. Technieken omvatten het gebruik van Apache Spark en Panda's om gegevens te verwerken.

>[!IMPORTANT]
>Wanneer u in een niet-Spark-omgeving werkt, staat Open Datasets het downloaden van slechts één maand gegevens tegelijk met bepaalde klassen toe om MemoryError met grote gegevenssets te voorkomen.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>NOAA Integrated Surface Database (ISD)-gegevens laden 
|Notebook        | Beschrijving                                    |
|----------------|------------------------------------------------|
|[Laad een recente maand van het weer gegevens in een Pandas dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Meer informatie over het laden van historische weergegevens in uw favoriete Panda's-gegevensframe. |
|[Een recente maand aan weergegevens in een Spark-gegevensframe laden](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Meer informatie over het laden van historische weergegevens in uw favoriete Spark-gegevensframe.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Deelnemen aan demogegevens met NOAA ISD-gegevens 
|Notebook        | Beschrijving                                    |
|----------------|------------------------------------------------|
|[Deelnemen aan demogegevens met weersgegevens - Panda's](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Word lid van een demodataset van 1 maand met sensorlocaties met weermetingen in een Pandas-dataframe.  |
|[Deelnemen aan demogegevens met weersgegevens – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Neem deel aan een demodataset van sensorlocaties met weermetingen in een Spark-gegevensframe. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Word lid van NYC taxi gegevens met NOAA ISD gegevens 
|Notebook        | Beschrijving                                    |
|----------------|------------------------------------------------|
|[Taxirit gegevens verrijkt met weersgegevens - Panda's](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Laad NYC groene taxi gegevens (meer dan 1 maand) en verrijken met het weer gegevens in een Pandas dataframe. In dit voorbeeld `get_pandas_limit` wordt de methode overschreven en worden de prestaties van gegevensbelasting in evenwicht gebracht met de hoeveelheid gegevens.|
|[Gegevens over taxiritten verrijkt met weersgegevens – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Laad NYC groene taxi gegevens en verrijken met het weer gegevens, in Spark dataframe.  |

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Regressiemodellering met geautomatiseerde machine learning en een open gegevensset](/azure/machine-learning/service/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Python SDK voor Open Datasets](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Catalogus Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/)
