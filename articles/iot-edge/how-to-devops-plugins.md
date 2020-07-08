---
title: CI/CD met Jenkins-invoeg toepassing inschakelen-Azure IoT Edge | Microsoft Docs
description: Met de Azure IoT Edge extensie voor Jenkins kunt u IoT Edge ontwikkelings-en implementatie taken integreren in uw bestaande DevOps-oplossing.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76510240"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Azure IoT Edge integreren met Jenkins-pijp lijnen

Azure IoT Edge heeft ingebouwde ondersteuning voor Azure DevOps en Azure DevOps Projects, maar biedt ook een invoeg toepassing om DevOps-functionaliteit uit te breiden naar Jenkins. [Jenkins](https://jenkins.io/) is een open-source automatiserings server die gebruikmaakt van invoeg toepassingen voor het ondersteunen van veel soorten ontwikkelings-en implementatie projecten, waaronder IOT Edge.

De Azure IoT Edge-invoeg toepassing voor Jenkins is gericht op continue integratie en continue implementatie. U kunt een build-en push-pijp lijn maken die modules bouwt en hun container installatie kopieën naar het container register pusht. Maak vervolgens een release pijplijn waarmee modules op uw IoT Edge-apparaten worden geïmplementeerd.

Voordat u begint met het gebruik van de IoT Edge-invoeg toepassing voor Jenkins, hebt u een IoT-hub in Azure en een container register nodig om de container installatie kopieën te bewaren. Gebruik een Azure-Service-Principal om Jenkins Inzender machtigingen voor uw IoT-hub te bieden zodat de invoeg toepassing implementaties voor uw IoT Edge-apparaten kan maken.

Als u klaar bent om aan de slag te gaan, kunt u de installatie en Details voor de [Azure IOT Edge-invoeg toepassing voor Jenkins](https://plugins.jenkins.io/azure-iot-edge)vinden.
