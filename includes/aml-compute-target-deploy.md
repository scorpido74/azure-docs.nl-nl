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
ms.openlocfilehash: e402989c6bdbbc90ae10047fcf19f4f2d74328a6
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014754"
---
| COMPUTE-doel | Gebruik | GPU-ondersteuning | FPGA-ondersteuning | Description |
| ----- | ----- | ----- | ----- | ----- |
| [Local&nbsp;web&nbsp;service](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testen/fouten opsporen | &nbsp; | &nbsp; | Geschikt voor beperkte testen en probleem oplossing. Hardwareversnelling is afhankelijk van het gebruik van bibliotheken in het lokale systeem.
| [VM&nbsp;van notebook&nbsp;-webservice](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Testen/fouten opsporen | &nbsp; | &nbsp; | Geschikt voor beperkte testen en probleem oplossing. 
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Real-time deinterferentie |  [klikt](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [klikt](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Geschikt voor grootschalige productie-implementaties. Biedt snelle reactie tijd en automatisch schalen van de geïmplementeerde service. Cluster automatisch schalen wordt niet ondersteund via de Azure Machine Learning SDK. Als u de knoop punten in het AKS-cluster wilt wijzigen, gebruikt u de gebruikers interface voor uw AKS-cluster in de Azure Portal. AKS is de enige optie die beschikbaar is voor de visuele interface. |
| [Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testen of dev | &nbsp;  | &nbsp; | Geschikt voor kleinschalige, op CPU gebaseerde workloads die < 48 GB RAM-geheugen vereisen |
| [Azure Machine Learning-Computing](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Evaluatie Batch&nbsp;-deinterferentie | &nbsp; | &nbsp;  | Batch Score uitvoeren op serverloze reken kracht. Biedt ondersteuning voor virtuele machines met normale en lage prioriteit. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Evaluatie IOT&nbsp;-module |  &nbsp; | &nbsp; | Implementeer & op IoT-apparaten. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | ja | Implementeer & op IoT-apparaten. |

> [!NOTE]
> Reken doelen als lokale, laptop-VM en Azure Machine Learning Compute-ondersteuning GPU voor training en experimenten, wordt met behulp van GPU alleen ondersteund in de Azure Kubernetes-service.