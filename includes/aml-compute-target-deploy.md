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
ms.date: 06/25/2020
ms.openlocfilehash: bd3ac8d512c1b9d151c0dc549ffeee6a05c7f94b
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542772"
---
Het rekendoel dat u gebruikt als host voor uw model, is van invloed op de kosten en beschikbaarheid van het geïmplementeerde eindpunt. Gebruik de onderstaande tabel om een geschikt rekendoel te kiezen.

| Rekendoel | Gebruikt voor | GPU-ondersteuning | FPGA-ondersteuning | Beschrijving |
| ----- | ----- | ----- | ----- | ----- |
| [Lokale&nbsp;web&nbsp;service](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testen/fouten opsporen | &nbsp; | &nbsp; | Gebruiken voor testen en problemen oplossen. Hardwareversnelling is afhankelijk van het gebruik van bibliotheken in het lokale systeem.
| [Azure Machine Learning-rekeninstantie&nbsp;web&nbsp;service](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testen/fouten opsporen | &nbsp; | &nbsp; | Gebruiken voor testen en problemen oplossen.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Realtime deductie |  [Ja](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (webservice-implementatie) | [Ja](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Gebruiken voor grootschalige productie-implementaties. Biedt een snelle reactietijd en automatische schaalaanpassing van de geïmplementeerde service. Automatische schaalaanpassing van clusters wordt niet ondersteund via de Azure Machine Learning SDK. Als u de knooppunten in het AKS-cluster wilt wijzigen, gebruikt u de gebruikersinterface voor uw AKS-cluster in de Azure-portal. AKS is de enige optie die beschikbaar is voor de ontwerpfunctie. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Testen of ontwikkelen | &nbsp;  | &nbsp; | Gebruiken voor lage CPU-werkbelastingen waarvoor minder dan 48 GB RAM-geheugen nodig is. |
| [Azure Machine Learning-rekenclusters](../articles/machine-learning/how-to-use-parallel-run-step.md) | Batch&nbsp;deductie | [Ja](../articles/machine-learning/how-to-use-parallel-run-step.md) (machine learning-pijplijn) | &nbsp;  | Batchscoreberekening uitvoeren op serverloze berekening. Ondersteunt VM's met normale en lage prioriteit. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | (Preview) Realtime deductie | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (Preview) IoT-&nbsp;module |  &nbsp; | &nbsp; | ML-modellen implementeren en bedienen op IoT-apparaten. |
| [Azure Data Box Edge](../articles/databox-online/azure-stack-edge-overview.md)   | Via IoT Edge |  &nbsp; | Ja | ML-modellen implementeren en bedienen op IoT-apparaten. |

> [!NOTE]
> Hoewel GPU voor trainings- en testdoeleinden wordt ondersteund voor rekendoelen als lokale Azure Machine Learning-rekeninstanties en Azure Machine Learning-rekenclusters, wordt het gebruik van GPU voor deductie __bij implementatie als een webservice__ alleen ondersteund in Azure Kubernetes Service.
>
> Het gebruik van een GPU voor deductie __bij scoreberekening met een machine learning-pijplijn__ wordt alleen ondersteund in Azure Machine Learning Compute.

> [!NOTE]
> * Azure-containerinstanties (ACI) zijn alleen geschikt voor kleine modellen met een grootte van minder dan 1 GB. 
> * We raden u aan om AKS-clusters (Azure Kubernetes Service-clusters) met één knooppunt te gebruiken voor het ontwikkelen/testen van grotere modellen.