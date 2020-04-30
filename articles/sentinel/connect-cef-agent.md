---
title: De logboek-doorstuur server implementeren om CEF-gegevens te verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het implementeren van de agent voor het verbinden van CEF-gegevens met Azure Sentinel.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731649"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Stap 1: de logboek-doorstuur server implementeren


In deze stap wijst en configureert u de Linux-computer waarmee de logboeken worden doorgestuurd van uw beveiligings oplossing naar uw Azure Sentinel-werk ruimte. Deze computer kan een fysieke of virtuele machine in uw on-premises omgeving, een Azure-VM of een VM in een andere cloud zijn. Met behulp van de opgegeven koppeling voert u een script uit op de aangewezen computer die de volgende taken uitvoert:
- Installeert de Log Analytics agent voor Linux (ook wel bekend als de OMS-agent) en configureert deze voor de volgende doel einden:
    - Luis teren naar CEF-berichten van de ingebouwde Linux syslog-daemon op TCP-poort 25226
    - de berichten veilig via TLS verzenden naar uw Azure Sentinel-werk ruimte, waar ze worden geparseerd en verrijkt

- Hiermee configureert u de ingebouwde Linux syslog-daemon (rsyslog. d/syslog-ng) voor de volgende doel einden:
    - Luis teren naar syslog-berichten van uw beveiligings oplossingen op TCP-poort 514
    - door sturen van alleen de berichten die worden geïdentificeerd als CEF aan de Log Analytics-agent op localhost via TCP-poort 25226
 
## <a name="prerequisites"></a>Vereisten

- U moet een verhoogde machtigingen (sudo) hebben op de aangewezen Linux-machine.
- Python moet zijn geïnstalleerd op de Linux-machine.<br>Gebruik de `python -version` opdracht om te controleren.
- De Linux-machine mag niet worden verbonden met een Azure-werk ruimte voordat u de Log Analytics-Agent installeert.

## <a name="run-the-deployment-script"></a>Het implementatie script uitvoeren
 
1. Klik in het navigatie menu van de Azure-Sentinel op **Data connectors**. Klik in de lijst met connectors op de tegel **algemene gebeurtenis indeling (CEF)** en klik vervolgens op de knop **pagina connector openen** op de rechter benedenhoek. 

1. Onder **1,2 Installeer de CEF-Collector op de Linux-computer**, kopieer de koppeling die u hebt gekregen onder **Voer het volgende script uit om de CEF-Collector te installeren en toe te passen**, of uit de onderstaande tekst:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Zorg ervoor dat het script wordt uitgevoerd en controleer of er geen fout-of waarschuwings berichten worden weer gegeven.

Ga verder met [stap 2: uw beveiligings oplossing configureren voor het door sturen van CEF-berichten](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Uitleg bij implementatie script

Hier volgt een beschrijving van de opdracht-voor-opdracht van de acties van het implementatie script.

Kies een syslog-daemon om de juiste beschrijving te bekijken.

# <a name="rsyslog-daemon"></a>[rsyslog-daemon](#tab/rsyslog)

1. **De Log Analytics-agent downloaden en installeren:**

    - Hiermee wordt het installatie script voor de Linux-agent van Log Analytics (OMS) gedownload<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Hiermee wordt de Log Analytics agent geïnstalleerd<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **De syslog-daemon configureren:**

    1. Opent poort 514 voor TCP-communicatie met het syslog- `/etc/rsyslog.conf`configuratie bestand.

    1. Hiermee configureert u de daemon voor het door sturen van CEF-berichten naar de Log Analytics-agent op TCP-poort 25226 `security-config-omsagent.conf` door een speciaal configuratie bestand `/etc/rsyslog.d/`in te voegen in de map syslog daemon.

        Inhoud van het `security-config-omsagent.conf` bestand:

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **Syslog-daemon opnieuw starten**

    `service rsyslog restart`

1. **De configuratie van de Log Analytics-agent instellen om te Luis teren op poort 25226 en CEF-berichten door sturen naar Azure Sentinel**

    1. Hiermee wordt de configuratie van de GitHub-opslag plaats van Log Analytics agent gedownload<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Hiermee wordt de Log Analytics agent opnieuw gestart<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng-daemon](#tab/syslogng)

1. **De Log Analytics-agent downloaden en installeren:**

    - Hiermee wordt het installatie script voor de Linux-agent van Log Analytics (OMS) gedownload<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Hiermee wordt de Log Analytics agent geïnstalleerd<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **De syslog-daemon configureren:**

    1. Opent poort 514 voor TCP-communicatie met het syslog- `/etc/syslog-ng/syslog-ng.conf`configuratie bestand.

    1. Hiermee configureert u de daemon voor het door sturen van CEF-berichten naar de Log Analytics-agent op TCP-poort 25226 `security-config-omsagent.conf` door een speciaal configuratie bestand `/etc/syslog-ng/conf.d/`in te voegen in de map syslog daemon.

        Inhoud van het `security-config-omsagent.conf` bestand:

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **Syslog-daemon opnieuw starten**

    `service syslog-ng restart`

1. **De configuratie van de Log Analytics-agent instellen om te Luis teren op poort 25226 en CEF-berichten door sturen naar Azure Sentinel**

    1. Hiermee wordt de configuratie van de GitHub-opslag plaats van Log Analytics agent gedownload<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Hiermee wordt de Log Analytics agent opnieuw gestart<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u de Log Analytics-agent implementeert voor het verbinden van CEF-apparaten met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats.md).

