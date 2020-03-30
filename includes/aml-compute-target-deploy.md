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
ms.openlocfilehash: ba1e0eee5de65364d8e5e762195f7de98384c2a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76122577"
---
| Rekendoel | Gebruikt voor | GPU-ondersteuning | FPGA-ondersteuning | Beschrijving |
| ----- | ----- | ----- | ----- | ----- |
| [Lokale&nbsp;&nbsp;webservice](../articles/machine-learning/how-to-deploy-and-where.md#local) | Testen/debuggen | &nbsp; | &nbsp; | Gebruiken voor beperkte tests en probleemoplossing. Hardwareversnelling is afhankelijk van het gebruik van bibliotheken in het lokale systeem.
| [Webservice voor Azure Machine Learning compute instance&nbsp;&nbsp;](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Testen/debuggen | &nbsp; | &nbsp; | Gebruiken voor beperkte tests en probleemoplossing.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Real-time gevolgtrekking |  [Ja](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (webservice-implementatie) | [Ja](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Gebruik voor grootschalige productie-implementaties. Biedt een snelle responstijd en automatisch schalen van de geïmplementeerde service. Clusterautomatisch schalen wordt niet ondersteund via de Azure Machine Learning SDK. Als u de knooppunten in het AKS-cluster wilt wijzigen, gebruikt u de gebruikersinterface voor uw AKS-cluster in de Azure-portal. AKS is de enige optie die beschikbaar is voor de ontwerper. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Testen of ontwikkelen | &nbsp;  | &nbsp; | Gebruik voor low-scale CPU-gebaseerde workloads waarvoor minder dan 48 GB RAM nodig is. |
| [Azure Machine Learning-rekenclusters](../articles/machine-learning/how-to-use-parallel-run-step.md) | (Preview) Partijgevolgtrekking&nbsp; | [Ja](../articles/machine-learning/how-to-use-parallel-run-step.md) (machine learning-pijplijn) | &nbsp;  | Batchscores uitvoeren op serverless compute. Ondersteunt normale en lage prioriteit VM's. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | (Preview) Real-time gevolgtrekking | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (Preview) IoT-module&nbsp; |  &nbsp; | &nbsp; | Mi-modellen implementeren en serveren op IoT-apparaten. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | Ja | Mi-modellen implementeren en serveren op IoT-apparaten. |

> [!NOTE]
> Hoewel rekendoelen zoals lokale, Azure Machine Learning-compute instance en Azure Machine Learning compute clusters GPU ondersteunen voor training en experimenten, wordt het gebruik van GPU voor gevolgtrekking __wanneer deze wordt geïmplementeerd als een webservice__ alleen ondersteund op Azure Kubernetes Service.
>
> Het gebruik van een GPU voor gevolgtrekking __bij het scoren met een machine learning-pijplijn__ wordt alleen ondersteund op Azure Machine Learning Compute.