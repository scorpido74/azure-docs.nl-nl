---
title: Architectuur van beveiligingsagenten
description: Inzicht in de architectuur van beveiligingsagents voor de agents die worden gebruikt in de Azure Security Center for IoT-service.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 9029ece923b7cda09c7a57d07736791e241c9e70
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310687"
---
# <a name="security-agent-reference-architecture"></a>Referentiearchitectuur voor beveiligingsagenten

Azure Security Center for IoT biedt referentiearchitectuur voor beveiligingsagents die beveiligingsgegevens registreren, verwerken, aggregeren en verzenden via IoT Hub.

Beveiligingsmedewerkers zijn ontworpen om te werken in een beperkte IoT-omgeving en zijn zeer aanpasbaar in termen van waarden die ze bieden in vergelijking met de resources die ze verbruiken.

Beveiligingsagenten ondersteunen de volgende functies:

- Verzamel onbewerkte beveiligingsgebeurtenissen van het onderliggende besturingssysteem (Linux, Windows). Zie [Azure Security Center for IoT-agentconfiguratie voor](how-to-agent-configuration.md)meer informatie over beschikbare beveiligingsgegevensverzamelingen.

- Verzamel onbewerkte beveiligingsgebeurtenissen in berichten die via IoT Hub worden verzonden.

- Verifieer met de bestaande apparaatidentiteit of een specifieke module-identiteit. Zie [verificatiemethoden voor beveiligingsagentvoor](concept-security-agent-authentication-methods.md) meer informatie.

- Configureer op afstand door gebruik te maken van de **azureiotsecurity** module twin. Zie [Een Azure Security Center voor IoT-agent configureren voor](how-to-agent-configuration.md)meer informatie.

Azure Security Center voor IoT-beveiligingsagents zijn ontwikkeld als open-sourceprojecten en zijn beschikbaar via GitHub:

- [Azure Security Center voor IoT C-gebaseerde agent](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Azure Security Center voor IoT C#-gebaseerde agent](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Door de agent ondersteunde platforms

Azure Security Center for IoT biedt verschillende installer agents voor 32bit en 64bit Windows, en hetzelfde voor 32bit en 64bit Linux. Zorg ervoor dat u de juiste agent installer voor elk van uw apparaten volgens de volgende tabel:

| Architectuur | Linux | Windows |    Details|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32bit  | C  | C#  ||
| 64bit  | C# of C           | C#      | We raden u aan de C-agent te gebruiken voor apparaten met beperktere of minimalere apparaatbronnen.|
|

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u meer te weten gekomen over azure security center voor iot-beveiligingsagentarchitectuur en de beschikbare installateurs.

Gebruik de volgende artikelen om verder te gaan met Azure Security Center for IoT-implementatie:

- Verificatiemethoden [voor beveiligingsagent begrijpen](concept-security-agent-authentication-methods.md)
- Een [beveiligingsagent](how-to-deploy-agent.md) selecteren en implementeren
- De vereisten van het Azure Security Center voor IoT-service [controleren](service-prerequisites.md)
- Meer informatie over het [inschakelen van Azure Security Center voor IoT-service in uw IoT-hub](quickstart-onboard-iot-hub.md)
- Meer informatie over de service vindt u van de veelgestelde vragen over [azure security center voor IoT](resources-frequently-asked-questions.md)
