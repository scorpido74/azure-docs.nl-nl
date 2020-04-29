---
title: Architectuur van beveiligings agent
description: Inzicht in de architectuur van de beveiligings agent voor de agents die worden gebruikt in de Azure Security Center voor IoT-service.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81310687"
---
# <a name="security-agent-reference-architecture"></a>Referentie architectuur beveiligings agent

Azure Security Center voor IoT biedt referentie architectuur voor beveiligings agenten waarmee beveiligings gegevens via IoT Hub kunnen worden geregistreerd, verwerkt, geaggregeerd en verzonden.

Beveiligings agenten zijn ontworpen om te werken in een beperkte IoT-omgeving en zijn zeer aanpasbaar in termen van waarden die ze bieden in vergelijking met de resources die ze gebruiken.

Beveiligings agenten bieden ondersteuning voor de volgende functies:

- Onbewerkte beveiligings gebeurtenissen van het onderliggende besturings systeem (Linux, Windows) verzamelen. Zie [Azure Security Center voor IOT-agent configuratie voor](how-to-agent-configuration.md)meer informatie over beschik bare beveiligings gegevens verzamelaars.

- Verdeel onbewerkte beveiligings gebeurtenissen in berichten die via IoT Hub worden verzonden.

- Verifieer met de bestaande apparaat-id of een toegewezen module-identiteit. Zie [verificatie methoden voor beveiligings agenten](concept-security-agent-authentication-methods.md) voor meer informatie.

- Configureer op afstand via het gebruik van de **azureiotsecurity** -module dubbele. Zie [Configure a Azure Security Center for IOT agent](how-to-agent-configuration.md)(Engelstalig) voor meer informatie.

Azure Security Center voor IoT-beveiligings agenten zijn ontwikkeld als open-source projecten en zijn verkrijgbaar via GitHub:

- [Azure Security Center voor IoT C-agent](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Azure Security Center voor IoT C#-gebaseerde agent](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Door agents ondersteunde platforms

Azure Security Center voor IoT biedt verschillende installatie agenten voor 32-en 64-bits Windows en hetzelfde voor 32-en 64-bits Linux. Zorg ervoor dat u het juiste installatie programma voor de agent hebt voor elk van uw apparaten volgens de volgende tabel:

| Architectuur | Linux | Windows |    Details|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32  | C  | C#  ||
| 64  | C# of C           | C#      | We raden u aan om de C-agent te gebruiken voor apparaten met meer beperkte of minimale bronnen van apparaten.|
|

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over Azure Security Center voor de architectuur van de IoT-beveiligings agent en de beschik bare installatie Programma's.

Als u aan de slag wilt gaan met Azure Security Center voor IoT-implementatie, gebruikt u de volgende artikelen:

- Meer informatie over [verificatie methoden voor beveiligings agenten](concept-security-agent-authentication-methods.md)
- Een [beveiligings agent](how-to-deploy-agent.md) selecteren en implementeren
- Bekijk de Azure Security Center voor vereisten voor IoT- [Services](service-prerequisites.md)
- Meer informatie over het [inschakelen van Azure Security Center voor IOT-service in uw IOT hub](quickstart-onboard-iot-hub.md)
- Meer informatie over de service vindt u in de [Veelgestelde vragen over Azure Security Center voor IOT](resources-frequently-asked-questions.md)
