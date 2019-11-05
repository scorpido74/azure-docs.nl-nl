---
title: Machine learning en AI met ONNX in Azure SQL Database Edge Preview | Microsoft Docs
description: Machine learning in Azure SQL Database Edge-Preview ondersteunt modellen in de open Neural Network Exchange (ONNX)-indeling. ONNX is een open indeling die u kunt gebruiken voor het uitwisselen van modellen tussen verschillende machine learning frameworks en hulpprogram ma's.
keywords: SQL data base-rand implementeren
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/04/2019
ms.openlocfilehash: c4c87f7f6f8735c9a50c61b0e083c77b915e0d98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514015"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Machine learning en AI met ONNX in SQL Database Edge-Preview

Machine learning in Azure SQL Database Edge-Preview ondersteunt modellen in de [Open Neural Network Exchange (ONNX)-](https://onnx.ai/) indeling. ONNX is een open indeling die u kunt gebruiken voor het uitwisselen van modellen tussen verschillende [machine learning frameworks en hulpprogram ma's](https://onnx.ai/supported-tools).

## <a name="supported-tool-kits"></a>Ondersteunde tool kits

Met ONNXMLTools kunt u modellen van verschillende machine learning-hulpprogram ma's converteren naar een ONNX-model. Op dit moment worden de volgende tool kits ondersteund voor numerieke gegevens typen en enkelvoudige kolom invoer:

* [scikit-learn](https://github.com/onnx/sklearn-onnx)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)
* [Spark ML (experimenteel)](https://github.com/onnx/onnxmltools/tree/master/onnxmltools/convert/sparkml)
* [LightGBM](https://github.com/onnx/onnxmltools)
* [libsvm](https://github.com/onnx/onnxmltools)
* [XGBoost](https://github.com/onnx/onnxmltools)

## <a name="get-onnx-models"></a>ONNX-modellen ophalen

Er zijn verschillende manieren waarop u een model in de ONNX-indeling kunt verkrijgen:

- [ONNX model Zoo](https://github.com/onnx/models): bevat verschillende vooraf getrainde ONNX-modellen voor verschillende typen taken. U kunt versies downloaden en gebruiken die worden ondersteund door Windows Machine Learning.

- [Systeem eigen export van machine learning-trainings raamwerken](https://onnx.ai/supported-tools): verschillende trainings frameworks ondersteunen systeem eigen export functionaliteit naar ONNX, waarmee u uw getrainde model kunt opslaan in een specifieke versie van de ONNX-indeling. Bijvoorbeeld Chainer, Caffee2 en PyTorch. Daarnaast bieden services als Azure Machine Learning- [service](https://azure.microsoft.com/services/machine-learning-service/) en [Azure Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) ook systeem eigen ONNX-export.

  - Voor meer informatie over het trainen en exporteren van een ONNX-model in de Cloud met behulp van Custom Vision, Zie [zelf studie: een ONNX-model gebruiken van Custom Vision met Windows ml (preview)](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml).

- [Bestaande modellen converteren met behulp van WinMLTools](https://docs.microsoft.com/windows/ai/windows-ml/convert-model-winmltools): dit python-pakket maakt het mogelijk dat modellen van verschillende indelingen van een trainings raamwerk worden GECONVERTEERD naar ONNX. U kunt opgeven naar welke versie van ONNX u uw model wilt converteren, afhankelijk van de builds van uw toepassings doelen van Windows. Als u niet bekend bent met python, kunt u het [Windows machine learning-dash board op basis van gebruikers interface](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard) gebruiken om uw modellen te converteren.

> [!IMPORTANT]
> Niet alle ONNX-versies worden ondersteund door Azure SQL database Edge. Alleen het voors pellen van numerieke gegevens typen via het ONNX-model wordt momenteel ondersteund.

Zodra u een ONNX-model hebt, kunt u het model implementeren in Azure SQL Database Edge. U kunt vervolgens [systeem eigen scores gebruiken met de functie voor speld T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="limitations"></a>Beperkingen

Deze ondersteuning is momenteel beperkt tot modellen met **numerieke gegevens typen**:

- [int en bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [werkelijk en vlotter](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Andere numerieke typen kunnen worden geconverteerd naar ondersteunde typen met behulp van CAST en CONVERT [cast en Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

De model invoer moet zodanig zijn gestructureerd dat elke invoer naar het model overeenkomt met één kolom in een tabel. Als u bijvoorbeeld een Panda data frame gebruikt om een model te trainen, moet elke invoer een afzonderlijke kolom zijn voor het model.

## <a name="next-steps"></a>Volgende stappen

- [SQL Database rand implementeren via Azure Portal](deploy-portal.md)
- [Een ONNX-model implementeren op Azure SQL Database Edge-Preview](deploy-onnx.md)