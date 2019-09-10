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
ms.openlocfilehash: 29da4ab949cf38419bc7a4d0a3b5da6669441887
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70847675"
---
| COMPUTE-doel | Gebruikt voor | GPU-ondersteuning | FPGA-ondersteuning | Description |
| ----- | ----- | ----- | ----- | ----- |
| [Local&nbsp;web&nbsp;service](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testen/fout opsporing | &nbsp; | &nbsp; | Gebruiken voor beperkte testen en probleem oplossing. Hardwareversnelling is afhankelijk van het gebruik van bibliotheken in het lokale systeem.
| [VM&nbsp;van notebook&nbsp;-webservice](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Testen/fout opsporing | &nbsp; | &nbsp; | Gebruiken voor beperkte testen en probleem oplossing.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Real-time deinterferentie |  [Ja](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md) (web service-implementatie) | [Ja](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Gebruiken voor grootschalige productie-implementaties. Biedt snelle reactie tijd en automatisch schalen van de geïmplementeerde service. Cluster automatisch schalen wordt niet ondersteund via de Azure Machine Learning SDK. Als u de knoop punten in het AKS-cluster wilt wijzigen, gebruikt u de gebruikers interface voor uw AKS-cluster in de Azure Portal. AKS is de enige optie die beschikbaar is voor de visuele interface. |
| [Azure Container Instances](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testen of ontwikkelen | &nbsp;  | &nbsp; | U kunt deze gebruiken voor werk belastingen op laag schaal die kleiner zijn dan 48 GB RAM-geheugen. |
| [Azure Machine Learning-Computing](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Evaluatie Batch&nbsp;-deinterferentie | [Ja](../articles/machine-learning/service/how-to-run-batch-predictions.md) (machine learning pijp lijn) | &nbsp;  | Batch Score uitvoeren op serverloze reken kracht. Biedt ondersteuning voor virtuele machines met normale en lage prioriteit. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Evaluatie IOT&nbsp;-module |  &nbsp; | &nbsp; | Implementeer en bedient ML modellen op IoT-apparaten. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | Ja | Implementeer en bedient ML modellen op IoT-apparaten. |

> [!NOTE]
> Hoewel Compute-doelen als lokale, laptop-VM en Azure Machine Learning Compute-ondersteuning GPU voor training en experimenten, met behulp van GPU voor demijnen __Wanneer een webservice wordt geïmplementeerd__ , wordt deze alleen ondersteund op de Azure Kubernetes-service.
>
> Het gebruik van een GPU voor het afstellen van een __Score met een machine learning pijp lijn__ wordt alleen ondersteund voor Azure machine learning compute.