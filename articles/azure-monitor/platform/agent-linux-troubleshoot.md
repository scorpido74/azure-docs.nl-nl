---
title: Problemen met Azure Log Analytics Linux-Agent oplossen | Microsoft Docs
description: Beschrijf de symptomen, oorzaken en oplossingen voor de meest voorkomende problemen met de Log Analytics agent voor Linux in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 24aa3462aef4f719e93d17389ff342084f6c7864
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668754"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Het oplossen van problemen met de Log Analytics-agent voor Linux 

In dit artikel vindt u informatie over het oplossen van problemen die u mogelijk ondervindt met de Log Analytics-agent voor Linux in Azure Monitor en worden mogelijke oplossingen voorgesteld om deze op te lossen.

Als geen van deze stappen voor u werkt, zijn ook de volgende ondersteuningskanalen beschikbaar:

* Klanten met premier-ondersteunings voordelen kunnen een ondersteunings aanvraag openen met [premier](https://premier.microsoft.com/).
* Klanten met ondersteunings overeenkomsten voor Azure kunnen een ondersteunings aanvraag openen [in de Azure Portal](https://manage.windowsazure.com/?getsupport=true).
* Diagnose OMI-problemen met de [Omi-gids voor probleem oplossing](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Een [github-probleem oplossen](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Ga naar de Log Analytics feedback pagina om de verzonden ideeën en bugs te bekijken [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) of een nieuwe bestand te openen.  

## <a name="important-log-locations-and-log-collector-tool"></a>Belangrijke logboeklocaties en Logboekverzamelaar hulpprogramma

 File | Pad
 ---- | -----
 Log Analytics-agent voor Linux-logboekbestand | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Log Analytics-agent configuratielogboekbestand | `/var/opt/microsoft/omsconfig/omsconfig.log`

 We raden u aan ons logboek collector-hulpprogramma gebruiken om op te halen van belangrijke logboeken voor het oplossen van problemen of voor het indienen van een GitHub-probleem. Meer informatie over het hulp programma vindt u [hier](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Belangrijk-configuratiebestanden

 Categorie | Bestandslocatie
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf` of `/etc/rsyslog.conf` of `/etc/rsyslog.d/95-omsagent.conf`
 Prestaties, Nagios, Zabbix, Log Analytics-uitvoer en algemene agent | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Aanvullende configuraties | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Het bewerken van configuratie bestanden voor prestatie meter items en syslog wordt overschreven als de verzameling is geconfigureerd in het [menu data log Analytics geavanceerde instellingen](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) in de Azure portal voor uw werk ruimte. Als u de configuratie voor alle agents wilt uitschakelen, schakelt u verzameling uit Log Analytics **Geavanceerde instellingen** of voor één agent het volgende uit:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Foutcodes voor clientinstallatie

| Foutcode | Betekenis |
| --- | --- |
| NOT_DEFINED | Omdat de vereiste afhankelijkheden niet zijn geïnstalleerd, wordt niet de auoms auditd-invoegtoepassing geïnstalleerd | Installatie van auoms is mislukt, installeer pakket auditd. |
| 2 | Ongeldige optie opgegeven voor de shell-bundel. `sudo sh ./omsagent-*.universal*.sh --help` uitvoeren voor gebruik |
| 3 | Er is geen optie opgegeven voor de shell-bundel. Voer `sudo sh ./omsagent-*.universal*.sh --help` uit voor gebruik. |
| 4 | Ongeldig pakket type of ongeldige proxy instellingen. omsagent-*rpm*. sh-pakketten kunnen alleen worden geïnstalleerd op installaties op basis van rpm en omsagent-*deb*. sh-pakketten kunnen alleen worden geïnstalleerd op Debian-systemen. Het is raadzaam om het universele installatie programma van de [meest recente release](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux)te gebruiken. Controleer ook de verificatie van uw proxy-instellingen. |
| 5 | De shell-bundel moet worden uitgevoerd als root of er is 403-fout geretourneerd tijdens onboarding. Voer uw opdracht uit met behulp van `sudo`. |
| 6 | Ongeldige pakket architectuur of er is een fout opgetreden 200 fout opgetreden tijdens het voorbereiden; omsagent-*x64.sh-pakketten kunnen alleen worden geïnstalleerd op 64-bits systemen en omsagent-x86.sh-* pakketten kunnen alleen worden geïnstalleerd op 32-bits systemen. Down load het juiste pakket voor uw architectuur vanuit de [nieuwste versie](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Installatie van de OMS-pakket is mislukt. Bekijk de uitvoer van de opdracht voor het root-mislukken. |
| 19 | Installatie van OMI-pakket is mislukt. Bekijk de uitvoer van de opdracht voor het root-mislukken. |
| 20 | Installatie van de SCX-pakket is mislukt. Bekijk de uitvoer van de opdracht voor het root-mislukken. |
| 21 | Installatie van Provider kits is mislukt. Bekijk de uitvoer van de opdracht voor het root-mislukken. |
| 22 | Installatie van gebundelde pakket is mislukt. Bekijk de uitvoer van de opdracht voor het root-fout |
| 23 | SCX of OMI pakket is al geïnstalleerd. Gebruik `--upgrade` in plaats van `--install` om de shell-bundel te installeren. |
| 30 | Fout bij het interne bundel. Een [github-probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met details van de uitvoer bestand. |
| 55 | Niet-ondersteunde openssl-versie of kan geen verbinding maken met Azure Monitor of met dpkg is een vergrendeld of ontbrekend krul-programma. |
| 61 | Ontbrekende Python ctypes-bibliotheek. Installeer de Python-bibliotheek voor ctypes of het pakket (python-ctypes). |
| 62 | Ontbrekende tar programma tar installeren. |
| 63 | Ontbrekende sed programma installeren sed. |
| 64 | Ontbrekende curl programma installeren curl. |
| 65 | Ontbrekende gpg programma gpg installeren. |

## <a name="onboarding-error-codes"></a>Onboarding-foutcodes

| Foutcode | Betekenis |
| --- | --- |
| 2 | Ongeldige optie opgegeven voor het script omsadmin. Voer `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` uit voor gebruik. |
| 3 | Ongeldige configuratie opgegeven voor het script omsadmin. Voer `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` uit voor gebruik. |
| 4 | Ongeldige proxy opgegeven voor het script omsadmin. Controleer de proxy en Raadpleeg onze [documentatie voor het gebruik van een HTTP-proxy](log-analytics-agent.md#network-firewall-requirements). |
| 5 | 403 HTTP-fout ontvangen van Azure Monitor. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 6 | Er is een niet-200 HTTP-fout ontvangen van Azure Monitor. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 7 | Kan geen verbinding maken met Azure Monitor. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 8 | Onboarding van de fout naar Log Analytics-werkruimte. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 30 | Interne scriptfout. Een [github-probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met details van de uitvoer bestand. |
| 31 | Fout bij het genereren agent-id. Een [github-probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met details van de uitvoer bestand. |
| 32 | Fout bij het genereren van certificaten. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 33 | Fout bij het genereren van metaconfiguration voor omsconfig. Een [github-probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met details van de uitvoer bestand. |
| 34 | Metaconfiguration generatie script niet aanwezig is. Voer onboarding opnieuw uit met `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`. |

## <a name="enable-debug-logging"></a>Inschakelen van logboekregistratie voor foutopsporing
### <a name="oms-output-plugin-debug"></a>OMS-uitvoer-invoegtoepassing foutopsporing
 FluentD kunt voor de invoegtoepassing-specifieke logboekregistratieniveaus zodat u kunt verschillende logboekniveaus voor invoer en uitvoer opgeven. Als u een ander logboek niveau voor OMS-uitvoer wilt opgeven, bewerkt u de algemene agent configuratie op `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 Wijzig in de invoeg toepassing OMS, vóór het einde van het configuratie bestand, de eigenschap `log_level` van `info` in `debug`:

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

*Voor beeld van debug-logboek:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Uitgebreide uitvoer
In plaats van de OMS-uitvoer-invoeg toepassing te gebruiken, kunt u gegevens items ook rechtstreeks naar `stdout`uitvoeren. deze worden weer gegeven in het Log Analytics agent voor Linux-logboek bestand.

In het configuratie bestand Log Analytics algemene agent op `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`kunt u de invoeg toepassing OMS-uitvoer inlichten door een `#` vóór elke regel toe te voegen:

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

Verwijder onder de invoeg toepassing voor uitvoer de opmerking de volgende sectie door de `#` vóór elke regel te verwijderen:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Probleem: kan geen verbinding maken via een proxy met Azure Monitor

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* De proxy die is opgegeven tijdens de voorbereiding is onjuist
* De Azure Monitor-en Azure Automation Service-eind punten zijn niet white list in uw Data Center 

### <a name="resolution"></a>Oplossing
1. Onboarding voor Azure Monitor met de Log Analytics-agent voor Linux met behulp van de volgende opdracht met de optie `-v` ingeschakeld. Hiermee kan uitgebreide uitvoer van de agent die verbinding maakt met de proxy, worden Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Raadpleeg de sectie [proxy-instellingen bijwerken](agent-manage.md#update-proxy-settings) om te controleren of u de agent op de juiste manier hebt geconfigureerd om te communiceren via een proxy server.    
* Controleer of de volgende Azure Monitor-eind punten white list zijn:

    |Agentresource| Poorten | Richting |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Poort 443| Binnenkomend en uitgaand |  
    |*.oms.opinsights.azure.com | Poort 443| Binnenkomend en uitgaand |  
    |*.blob.core.windows.net | Poort 443| Binnenkomend en uitgaand |  

    Als u van plan bent de Azure Automation Hybrid Runbook Worker te gebruiken om verbinding te maken met de Automation-Service voor het gebruik van runbooks of beheer oplossingen in uw omgeving, moet deze toegang hebben tot het poort nummer en de Url's die worden beschreven in [uw netwerk configureren voor de Hybrid Runbook worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Probleem: U ontvangt een 403-fout bij het vrijgeven

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* Datum en tijd is niet correct op Linux-Server 
* Werkruimte-ID en Werkruimtesleutel gebruikt zijn niet juist

### <a name="resolution"></a>Oplossing

1. Controleer de tijd op uw Linux-server met de datum van de opdracht. Als de tijd +/-15 minuten na de huidige tijd is, mislukt onboarding. Op juiste dit bijwerken de datum en/of de tijdzone van de Linux-server. 
2. Controleer of dat u de nieuwste versie van de Log Analytics-agent voor Linux hebt geïnstalleerd.  De nieuwste versie wordt nu waarschuwt u als tijdverschilbereik wordt veroorzaakt door het onboarding-fout.
3. Reonboard met behulp van de juiste werkruimte-ID en Werkruimtesleutel na de installatie-instructies eerder in dit artikel.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Probleem: Ziet u een 500 en 404-fout in het logboekbestand direct na de onboarding
Dit is een bekend probleem dat zich op de eerste uploaden van gegevens van Linux in een Log Analytics-werkruimte voordoet. Dit heeft geen invloed op gegevens die worden verzonden of service-ervaring.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Probleem: u ziet omiagent met een CPU van 100%

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
Een regressie in nss-PEM Package [v 1.0.3 -5. EL7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) heeft een ernstig prestatie probleem veroorzaakt, die we hebben gezien een heleboel in RedHat/CentOS 7. x-distributies. Raadpleeg de volgende documentatie voor meer informatie over dit probleem: bug [1667121 prestatie regressie in libkrul](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

Problemen met betrekking tot prestaties doen zich niet altijd voor en ze zijn zeer lastig te reproduceren. Als u een dergelijk probleem ondervindt met omiagent, moet u het script omiHighCPUDiagnostics.sh gebruiken waarmee de stack tracering van de omiagent wordt verzameld wanneer een bepaalde drempel waarde wordt overschreden.

1. Het script downloaden <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Diagnostische gegevens 24 uur uitvoeren met een CPU-drempel van 30% <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Call stack wordt in omiagent_trace bestand gedumpt. Als u veel krul-en NSS-functie aanroepen ziet, volgt u de onderstaande stappen voor de oplossing.

### <a name="resolution-step-by-step"></a>Oplossing (stapsgewijze stap)

1. Voer een upgrade uit van het pakket nss-PEM naar [v 1.0.3-5. el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Als nss-PEM niet beschikbaar is voor upgrade (meestal gebeurt op CentOS), downgradet u krul naar 7.29.0-46. Als u per ongeluk ' yum update ' uitvoert, wordt krul bijgewerkt naar 7.29.0-51 en wordt het probleem opnieuw uitgevoerd. <br/>
`sudo yum downgrade curl libcurl`

3. OMI opnieuw starten: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Probleem: U ziet geen gegevens in Azure portal

### <a name="probable-causes"></a>Waarschijnlijke oorzaken

- Onboarding naar Azure Monitor is mislukt
- De verbinding met de Azure Monitor is geblokkeerd
- Log Analytics-agent voor Linux-gegevens een back-up

### <a name="resolution"></a>Oplossing
1. Controleer of het voorbereidings Azure Monitor is geslaagd door te controleren of het volgende bestand bestaat: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Onboarding uitvoeren met behulp van de `omsadmin.sh` opdracht regel instructies
3. Als u een proxy gebruikt, raadpleegt u de stappen voor het oplossen van proxy die eerder is verkregen.
4. In sommige gevallen, wanneer de Log Analytics-agent voor Linux kan niet met de service communiceren gegevens op de agent is in de wachtrij voor de volledige buffergrootte, 50 MB. De agent moet opnieuw worden gestart door de volgende opdracht uit te voeren: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Dit probleem is opgelost in agent versie 1.1.0-28 en hoger.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Probleem: U ziet geen doorgestuurde Syslog-berichten 

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* De configuratie die is toegepast op de Linux-server staat niet toe dat de verzameling van de verzonden faciliteiten en/of de logboekniveaus
* Syslog wordt niet correct doorgestuurd naar de Linux-server
* Het aantal berichten per seconde wordt doorgestuurd zijn te groot voor de basisconfiguratie van de Log Analytics-agent voor Linux om af te handelen

### <a name="resolution"></a>Oplossing
* Controleer of dat de configuratie in de werkruimte voor logboekanalyse voor Syslog heeft de faciliteiten en het juiste logboek-niveau. Beoordeling [van syslog-verzameling configureren in de Azure Portal](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Controleer of de systeem eigen syslog Messa ging-daemons (`rsyslog`, `syslog-ng`) de doorgestuurde berichten kunnen ontvangen
* Controleer de firewall-instellingen op de Syslog-server om ervoor te zorgen dat berichten worden niet geblokkeerd
* Een syslog-bericht simuleren voor het Log Analytics met behulp van `logger` opdracht
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Probleem: U ontvangt Errno adres al in gebruik is in het logboekbestand omsagent
Als `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` in omsagent. log wordt weer geven.

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
Deze fout geeft aan dat de Linux Diagnostic-extensie (LAD) naast de Log Analytics Linux VM-extensie is geïnstalleerd, en dezelfde poort wordt gebruikt om gegevens te verzamelen als omsagent syslog.

### <a name="resolution"></a>Oplossing
1. Uitvoeren als de hoofdmap van de volgende opdrachten (Let erop dat 25224 een voorbeeld is en is het mogelijk dat in uw omgeving u een ander poortnummer gebruikt door LAD ziet):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Vervolgens moet u het juiste `rsyslogd`-of `syslog_ng` config-bestand bewerken en de LAD configuratie wijzigen om naar poort 25229 te schrijven.

2. Als de virtuele machine wordt uitgevoerd `rsyslogd`, is het bestand dat moet worden gewijzigd: `/etc/rsyslog.d/95-omsagent.conf` (indien aanwezig, else `/etc/rsyslog`). Als de virtuele machine wordt uitgevoerd `syslog_ng`, is het bestand dat moet worden gewijzigd: `/etc/syslog-ng/syslog-ng.conf`.
3. Start omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`opnieuw op.
4. Syslog-service opnieuw starten.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Probleem: U bent niet verwijderen met de optie opschonen omsagent

### <a name="probable-causes"></a>Waarschijnlijke oorzaken

* Linux Diagnostic-extensie is geïnstalleerd
* Linux Diagnostic-extensie is geïnstalleerd en verwijderd, maar u nog steeds een foutbericht dat wordt gebruikt door mdsd omsagent zien en kan niet worden verwijderd.

### <a name="resolution"></a>Oplossing
1. Verwijder de Linux Diagnostic-extensie (LAD).
2. Bestanden van de Linux-extensie voor diagnostische gegevens verwijderen van de computer als deze aanwezig zijn op de volgende locatie: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` en `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Probleem: U kunt geen gegevens Nagios gegevens ziet 

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* Omsagent gebruiker heeft geen machtigingen om te lezen uit Nagios-logbestand
* Nagios-bron- en filter zijn niet zonder opmerkingen van omsagent.conf bestand

### <a name="resolution"></a>Oplossing
1. Voeg de omsagent-gebruiker toe om te lezen uit het nagios-bestand door deze [instructies](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts)te volgen.
2. Zorg er in het Log Analytics-agent voor algemeen configuratie bestand voor Linux op `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`voor dat **zowel** de nagios-bron als het filter onwaar zijn.

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

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Probleem: U ziet geen alle Linux-gegevens 

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* Onboarding naar Azure Monitor is mislukt
* De verbinding met de Azure Monitor is geblokkeerd
* Virtuele machine opnieuw is opgestart
* OMI pakket is handmatig bijgewerkt naar een nieuwere versie in vergelijking met wat is geïnstalleerd door de Log Analytics-agent voor Linux-pakket
* Fout met de *klasse niet gevonden* in de DSC-resource Logboeken in `omsconfig.log` logboek bestand
* Log Analytics-agent voor de gegevens een back-up
* DSC-logboeken *huidige configuratie bestaat niet. Voer de opdracht start-DscConfiguration uit met de para meter-Path om een configuratie bestand op te geven en maak eerst een huidige configuratie.* in `omsconfig.log` logboek bestand, maar er bestaat geen logboek bericht over `PerformRequiredConfigurationChecks` bewerkingen.

### <a name="resolution"></a>Oplossing
1. Installeer alle afhankelijkheden zoals auditd-pakket.
2. Controleer of er een onboarding van Azure Monitor is geslaagd door te controleren of het volgende bestand bestaat: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.  Als dat niet het geval is, gebruikt u de omsadmin.sh-opdracht regel [instructies](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
4. Als u een proxy, controleert u bovenstaande stappen voor probleemoplossing proxy.
5. In sommige distributiesystemen Azure omid OMI-server-daemon niet wordt gestart nadat de virtuele machine opnieuw wordt opgestart. Dit leidt er geen Audit voor wijzigingen bijhouden of UpdateManagement oplossing-gerelateerde gegevens weergegeven. De tijdelijke oplossing is om de Omi-server hand matig te starten door `sudo /opt/omi/bin/service_control restart`uit te voeren.
6. Na de upgrade handmatig OMI-pakket naar een nieuwere versie, is het handmatig opnieuw worden gestart om Log Analytics-agent blijven werken. Deze stap is vereist voor sommige distributies waar OMI-server niet automatisch wordt gestart nadat het is bijgewerkt. Voer `sudo /opt/omi/bin/service_control restart` uit om OMI opnieuw te starten.
7. Als er een fout bericht *klasse niet gevonden* wordt weer gegeven in omsconfig. log, voert u `sudo /opt/omi/bin/service_control restart`uit.
8. In sommige gevallen wordt er een back-up gemaakt van de gegevens op de agent, wanneer de Log Analytics-agent voor Linux niet kan communiceren met Azure Monitor, de volledige buffer grootte: 50 MB. De agent moet opnieuw worden gestart door de volgende opdracht uit te voeren `/opt/microsoft/omsagent/bin/service_control restart`.

    >[!NOTE]
    >Dit probleem is opgelost in Agent versie 1.1.0-28 of hoger
    >

* Als `omsconfig.log` logboek bestand niet aangeeft dat `PerformRequiredConfigurationChecks` bewerkingen periodiek op het systeem worden uitgevoerd, is er mogelijk een probleem met de cron-taak/-service. Zorg ervoor dat de cron-taak bestaat onder `/etc/cron.d/OMSConsistencyInvoker`. Indien nodig de volgende opdrachten voor het maken van de cron-taak uitvoeren:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Zorg ervoor dat de cron-service wordt uitgevoerd. U kunt `service cron status` gebruiken met Debian, Ubuntu, SUSE of `service crond status` met RHEL, CentOS, Oracle Linux om de status van deze service te controleren. Als de service niet bestaat, kunt u de binaire bestanden installeren en start de service met het volgende:

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

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Probleem: Bij het configureren van het verzamelen van de portal voor Syslog- of Linux-prestatiemeteritems, de instellingen worden niet toegepast

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* De Log Analytics-agent voor Linux is niet doorgevoerd. de meest recente configuratie
* De gewijzigde instellingen in de portal zijn niet toegepast.

### <a name="resolution"></a>Oplossing
**Achtergrond:** `omsconfig` is de log Analytics agent voor Linux-configuratie agent die elke vijf minuten zoekt naar nieuwe configuratie op de portal-zijde. Deze configuratie wordt vervolgens toegepast op de Log Analytics-agent voor Linux-configuratiebestanden vinden op /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* In sommige gevallen kan de Log Analytics-agent voor Linux-configuratie-agent niet mogelijk om te communiceren met de portal configuratieservice, wat resulteert in de meest recente configuratie niet wordt toegepast.
  1. Controleer of de `omsconfig`-agent is geïnstalleerd door `dpkg --list omsconfig` of `rpm -qi omsconfig`uit te voeren.  Als niet is geïnstalleerd, installeert u de nieuwste versie van de Log Analytics-agent voor Linux opnieuw.

  2. Controleer of de `omsconfig` agent kan communiceren met Azure Monitor door de volgende opdracht `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`uit te voeren. Met deze opdracht retourneert de configuratie die agent ontvangt van de service, waaronder instellingen voor Syslog-, Linux-prestatiemeteritems en aangepaste logboeken. Als deze opdracht mislukt, voert u de volgende opdracht uit `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Met deze opdracht wordt de omsconfig-agent gedwongen om over Azure Monitor te praten en de nieuwste configuratie op te halen.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Probleem: U ziet geen eventuele aangepaste logboekgegevens 

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* Het onboarden van Azure Monitor is mislukt.
* De instelling **voor het Toep assen van de volgende configuratie op mijn Linux-servers** is niet geselecteerd.
* omsconfig heeft niet de meest recente configuratie van het aangepaste logboek van de service opgehaald.
* Log Analytics agent voor Linux-gebruikers `omsagent` heeft geen toegang tot het aangepaste logboek vanwege machtigingen of niet gevonden.  U ziet mogelijk de volgende fouten:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Bekend probleem met een zeldzame situatie vast in Log Analytics-agent voor Linux-versie 1.1.0-217

### <a name="resolution"></a>Oplossing
1. Controleer of het volgende bestand bestaat: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`om de onboarding naar Azure Monitor te controleren. Als niet, beide:  

  1. Onboarding uitvoeren met behulp van de omsadmin.sh-opdracht regel [instructies](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
  2. Zorg ervoor dat onder **Geavanceerde instellingen** in de Azure Portal de instelling **de volgende configuratie Toep assen op mijn Linux-servers** is ingeschakeld.  

2. Controleer of de `omsconfig` agent kan communiceren met Azure Monitor door de volgende opdracht `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`uit te voeren.  Met deze opdracht retourneert de configuratie die agent ontvangt van de service, waaronder instellingen voor Syslog-, Linux-prestatiemeteritems en aangepaste logboeken. Als deze opdracht mislukt, voert u de volgende opdracht uit `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Met deze opdracht wordt de omsconfig-agent gedwongen om over Azure Monitor te praten en de nieuwste configuratie op te halen.

**Achtergrond:** In plaats van de Log Analytics-agent voor Linux dat wordt uitgevoerd als een bevoegde gebruiker-`root`, wordt de agent uitgevoerd als de `omsagent` gebruiker. In de meeste gevallen moet de expliciete machtiging worden verleend aan deze gebruiker in volgorde van bepaalde bestanden worden gelezen. Voer de volgende opdrachten uit om een machtiging te verlenen aan `omsagent` gebruiker:

1. De `omsagent` gebruiker toevoegen aan een specifieke groep `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Universele Lees toegang verlenen aan de vereiste bestands `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Er is een bekend probleem met een zeldzame situatie met de Log Analytics-agent voor Linux-versie ouder is dan 1.1.0-217. Voer na het bijwerken naar de nieuwste agent de volgende opdracht uit om de nieuwste versie van de invoeg toepassing voor uitvoer op te halen `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Probleem: U probeert te reonboard aan een nieuwe werkruimte
Wanneer u te reonboard een agent naar een nieuwe werkruimte probeert, moet de configuratie van de Log Analytics-agent worden opgeschoond voordat u reonboarding. Als u de oude configuratie van de agent wilt opschonen, voert u de shell-bundel uit met `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
of

```
sudo sh ./onboard_agent.sh --purge
```

U kunt door gaan met het gebruik van de `--purge` optie

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Log Analytics agent-extensie in de Azure-portal is gemarkeerd met een mislukte status: inrichten is mislukt

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* Log Analytics-agent is verwijderd uit het besturingssysteem
* Log Analytics-agent-service is niet actief, uitgeschakeld of niet geconfigureerd

### <a name="resolution"></a>Oplossing 
Voer de volgende stappen uit om het probleem te verhelpen.
1. Verwijder de extensie van Azure-portal.
2. Installeer de agent volgens de [instructies](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Start de agent opnieuw door de volgende opdracht uit te voeren: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Wacht enkele minuten en de inrichtings status is gewijzigd in **inrichting geslaagd**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Probleem: De Log Analytics-agent bijwerken op aanvraag

### <a name="probable-causes"></a>Waarschijnlijke oorzaken

De Log Analytics-agent-pakketten op de host zijn verouderd.

### <a name="resolution"></a>Oplossing 
Voer de volgende stappen uit om het probleem te verhelpen.

1. Controleer op de meest recente release op de [pagina](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Script voor installatie downloaden (1.4.2-124 als voorbeeld-versie):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Upgrade pakketten door `sudo sh ./omsagent-*.universal.x64.sh --upgrade`uit te voeren.
