---
title: C#-agent installeren op Windows-apparaat
description: Meer informatie over het installeren van Azure Security Center voor IoT-agent op 32-bits of 64-bits Windows-apparaten.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4d7d2f0a423a50f85160a856480eaa973be7e2b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537607"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Een beveiligingsagent voor Azure Security Center for IoT op basis van C# implementeren voor Windows

In deze hand leiding wordt uitgelegd hoe u de Azure Security Center voor IoT C#-gebaseerde beveiligings Agent installeert in Windows.

In deze handleiding leert u het volgende:

> [!div class="checklist"]
> * Installeren
> * Implementatie verifiëren
> * Agent verwijderen
> * Problemen oplossen

## <a name="prerequisites"></a>Vereisten

Zie [de juiste beveiligings agent kiezen](how-to-deploy-agent.md)voor andere platforms en agents.

1. Lokale beheerders rechten op de computer waarop u wilt installeren.

1. [Maak een beveiligings module](quickstart-create-security-twin.md) voor het apparaat.

## <a name="installation"></a>Installatie

Als u de beveiligings agent wilt installeren, gebruikt u de volgende werk stroom:

1. Installeer de Azure Security Center voor IoT Windows C#-agent op het apparaat. Down load de meest recente versie naar uw computer vanuit de Azure Security Center voor IoT [github-opslag plaats](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

1. Pak de inhoud van het pakket uit en navigeer naar de map/install.

1. Open Windows Power shell als Administrator.
1. Voer de volgende handelingen uit om actieve machtigingen toe te voegen aan het InstallSecurityAgent-script:

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    Voer vervolgens de volgende handelingen uit:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    Bijvoorbeeld:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    Zie [verificatie configureren](concept-security-agent-authentication-methods.md)voor meer informatie over verificatie parameters.

Met dit script worden de volgende acties uitgevoerd:

* Hiermee worden vereisten geïnstalleerd.
* Hiermee wordt een service gebruiker (met interactieve aanmelding uitgeschakeld) toegevoegd.
* Installeert de agent als een **systeem service**.
* Hiermee configureert u de agent met de opgegeven verificatie parameters.

Gebruik de opdracht Get-Help in Power shell voor meer informatie.

Get-Help-voor beeld:```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Implementatie status controleren

Controleer de implementatie status van de agent door uit te voeren:

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Agent verwijderen

De agent verwijderen:

1. Voer het volgende Power shell-script uit met de para meter **-mode** ingesteld op **verwijderen**.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>Problemen oplossen

Als de agent niet kan worden gestart, schakelt u logboek registratie in (logboek registratie is standaard *uitgeschakeld* ) om meer informatie te krijgen.

Logboek registratie inschakelen:

1. Open het configuratie bestand (General. config) voor bewerking met een standaard bestands editor.

1. Bewerk de volgende waarden:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Het is raadzaam om de logboek registratie **uit** te scha kelen nadat de probleem oplossing is voltooid. Wanneer de logboek registratie is ingeschakeld, neemt de grootte **van** het logboek bestand en het gegevens gebruik toe.

1. Start de agent opnieuw door de volgende Power shell of opdracht regel uit te voeren:

    **Zo**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   of

    **CMD**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. Raadpleeg het logboek bestand voor meer informatie over de fout. Het logboek bestand is aanwezig in de werkmap waarin het script wordt uitgevoerd. 

   Locatie van logboek bestand:`.\IoTAgentLog.log`

## <a name="next-steps"></a>Volgende stappen

* Lees het [overzicht](overview.md) van de Azure Security Center voor IOT-service
* Meer informatie over Azure Security Center voor IoT- [architectuur](architecture.md)
* De [service](quickstart-onboard-iot-hub.md) inschakelen
* Lees de [Veelgestelde vragen](resources-frequently-asked-questions.md)
* Meer informatie over [waarschuwingen](concept-security-alerts.md)
