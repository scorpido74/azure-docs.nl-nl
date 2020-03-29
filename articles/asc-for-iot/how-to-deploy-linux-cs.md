---
title: Handleiding voor het installeren en implementeren van Linux C#-agent van Azure Security Center voor IoT| Microsoft Documenten
description: Meer informatie over het installeren van het Azure Security Center voor IoT-agent op zowel 32-bits als 64-bits Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: b675198756ff7bc0791d49fee3649717e3e4da7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75367413"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Een beveiligingsagent voor Azure Security Center for IoT op basis van C# implementeren voor Linux


In deze handleiding wordt uitgelegd hoe u het Azure Security Center voor IoT C#-gebaseerde beveiligingsagent op Linux installeren en implementeren.

In deze handleiding leert u het volgende: 
> [!div class="checklist"]
> * Installeren
> * Implementatie verifiëren
> * Agent verwijderen
> * Problemen oplossen 

## <a name="prerequisites"></a>Vereisten

Zie [De juiste beveiligingsagent kiezen voor](how-to-deploy-agent.md)andere platforms en agentsmaken.

1. Om de beveiligingsagent te implementeren, zijn lokale beheerdersrechten vereist op de machine waarop u wilt installeren. 

1. [Maak een beveiligingsmodule](quickstart-create-security-twin.md) voor het apparaat.

## <a name="installation"></a>Installeren 

Als u de beveiligingsagent wilt implementeren, gebruikt u de volgende stappen:

1. Download de meest recente versie van [GitHub](https://aka.ms/iot-security-github-cs)naar uw machine.

1. Haal de inhoud van de verpakking eruit en navigeer naar de map _/Installeren._

1. Uitvoerenvan machtigingen toevoegen aan het **InstallSecurityAgent-script** door`chmod +x InstallSecurityAgent.sh` 

1. Voer vervolgens de volgende opdracht uit met **hoofdbevoegdheden:** 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Zie [Verificatie configureren](concept-security-agent-authentication-methods.md)voor meer informatie over verificatieparameters.

In dit script worden de volgende acties uitgevoerd:

- Installeert vereisten.

- Hiermee voegt u een servicegebruiker toe (met interactief aanmelden uitgeschakeld).

- Installeert de agent als een **Daemon** - gaat ervan uit dat het apparaat **systemd** gebruikt voor een klassiek implementatiemodel.

- Hiermee configureert **u sudoers** zodat de agent bepaalde taken als root kan uitvoeren.

- Hiermee configureert u de agent met de opgegeven verificatieparameters.


Voer voor extra hulp het script uit met de parameter -Help:`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Agent verwijderen

Als u de agent wilt verwijderen, voert `./InstallSecurityAgent.sh -u`u het script uit met de parameter –u: . 

> [!NOTE]
> Als u de verwijdering verwijdert, worden ontbrekende vereisten die tijdens de installatie zijn geïnstalleerd, niet verwijderd.

## <a name="troubleshooting"></a>Problemen oplossen  

1. Controleer de implementatiestatus door het uitvoeren van:

    `systemctl status ASCIoTAgent.service`

2. Logboekregistratie inschakelen.  
   Als de agent niet start, schakelt u logboekregistratie in om meer informatie te krijgen.

   Schakel de logboekregistratie in door:

   1. Open het configuratiebestand voor bewerking in een Linux-editor:

        `vi /var/ASCIoTAgent/General.config`

   1. Bewerk de volgende waarden: 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       De **waarde logFilePath** is configureerbaar. 

       > [!NOTE]
       > We raden u aan het afmelden **uit te** schakelen nadat het oplossen van problemen is voltooid. Als u zich **aanmeldt,** wordt de bestandsgrootte en het gegevensgebruik vergroot.

   1. Start de agent opnieuw door het uitvoeren van:

       `systemctl restart ASCIoTAgent.service`

   1. Bekijk het logboekbestand voor meer informatie over de fout.  

       Logbestandslocatie is:`/var/ASCIoTAgent/IotAgentLog.log`

       Wijzig het pad naar de locatie van het bestand op basis van de naam die u hebt gekozen voor de **logFilePath** in stap 2. 

## <a name="next-steps"></a>Volgende stappen

- Lees het [overzicht](overview.md) van de Azure Security Center for IoT-service
- Meer informatie over Azure Security Center voor [IoT-architectuur](architecture.md)
- De [service inschakelen](quickstart-onboard-iot-hub.md)
- Lees de [faq](resources-frequently-asked-questions.md)
- Inzicht in [waarschuwingen](concept-security-alerts.md)
