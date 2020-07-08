---
title: Connectiviteit met Azure Sentinel valideren | Microsoft Docs
description: Valideer de connectiviteit van uw beveiligings oplossing om ervoor te zorgen dat CEF-berichten worden doorgestuurd naar Azure Sentinel.
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
ms.openlocfilehash: 07a6b84569fe0356267440e38b31ac738b2659d6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85260828"
---
# <a name="step-3-validate-connectivity"></a>STAP 3: connectiviteit valideren

Nadat u uw logboek doorstuur server (in stap 1) hebt geïmplementeerd en uw beveiligings oplossing hebt geconfigureerd om IT-CEF berichten te verzenden (in stap 2), volgt u deze instructies om de connectiviteit tussen uw beveiligings oplossing en Azure-Sentinel te controleren. 

## <a name="prerequisites"></a>Vereisten

- U moet een verhoogde machtigingen (sudo) hebben op uw logboek-doorstuur machine.

- Python moet op uw logboek-doorstuur machine zijn geïnstalleerd.<br>
Gebruik de `python –version` opdracht om te controleren.

## <a name="how-to-validate-connectivity"></a>Connectiviteit valideren

1. Open **Logboeken**vanuit het Sentinel-navigatie menu van Azure. Voer een query uit met behulp van het **CommonSecurityLog** -schema om te controleren of er logboeken van uw beveiligings oplossing worden ontvangen.<br>
Houd er rekening mee dat het ongeveer 20 minuten kan duren voordat uw logboeken in **log Analytics**worden weer gegeven. 

1. Als er geen resultaten van de query worden weer geven, controleert u of er gebeurtenissen worden gegenereerd op basis van uw beveiligings oplossing of probeer het te genereren, en te controleren of ze worden doorgestuurd naar de door u opgegeven syslog-doorstuur machine. 

1. Voer het volgende script uit op de logboek-doorstuur server om de connectiviteit tussen uw beveiligings oplossing, de logboek forwarder en Azure Sentinel te controleren. Met dit script wordt gecontroleerd of de daemon op de juiste poorten luistert, of het door sturen correct is geconfigureerd en dat er geen communicatie tussen de daemon en de Log Analytics agent wordt geblokkeerd. Er worden ook beeldberichten TestCommonEventFormat verzonden om end-to-end-connectiviteit te controleren. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Validatie script uitgelegd

Het validatie script voert de volgende controles uit:

# <a name="rsyslog-daemon"></a>[rsyslog-daemon](#tab/rsyslog)

1. Hiermee wordt gecontroleerd of het bestand<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    bestaat en is geldig.

1. Hiermee wordt gecontroleerd of het bestand de volgende tekst bevat:

        <source>
            type syslog
            port 25226
            bind 127.0.0.1
            protocol_type tcp
            tag oms.security
            format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
            <parse>
                message_format auto
            </parse>
        </source>

        <filter oms.security.**>
            type filter_syslog_security
        </filter>

1. Hiermee wordt gecontroleerd of er beveiligings uitbreidingen worden uitgevoerd op de computer die netwerk verkeer mogelijk blokkeert (zoals een firewall van een host).

1. Controleert of de syslog-daemon (rsyslog) correct is geconfigureerd voor het verzenden van berichten die worden geïdentificeerd als CEF (met behulp van een reguliere expressie) naar de Log Analytics agent op TCP-poort 25226:

    - Configuratie bestand:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF"|"ASA"
            *.* @@127.0.0.1:25226

1. Controleert of de syslog-daemon gegevens ontvangt op poort 514

1. Controleert of de benodigde verbindingen tot stand zijn gebracht: TCP 514 voor het ontvangen van gegevens, TCP 25226 voor interne communicatie tussen de syslog-daemon en de Log Analytics-agent

1. Verzendt gegevens over het model naar poort 514 op localhost. Deze gegevens moeten worden waarneembaar in de Azure Sentinel-werk ruimte door de volgende query uit te voeren:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[syslog-ng-daemon](#tab/syslogng)

1. Hiermee wordt gecontroleerd of het bestand<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    bestaat en is geldig.

1. Hiermee wordt gecontroleerd of het bestand de volgende tekst bevat:

        <source>
            type syslog
            port 25226
            bind 127.0.0.1
            protocol_type tcp
            tag oms.security
            format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
            <parse>
                message_format auto
            </parse>
        </source>

        <filter oms.security.**>
            type filter_syslog_security
        </filter>

1. Hiermee wordt gecontroleerd of er beveiligings uitbreidingen worden uitgevoerd op de computer die netwerk verkeer mogelijk blokkeert (zoals een firewall van een host).

1. Controleert of de syslog-daemon (syslog-ng) correct is geconfigureerd voor het verzenden van berichten die worden geïdentificeerd als CEF (met behulp van een reguliere expressie) naar de Log Analytics-agent op TCP-poort 25226:

    - Configuratie bestand:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Controleert of de syslog-daemon gegevens ontvangt op poort 514

1. Controleert of de benodigde verbindingen tot stand zijn gebracht: TCP 514 voor het ontvangen van gegevens, TCP 25226 voor interne communicatie tussen de syslog-daemon en de Log Analytics-agent

1. Verzendt gegevens over het model naar poort 514 op localhost. Deze gegevens moeten worden waarneembaar in de Azure Sentinel-werk ruimte door de volgende query uit te voeren:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u CEF-apparaten verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.

