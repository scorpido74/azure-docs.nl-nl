---
title: Beveiligingsmodule en apparaattweeling
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
ms.openlocfilehash: d598d291612c6e4f58caf77e1b213b2bc3f42820
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311448"
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
