---
title: IoT Edge installeren op Kubernetes | Microsoft Documenten
description: Meer informatie over het installeren van IoT Edge op Kubernetes met behulp van een clusteromgeving voor lokale ontwikkeling
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471282"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>IoT Edge installeren op Kubernetes (Preview)

IoT Edge kan integreren met Kubernetes en het gebruiken als een veerkrachtige, zeer beschikbare infrastructuurlaag. Hier past deze ondersteuning in een IoT Edge-oplossing op hoog niveau:

![k8s intro](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Een goed mentaal model voor deze integratie is om Kubernetes te zien als een andere operationele omgeving IoT Edge-applicaties kunnen draaien op in aanvulling op Linux en Windows.

## <a name="architecture"></a>Architectuur 
Op Kubernetes biedt IoT Edge *Custom Resource Definition* (CRD) voor edge-workloadimplementaties. IoT Edge Agent neemt de rol aan van een *CRD-controller* die de gewenste status van cloudbeheer verzoent met de lokale clusterstatus.

De levensduur van de module wordt beheerd door de Kubernetes-planner, die de beschikbaarheid van de module behoudt en de plaatsing ervan kiest. IoT Edge beheert het edge-toepassingsplatform dat bovenaan wordt uitgevoerd en verzoent continu de gewenste status die is opgegeven in IoT Hub met de status op het randcluster. Het toepassingsmodel is nog steeds het bekende model op basis van IoT Edge-modules en -routes. De IoT Edge Agent-controller voert het toepassingsmodel van *IoT* Edge automatisch uit naar de Kubernetes native constructen zoals pods, implementaties, services enz.

Hier is een architectuurdiagram op hoog niveau:

![kubernetes boog](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Elk onderdeel van de edge-implementatie is beperkt tot een Kubernetes-naamruimte die specifiek is voor het apparaat, waardoor het mogelijk is om dezelfde clusterbronnen te delen tussen meerdere edge-apparaten en hun implementaties.

>[!NOTE]
>IoT Edge op Kubernetes is in [openbare preview.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="tutorials-and-references"></a>Zelfstudies en verwijzingen 

Zie de [IoT Edge op Kubernetes preview docs mini-site](https://aka.ms/edgek8sdoc) voor meer informatie, inclusief diepgaande tutorials en referenties.
