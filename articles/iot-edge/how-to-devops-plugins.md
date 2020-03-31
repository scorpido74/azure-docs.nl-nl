---
title: CI/CD inschakelen met Jenkins-plug-in - Azure IoT Edge | Microsoft Documenten
description: Met de Azure IoT Edge-extensie voor Jenkins u IoT Edge-ontwikkelings- en implementatietaken integreren in uw bestaande DevOps-oplossing.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510240"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Azure IoT Edge integreren met Jenkins-pijplijnen

Azure IoT Edge heeft ingebouwde ondersteuning voor Azure DevOps en Azure DevOps Projects, maar biedt ook een plug-in om de DevOps-functionaliteit uit te breiden naar Jenkins. [Jenkins](https://jenkins.io/) is een open-source automatiseringsserver die plug-ins gebruikt om vele soorten ontwikkelings- en implementatieprojecten te ondersteunen, waaronder IoT Edge.

De Azure IoT Edge-plug-in voor Jenkins richt zich op continue integratie en continue implementatie. U een build- en pushpijplijn maken die modules bouwt en hun containerafbeeldingen naar uw containerregister duwt. Maak vervolgens een releasepijplijn die modules implementeert naar uw IoT Edge-apparaten.

Voordat u de IoT Edge-plug-in voor Jenkins gaat gebruiken, hebt u een IoT-hub in Azure en een containerregister nodig om uw containerafbeeldingen vast te houden. Gebruik een Azure Service Principal om Jenkins-bijdragermachtigingen te geven aan uw IoT-hub, zodat de plug-in implementaties voor uw IoT Edge-apparaten kan maken.

Als u klaar bent om aan de slag te gaan, zoekt u installatie en gebruikt u details voor de [Azure IoT Edge-plug-in voor Jenkins.](https://plugins.jenkins.io/azure-iot-edge)
