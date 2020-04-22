---
title: Connectiviteit met Azure Sentinel valideren | Microsoft Documenten
description: Valideer de connectiviteit van uw beveiligingsoplossing om ervoor te zorgen dat CEF-berichten worden doorgestuurd naar Azure Sentinel.
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
ms.openlocfilehash: 6b91e36ee09aa855c119add2c0eb268cf8b97393
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731818"
---
# <a name="step-3-validate-connectivity"></a>STAP 3: Connectiviteit valideren

Zodra u uw logboekexpediteier (in stap 1) hebt geïmplementeerd en uw beveiligingsoplossing hebt geconfigureerd om CEF-berichten te verzenden (in stap 2), volgt u deze instructies om de verbinding tussen uw beveiligingsoplossing en Azure Sentinel te verifiëren. 

## <a name="prerequisites"></a>Vereisten

- U moet verhoogde machtigingen (sudo) op uw log forwarder machine.

- U moet Python op uw log forwarder machine hebben geïnstalleerd.<br>
Gebruik `python –version` de opdracht om het te controleren.

## <a name="how-to-validate-connectivity"></a>Connectiviteit valideren

1. Open **Logboeken**in het navigatiemenu azure sentinel . Voer een query uit met het **CommonSecurityLog-schema** om te zien of u logboeken ontvangt van uw beveiligingsoplossing.<br>
Houd er rekening mee dat het ongeveer 20 minuten kan duren voordat uw logboeken worden weergegeven in **Log Analytics.** 

1. Als u geen resultaten van de query ziet, controleert u of gebeurtenissen worden gegenereerd vanuit uw beveiligingsoplossing of probeert u een aantal te genereren en controleert u of ze worden doorgestuurd naar de door u aangewezen Syslog-expediteermachine. 

1. Voer het volgende script uit op de logboekexpediteer om de connectiviteit tussen uw beveiligingsoplossing, de logboekexpediteer en Azure Sentinel te controleren. Dit script controleert of de daemon luistert op de juiste poorten, of het doorsturen correct is geconfigureerd en dat niets de communicatie tussen de daemon en de Log Analytics-agent blokkeert. Het stuurt ook mock berichten 'TestCommonEventFormat' om end-to-end connectiviteit te controleren. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Validatiescript uitgelegd

Het validatiescript voert de volgende controles uit:

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. Controleert of het bestand<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    bestaat en is geldig.

1. Hiermee controleert u of het bestand de volgende tekst bevat:

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

1. Hiermee wordt gecontroleerd of er beveiligingsverbeteringen op de machine zijn die netwerkverkeer kunnen blokkeren (zoals een hostfirewall).

1. Controleert of de syslog daemon (rsyslog) correct is geconfigureerd om berichten die het identificeert als CEF (met behulp van een regex) naar de Log Analytics-agent op TCP-poort 25226 te verzenden:

    - Configuratiebestand:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. Controleert of de syslog daemon gegevens ontvangt op poort 514

1. Controleert of de benodigde verbindingen tot stand komen: tcp 514 voor het ontvangen van gegevens, tcp 25226 voor interne communicatie tussen de syslog daemon en de Log Analytics-agent

1. Hiermee verzendt u MOCK-gegevens naar poort 514 op localhost. Deze gegevens moeten waarneembaar zijn in de Azure Sentinel-werkruimte door de volgende query uit te voeren:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. Controleert of het bestand<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    bestaat en is geldig.

1. Hiermee controleert u of het bestand de volgende tekst bevat:

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

1. Hiermee wordt gecontroleerd of er beveiligingsverbeteringen op de machine zijn die netwerkverkeer kunnen blokkeren (zoals een hostfirewall).

1. Controleert of de syslog daemon (syslog-ng) correct is geconfigureerd om berichten die het identificeert als CEF (met behulp van een regex) naar de Log Analytics-agent op TCP-poort 25226 te verzenden:

    - Configuratiebestand:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Controleert of de syslog daemon gegevens ontvangt op poort 514

1. Controleert of de benodigde verbindingen tot stand komen: tcp 514 voor het ontvangen van gegevens, tcp 25226 voor interne communicatie tussen de syslog daemon en de Log Analytics-agent

1. Hiermee verzendt u MOCK-gegevens naar poort 514 op localhost. Deze gegevens moeten waarneembaar zijn in de Azure Sentinel-werkruimte door de volgende query uit te voeren:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u CEF-apparaten verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.

