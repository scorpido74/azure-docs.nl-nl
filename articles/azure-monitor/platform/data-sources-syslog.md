---
title: Syslog-berichten verzamelen en analyseren in Azure Monitor | Microsoft Documenten
description: Syslog is een event logging protocol dat gemeenschappelijk is voor Linux. In dit artikel wordt beschreven hoe u het verzamelen van Syslog-berichten in Log Analytics configureren en hoe de records die ze maken, worden beschreven.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/22/2019
ms.openlocfilehash: 8d68a8d6d28d79c50a92cd2d18df2abab26c30ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274721"
---
# <a name="syslog-data-sources-in-azure-monitor"></a>Syslog-gegevensbronnen in Azure Monitor
Syslog is een event logging protocol dat gemeenschappelijk is voor Linux. Toepassingen sturen berichten die op de lokale machine kunnen worden opgeslagen of aan een Syslog-verzamelaar kunnen worden geleverd. Wanneer de Log Analytics-agent voor Linux is geïnstalleerd, configureert deze de lokale Syslog-daemon om berichten naar de agent door te sturen. De agent stuurt het bericht vervolgens naar Azure Monitor waar een bijbehorende record wordt gemaakt.  

> [!NOTE]
> Azure Monitor ondersteunt het verzamelen van berichten die worden verzonden door rsyslog of syslog-ng, waarbij rsyslog de standaarddaemon is. De standaard syslog daemon op versie 5 van Red Hat Enterprise Linux, CentOS en Oracle Linux versie (sysklog) wordt niet ondersteund voor syslog event collection. Om sysloggegevens uit deze versie van deze distributies te verzamelen, moet de [rsyslog daemon](http://rsyslog.com) worden geïnstalleerd en geconfigureerd om sysklog te vervangen.
>
>

![Syslog collectie](media/data-sources-syslog/overview.png)

De volgende faciliteiten worden ondersteund met de Syslog verzamelaar:

* kern
* gebruiker
* mail
* Daemon
* Auth
* syslog
* Lpr
* news
* Uucp
* Cron
* authpriv authpriv authpriv
* ftp
* local0-local7

Configureer voor elke andere faciliteit [een gegevensbron voor aangepaste logboeken](data-sources-custom-logs.md) in Azure Monitor.
 
## <a name="configuring-syslog"></a>Syslog configureren
De Log Analytics-agent voor Linux verzamelt alleen gebeurtenissen met de faciliteiten en ernst die zijn opgegeven in de configuratie. U Syslog configureren via de Azure-portal of door configuratiebestanden op uw Linux-agents te beheren.

### <a name="configure-syslog-in-the-azure-portal"></a>Syslog configureren in de Azure-portal
Configureer Syslog in het [menu Gegevens in Geavanceerde instellingen](agent-data-sources.md#configuring-data-sources). Deze configuratie wordt geleverd aan het configuratiebestand op elke Linux-agent.

U een nieuwe faciliteit toevoegen door eerst de optie **Toepassen onder configuratie op mijn machines te** selecteren en vervolgens de naam in te typen en te **+** klikken. Voor elke faciliteit worden alleen berichten met de geselecteerde ernst verzameld.  Controleer de ernst van de specifieke faciliteit die u wilt verzamelen. U geen aanvullende criteria geven om berichten te filteren.

![Syslog configureren](media/data-sources-syslog/configure.png)

Standaard worden alle configuratiewijzigingen automatisch naar alle agents gepusht. Als u Syslog handmatig wilt configureren op elke Linux-agent, schakelt u het selectievakje *Onderstaande configuratie toepassen op mijn machines uit.*

### <a name="configure-syslog-on-linux-agent"></a>Syslog configureren op Linux-agent
Wanneer de [Log Analytics-agent op een Linux-client is geïnstalleerd,](../../azure-monitor/learn/quick-collect-linux-computer.md)installeert deze een standaard syslog-configuratiebestand dat de faciliteit en de ernst van de berichten die worden verzameld, definieert. U dit bestand wijzigen om de configuratie te wijzigen. Het configuratiebestand is anders afhankelijk van de Syslog daemon die de client heeft geïnstalleerd.

> [!NOTE]
> Als u de syslog-configuratie bewerkt, moet u de syslog-daemon opnieuw starten om de wijzigingen van kracht te laten worden.
>
>

#### <a name="rsyslog"></a>Rsyslog
Het configuratiebestand voor rsyslog bevindt zich op **/etc/rsyslog.d/95-omsagent.conf**. De standaardinhoud wordt hieronder weergegeven. Dit verzamelt syslog berichten verzonden van de lokale agent voor alle faciliteiten met een niveau van waarschuwing of hoger.

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

U een faciliteit verwijderen door het gedeelte van het configuratiebestand te verwijderen. U de ernst beperken die voor een bepaalde faciliteit wordt verzameld door de vermelding van die faciliteit te wijzigen. Als u bijvoorbeeld de gebruikersfaciliteit wilt beperken tot berichten met een ernst van de fout of hoger, wijzigt u die regel van het configuratiebestand tot het volgende:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng
Het configuratiebestand voor syslog-ng is locatie op **/etc/syslog-ng/syslog-ng.conf**.  De standaardinhoud wordt hieronder weergegeven. Dit verzamelt syslog berichten verzonden van de lokale agent voor alle faciliteiten en alle ernst.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };

    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };

    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };

    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };

    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };

    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };

    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

