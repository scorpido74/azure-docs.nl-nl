---
title: Machine learning en AI met ONNX in Azure SQL Edge (preview-versie)
description: Machine learning in Azure SQL Edge (preview) ondersteunt modellen in de open Neural Network Exchange (ONNX)-indeling. ONNX is een open indeling die u kunt gebruiken voor het uitwisselen van modellen tussen verschillende machine learning frameworks en hulpprogram ma's.
keywords: SQL-rand implementeren
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 12c1294a804c2063d405c5ec08440865283d51d3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597364"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge-preview"></a>Machine learning en AI met ONNX in SQL Edge (preview-versie)

Machine learning in Azure SQL Edge (preview) ondersteunt modellen in de [Open Neural Network Exchange (ONNX)-](https://onnx.ai/) indeling. ONNX is een open indeling die u kunt gebruiken voor het uitwisselen van modellen tussen verschillende [machine learning frameworks en hulpprogram ma's](https://onnx.ai/supported-tools).

## <a name="overview"></a>Overzicht

Als u machine learning modellen wilt afleiden in Azure SQL Edge, moet u eerst een model ophalen. Dit kan een vooraf getraind model zijn of een aangepast model dat is getraind met uw Framework van Choice. Azure SQL Edge ondersteunt de ONNX-indeling en u moet het model naar deze indeling converteren. Het is niet van invloed op de nauw keurigheid van het model en wanneer u het ONNX-model hebt, kunt u het model in Azure SQL Edge implementeren en [systeem eigen scores gebruiken met de functie voors pellen T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>ONNX-modellen ophalen

Een model ophalen in de ONNX-indeling:

- **Model buil ding Services**: services zoals de [functie voor automatische machine learning in Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) en [Azure Custom Vision service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) ondersteunen het getrainde model rechtstreeks in de ONNX-indeling te exporteren.

- [**Bestaande modellen converteren en/of exporteren**](https://github.com/onnx/tutorials#converting-to-onnx-format): verschillende trainings Frameworks (zoals [PyTorch](https://pytorch.org/docs/stable/onnx.html), Chainer en Caffe2) ondersteunen systeem eigen export functionaliteit naar ONNX, waarmee u uw getrainde model kunt opslaan in een specifieke versie van de ONNX-indeling. Voor frameworks die systeem eigen export niet ondersteunen, zijn er zelfstandige ONNX-Converter Installeer bare pakketten waarmee u modellen kunt converteren die zijn getraind van verschillende machine learning Frameworks naar de ONNX-indeling.

     **Ondersteunde frameworks**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensor flow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Zie [converteren naar ONNX-indeling](https://github.com/onnx/tutorials#converting-to-onnx-format)voor een volledige lijst met ondersteunde frameworks en voor beelden.

## <a name="limitations"></a>Beperkingen

Momenteel worden niet alle ONNX-modellen ondersteund door Azure SQL Edge. De ondersteuning is beperkt tot modellen met **numerieke gegevens typen**:

- [int en bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [werkelijk en vlotter](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Andere numerieke typen kunnen worden geconverteerd naar ondersteunde typen met behulp van [cast en Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

De model invoer moet zodanig zijn gestructureerd dat elke invoer naar het model overeenkomt met één kolom in een tabel. Als u bijvoorbeeld een Panda data frame gebruikt om een model te trainen, moet elke invoer een afzonderlijke kolom zijn voor het model.

## <a name="next-steps"></a>Volgende stappen

- [SQL-rand via Azure Portal implementeren](deploy-portal.md)
- [Een ONNX-model implementeren op Azure SQL Edge (preview)](deploy-onnx.md)
