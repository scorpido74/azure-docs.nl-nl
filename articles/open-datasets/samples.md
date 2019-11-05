---
title: Voor beelden van Jupyter-notebooks met behulp van een NOAA-geopende gegevensset
titleSuffix: Azure Open Datasets
description: Gebruik voor beelden van Jupyter-notebooks voor Azure open-gegevens sets voor meer informatie over het laden van open gegevens sets en het gebruik ervan om voorbeeld gegevens te verrijken. Technieken zijn het gebruik van Spark en Pandas voor het verwerken van gegevens.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: 2ccdb7adcae40d22a36fbfb47fba6e4c87b3349a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496571"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Voor beelden van Jupyter-notebooks laten zien hoe gegevens kunnen worden verrijkt met open gegevens sets 
In het voor beeld Jupyter-notebooks voor Azure open gegevens sets kunt u zien hoe u open gegevens sets laadt en hoe u deze gebruikt voor het verrijken van demo gegevens Technieken zijn het gebruik van Apache Spark en Pandas voor het verwerken van gegevens.

>[!IMPORTANT]
>Wanneer u in een niet-Spark-omgeving werkt, kunt u met data sets een gegevens verzameling van slechts één maand met bepaalde klassen downloaden om MemoryError te voor komen met grote gegevens sets.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>ISD-gegevens (NOAA Integrated Surface data base) laden 
|Notebook        | Beschrijving                                    |
|----------------|------------------------------------------------|
|[Eén recente maand van weer gegevens laden in een Panda data frame](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Meer informatie over het laden van historische weers gegevens in uw favoriete Panda data frame. |
|[Eén recente maand van weer gegevens in een Spark-data frame laden](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Meer informatie over het laden van historische weer gegevens in uw favoriete Spark-data frame.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Demo gegevens samen voegen met NOAA ISD-gegevens 
|Notebook        | Beschrijving                                    |
|----------------|------------------------------------------------|
|[Demo gegevens samen voegen met weer gegevens-Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Neem een demo-gegevensset van 1 maand van sensor locaties met weers-lezen in een Panda data frame.  |
|[Demo gegevens samen voegen met weer gegevens: Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Voeg een demo-gegevensset van sensor locaties toe met weers leesingen in een Spark-data frame. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>NYC taxi-gegevens samen voegen met NOAA ISD-gegevens 
|Notebook        | Beschrijving                                    |
|----------------|------------------------------------------------|
|[Gegevens over de taxi-reis worden verrijkt met weer gegevens-Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Laad NYC groene taxi gegevens (meer dan 1 maand) en Verrijk deze met weer gegevens in een Panda data frame. In dit voor beeld wordt de methode overschreven `get_pandas_limit` en worden de prestaties van gegevens belasting met de hoeveelheid gegevens gebalanceerd.|
|[Gegevens over de taxi-reis verrijken met weer gegevens-Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Laad NYC groene taxi gegevens en verrijkt deze met weer gegevens in Spark data frame.  |

## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: regressie modellen met automatische machine learning en een open gegevensset](tutorial-opendatasets-automl.md)
* [Python-SDK voor open gegevens sets](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Azure open data sets Catalog](https://azure.microsoft.com/services/open-datasets/catalog/)
