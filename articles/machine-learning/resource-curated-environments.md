---
title: Gecuratore omgevingen
titleSuffix: Azure Machine Learning
description: Verzameling van in Azure Machine Learning beschik bare omgevingen
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 9d0964fb28451abe0a77276d6f9d605fcaac2514
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156425"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learningde gecuratore omgevingen

In dit artikel vindt u een overzicht van de gewerkte omgevingen in Azure Machine Learning en de pakketten en kanalen die vooraf zijn geïnstalleerd.

> [!NOTE]
> Deze lijst wordt bijgewerkt vanaf juni 2020. Gebruik de python-SDK om de meest recente lijst op te halen. Zie het [artikel omgevingen](./how-to-use-environments.md#use-a-curated-environment)voor meer informatie.

## <a name="azure-automl"></a>Azure-AutoML

- [AzureML-AutoML](#azureml-automl)
- [AutoML-GPU voor AzureML](#azureml-automl-gpu)
- [DNN voor AzureML AutoML](#azureml-automl-dnn)
- [AutoML DNN GPU voor AzureML](#azureml-automl-dnn-gpu)
- [DNN Vision GPU Azure AutoML](#azureml-automl-dnn-vision-gpu)

### <a name="azureml-automl"></a>AzureML-AutoML

pakketten kanalen:
- Anaconda
- Conda-vervalsing
- pytorch

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-pijp lijn-Core = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-interpret = = 1.8.0
  - azureml-uitleg-model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-runtime = = 1.8.0
  - azureml-Train-automl-client = = 1.8.0
  - azureml-Train-automl-runtime = = 1.8.0. post1
  - demijnen-schema
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- numpy>= 1.16.0, <= 1.16.2
- Panda>= 0.21.0, <= 0.23.4
- py-xgboost<= 0,90
- fbprophet = = 0.5
- installatie-en git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-gpu"></a>AutoML-GPU voor AzureML

pakketten kanalen:
- Anaconda
- Conda-vervalsing
- pytorch

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-pijp lijn-Core = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-interpret = = 1.8.0
  - azureml-uitleg-model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-runtime = = 1.8.0
  - azureml-Train-automl-client = = 1.8.0
  - azureml-Train-automl-runtime = = 1.8.0. post1
  - demijnen-schema
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- numpy>= 1.16.0, <= 1.16.2
- Panda>= 0.21.0, <= 0.23.4
- fbprophet = = 0.5
- installatie-en git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn"></a>DNN voor AzureML AutoML

pakketten kanalen:
- Anaconda
- Conda-vervalsing
- pytorch

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-pijp lijn-Core = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-interpret = = 1.8.0
  - azureml-uitleg-model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-runtime = = 1.8.0
  - azureml-Train-automl-client = = 1.8.0
  - azureml-Train-automl-runtime = = 1.8.0. post1
  - demijnen-schema
  - pytorch-trans formaties = = 1.0.0
  - spacy = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- Panda>= 0.21.0, <= 0.23.4
- numpy>= 1.16.0, <= 1.16.2
- py-xgboost<= 0,90
- fbprophet = = 0.5
- installatie-en git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-gpu"></a>AutoML DNN GPU voor AzureML

pakketten kanalen:
- Anaconda
- Conda-vervalsing
- pytorch

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-pijp lijn-Core = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-interpret = = 1.8.0
  - azureml-uitleg-model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-runtime = = 1.8.0
  - azureml-Train-automl-client = = 1.8.0
  - azureml-Train-automl-runtime = = 1.8.0. post1
  - demijnen-schema
  - horovod = = 0.19.4
  - pytorch-trans formaties = = 1.0.0
  - spacy = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- Panda>= 0.21.0, <= 0.23.4
- numpy>= 1.16.0, <= 1.16.2
- fbprophet = = 0.5
- installatie-en git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-vision-gpu"></a>DNN Vision GPU voor AzureML AutoML

elkaar
- python = 3.7
- gooien
  - azureml-automl-Core = = 1.8.0
  - azureml-Core = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-automl-client = = 1.8.0
  - azureml-automl-runtime = = 1.8.0
  - azureml-interpret = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-uitleg-model = = 1.8.0
  - azureml-Train-automl-runtime = = 1.8.0. post1
  - azureml-Train-automl = = 1.8.0
  - azureml-contrib-dataset = = 1.8.0
  - azureml-pijp lijn-Core = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - azureml-pijp lijn-stappen = = 1.8.0
  - azureml-pijp lijn = = 1.8.0
  - azureml-train = = 1.8.0
  - azureml-SDK = = 1.8.0
  - azureml-contrib-automl-DNN-Vision = = 1.8.0

## <a name="azure-ml-designer"></a>Azure ML Designer

- [AzureML Designer](#azureml-designer)
- [CV voor AzureML Designer](#azureml-designer-cv)
- [CV-trans formatie voor AzureML Designer](#azureml-designer-cv-transform)
- [I/o voor AzureML Designer](#azureml-designer-io)
- [NLP voor AzureML Designer](#azureml-designer-nlp)
- [PyTorch voor AzureML Designer](#azureml-designer-pytorch)
- [PyTorch-trein voor AzureML Designer](#azureml-designer-pytorch-train)
- [AzureML Designer R](#azureml-designer-r)
- [Advieser voor AzureML Designer](#azureml-designer-recommender)
- [Score voor AzureML Designer](#azureml-designer-score)
- [Trans formatie van AzureML Designer](#azureml-designer-transform)

### <a name="azureml-designer"></a>AzureML Designer

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.8
- scikit-verrassing = 1.0.6
- gooien
  - azureml-Designer-Classic-modules = = 0.0.124
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - spacy = = 2.1.7

### <a name="azureml-designer-cv"></a>CV voor AzureML Designer

pakketten kanalen:
- standaardwaarden

elkaar
- python = 3.6.8
- gooien
  - azureml-Designer-CV-modules = = 0.0.6

### <a name="azureml-designer-cv-transform"></a>CV-trans formatie voor AzureML Designer

pakketten kanalen:
- standaardwaarden

elkaar
- python = 3.6.8
- gooien
  - azureml-Designer-CV-modules [pytorch] = = 0.0.6

### <a name="azureml-designer-io"></a>I/o voor AzureML Designer

pakketten kanalen:
- standaardwaarden

elkaar
- python = 3.6.8
- gooien
  - azureml-dataprep>= 1,6
  - azureml-Designer-dataio-modules = = 0.0.30

### <a name="azureml-designer-nlp"></a>NLP voor AzureML Designer

pakketten kanalen:
- standaardwaarden

elkaar
- python = 3.6.8
- gooien
  - azureml-Designer-Classic-modules = = 0.0.121
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - spacy = = 2.1.7

### <a name="azureml-designer-pytorch"></a>PyTorch voor AzureML Designer

pakketten kanalen:
- standaardwaarden

elkaar
- python = 3.6.8
- gooien
  - azureml-Designer-pytorch-modules = = 0.0.8

### <a name="azureml-designer-pytorch-train"></a>PyTorch-trein voor AzureML Designer

pakketten kanalen:
- standaardwaarden

elkaar
- python = 3.6.8
- gooien
  - azureml-Designer-pytorch-modules = = 0.0.8

### <a name="azureml-designer-r"></a>AzureML Designer R

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.8
- r-caret = 6.0
- r-catools = 1.17.1
- r-cluster = 2.1.0
- r-dplyr = 0.8.5
- r-e1071 = 1.7
- r-Forcats = 0.5.0
- r-Forecast = 8.12
- r-glmnet = 2.0
- r-igraph = 1.2.4
- r-matrix = 1.2
- r-mclust = 5.4.6
- r-mgcv = 1,8
- r-nlme = 3.1
- r-nnet = 7.3
- r-plyr = 1.8.6
- r-randomforest = 4.6
- r-Reticulate = 1.12
- r-rocr = 1.0
- r-rodbc = 1.3
- r-rpart = 4.1
- r-stringr = 1.4.0
- r-tidyverse = 1.2.1
- r-timedate = 3043.102
- r-tseries = 0,10
- r = 3.5.1
- gooien
  - azureml-Designer-Classic-modules = = 0.0.124

### <a name="azureml-designer-recommender"></a>Advieser voor AzureML Designer

pakketten kanalen:
- standaardwaarden

elkaar
- python = 3.6.8
- gooien
  - azureml-Designer-aanbevolen-modules = = 0.0.5

### <a name="azureml-designer-score"></a>Score voor AzureML Designer

pakketten kanalen:
- standaardwaarden

elkaar
- Conda
- python = 3.6.8
- gooien
  - azureml-Designer-Score-modules = = 0.0.5

### <a name="azureml-designer-transform"></a>Trans formatie van AzureML Designer

pakketten kanalen:
- standaardwaarden

elkaar
- python = 3.6.8
- gooien
  - azureml-Designer-datatransform-modules = = 0.0.49

## <a name="azureml-hyperdrive-forecastdnn"></a>ForecastDNN voor AzureML Hyperdrive

elkaar
- python = 3.7
- gooien
  - azureml-Core = = 1.8.0
  - azureml-pijp lijn-Core = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-runtime = = 1.8.0
  - azureml-Train-automl-client = = 1.8.0
  - azureml-Train-automl-runtime = = 1.8.0. post1
  - azureml-contrib-automl-DNN-Forecasting = = 1.8.0

## <a name="azureml-minimal"></a>Mini maal AzureML

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0

## <a name="azureml-sidecar"></a>AzureML-zijspan

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2

## <a name="azureml-tutorial"></a>Zelf studie voor AzureML

pakketten kanalen:
- Anaconda
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - azureml-widgets = = 1.8.0
  - azureml-pijp lijn-Core = = 1.8.0
  - azureml-pijp lijn-stappen = = 1.8.0
  - azureml-opengegevenssets = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-runtime = = 1.8.0
  - azureml-Train-automl-client = = 1.8.0
  - azureml-Train-automl-runtime = = 1.8.0. post1  
  - azureml-Train-automl = = 1.8.0
  - azureml-train = = 1.8.0
  - azureml-SDK = = 1.8.0
  - azureml-interpret = = 1.8.0
  - azureml-tensorboard = = 1.8.0
  - azureml-mlflow = = 1.8.0
  - mlflow
  - sklearn: Pandas
- pandas
- numpy
- tqdm
- scikit-learn
- matplotlib

## <a name="azureml-vowpalwabbit-880"></a>8.8.0 voor AzureML VowpalWabbit

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-dataprep [zeker, Pandas]

## <a name="dask"></a>Dask

- [Dask-CPU voor AzureML](#azureml-dask-cpu)
- [Dask-GPU voor AzureML](#azureml-dask-gpu)

### <a name="azureml-dask-cpu"></a>Dask-CPU voor AzureML

pakketten kanalen:
- Conda-vervalsing
- pytorch
- standaardwaarden

elkaar
- python = 3.6.9
- gooien
  - adlfs
  - azureml-Core = = 1.8.0
  - azureml-dataprep
  - dask [volt ooien]
  - dask-ml [complete]
  - gedistribueerde
  - fastparquet
  - fsspec
  - joblib
  - jjupyterlab
  - lz4
  - mpi4py
  - notitieblok
  - pyarrow

### <a name="azureml-dask-gpu"></a>Dask-GPU voor AzureML

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.9
- gooien
  - azureml-defaults = = 1.8.0
  - adlfs
  - azureml-Core = = 1.8.0
  - azureml-dataprep
  - dask [volt ooien]
  - dask-ml [complete]
  - gedistribueerde
  - fastparquet
  - fsspec
  - joblib
  - jjupyterlab
  - lz4
  - mpi4py
  - notitieblok
  - pyarrow
- matplotlib

## <a name="chainer"></a>Chainer

- [5.1.0-CPU voor AzureML-Chainer](#azureml-chainer-510-cpu)
- [5.1.0 GPU voor AzureML-Chainer](#azureml-chainer-510-gpu)

### <a name="azureml-chainer-510-cpu"></a>5.1.0-CPU voor AzureML-Chainer

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Chainer = = 5.1.0
  - mpi4py = = 3.0.0

### <a name="azureml-chainer-510-gpu"></a>5.1.0 GPU voor AzureML-Chainer

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Chainer = = 5.1.0
  - cupy-cuda90 = = 5.1.0
  - mpi4py = = 3.0.0

## <a name="pyspark"></a>PySpark

### <a name="azureml-pyspark-mmlspark-015"></a>AzureML PySpark MmlSpark 0,15

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0

## <a name="pytorch"></a>PyTorch

- [PyTorch 1,0-CPU voor AzureML](#azureml-pytorch-10-cpu)
- [AzureML PyTorch 1,0 GPU](#azureml-pytorch-10-gpu)
- [PyTorch 1,1-CPU voor AzureML](#azureml-pytorch-11-cpu)
- [AzureML PyTorch 1,1 GPU](#azureml-pytorch-11-gpu)
- [PyTorch 1,2-CPU voor AzureML](#azureml-pytorch-12-cpu)
- [AzureML PyTorch 1,2 GPU](#azureml-pytorch-12-gpu)
- [PyTorch 1,3-CPU voor AzureML](#azureml-pytorch-13-cpu)
- [AzureML PyTorch 1,3 GPU](#azureml-pytorch-13-gpu)
- [PyTorch 1,4-CPU voor AzureML](#azureml-pytorch-14-cpu)
- [AzureML PyTorch 1,4 GPU](#azureml-pytorch-14-gpu)
- [PyTorch 1,5-CPU voor AzureML](#azureml-pytorch-15-cpu)
- [AzureML PyTorch 1,5 GPU](#azureml-pytorch-15-gpu)

### <a name="azureml-pytorch-10-cpu"></a>PyTorch 1,0-CPU voor AzureML

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.0
  - Torchvision = = 0.2.1
  - mkl = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-10-gpu"></a>AzureML PyTorch 1,0 GPU

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.0
  - Torchvision = = 0.2.1
  - mkl = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch 1,1-CPU

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.1
  - Torchvision = = 0.2.1
  - mkl = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - toekomst = = 0.17.1

### <a name="azureml-pytorch-11-gpu"></a>AzureML PyTorch 1,1 GPU

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.1
  - Torchvision = = 0.2.1
  - mkl = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - toekomst = = 0.17.1

### <a name="azureml-pytorch-12-cpu"></a>PyTorch 1,2-CPU voor AzureML

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.2
  - Torchvision = = 0.4.0
  - mkl = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - toekomst = = 0.17.1

### <a name="azureml-pytorch-12-gpu"></a>AzureML PyTorch 1,2 GPU

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.2
  - Torchvision = = 0.4.0
  - mkl = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard = = 1.14.0
  - toekomst = = 0.17.1

### <a name="azureml-pytorch-13-cpu"></a>PyTorch 1,3-CPU voor AzureML

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.3
  - Torchvision = = 0.4.1
  - mkl = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - toekomst = = 0.17.1

### <a name="azureml-pytorch-13-gpu"></a>AzureML PyTorch 1,3 GPU

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.3
  - Torchvision = = 0.4.1
  - mkl = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - toekomst = = 0.17.1

### <a name="azureml-pytorch-14-cpu"></a>PyTorch 1,4-CPU voor AzureML

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.4.0
  - Torchvision = = 0.5.0
  - mkl = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - toekomst = = 0.17.1

### <a name="azureml-pytorch-14-gpu"></a>AzureML PyTorch 1,4 GPU

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.4.0
  - Torchvision = = 0.5.0
  - mkl = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard = = 1.14.0
  - toekomst = = 0.17.1

### <a name="azureml-pytorch-15-cpu"></a>PyTorch 1,5-CPU voor AzureML

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.5.0
  - Torchvision = = 0.5.0
  - mkl = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard = = 1.14.0
  - toekomst = = 0.17.1

### <a name="azureml-pytorch-15-gpu"></a>AzureML PyTorch 1,5 GPU

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.5.0
  - Torchvision = = 0.5.0
  - mkl = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard = = 1.14.0
  - toekomst = = 0.17.1

## <a name="scikit-learn"></a>Scikit leren

### <a name="azureml-scikit-learn-0203"></a>AzureML Scikit-meer informatie over 0.20.3

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - scikit-Learn = = 0.20.3
  - scipy = = 1.2.1
  - joblib = = 0.13.2

## <a name="tensorflow"></a>TensorFlow

- [Azure ML tensor flow 1,10 CPU](#azureml-tensorflow-110-cpu)
- [Azure ML tensor flow 1,10 GPU](#azureml-tensorflow-110-gpu)
- [Azure ML tensor flow 1,12 CPU](#azureml-tensorflow-112-cpu)
- [Azure ML tensor flow 1,12 GPU](#azureml-tensorflow-112-gpu)
- [Azure ML tensor flow 1,13 CPU](#azureml-tensorflow-113-cpu)
- [Azure ML tensor flow 1,13 GPU](#azureml-tensorflow-113-gpu)
- [Azure ML tensor Flow 2,0 CPU](#azureml-tensorflow-20-cpu)
- [Azure ML tensor Flow 2,0 GPU](#azureml-tensorflow-20-gpu)
- [Azure ML tensor Flow 2,1 CPU](#azureml-tensorflow-21-cpu)
- [Azure ML tensor Flow 2,1 GPU](#azureml-tensorflow-21-gpu)

### <a name="azureml-tensorflow-110-cpu"></a>Tensor flow 1,10-CPU voor AzureML

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensor flow = = 1,10
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-110-gpu"></a>AzureML tensor flow 1,10 GPU

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensor flow-GPU = = 1.10.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-cpu"></a>Tensor flow 1,12-CPU voor AzureML

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensor flow = = 1.12
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-gpu"></a>AzureML tensor flow 1,12 GPU

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensor flow-GPU = = 1.12.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-113-cpu"></a>AzureML-tensor flow 1,13-CPU

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensor flow = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-113-gpu"></a>AzureML tensor flow 1,13 GPU

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensor flow-GPU = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-20-cpu"></a>Tensor Flow 2,0-CPU voor AzureML

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensor Flow = = 2.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-20-gpu"></a>AzureML tensor Flow 2,0 GPU

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensor flow-GPU = = 2.0.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-21-cpu"></a>Tensor Flow 2,1-CPU voor AzureML

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensor Flow = = 2.1.0
  - horovod = = 0.19.1

### <a name="azureml-tensorflow-21-gpu"></a>AzureML tensor Flow 2,1 GPU

pakketten kanalen:
- Conda-vervalsing

elkaar
- python = 3.6.2
- gooien
  - azureml-Core = = 1.8.0
  - azureml-defaults = = 1.8.0
  - azureml-telemetrie = = 1.8.0
  - azureml-Train-restclients-Hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensor flow-GPU = = 2.1.0
  - horovod = = 0.19.1

