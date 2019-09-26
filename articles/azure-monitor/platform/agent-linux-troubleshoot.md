---
title: Problemen met Azure Log Analytics Linux-Agent oplossen | Microsoft Docs
description: Beschrijf de symptomen, oorzaken en oplossingen voor de meest voorkomende problemen met de Log Analytics agent voor Linux in Azure Monitor.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 732c93688dbc73cb5a4ce21e4669744be61c5925
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299554"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Het oplossen van problemen met de Log Analytics-agent voor Linux 

In dit artikel vindt u informatie over het oplossen van problemen die u mogelijk ondervindt met de Log Analytics-agent voor Linux in Azure Monitor en worden mogelijke oplossingen voorgesteld om deze op te lossen.

Als geen van deze stappen voor u werkt, zijn ook de volgende ondersteuningskanalen beschikbaar:

* Klanten met Premier-ondersteuning voordelen kunt opent u een ondersteuningsaanvraag met [Premier](https://premier.microsoft.com/).
* Klanten met Azure support-overeenkomsten kunnen een ondersteuningsaanvraag openen [in Azure portal](https://manage.windowsazure.com/?getsupport=true).
* OMI diagnosticeren met de [OMI-gids voor probleemoplossing](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Bestand een [GitHub-probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Ga naar de pagina Log Analytics Feedback om te controleren ingediende ideeën en bugs [ https://aka.ms/opinsightsfeedback ](https://aka.ms/opinsightsfeedback) of een nieuw bestand.  

## <a name="important-log-locations-and-log-collector-tool"></a>Belangrijke logboeklocaties en Logboekverzamelaar hulpprogramma

 File | Pad
 ---- | -----
 Log Analytics-agent voor Linux-logboekbestand | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Log Analytics-agent configuratielogboekbestand | `/var/opt/microsoft/omsconfig/omsconfig.log`

 We raden u aan ons logboek collector-hulpprogramma gebruiken om op te halen van belangrijke logboeken voor het oplossen van problemen of voor het indienen van een GitHub-probleem. U vindt meer informatie over het hulpprogramma en hoe u deze uitvoert [hier](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Belangrijk-configuratiebestanden

 Categorie | Bestandslocatie
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf` of `/etc/rsyslog.conf` of `/etc/rsyslog.d/95-omsagent.conf`
 Prestaties, Nagios, Zabbix, Log Analytics-uitvoer en algemene agent | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Aanvullende configuraties | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Configuratiebestanden voor prestatiemeteritems en Syslog bewerken wordt overschreven als u de verzameling wordt geconfigureerd via de [menu data Log Analytics geavanceerde instellingen](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) in Azure portal voor uw werkruimte. Als u wilt uitschakelen configuratie voor alle agents, verzamelen van Log Analytics uitschakelen **geavanceerde instellingen** of voor een afzonderlijke agent, voert u het volgende:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Foutcodes voor clientinstallatie

| Foutcode | Betekenis |
| --- | --- |
| NOT_DEFINED | Omdat de vereiste afhankelijkheden niet zijn geïnstalleerd, wordt niet de auoms auditd-invoegtoepassing geïnstalleerd | Installatie van auoms is mislukt, installeer pakket auditd. |
| 2 | Ongeldige optie opgegeven voor de shell-bundel. Voer `sudo sh ./omsagent-*.universal*.sh --help` voor gebruik |
| 3 | Er is geen optie opgegeven voor de shell-bundel. Voer `sudo sh ./omsagent-*.universal*.sh --help` voor gebruik. |
| 4 | Typt u het pakket is ongeldig of ongeldige proxy-instellingen. omsagent -*rpm*.sh pakketten kunnen alleen worden geïnstalleerd op de RPM-systemen, en omsagent -*deb*.sh pakketten kunnen alleen worden geïnstalleerd op Debian gebaseerde systemen. Het is raadzaam om het gebruik van de universal installer uit de [nieuwste release](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Controleer ook de verificatie van uw proxy-instellingen. |
| 5 | De shell-bundel moet worden uitgevoerd als root of er is 403-fout geretourneerd tijdens onboarding. Voer uw opdracht met `sudo`. |
| 6 | Ongeldige pakket-architectuur of er is fout 200-fout geretourneerd tijdens onboarding; omsagent -*x64.sh pakketten kunnen alleen worden geïnstalleerd op 64-bits systemen, en omsagent -* x86.sh pakketten kunnen alleen worden geïnstalleerd op 32-bits systemen. Download het juiste pakket voor de architectuur van de [nieuwste release](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Installatie van de OMS-pakket is mislukt. Bekijk de uitvoer van de opdracht voor het root-mislukken. |
| 19 | Installatie van OMI-pakket is mislukt. Bekijk de uitvoer van de opdracht voor het root-mislukken. |
| 20 | Installatie van de SCX-pakket is mislukt. Bekijk de uitvoer van de opdracht voor het root-mislukken. |
| 21 | Installatie van Provider kits is mislukt. Bekijk de uitvoer van de opdracht voor het root-mislukken. |
| 22 | Installatie van gebundelde pakket is mislukt. Bekijk de uitvoer van de opdracht voor het root-fout |
| 23 | SCX of OMI pakket is al geïnstalleerd. Gebruik `--upgrade` in plaats van `--install` voor het installeren van de shell-bundel. |
| 30 | Fout bij het interne bundel. Bestand een [GitHub-probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met informatie uit de uitvoer. |
| 55 | Niet-ondersteunde openssl-versie of kan geen verbinding maken met Azure Monitor of met dpkg is een vergrendeld of ontbrekend krul-programma. |
| 61 | Ontbrekende Python ctypes-bibliotheek. Installeer de Python-bibliotheek voor ctypes of het pakket (python-ctypes). |
| 62 | Ontbrekende tar programma tar installeren. |
| 63 | Ontbrekende sed programma installeren sed. |
| 64 | Ontbrekende curl programma installeren curl. |
| 65 | Ontbrekende gpg programma gpg installeren. |

## <a name="onboarding-error-codes"></a>Onboarding-foutcodes

| Foutcode | Betekenis |
| --- | --- |
| 2 | Ongeldige optie opgegeven voor het script omsadmin. Voer `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` voor gebruik. |
| 3 | Ongeldige configuratie opgegeven voor het script omsadmin. Voer `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` voor gebruik. |
| 4 | Ongeldige proxy opgegeven voor het script omsadmin. Controleer of de proxy en Zie onze [documentatie voor het gebruik van een HTTP-proxy](log-analytics-agent.md#network-firewall-requirements). |
| 5 | 403 HTTP-fout ontvangen van Azure Monitor. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 6 | Er is een niet-200 HTTP-fout ontvangen van Azure Monitor. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 7 | Kan geen verbinding maken met Azure Monitor. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 8 | Onboarding van de fout naar Log Analytics-werkruimte. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 30 | Interne scriptfout. Bestand een [GitHub-probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met informatie uit de uitvoer. |
| 31 | Fout bij het genereren agent-id. Bestand een [GitHub-probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met informatie uit de uitvoer. |
| 32 | Fout bij het genereren van certificaten. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 33 | Fout bij het genereren van metaconfiguration voor omsconfig. Bestand een [GitHub-probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met informatie uit de uitvoer. |
| 34 | Metaconfiguration generatie script niet aanwezig is. Probeer onboarding met `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`. |

## <a name="enable-debug-logging"></a>Inschakelen van logboekregistratie voor foutopsporing
### <a name="oms-output-plugin-debug"></a>OMS-uitvoer-invoegtoepassing foutopsporing
 FluentD kunt voor de invoegtoepassing-specifieke logboekregistratieniveaus zodat u kunt verschillende logboekniveaus voor invoer en uitvoer opgeven. Als u een ander logboek-niveau voor OMS-uitvoer, bewerkt u de configuratie van de algemene agent op `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 In de OMS-uitvoer-invoegtoepassing, vóór het einde van het configuratiebestand, wijzigt u de `log_level` eigenschap `info` naar `debug`:

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

Met logboek registratie voor fout opsporing kunt u batch-uploads zien die worden Azure Monitor gescheiden door type, het aantal gegevens items en de gebruikte tijd voor verzen ding:

*Voorbeeld van het logboek voor foutopsporing ingeschakeld:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Uitgebreide uitvoer
In plaats van de OMS-invoegtoepassing voor uitvoer kunt u ook uitvoeren gegevensitems rechtstreeks naar `stdout`, die wordt weergegeven in de Log Analytics-agent voor Linux-logboekbestand.

In het configuratiebestand van Log Analytics algemeen agent op `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, een opmerking bij de OMS-invoegtoepassing uitvoer door toe te voegen een `#` voor elke regel:

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Onder de uitvoer-invoegtoepassing, verwijder opmerkingen bij de volgende sectie door het verwijderen van de `#` voor elke regel:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Probleem:  Kan geen verbinding maken via een proxy met Azure Monitor

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* De proxy die is opgegeven tijdens de voorbereiding is onjuist
* De Azure Monitor-en Azure Automation Service-eind punten zijn niet white list in uw Data Center 

### <a name="resolution"></a>Oplossing
1. Onboarding voor Azure monitor met de log Analytics-agent voor Linux met behulp van de volgende `-v` opdracht met de optie ingeschakeld. Hiermee kan uitgebreide uitvoer van de agent die verbinding maakt met de proxy, worden Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Raadpleeg de sectie [proxy-instellingen bijwerken](agent-manage.md#update-proxy-settings) om te controleren of de agent kan communiceren via een proxyserver goed is geconfigureerd.    
* Controleer of de volgende Azure Monitor-eind punten white list zijn:

    |Agentresource| Poorten | Richting |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Poort 443| Binnenkomend en uitgaand |  
    |*.oms.opinsights.azure.com | Poort 443| Binnenkomend en uitgaand |  
    |*.blob.core.windows.net | Poort 443| Binnenkomend en uitgaand |  
    |*.azure-automation.net | Poort 443| Binnenkomend en uitgaand | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Probleem: Er wordt een 403-fout weer gegeven wanneer u probeert onboarding uit te voeren

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* Datum en tijd is niet correct op Linux-Server 
* Werkruimte-ID en Werkruimtesleutel gebruikt zijn niet juist

### <a name="resolution"></a>Oplossing

1. Controleer de tijd op uw Linux-server met de datum van de opdracht. Als de tijd +/-15 minuten na de huidige tijd is, mislukt onboarding. Op juiste dit bijwerken de datum en/of de tijdzone van de Linux-server. 
2. Controleer of dat u de nieuwste versie van de Log Analytics-agent voor Linux hebt geïnstalleerd.  De nieuwste versie wordt nu waarschuwt u als tijdverschilbereik wordt veroorzaakt door het onboarding-fout.
3. Reonboard met behulp van de juiste werkruimte-ID en Werkruimtesleutel na de installatie-instructies eerder in dit artikel.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Probleem: U ziet de fout 500 en 404 in het logboek bestand nadat de voor bereiding is uitgevoerd
Dit is een bekend probleem dat zich op de eerste uploaden van gegevens van Linux in een Log Analytics-werkruimte voordoet. Dit heeft geen invloed op gegevens die worden verzonden of service-ervaring.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Probleem: U ziet omiagent met een CPU van 100%

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
Een regressie in nss-PEM Package [v 1.0.3 -5. EL7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) heeft een ernstig prestatie probleem veroorzaakt, die we hebben gezien een heleboel in RedHat/CentOS 7. x-distributies. Raadpleeg de volgende documentatie voor meer informatie over dit probleem: Bug [1667121 prestatie regressie in libkrul](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

Problemen met betrekking tot prestaties doen zich niet altijd voor en ze zijn zeer lastig te reproduceren. Als u een dergelijk probleem ondervindt met omiagent, moet u het script omiHighCPUDiagnostics.sh gebruiken waarmee de stack tracering van de omiagent wordt verzameld wanneer een bepaalde drempel waarde wordt overschreden.

1. Het script downloaden <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Diagnostische gegevens 24 uur uitvoeren met een CPU-drempel van 30% <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Call stack wordt gedumpt in het omiagent_trace-bestand, als u veel krul-en NSS-functie aanroepen ziet, volgt u de onderstaande stappen voor de oplossing.

### <a name="resolution-step-by-step"></a>Oplossing (stapsgewijze stap)

1. Voer een upgrade uit voor het pakket nss-PEM naar [v 1.0.3 -5. EL7 _ 6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Als nss-PEM niet beschikbaar is voor upgrade (meestal gebeurt op CentOS), downgradet u krul naar 7.29.0-46. Als u per ongeluk ' yum update ' uitvoert, wordt krul bijgewerkt naar 7.29.0-51 en wordt het probleem opnieuw uitgevoerd. <br/>
`sudo yum downgrade curl libcurl`

3. OMI opnieuw starten: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Probleem: U ziet geen gegevens in de Azure Portal

### <a name="probable-causes"></a>Waarschijnlijke oorzaken

- Onboarding naar Azure Monitor is mislukt
- De verbinding met de Azure Monitor is geblokkeerd
- Log Analytics-agent voor Linux-gegevens een back-up

### <a name="resolution"></a>Oplossing
1. Controleer of het voorbereidings Azure Monitor is geslaagd door te controleren of het volgende bestand bestaat:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Met behulp van Reonboard de `omsadmin.sh` opdrachtregelinstructies
3. Als u een proxy gebruikt, raadpleegt u de stappen voor het oplossen van proxy die eerder is verkregen.
4. In sommige gevallen, wanneer de Log Analytics-agent voor Linux kan niet met de service communiceren gegevens op de agent is in de wachtrij voor de volledige buffergrootte, 50 MB. De agent moet opnieuw worden gestart door de volgende opdracht uit: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Dit probleem is opgelost in agent versie 1.1.0-28 en hoger.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Probleem: U ziet geen doorgestuurde syslog-berichten 

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* De configuratie die is toegepast op de Linux-server staat niet toe dat de verzameling van de verzonden faciliteiten en/of de logboekniveaus
* Syslog wordt niet correct doorgestuurd naar de Linux-server
* Het aantal berichten per seconde wordt doorgestuurd zijn te groot voor de basisconfiguratie van de Log Analytics-agent voor Linux om af te handelen

### <a name="resolution"></a>Oplossing
* Controleer of dat de configuratie in de werkruimte voor logboekanalyse voor Syslog heeft de faciliteiten en het juiste logboek-niveau. Beoordeling [Syslog-verzameling in Azure portal configureren](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Controleer of de systeemeigen syslog-berichten daemons (`rsyslog`, `syslog-ng`) kunnen het doorgestuurde berichten ontvangen
* Controleer de firewall-instellingen op de Syslog-server om ervoor te zorgen dat berichten worden niet geblokkeerd
* Simuleren van een Syslog-bericht voor het gebruik van Log Analytics `logger` opdracht
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Probleem: Het ontvangen errno-adres wordt al gebruikt in het omsagent-logboek bestand
Als u ziet `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` in omsagent.log.

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
Deze fout geeft aan dat de Linux Diagnostic-extensie (LAD) naast de Log Analytics Linux VM-extensie is geïnstalleerd, en dezelfde poort wordt gebruikt om gegevens te verzamelen als omsagent syslog.

### <a name="resolution"></a>Oplossing
1. Uitvoeren als de hoofdmap van de volgende opdrachten (Let erop dat 25224 een voorbeeld is en is het mogelijk dat in uw omgeving u een ander poortnummer gebruikt door LAD ziet):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Vervolgens moet u de juiste `rsyslogd` of `syslog_ng` config-bestand en de configuratie met betrekking tot LAD schrijven naar poort 25229 wijzigen.

2. Als de virtuele machine wordt uitgevoerd `rsyslogd`, is het bestand dat moet worden gewijzigd: `/etc/rsyslog.d/95-omsagent.conf` (indien aanwezig, anders `/etc/rsyslog`). Als de virtuele machine wordt uitgevoerd `syslog_ng`, is het bestand dat moet worden gewijzigd: `/etc/syslog-ng/syslog-ng.conf`.
3. Opnieuw opstarten omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Syslog-service opnieuw starten.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Probleem: U kunt omsagent niet verwijderen met de optie leegmaken

### <a name="probable-causes"></a>Waarschijnlijke oorzaken

* Linux Diagnostic-extensie is geïnstalleerd
* Linux Diagnostic-extensie is geïnstalleerd en verwijderd, maar u nog steeds een foutbericht dat wordt gebruikt door mdsd omsagent zien en kan niet worden verwijderd.

### <a name="resolution"></a>Oplossing
1. Verwijder de Linux Diagnostic-extensie (LAD).
2. Verwijder bestanden van de Linux Diagnostic-extensie van de machine als deze aanwezig zijn in de volgende locatie: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` en `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Probleem: U kunt geen gegevens van nagios weer geven 

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* Omsagent gebruiker heeft geen machtigingen om te lezen uit Nagios-logbestand
* Nagios-bron- en filter zijn niet zonder opmerkingen van omsagent.conf bestand

### <a name="resolution"></a>Oplossing
1. Toevoegen van gebruiker lezen uit bestand Nagios Volg hiervoor de volgende omsagent [instructies](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. In de Log Analytics-agent voor Linux-bestand voor algemene configuratie op `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, zorg ervoor dat **beide** de Nagios-bron en het filter zijn zonder opmerkingen.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Probleem: U ziet geen Linux-gegevens 

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* Onboarding naar Azure Monitor is mislukt
* De verbinding met de Azure Monitor is geblokkeerd
* Virtuele machine opnieuw is opgestart
* OMI pakket is handmatig bijgewerkt naar een nieuwere versie in vergelijking met wat is geïnstalleerd door de Log Analytics-agent voor Linux-pakket
* Logboeken voor DSC-resource *klasse is niet gevonden* fout in `omsconfig.log` logboekbestand
* Log Analytics-agent voor de gegevens een back-up
* DSC-logboeken *huidige configuratie bestaat niet. Start-DscConfiguration opdracht met de - Path-parameter voor het opgeven van een configuratiebestand en maakt u eerst een huidige configuratie uitvoeren.* in `omsconfig.log` logboekbestand, maar er zijn geen logboekbericht bestaat over `PerformRequiredConfigurationChecks` bewerkingen.

### <a name="resolution"></a>Oplossing
1. Installeer alle afhankelijkheden zoals auditd-pakket.
2. Controleer of er een onboarding van Azure Monitor is geslaagd door te controleren of het `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`volgende bestand bestaat:.  Als dat niet het geval is, is via de opdrachtregel omsadmin.sh reonboard [instructies](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
4. Als u een proxy, controleert u bovenstaande stappen voor probleemoplossing proxy.
5. In sommige distributiesystemen Azure omid OMI-server-daemon niet wordt gestart nadat de virtuele machine opnieuw wordt opgestart. Dit leidt er geen Audit voor wijzigingen bijhouden of UpdateManagement oplossing-gerelateerde gegevens weergegeven. De tijdelijke oplossing is om handmatig te starten omi-server door te voeren `sudo /opt/omi/bin/service_control restart`.
6. Na de upgrade handmatig OMI-pakket naar een nieuwere versie, is het handmatig opnieuw worden gestart om Log Analytics-agent blijven werken. Deze stap is vereist voor sommige distributies waar OMI-server niet automatisch wordt gestart nadat het is bijgewerkt. Voer `sudo /opt/omi/bin/service_control restart` OMI opnieuw starten.
7. Als u DSC-resource ziet *klasse is niet gevonden* fout in omsconfig.log, voeren `sudo /opt/omi/bin/service_control restart`.
8. In sommige gevallen wordt er een back-up gemaakt van de gegevens op de agent, wanneer de Log Analytics-agent voor Linux niet kan communiceren met Azure Monitor. 50 MB. De agent moet opnieuw worden gestart met de volgende opdracht `/opt/microsoft/omsagent/bin/service_control restart`.

    >[!NOTE]
    >Dit probleem is opgelost in Agent versie 1.1.0-28 of hoger
    >

* Als `omsconfig.log` logboekbestand geeft niet aan die `PerformRequiredConfigurationChecks` bewerkingen worden periodiek wordt uitgevoerd op het systeem, kan er een probleem met de cron-taak/service. Zorg ervoor dat de cron-taak bestaat onder `/etc/cron.d/OMSConsistencyInvoker`. Indien nodig de volgende opdrachten voor het maken van de cron-taak uitvoeren:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Zorg ervoor dat de cron-service wordt uitgevoerd. U kunt `service cron status` met Debian, Ubuntu, SUSE, of `service crond status` met RHEL, CentOS, Oracle Linux om de status van deze service te controleren. Als de service niet bestaat, kunt u de binaire bestanden installeren en start de service met het volgende:

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Probleem: Wanneer u een verzameling configureert vanuit de portal voor syslog-of Linux-prestatie meter items, worden de instellingen niet toegepast

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* De Log Analytics-agent voor Linux is niet doorgevoerd. de meest recente configuratie
* De gewijzigde instellingen in de portal zijn niet toegepast.

### <a name="resolution"></a>Oplossing
**Achtergrond:** `omsconfig` is van de Log Analytics-agent voor Linux-agent voor configuratie die naar de nieuwe portal-side-configuratie om de vijf minuten zoekt. Deze configuratie wordt vervolgens toegepast op de Log Analytics-agent voor Linux-configuratiebestanden vinden op /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* In sommige gevallen kan de Log Analytics-agent voor Linux-configuratie-agent niet mogelijk om te communiceren met de portal configuratieservice, wat resulteert in de meest recente configuratie niet wordt toegepast.
  1. Controleer of de `omsconfig` -agent is geïnstalleerd door het uitvoeren van `dpkg --list omsconfig` of `rpm -qi omsconfig`.  Als niet is geïnstalleerd, installeert u de nieuwste versie van de Log Analytics-agent voor Linux opnieuw.

  2. Controleer of de `omsconfig` agent kan communiceren met Azure monitor door de volgende opdracht `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`uit te voeren. Met deze opdracht retourneert de configuratie die agent ontvangt van de service, waaronder instellingen voor Syslog-, Linux-prestatiemeteritems en aangepaste logboeken. Als deze opdracht mislukt, voert u de volgende opdracht `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Met deze opdracht wordt de omsconfig-agent gedwongen om over Azure Monitor te praten en de nieuwste configuratie op te halen.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Probleem: U ziet geen aangepaste logboek gegevens 

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* Het onboarden van Azure Monitor is mislukt.
* De instelling **de volgende configuratie toepassen op mijn Linux-Servers** niet is geselecteerd.
* omsconfig heeft niet de meest recente configuratie van het aangepaste logboek van de service opgehaald.
* Log Analytics-agent voor Linux-gebruiker `omsagent` geen toegang krijgt tot het aangepaste logboek vanwege ontoereikende machtigingen of niet kan worden gevonden.  U ziet mogelijk de volgende fouten:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Bekend probleem met een zeldzame situatie vast in Log Analytics-agent voor Linux-versie 1.1.0-217

### <a name="resolution"></a>Oplossing
1. Controleer of het volgende bestand bestaat `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`als de onboarding van Azure monitor is geslaagd. Als niet, beide:  

  1. Via de opdrachtregel omsadmin.sh Reonboard [instructies](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
  2. Onder **geavanceerde instellingen** in Azure portal, zorg ervoor dat de instelling **de volgende configuratie toepassen op mijn Linux-Servers** is ingeschakeld.  

2. Controleer of de `omsconfig` agent kan communiceren met Azure monitor door de volgende opdracht `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`uit te voeren.  Met deze opdracht retourneert de configuratie die agent ontvangt van de service, waaronder instellingen voor Syslog-, Linux-prestatiemeteritems en aangepaste logboeken. Als deze opdracht mislukt, voert u de volgende opdracht `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Met deze opdracht wordt de omsconfig-agent gedwongen om over Azure Monitor te praten en de nieuwste configuratie op te halen.

**Achtergrondbitmap** In plaats van de log Analytics- `root`agent voor Linux dat als een bevoegde gebruiker wordt uitgevoerd, wordt de agent uitgevoerd als de `omsagent` gebruiker. In de meeste gevallen moet de expliciete machtiging worden verleend aan deze gebruiker in volgorde van bepaalde bestanden worden gelezen. Toestemming te verlenen voor `omsagent` gebruiker, voer de volgende opdrachten uit:

1. Voeg de `omsagent` gebruiker aan een specifieke groep `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Universele leestoegang tot het vereiste bestand `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Er is een bekend probleem met een zeldzame situatie met de Log Analytics-agent voor Linux-versie ouder is dan 1.1.0-217. Na het bijwerken naar de meest recente agent, voert u de volgende opdracht om de meest recente versie van de uitvoer-invoegtoepassing `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Probleem: U probeert uit te voeren voor een nieuwe werk ruimte
Wanneer u te reonboard een agent naar een nieuwe werkruimte probeert, moet de configuratie van de Log Analytics-agent worden opgeschoond voordat u reonboarding. Uitvoeren om op te schonen oude configuratie van de agent, de shell-bundel met `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
of

```
sudo sh ./onboard_agent.sh --purge
```

U kunt reonboard nadat u de `--purge` optie

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Log Analytics agent-extensie in de Azure Portal is gemarkeerd met een mislukte status: Inrichten is mislukt

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* Log Analytics-agent is verwijderd uit het besturingssysteem
* Log Analytics-agent-service is niet actief, uitgeschakeld of niet geconfigureerd

### <a name="resolution"></a>Oplossing 
Voer de volgende stappen uit om het probleem te verhelpen.
1. Verwijder de extensie van Azure-portal.
2. Installeer de agent na de [instructies](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Start de agent opnieuw met de volgende opdracht: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Wacht enkele minuten en de Inrichtingsstatus is gewijzigd in **inrichting geslaagd**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Probleem: De upgrade van de Log Analytics-agent op aanvraag

### <a name="probable-causes"></a>Waarschijnlijke oorzaken

De Log Analytics-agent-pakketten op de host zijn verouderd.

### <a name="resolution"></a>Oplossing 
Voer de volgende stappen uit om het probleem te verhelpen.

1. Op de meest recente versie controleren [pagina](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Script voor installatie downloaden (1.4.2-124 als voorbeeld-versie):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Upgradepakketten voor besturings door het uitvoeren van `sudo sh ./omsagent-*.universal.x64.sh --upgrade`.
