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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537607"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Een beveiligingsagent voor Azure Security Center for IoT op basis van C# implementeren voor Windows

In deze handleiding wordt uitgelegd hoe u de azure security center voor iot c#-gebaseerde beveiligingsagent op Windows installeert.

In deze handleiding leert u het volgende:

> [!div class="checklist"]
> * Installeren
> * Implementatie verifiëren
> * Agent verwijderen
> * Problemen oplossen

## <a name="prerequisites"></a>Vereisten

Zie [De juiste beveiligingsagent kiezen voor](how-to-deploy-agent.md)andere platforms en agentsmaken.

1. Lokale beheerdersrechten op de machine waarop u wilt installeren.

1. [Maak een beveiligingsmodule](quickstart-create-security-twin.md) voor het apparaat.

## <a name="installation"></a>Installeren

Als u de beveiligingsagent wilt installeren, gebruikt u de volgende werkstroom:

1. Installeer de Azure Security Center voor IoT Windows C#-agent op het apparaat. Download de meest recente versie naar uw machine vanuit de Azure Security Center for IoT [GitHub-repository.](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

1. Haal de inhoud van de verpakking eruit en navigeer naar de map /Installeren.

1. Open Windows PowerShell als beheerder.
1. Voeg uitvoerende machtigingen toe aan het InstallSecurityAgent-script door het uitvoeren van:

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    vervolgens uitvoeren:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    Bijvoorbeeld:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    Zie [Verificatie configureren](concept-security-agent-authentication-methods.md)voor meer informatie over verificatieparameters.

Dit script doet de volgende acties:

* Installeert vereisten.
* Hiermee voegt u een servicegebruiker toe (met interactief aanmelden uitgeschakeld).
* Installeert de agent als **een systeemservice.**
* Hiermee configureert u de agent met de opgegeven verificatieparameters.

Voor extra hulp gebruikt u de opdracht Get-Help in PowerShell.

Voorbeeld van Get-Help:```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Implementatiestatus verifiëren

Controleer de status van de implementatie van de agent door het uitvoeren van:

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Agent verwijderen

Ga als u de agent verwijdert:

1. Voer het volgende **PowerShell-script** uit met de parameter -modus die is ingesteld op **Verwijderen**.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>Problemen oplossen

Als de agent niet start, schakelt u logboekregistratie in (logboekregistratie is standaard *uitgeschakeld)* om meer informatie te krijgen.

Logboekregistratie inschakelen:

1. Open het configuratiebestand (General.config) voor bewerken met behulp van een standaard bestandseditor.

1. Bewerk de volgende waarden:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > We raden u aan het afmelden **uit te** schakelen nadat het oplossen van problemen is voltooid. Als u zich **aanmeldt,** wordt de bestandsgrootte en het gegevensgebruik vergroot.

1. Start de agent opnieuw door de volgende PowerShell- of opdrachtregel uit te voeren:

    **Powershell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   of

    **Cmd**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. Bekijk het logboekbestand voor meer informatie over de fout. Het logboekbestand zou aanwezig zijn in de werkmap waar we het script uitvoeren. 

   Locatie van logboekbestand:`.\IoTAgentLog.log`

## <a name="next-steps"></a>Volgende stappen

* Lees het [overzicht](overview.md) van de Azure Security Center for IoT-service
* Meer informatie over Azure Security Center voor [IoT-architectuur](architecture.md)
* De [service inschakelen](quickstart-onboard-iot-hub.md)
* Lees de [faq](resources-frequently-asked-questions.md)
* Inzicht in [waarschuwingen](concept-security-alerts.md)
