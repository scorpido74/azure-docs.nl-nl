---
title: De logboekexpediteer implementeren om CEF-gegevens te verbinden met Azure Sentinel | Microsoft Documenten
description: Meer informatie over het implementeren van de agent om CEF-gegevens te verbinden met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 5a8b97e5bef57b29f388c86628f0af5d05e1724a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731649"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Stap 1: De logboekvooruitstuurgeman implementeren


In deze stap wijst u de Linux-machine aan en configureert u deze die de logboeken van uw beveiligingsoplossing doorstuurt naar uw Azure Sentinel-werkruimte. Deze machine kan een fysieke of virtuele machine zijn in uw on-premises omgeving, een Azure VM of een VM in een andere cloud. Met behulp van de meegeleverde koppeling voert u een script uit op de aangewezen machine die de volgende taken uitvoert:
- Installeert de Log Analytics-agent voor Linux (ook wel OMS-agent genoemd) en configureert deze voor de volgende doeleinden:
    - luisteren naar CEF berichten van de ingebouwde Linux Syslog daemon op TCP poort 25226
    - de berichten veilig via TLS verzenden naar uw Azure Sentinel-werkruimte, waar ze worden ontleed en verrijkt

- Configureert de ingebouwde Linux Syslog daemon (rsyslog.d/syslog-ng) voor de volgende doeleinden:
    - luisteren naar Syslog-berichten van uw beveiligingsoplossingen op TCP-poort 514
    - alleen de berichten die het identificeert als CEF doorsturen naar de Log Analytics-agent op localhost met TCP-poort 25226
 
## <a name="prerequisites"></a>Vereisten

- U moet verhoogde machtigingen (sudo) op uw aangewezen Linux-machine hebben.
- Je moet python geïnstalleerd hebben op de Linux machine.<br>Gebruik `python -version` de opdracht om het te controleren.
- De Linux-machine mag niet zijn verbonden met Azure-werkruimten voordat u de Log Analytics-agent installeert.

## <a name="run-the-deployment-script"></a>Het implementatiescript uitvoeren
 
1. Klik in het azure sentinel-navigatiemenu op **Gegevensconnectoren**. Klik in de lijst met connectoren op de tegel **Common Event Format (CEF)** en vervolgens op de knop **Connector openen** rechtsonder. 

1. Onder **1.2 Installeer de CEF collector op de Linux-machine,** kopieer de link onder **Voer het volgende script te installeren en toe te passen van de CEF verzamelaar,** of uit de tekst hieronder:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Terwijl het script wordt uitgevoerd, controleert u of u geen foutmeldingen of waarschuwingsberichten ontvangt.

Ga verder naar [STAP 2: Configureer uw beveiligingsoplossing om CEF-berichten door te sturen.](connect-cef-solution-config.md)

## <a name="deployment-script-explained"></a>Implementatiescript uitgelegd

Het volgende is een command-by-command beschrijving van de acties van het implementatiescript.

Kies een syslog daemon om de juiste beschrijving te zien.

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. **De log-analyseagent downloaden en installeren:**

    - Downloadt het installatiescript voor de Log Analytics (OMS) Linux-agent<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Installeert de Log Analytics-agent<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Het configureren van de Syslog daemon:**

    1. Hiermee opent u poort 514 voor `/etc/rsyslog.conf`TCP-communicatie met behulp van het syslog-configuratiebestand .

    1. Hiermee configureert u de daemon om CEF-berichten door te sturen naar de Log `security-config-omsagent.conf` Analytics-agent op `/etc/rsyslog.d/`TCP-poort 25226, door een speciaal configuratiebestand in de syslog daemon-map in te voegen.

        Inhoud van `security-config-omsagent.conf` het bestand:

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **De Syslog daemon opnieuw opstarten**

    `service rsyslog restart`

1. **De configuratie van de Log Analytics-agent instellen om te luisteren op poort 25226 en CEF-berichten doortesturen naar Azure Sentinel**

    1. Downloadt de configuratie van de Log Analytics-agent GitHub-repository<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Start de log-analyse-agent opnieuw<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. **De log-analyseagent downloaden en installeren:**

    - Downloadt het installatiescript voor de Log Analytics (OMS) Linux-agent<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Installeert de Log Analytics-agent<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Het configureren van de Syslog daemon:**

    1. Hiermee opent u poort 514 voor `/etc/syslog-ng/syslog-ng.conf`TCP-communicatie met behulp van het syslog-configuratiebestand .

    1. Hiermee configureert u de daemon om CEF-berichten door te sturen naar de Log `security-config-omsagent.conf` Analytics-agent op `/etc/syslog-ng/conf.d/`TCP-poort 25226, door een speciaal configuratiebestand in de syslog daemon-map in te voegen.

        Inhoud van `security-config-omsagent.conf` het bestand:

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **De Syslog daemon opnieuw opstarten**

    `service syslog-ng restart`

1. **De configuratie van de Log Analytics-agent instellen om te luisteren op poort 25226 en CEF-berichten doortesturen naar Azure Sentinel**

    1. Downloadt de configuratie van de Log Analytics-agent GitHub-repository<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Start de log-analyse-agent opnieuw<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u de log-analyse-agent implementeert om CEF-apparaten te verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats.md)

