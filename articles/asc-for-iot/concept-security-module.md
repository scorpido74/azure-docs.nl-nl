---
title: Informatie over Azure Security Center for IoT security module twins| Microsoft Documenten
description: Meer informatie over het concept van beveiligingsmoduletweelingen en hoe deze worden gebruikt in Azure Security Center for IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: ab3b6e740e644a1ed1495eb776045888be448047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596496"
---
# <a name="security-module"></a>Beveiligingsmodule


In dit artikel wordt uitgelegd hoe Azure Security Center for IoT apparaattweelingen en modules gebruikt. 

## <a name="device-twins"></a>De tweelingen van het apparaat

Voor IoT-oplossingen die in Azure zijn ingebouwd, spelen apparaattweelingen een belangrijke rol in zowel apparaatbeheer als procesautomatisering.  

Azure Security Center for IoT biedt volledige integratie met uw bestaande IoT-apparaatbeheerplatform, zodat u de beveiligingsstatus van uw apparaat beheren en gebruik maken van bestaande apparaatbeheermogelijkheden. Integratie wordt bereikt door gebruik te maken van het IoT Hub twin mechanisme.  

Meer informatie over het concept van [apparaattweelingen](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) in Azure IoT Hub. 

## <a name="security-module-twins"></a>De moduletweelingvan de veiligheid

Azure Security Center for IoT onderhoudt een beveiligingsmodulevoor elk apparaat in de service.
De beveiligingsmodule twee bevat alle informatie die relevant is voor apparaatbeveiliging voor elk specifiek apparaat in uw oplossing.
Beveiligingseigenschappen van apparaten worden onderhouden in een speciale beveiligingsmodulevoor veiligere communicatie en voor het inschakelen van updates en onderhoud waarvoor minder resources nodig zijn.  

Zie [De dubbele beveiligingsmodule maken](quickstart-create-security-twin.md) en [Beveiligingsagents configureren](how-to-agent-configuration.md) voor meer informatie over het maken, aanpassen en configureren van de tweeling. Zie [Moduletweelingbegrijpen](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) voor meer informatie over het concept van moduletweelingen in IoT Hub. 
 

## <a name="see-also"></a>Zie ook
- [Overzicht azure security center voor IoT](overview.md)
- [Beveiligingsagents implementeren](how-to-deploy-agent.md)
- [Verificatiemethoden voor beveiligingsagent](concept-security-agent-authentication-methods.md)