U een faciliteit verwijderen door het gedeelte van het configuratiebestand te verwijderen. U de ernst beperken die voor een bepaalde faciliteit wordt verzameld door ze uit de lijst te verwijderen.  Als u bijvoorbeeld wilt dat de gebruikersfaciliteit wordt beperkt tot alleen waarschuwingsberichten en kritieke berichten, wijzigt u dat gedeelte van het configuratiebestand als volgt:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Gegevens verzamelen van aanvullende Syslog-poorten
De Log Analytics-agent luistert naar Syslog-berichten op de lokale client op poort 25224.  Wanneer de agent is geïnstalleerd, wordt een standaard syslog-configuratie toegepast en op de volgende locatie gevonden:

* Rsyslog:`/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng:`/etc/syslog-ng/syslog-ng.conf`

U het poortnummer wijzigen door twee configuratiebestanden te maken: een FluentD config-bestand en een rsyslog-or-syslog-ng-bestand, afhankelijk van de Syslog-daemon die u hebt geïnstalleerd.  

* Het FluentD-config-bestand moet een `/etc/opt/microsoft/omsagent/conf/omsagent.d` nieuw bestand zijn waarin zich is gevestigd: en vervang de waarde in de **poortinvoer** door uw aangepaste poortnummer.

        <source>
          type syslog
          port %SYSLOG_PORT%
          bind 127.0.0.1
          protocol_type udp
          tag oms.syslog
        </source>
        <filter oms.syslog.**>
          type filter_syslog
        </filter>

* Voor rsyslog moet u een nieuw configuratiebestand maken waarin zich zich bevindt: `/etc/rsyslog.d/` en de waarde %SYSLOG_PORT% vervangen door uw aangepaste poortnummer.  

    > [!NOTE]
    > Als u deze waarde wijzigt in het configuratiebestand, `95-omsagent.conf`wordt deze overschreven wanneer de agent een standaardconfiguratie toepast.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* De syslog-ng config moet worden gewijzigd door het kopiëren van de voorbeeldconfiguratie hieronder en het toevoegen van `/etc/syslog-ng/`de aangepaste gewijzigde instellingen aan het einde van de syslog-ng.conf configuratiebestand gelegen in . Gebruik het standaardlabel **%WORKSPACE_ID%_oms** of **%WORKSPACE_ID_OMS** **niet,** definieer een aangepast label om uw wijzigingen te onderscheiden.  

    > [!NOTE]
    > Als u de standaardwaarden in het configuratiebestand wijzigt, worden deze overschreven wanneer de agent een standaardconfiguratie toepast.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Na het voltooien van de wijzigingen moeten de Syslog- en de Log Analytics-agentservice opnieuw worden gestart om ervoor te zorgen dat de configuratiewijzigingen van kracht worden.   

## <a name="syslog-record-properties"></a>Syslog-recordeigenschappen
Syslog-records hebben een type **Syslog** en hebben de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Computer |Computer waarvan de gebeurtenis is verzameld. |
| Faciliteit |Hiermee definieert u het deel van het systeem dat het bericht heeft gegenereerd. |
| Hostip |IP-adres van het systeem dat het bericht verzendt. |
| HostName |Naam van het systeem dat het bericht verzendt. |
| ErnstNiveau |Ernstniveau van de gebeurtenis. |
| SyslogMessage (SyslogMessage) |Tekst van het bericht. |
| ProcessID |ID van het proces dat het bericht heeft gegenereerd. |
| EventTijd |Datum en tijd waarop de gebeurtenis is gegenereerd. |

## <a name="log-queries-with-syslog-records"></a>Query's registreren met Syslog-records
In de volgende tabel vindt u verschillende voorbeelden van logboekquery's waarmee Syslog-records worden opgehaald.

| Query’s uitvoeren | Beschrijving |
|:--- |:--- |
| Syslog |Allemaal Syslogs. |
| Syslog &#124; waar SeverityLevel == "fout" |Alle Syslog-records met de ernst van de fouten. |
| Syslog &#124; aggregatedvalue = count() per computer samenvatten |Aantal Syslog-records per computer. |
| Syslog &#124; aggregatedvalue = count() per faciliteit samenvatten |Telling van Syslog records per faciliteit. |

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboekquery's](../../azure-monitor/log-query/log-query-overview.md) om de gegevens te analyseren die zijn verzameld uit gegevensbronnen en -oplossingen.
* Gebruik [Aangepaste velden](../../azure-monitor/platform/custom-fields.md) om gegevens uit syslogrecords in afzonderlijke velden te ontleeden.
* [Configureer Linux-agents](../../azure-monitor/learn/quick-collect-linux-computer.md) om andere soorten gegevens te verzamelen.
