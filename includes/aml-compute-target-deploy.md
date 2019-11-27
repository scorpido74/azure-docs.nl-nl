---
title: bestand opnemen
description: bestand opnemen
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/23/2019
ms.openlocfilehash: 1bf1202a635184ad7f0022cf5baa9d72e8547f14
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74549387"
---
| Rekendoel | Gebruikt voor | GPU-ondersteuning | FPGA-ondersteuning | Beschrijving |
| ----- | ----- | ----- | ----- | ----- |
| [Lokale&nbsp;Web&nbsp;-service](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testen/fout opsporing | &nbsp; | &nbsp; | Gebruiken voor beperkte testen en probleem oplossing. Hardwareversnelling is afhankelijk van het gebruik van bibliotheken in het lokale systeem.
| [Azure Machine Learning Reken exemplaar&nbsp;Web&nbsp;service](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Testen/fout opsporing | &nbsp; | &nbsp; | Gebruiken voor beperkte testen en probleem oplossing.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Real-time deinterferentie |  [Ja](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md) (web service-implementatie) | [Ja](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Gebruiken voor grootschalige productie-implementaties. Biedt snelle reactie tijd en automatisch schalen van de geïmplementeerde service. Cluster automatisch schalen wordt niet ondersteund via de Azure Machine Learning SDK. Als u de knoop punten in het AKS-cluster wilt wijzigen, gebruikt u de gebruikers interface voor uw AKS-cluster in de Azure Portal. AKS is de enige optie die beschikbaar is voor de ontwerp functie. |
| [Azure Container Instances](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testen of ontwikkelen | &nbsp;  | &nbsp; | U kunt deze gebruiken voor werk belastingen op laag schaal die kleiner zijn dan 48 GB RAM-geheugen. |
| [Azure Machine Learning compute-clusters](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Evaluatie Batch-&nbsp;afleiding | [Ja](../articles/machine-learning/service/how-to-run-batch-predictions.md) (machine learning pijp lijn) | &nbsp;  | Batch Score uitvoeren op serverloze reken kracht. Biedt ondersteuning voor virtuele machines met normale en lage prioriteit. |
| [Azure Functions](../articles/machine-learning/service/how-to-deploy-functions.md) | Evaluatie Real-time deinterferentie | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Evaluatie IoT&nbsp;-module |  &nbsp; | &nbsp; | Implementeer en bedient ML modellen op IoT-apparaten. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | Ja | Implementeer en bedient ML modellen op IoT-apparaten. |

> [!NOTE]
> Hoewel Compute-doelen als lokale, Azure Machine Learning Reken instantie en Azure Machine Learning Reken clusters GPU ondersteunen voor training en experimenten, met behulp van GPU voor demijnen __Wanneer een webservice wordt geïmplementeerd__ , wordt deze alleen ondersteund op Azure Kubernetes-service.
>
> Het gebruik van een GPU voor het afstellen van een __Score met een machine learning pijp lijn__ wordt alleen ondersteund voor Azure machine learning compute.