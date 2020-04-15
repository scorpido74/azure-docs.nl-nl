---
title: Linux C-agent & implementeren
description: Meer informatie over het installeren van het Azure Security Center voor IoT-agent op zowel 32-bits als 64-bits Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: d9f9602a19a266c70b17422e90566f72de2978f6
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311196"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Een beveiligingsagent voor Azure Security Center for IoT op basis van C implementeren voor Linux

In deze handleiding wordt uitgelegd hoe u het Azure Security Center voor IoT C-gebaseerde beveiligingsagent op Linux installeren en implementeren.

In deze handleiding leert u het volgende:

> [!div class="checklist"]
> * Installeren
> * Implementatie verifiëren
> * Agent verwijderen
> * Problemen oplossen

## <a name="prerequisites"></a>Vereisten

Zie [De juiste beveiligingsagent kiezen voor](how-to-deploy-agent.md)andere platforms en agentsmaken.

1. Om de beveiligingsagent te implementeren, zijn lokale beheerdersrechten vereist op de machine waarop u wilt installeren (sudo).

1. [Maak een beveiligingsmodule](quickstart-create-security-twin.md) voor het apparaat.

## <a name="installation"></a>Installeren

Als u de beveiligingsagent wilt installeren en implementeren, gebruikt u de volgende werkstroom:

1. Download de meest recente versie van [GitHub](https://aka.ms/iot-security-github-c)naar uw machine.

1. Haal de inhoud van de verpakking eruit en navigeer naar de map _/src/installatie._

1. Voeg voermachtigingen toe aan het **Script InstallSecurityAgent** door de volgende opdracht uit te voeren:

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Vervolgens uitvoeren:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Zie [Verificatie configureren](concept-security-agent-authentication-methods.md) voor meer informatie over verificatieparameters.

In dit script wordt de volgende functie uitgevoerd:

1. Installeert vereisten.

1. Hiermee voegt u een servicegebruiker toe (met interactief aanmelden uitgeschakeld).

1. Installeert de agent als een **Daemon** - gaat ervan uit dat het apparaat **systemd** gebruikt voor servicebeheer.

1. Hiermee configureert u de agent met de opgegeven verificatieparameters.

Voer voor extra hulp het script uit met de parameter -Help:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Agent verwijderen

Als u de agent wilt verwijderen, voert u het script uit met de parameter -verwijderen:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Problemen oplossen

Controleer de implementatiestatus door het uitvoeren van:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Volgende stappen

- Lees het [overzicht](overview.md) van de Azure Security Center for IoT-service
- Meer informatie over Azure Security Center voor [IoT-architectuur](architecture.md)
- De [service inschakelen](quickstart-onboard-iot-hub.md)
- Lees de [faq](resources-frequently-asked-questions.md)
- [Beveiligingswaarschuwingen begrijpen](concept-security-alerts.md)
