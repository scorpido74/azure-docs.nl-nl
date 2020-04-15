---
title: Beveiligingsagents selecteren en implementeren
description: Meer informatie over hoe u Azure Security Center voor IoT-beveiligingsagents selecteert en implementeert op IoT-apparaten.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: c3b514c79be87cd136375b4853226426965f4185
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311205"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Een beveiligingsagent selecteren en implementeren op uw IoT-apparaat

Azure Security Center for IoT biedt referentiearchitecturen voor beveiligingsagents die gegevens van IoT-apparaten controleren en verzamelen.
Zie [Referentiearchitectuur voor beveiligingsagent .](security-agent-architecture.md)

Agenten zijn ontwikkeld als open-source projecten, en zijn beschikbaar in twee smaken: <br> [C](https://aka.ms/iot-security-github-c), en [C#](https://aka.ms/iot-security-github-cs).

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Vergelijk smaken van beveiligingsagenten
> * Ontdek ondersteunde agentplatforms
> * Kies de juiste agent smaak voor uw oplossing

## <a name="understand-security-agent-options"></a>Inzicht in de opties van beveiligingsagent

Elk Azure Security Center voor IoT-beveiligingsagentsmaak biedt dezelfde set functies en ondersteunt vergelijkbare configuratieopties.

De C-gebaseerde beveiligingsagent heeft een lagere geheugenvoetafdruk en is de ideale keuze voor apparaten met minder beschikbare resources.

|     | C-gebaseerde beveiligingsagent | C#-gebaseerde beveiligingsagent |
| --- | ----------- | --------- |
| Open-source | Beschikbaar onder [MIT-licentie](https://en.wikipedia.org/wiki/MIT_License) in [GitHub](https://aka.ms/iot-security-github-cs) | Beschikbaar onder [MIT-licentie](https://en.wikipedia.org/wiki/MIT_License) in [GitHub](https://aka.ms/iot-security-github-c) |
| Ontwikkelingstaal    | C | C# |
| Ondersteunde Windows-platforms? | Nee | Ja |
| Windows-vereisten | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Ondersteunde Linux-platforms? | Ja, x64 en x86 | Ja, alleen x64 |
| Linux-vereisten | libunwind8, libcurl3, uuid-runtime, gecontroleerd, audispd-plugins | libunwind8, libcurl3, uuid-runtime, gecontroleerd, audispd-plugins, sudo, netstat, iptables |
| Schijfvoetafdruk | 10,5 MB | 90 MB |
| Geheugenvoetafdruk (gemiddeld) | 5,5 MB | 33 MB |
| [Verificatie](concept-security-agent-authentication-methods.md) naar IoT-hub | Ja | Ja |
| Het [verzamelen van beveiligingsgegevens](how-to-agent-configuration.md#supported-security-events) | Ja | Ja |
| Aggregatie van gebeurtenissen | Ja | Ja |
| Externe configuratie via [beveiligingsmodule twin](concept-security-module.md) | Ja | Ja |
|

## <a name="security-agent-installation-guidelines"></a>Richtlijnen voor de installatie van beveiligingsagenten

Voor **Windows:** Het script Install SecurityAgent.ps1 moet worden uitgevoerd vanuit een PowerShell-venster van de beheerder.

Voor **Linux**: De InstallSecurityAgent.sh moet worden uitgevoerd als superuser. Wij raden u aan de installatieopdracht vooraf te bevestigen met "sudo".

## <a name="choose-an-agent-flavor"></a>Kies een agent smaak

Beantwoord de volgende vragen over uw IoT-apparaten om de juiste agent te selecteren:

- Gebruikt u _Windows Server_ of Windows _IoT Core?_

    [Een c#-gebaseerde beveiligingsagent voor Windows implementeren.](how-to-deploy-windows-cs.md)

- Gebruik je een Linux distributie met x86 architectuur?

    [Implementeer een C-gebaseerde beveiligingsagent voor Linux.](how-to-deploy-linux-c.md)

- Gebruik je een Linux distributie met x64 architectuur?

    Beide agent smaken kunnen worden gebruikt. <br>
    [Implementeer een C-gebaseerde beveiligingsagent voor Linux](how-to-deploy-linux-c.md) en/of [implementeer een C#-gebaseerde beveiligingsagent voor Linux.](how-to-deploy-linux-cs.md)

Beide agent smaken bieden dezelfde set van functies en ondersteuning voor soortgelijke configuratie-opties.
Zie [Vergelijking van beveiligingsagenten](how-to-deploy-agent.md#understand-security-agent-options) voor meer informatie.

## <a name="supported-platforms"></a>Ondersteunde platforms

De volgende lijst bevat alle momenteel ondersteunde platforms.

|Azure Security Center voor IoT-agent |Besturingssysteem |Architectuur |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    x64|
|C|Ubuntu 18.04 |    x64, ARMv7|
|C|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, build 17763    |x64|
|

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over configuratieopties verder naar de handleiding voor agentconfiguratie.
> [!div class="nextstepaction"]
> [Agentconfiguratie hoe te begeleiden](./how-to-agent-configuration.md)
