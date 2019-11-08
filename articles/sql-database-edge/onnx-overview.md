---
title: Machine learning en AI met ONNX in Azure SQL Database Edge Preview | Microsoft Docs
description: Machine learning in Azure SQL Database Edge-Preview ondersteunt modellen in de open Neural Network Exchange (ONNX)-indeling. ONNX is een open indeling die u kunt gebruiken voor het uitwisselen van modellen tussen verschillende machine learning frameworks en hulpprogram ma's.
keywords: SQL data base-rand implementeren
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/07/2019
ms.openlocfilehash: 976c849f9cb48e1c197f70d10e911216a6a7425c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822849"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Machine learning en AI met ONNX in SQL Database Edge-Preview

Machine learning in Azure SQL Database Edge-Preview ondersteunt modellen in de [Open Neural Network Exchange (ONNX)-](https://onnx.ai/) indeling. ONNX is een open indeling die u kunt gebruiken voor het uitwisselen van modellen tussen verschillende [machine learning frameworks en hulpprogram ma's](https://onnx.ai/supported-tools).

## <a name="overview"></a>Overzicht

Als u machine learning modellen in Azure SQL Database Edge wilt afleiden, moet u eerst een model ophalen. Dit kan een vooraf getraind model zijn of een aangepast model dat is getraind met uw Framework van Choice. Azure SQL Database Edge ondersteunt de ONNX-indeling en u moet het model converteren naar deze indeling. Het is niet van invloed op de nauw keurigheid van het model en wanneer u het ONNX-model hebt, kunt u het model in Azure SQL Database Edge implementeren en [systeem eigen score gebruiken met de functie voors pellen T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>ONNX-modellen ophalen

Er zijn verschillende manieren waarop u een model in de ONNX-indeling kunt verkrijgen:

- [ONNX model Zoo](https://github.com/onnx/models): bevat veel vooraf getrainde ONNX-modellen voor verschillende typen taken die kunnen worden gedownload en gereed zijn voor gebruik.

- [Systeem eigen export van ml-trainings raamwerken](https://onnx.ai/supported-tools): verschillende trainings frameworks ondersteunen systeem eigen export functionaliteit naar ONNX, waarmee u uw getrainde model kunt opslaan in een specifieke versie van de ONNX-indeling, waaronder [PyTorch](https://pytorch.org/docs/stable/onnx.html), Chainer en Caffe2. Daarnaast kunt u met model buil ding services zoals de [functie geautomatiseerde machine learning in azure machine learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) en [Azure Custom Vision service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) ONNX-export bieden.

- [Bestaande modellen converteren](https://github.com/onnx/tutorials#converting-to-onnx-format): voor frameworks die systeem eigen export niet ondersteunen, zijn er zelfstandige pakketten voor het converteren van modellen naar de ONNX-indeling. Zie [converteren naar ONNX-indeling](https://github.com/onnx/tutorials#converting-to-onnx-format)voor voor beelden en zelf studies. 

### <a name="supported-frameworks"></a>Ondersteunde frameworks

Met ONNX Conversieprogramma's kunt u modellen die zijn getraind van verschillende machine learning frameworks, converteren naar de ONNX-indeling. Populaire conversie Programma's zijn onder andere: 

* [PyTorch](http://pytorch.org/docs/master/onnx.html)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [Scikit-learn](https://github.com/onnx/sklearn-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)

Zie [converteren naar ONNX-indeling](https://github.com/onnx/tutorials#converting-to-onnx-format)voor een volledige lijst met ondersteunde frameworks.

## <a name="limitations"></a>Beperkingen

Momenteel worden niet alle ONNX-modellen ondersteund door Azure SQL Database Edge. De ondersteuning is beperkt tot modellen met **numerieke gegevens typen**:

- [int en bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [werkelijk en vlotter](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Andere numerieke typen kunnen worden geconverteerd naar ondersteunde typen met behulp van [cast en Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

De model invoer moet zodanig zijn gestructureerd dat elke invoer naar het model overeenkomt met één kolom in een tabel. Als u bijvoorbeeld een Panda data frame gebruikt om een model te trainen, moet elke invoer een afzonderlijke kolom zijn voor het model.

## <a name="next-steps"></a>Volgende stappen

- [SQL Database rand implementeren via Azure Portal](deploy-portal.md)
- [Een ONNX-model implementeren op Azure SQL Database Edge-Preview](deploy-onnx.md)
