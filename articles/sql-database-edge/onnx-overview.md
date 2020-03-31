---
title: Machine learning en AI met ONNX in Azure SQL Database Edge Preview | Microsoft Documenten
description: Machine learning in Azure SQL Database Edge Preview ondersteunt modellen in de ONNX-indeling (Open Neural Network Exchange). ONNX is een open formaat dat u gebruiken om modellen uit te wisselen tussen verschillende machine learning frameworks en tools.
keywords: sql-databaserand implementeren
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: 7813a08b6b18e517b81e8c4bfac660d198eba7f7
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366275"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Machine learning en AI met ONNX in SQL Database Edge Preview

Machine learning in Azure SQL Database Edge Preview ondersteunt modellen in de [ONNX-indeling (Open Neural Network Exchange).](https://onnx.ai/) ONNX is een open formaat dat u gebruiken om modellen uit te wisselen tussen verschillende [machine learning frameworks en tools.](https://onnx.ai/supported-tools)

## <a name="overview"></a>Overzicht

Als u machine learning-modellen wilt afleiden in Azure SQL Database Edge, moet u eerst een model krijgen. Dit kan een vooraf opgeleid model zijn of een aangepast model getraind met uw framework naar keuze. Azure SQL Database Edge ondersteunt de ONNX-indeling en u moet het model converteren naar deze indeling. Er mag geen invloed zijn op de nauwkeurigheid van het model en zodra u het ONNX-model hebt, u het model implementeren in Azure SQL Database Edge en native scores gebruiken [met de functie PREDICT T-SQL.](/sql/advanced-analytics/sql-native-scoring/)

## <a name="get-onnx-models"></a>OnNX-modellen

Ga als lid van het ONNX-formaat naar een model in de ONNX-indeling:

- **Modelbuildingservices:** Services zoals de [geautomatiseerde Machine Learning-functie in Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) en Azure Custom Vision [Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) ondersteunen het rechtstreeks exporteren van het getrainde model in de ONNX-indeling.

- [**Bestaande modellen converteren en/of exporteren:**](https://github.com/onnx/tutorials#converting-to-onnx-format)Verschillende trainingsframeworks (bijvoorbeeld [PyTorch,](https://pytorch.org/docs/stable/onnx.html)Chainer en Caffe2) ondersteunen native exportfunctionaliteit naar ONNX, waarmee u uw getrainde model opslaan in een specifieke versie van het ONNX-formaat. Voor frameworks die native export niet ondersteunen, zijn er standalone ONNX Converter-installatiepakketten waarmee u modellen converteren die zijn opgeleid van verschillende machine learning-frameworks naar het ONNX-formaat.

     **Ondersteunde frameworks**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML (CoreML)](https://github.com/onnx/onnxmltools)
    
    Zie [Converteren naar ONNX-indeling](https://github.com/onnx/tutorials#converting-to-onnx-format)voor de volledige lijst met ondersteunde frameworks en voorbeelden.

## <a name="limitations"></a>Beperkingen

Momenteel worden niet alle ONNX-modellen ondersteund door Azure SQL Database Edge. De ondersteuning is beperkt tot modellen met **numerieke gegevenstypen:**

- [int en bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [echt en zweven](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Andere numerieke typen kunnen worden geconverteerd naar ondersteunde typen met behulp van [CAST en CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

De modelingangen moeten zo zijn gestructureerd dat elke invoer naar het model overeenkomt met één kolom in een tabel. Als u bijvoorbeeld een panda's-gegevensframe gebruikt om een model te trainen, moet elke invoer een afzonderlijke kolom naar het model zijn.

## <a name="next-steps"></a>Volgende stappen

- [SQL Database Edge implementeren via Azure-portal](deploy-portal.md)
- [Een ONNX-model implementeren in Azure SQL Database Edge Preview](deploy-onnx.md)
