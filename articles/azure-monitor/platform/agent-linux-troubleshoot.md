---
title: Problemen met Azure Log Analytics Linux Agent oplossen | Microsoft Documenten
description: Beschrijf de symptomen, oorzaken en oplossingen voor de meest voorkomende problemen met de Log Analytics-agent voor Linux in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 24aa3462aef4f719e93d17389ff342084f6c7864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668754"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Problemen met de Log Analytics-agent voor Linux oplossen 

In dit artikel u helpen bij het oplossen van fouten die u mogelijk ondervindt van de Log Analytics-agent voor Linux in Azure Monitor en worden mogelijke oplossingen voorgesteld om deze op te lossen.

Als geen van deze stappen voor u werkt, zijn de volgende ondersteuningskanalen ook beschikbaar:

* Klanten met Premier-ondersteuningsvoordelen kunnen een ondersteuningsaanvraag openen bij [Premier.](https://premier.microsoft.com/)
* Klanten met Azure-ondersteuningsovereenkomsten kunnen een ondersteuningsaanvraag openen [in de Azure-portal.](https://manage.windowsazure.com/?getsupport=true)
* Diagnose OMI Problemen met de [OMI troubleshooting guide](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Een [GitHub-probleem indienen](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Ga naar de feedbackpagina van Log [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) Analytics om ingediende ideeën en bugs te bekijken of een nieuwe in te dienen.  

## <a name="important-log-locations-and-log-collector-tool"></a>Belangrijke logboeklocaties en gereedschap Logboekverzamelaar

 File | Pad
 ---- | -----
 Log Analytics-agent voor Linux-logboekbestand | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Log Analytics-configuratielogboekbestand | `/var/opt/microsoft/omsconfig/omsconfig.log`

 We raden u aan om onze tool voor logboekverzamelaar te gebruiken om belangrijke logboeken op te halen voor het oplossen van problemen of voordat u een GitHub-probleem indient. U meer lezen over de tool en hoe het [hier](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)uit te voeren.

## <a name="important-configuration-files"></a>Belangrijke configuratiebestanden

 Categorie | Bestandslocatie
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf`of `/etc/rsyslog.conf``/etc/rsyslog.d/95-omsagent.conf`
 Prestaties, Nagios, Zabbix, Log Analytics output en general agent | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Aanvullende configuraties | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Configuratiebestanden bewerken voor prestatiemeteritems en Syslog wordt overschreven als de verzameling is geconfigureerd in het [gegevensmenu Log Analytics Advanced Settings](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) in de Azure-portal voor uw werkruimte. Als u de configuratie voor alle agents wilt uitschakelen, schakelt u de verzameling uit de **geavanceerde instellingen** van Log Analytics of voor één agent:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Installatiefoutcodes

| Foutcode | Betekenis |
| --- | --- |
| NOT_DEFINED | Omdat de benodigde afhankelijkheden niet zijn geïnstalleerd, wordt de auoms gecontroleerde plug-in niet geïnstalleerd | Installatie van auoms mislukt, installatie pakket gecontroleerd. |
| 2 | Ongeldige optie die wordt verstrekt aan de shell bundel. Uitvoeren `sudo sh ./omsagent-*.universal*.sh --help` voor gebruik |
| 3 | Geen optie verstrekt aan de shell bundel. Uitvoeren `sudo sh ./omsagent-*.universal*.sh --help` voor gebruik. |
| 4 | Ongeldig pakkettype OF ongeldige proxy-instellingen; omsagent-rpm .sh pakketten kunnen alleen worden geïnstalleerd op RPM-gebaseerde systemen, en omsagent-*deb*.sh pakketten kunnen alleen worden geïnstalleerd op Debian-gebaseerde systemen.*rpm* Het is aan te raden u gebruik maken van de universele installateur van de [nieuwste release](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Controleer ook om uw proxy-instellingen te verifiëren. |
| 5 | De shell bundel moet worden uitgevoerd als root OF er was 403 fout geretourneerd tijdens onboarding. Voer uw `sudo`opdracht uit met . |
| 6 | Ongeldige pakketarchitectuur OF er is een fout van 200 geretourneerd tijdens het instappen; omsagent-x64.sh*pakketten kunnen alleen worden geïnstalleerd op 64-bits systemen, en omsagent-x86.sh*pakketten kunnen alleen worden geïnstalleerd op 32-bits systemen. Download het juiste pakket voor uw architectuur van de [nieuwste release.](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest) |
| 17 | De installatie van OMS-pakket is mislukt. Kijk door de opdrachtuitvoer voor de root-fout. |
| 19 | De installatie van OMI-pakket is mislukt. Kijk door de opdrachtuitvoer voor de root-fout. |
| 20 | De installatie van het SCX-pakket is mislukt. Kijk door de opdrachtuitvoer voor de root-fout. |
| 21 | De installatie van providerkits is mislukt. Kijk door de opdrachtuitvoer voor de root-fout. |
| 22 | Installatie van gebundeld pakket is mislukt. De opdrachtuitvoer voor de hoofdfout bekijken |
| 23 | SCX- of OMI-pakket al geïnstalleerd. Gebruik `--upgrade` in `--install` plaats van om de shell bundel te installeren. |
| 30 | Interne bundelfout. Bestand een [GitHub Probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met details uit de uitvoer. |
| 55 | Niet-ondersteunde openssl-versie OR kan geen verbinding maken met Azure Monitor OF dpkg is vergrendeld OF ontbreekt krulprogramma. |
| 61 | Ontbrekende Python ctypes bibliotheek. Installeer de Python ctypes bibliotheek of pakket (python-ctypes). |
| 62 | Ontbrekende teer programma, installeer teer. |
| 63 | Ontbrekende sed-programma, installeren sed. |
| 64 | Ontbrekende krul programma, installeer krul. |
| 65 | Ontbrekende gpg programma, installeren gpg. |

## <a name="onboarding-error-codes"></a>Onboarding foutcodes

| Foutcode | Betekenis |
| --- | --- |
| 2 | Ongeldige optie die wordt verstrekt aan het omsadmin-script. Uitvoeren `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` voor gebruik. |
| 3 | Ongeldige configuratie die wordt verstrekt aan het omsadmin-script. Uitvoeren `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` voor gebruik. |
| 4 | Ongeldige proxy die wordt verstrekt aan het omsadmin-script. Controleer de proxy en bekijk onze [documentatie voor het gebruik van een HTTP-proxy.](log-analytics-agent.md#network-firewall-requirements) |
| 5 | 403 HTTP-fout ontvangen van Azure Monitor. Zie de volledige uitvoer van het omsadmin-script voor meer informatie. |
| 6 | Niet-200 HTTP-fout ontvangen van Azure Monitor. Zie de volledige uitvoer van het omsadmin-script voor meer informatie. |
| 7 | Kan geen verbinding maken met Azure Monitor. Zie de volledige uitvoer van het omsadmin-script voor meer informatie. |
| 8 | Fout onboarding voor Log Analytics-werkruimte. Zie de volledige uitvoer van het omsadmin-script voor meer informatie. |
| 30 | Interne scriptfout. Bestand een [GitHub Probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met details uit de uitvoer. |
| 31 | Foutgenererende agent-id. Bestand een [GitHub Probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met details uit de uitvoer. |
| 32 | Fout genereren van certificaten. Zie de volledige uitvoer van het omsadmin-script voor meer informatie. |
| 33 | Fout genereren metaconfiguratie voor omsconfig. Bestand een [GitHub Probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met details uit de uitvoer. |
| 34 | Metaconfiguration generatie script niet aanwezig. Probeer onboarding `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`opnieuw uit met . |

## <a name="enable-debug-logging"></a>Foutopsporingslogboekregistratie inschakelen
### <a name="oms-output-plugin-debug"></a>OMS-uitvoerplug-in foutopsporing
 FluentD maakt plug-specifieke logboekregistratieniveaus mogelijk, zodat u verschillende logboekniveaus opgeven voor ingangen en uitgangen. Als u een ander logboekniveau voor OMS-uitvoer `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`wilt opgeven, bewerkt u de algemene agentconfiguratie op .  

 Wijzig de eigenschap vóór het einde van `log_level` het `info` configuratiebestand in de OMS-uitvoerplug-in van : `debug`

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

Met foutopsporing u batcheeruploads naar Azure Monitor bekijken, gescheiden door type, aantal gegevensitems en tijd die nodig is om te verzenden:

*Voorbeeld foutopsporing ingeschakeld logboek:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Verbose-uitvoer
In plaats van de OMS-uitvoerplug-in `stdout`te gebruiken, u ook gegevensitems rechtstreeks naar, die zichtbaar is in het Log Analytics-bestand voor Linux-logboeken, uitvoeren.

Geef in het configuratiebestand `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`van de algemene agent log Analytics `#` op , reageer op de OMS-uitvoerplug-in door een voor elke regel toe te voegen:

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

Onder de uitvoerplugin, uncomment de `#` volgende sectie door het verwijderen van de voorkant van elke regel:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Probleem: kan geen verbinding maken via proxy met Azure Monitor

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* De proxy die is opgegeven tijdens het instappen was onjuist
* De eindpunten Azure Monitor en Azure Automation Service staan niet op de witte lijst in uw datacenter 

### <a name="resolution"></a>Oplossing
1. Reonboard naar Azure Monitor met de Log Analytics-agent voor `-v` Linux met behulp van de volgende opdracht met de optie ingeschakeld. Hiermee kan de agent worden uitgevoerd die verbinding maakt via de proxy naar Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Controleer de [proxy-instellingen van](agent-manage.md#update-proxy-settings) de sectie Bijwerken om te controleren of u de agent correct hebt geconfigureerd om te communiceren via een proxyserver.    
* Controleer nogmaals of de volgende Azure Monitor-eindpunten op de witte lijst staan:

    |Agentresource| Poorten | Richting |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Poort 443| Inkomend en uitgaand |  
    |*.oms.opinsights.azure.com | Poort 443| Inkomend en uitgaand |  
    |*.blob.core.windows.net | Poort 443| Inkomend en uitgaand |  

    Als u van plan bent de Azure Automation Hybrid Runbook Worker te gebruiken om verbinding te maken met en te registreren bij de automatiseringsservice om runbooks of beheeroplossingen in uw omgeving te gebruiken, moet deze toegang hebben tot het poortnummer en de URL's die zijn beschreven in [Uw netwerk configureren voor de hybride runbookworker.](../../automation/automation-hybrid-runbook-worker.md#network-planning) 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Probleem: U ontvangt een fout van 403 wanneer u probeert

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* Datum en tijd is onjuist op Linux Server 
* Werkruimte-id en werkruimtesleutel die worden gebruikt, zijn niet correct

### <a name="resolution"></a>Oplossing

1. Controleer de tijd op uw Linux-server met de opdrachtdatum. Als de tijd +/- 15 minuten van de huidige tijd is, mislukt de onboarding. Om deze update de datum en/of tijdzone van uw Linux server te corrigeren. 
2. Controleer of u de nieuwste versie van de Log Analytics-agent voor Linux hebt geïnstalleerd.  De nieuwste versie waarschuwt u nu als de time skew de onboarding-fout veroorzaakt.
3. Reonboard met behulp van de juiste Workspace ID en Workspace Key volgens de installatie-instructies eerder in dit artikel.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Probleem: U ziet een fout van 500 en 404 in het logboekbestand direct na het instappen
Dit is een bekend probleem dat optreedt bij het eerste uploaden van Linux-gegevens naar een Log Analytics-werkruimte. Dit heeft geen invloed op de verzonden gegevens of de service-ervaring.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Probleem: U ziet omiagent met behulp van 100% CPU

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
Een regressie in nss-pem pakket [v1.0.3-5.el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) veroorzaakte een ernstige prestatieprobleem, dat we hebben gezien komen veel in Redhat / Centos 7.x distributies. Voor meer informatie over dit probleem, controleer de volgende documentatie: Bug [1667121 Prestatieregressie in libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

Prestatiegerelateerde bugs gebeuren niet de hele tijd, en ze zijn erg moeilijk te reproduceren. Als u een dergelijk probleem met omiagent ondervindt, moet u het script omiHighCPUDiagnostics.sh gebruiken dat het stackspoor van de omiagent verzamelt wanneer u een bepaalde drempel overschrijdt.

1. Het script downloaden <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Voer diagnostiek 24 uur uit met 30% CPU-drempelwaarde <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Callstack zal worden gedumpt in omiagent_trace bestand, Als u merkt dat veel Curl en NSS functie oproepen, volg oplossing stappen hieronder.

### <a name="resolution-step-by-step"></a>Resolutie (stap voor stap)

1. Upgrade het nss-pem pakket naar [v1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Als nss-pem niet beschikbaar is voor een upgrade (meestal gebeurt op Centos), dan downgrade krul naar 7.29.0-46. Als je per ongeluk "yum update" uitvoert, wordt curl geüpgraded naar 7.29.0-51 en zal het probleem opnieuw gebeuren. <br/>
`sudo yum downgrade curl libcurl`

3. Start OMI opnieuw: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Probleem: u ziet geen gegevens in de Azure-portal

### <a name="probable-causes"></a>Waarschijnlijke oorzaken

- Onboarding naar Azure Monitor is mislukt
- Verbinding met Azure Monitor is geblokkeerd
- Er wordt een back-up van een log Analytics-agent voor Linux-gegevens

### <a name="resolution"></a>Oplossing
1. Controleer of onboarding Azure Monitor is gelukt door te controleren of het volgende bestand bestaat:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Opnieuw aan `omsadmin.sh` boord met behulp van de instructies voor de opdrachtregel
3. Als u een proxy gebruikt, raadpleegt u de eerder verstrekte stappen voor proxy-resolutie.
4. In sommige gevallen, wanneer de Log Analytics-agent voor Linux niet met de service kan communiceren, worden gegevens over de agent in de wachtrij geplaatst voor de volledige buffergrootte, namelijk 50 MB. De agent moet opnieuw worden gestart `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`door de volgende opdracht uit te voeren: . 

    >[!NOTE]
    >Dit probleem is opgelost in agentversie 1.1.0-28 en hoger.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Probleem: U ziet geen doorgestuurde Syslog-berichten 

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* De configuratie die op de Linux-server wordt toegepast, staat het verzamelen van de verzonden faciliteiten en/of logboekniveaus niet toe
* Syslog wordt niet correct doorgestuurd naar de Linux server
* Het aantal berichten dat per seconde wordt doorgestuurd, is te groot voor de basisconfiguratie van de Log Analytics-agent voor Linux om te verwerken

### <a name="resolution"></a>Oplossing
* Controleer of de configuratie in de log analytics-werkruimte voor Syslog alle faciliteiten en de juiste logboekniveaus heeft. [Syslog-verzameling configureren controleren in de Azure-portal](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Controleer of de native syslog `syslog-ng`messaging daemons ( ,`rsyslog`) in staat zijn om de doorgestuurde berichten te ontvangen
* Controleer firewall-instellingen op de Syslog-server om te controleren of berichten niet worden geblokkeerd
* Een Syslog-bericht simuleren `logger` om Analytics te registreren met opdracht
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Probleem: U ontvangt Errno-adres dat al in omsagentlogbestand is gebruikt
Als je `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` ziet in omsagent.log.

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
Deze fout geeft aan dat de Linux Diagnostic extensie (LAD) naast de Log Analytics Linux VM-extensie is geïnstalleerd en dat deze dezelfde poort gebruikt voor het verzamelen van syslog-gegevens als omsagent.

### <a name="resolution"></a>Oplossing
1. Voer als hoofdmap de volgende opdrachten uit (merk op dat 25224 een voorbeeld is en het is mogelijk dat u in uw omgeving een ander poortnummer ziet dat door LAD wordt gebruikt):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    U moet vervolgens het `rsyslogd` juiste `syslog_ng` of config-bestand bewerken en de LAD-gerelateerde configuratie wijzigen om naar poort 25229 te schrijven.

2. Als de vm `rsyslogd`wordt uitgevoerd, is `/etc/rsyslog.d/95-omsagent.conf` het bestand dat `/etc/rsyslog`moet worden gewijzigd: (als het bestaat, anders ). Als de vm `syslog_ng`wordt uitgevoerd, is `/etc/syslog-ng/syslog-ng.conf`het bestand dat moet worden gewijzigd: .
3. Omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`opnieuw starten .
4. Start de syslog-service opnieuw.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Probleem: u omsagent niet verwijderen met de uitzuiveringsoptie

### <a name="probable-causes"></a>Waarschijnlijke oorzaken

* Linux Diagnostische Extensie is geïnstalleerd
* Linux Diagnostic Extension is geïnstalleerd en verwijderd, maar je ziet nog steeds een fout over omsagent wordt gebruikt door mdsd en kan niet worden verwijderd.

### <a name="resolution"></a>Oplossing
1. Verwijder de Linux Diagnostic Extension (LAD).
2. Verwijder Linux Diagnostic Extension-bestanden van de machine als `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` `/var/opt/microsoft/omsagent/LAD/`ze op de volgende locatie aanwezig zijn: en .

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Probleem: U geen gegevens zien nagios-gegevens 

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* Omsagent gebruiker heeft geen machtigingen om te lezen uit Nagios log bestand
* Nagios bron en filter zijn niet onbecommentarieerd van omsagent.conf bestand

### <a name="resolution"></a>Oplossing
1. Voeg omsagent gebruiker te lezen uit Nagios bestand door het volgen van deze [instructies](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. Zorg er in de Log Analytics-agent voor Linux het algemene configuratiebestand op `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, ervoor te zorgen dat zowel de Nagios-bron **als** het filter niet worden becommentarieerd.

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
* Verbinding met Azure Monitor is geblokkeerd
* Virtuele machine werd opnieuw opgestart
* OMI-pakket is handmatig geüpgraded naar een nieuwere versie in vergelijking met wat werd geïnstalleerd door Log Analytics-agent voor Linux-pakket
* DSC-bronlogboekenklasse niet `omsconfig.log` *gevonden* fout in logboekbestand
* Er wordt een back-up van een log-analyse-agent voor gegevens
* DSC-logboeken *De huidige configuratie bestaat niet. De opdracht Start-DscConfiguration met de parameter Pad uitvoeren om een configuratiebestand op te geven en eerst een huidige configuratie te maken.* in `omsconfig.log` logboekbestand, maar er `PerformRequiredConfigurationChecks` bestaat geen logboekbericht over bewerkingen.

### <a name="resolution"></a>Oplossing
1. Installeer alle afhankelijkheden zoals gecontroleerd pakket.
2. Controleer of het inwerken naar Azure Monitor is `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`gelukt door te controleren of het volgende bestand bestaat: .  Als dit niet het zo was, u de instructies van de omsadmin.sh opdrachtregel [opnieuw gebruiken.](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)
4. Als u een proxy gebruikt, controleert u de bovenstaande stappen voor het oplossen van proxy's.
5. In sommige Azure-distributiesystemen start de OMId OMI-serverdaemon niet nadat de virtuele machine opnieuw is opgestart. Dit zal resulteren in het niet zien van Audit, ChangeTracking, of UpdateManagement oplossing-gerelateerde gegevens. De tijdelijke oplossing is om handmatig omi-server te starten door het uitvoeren van `sudo /opt/omi/bin/service_control restart`.
6. Nadat het OMI-pakket handmatig is geüpgraded naar een nieuwere versie, moet het handmatig opnieuw worden gestart om de Log Analytics-agent te laten functioneren. Deze stap is vereist voor sommige distro's waarbij OMI-server niet automatisch wordt gestart nadat deze is bijgewerkt. Uitvoeren `sudo /opt/omi/bin/service_control restart` om OMI opnieuw te starten.
7. Als u de DSC-resourceklasse *niet in* omsconfig.log ziet, voert u . `sudo /opt/omi/bin/service_control restart`
8. In sommige gevallen, wanneer de Log Analytics-agent voor Linux niet met Azure Monitor kan praten, wordt een back-up van gegevens op de agent opgenomen naar de volledige buffergrootte: 50 MB. De agent moet opnieuw worden gestart `/opt/microsoft/omsagent/bin/service_control restart`door de volgende opdracht uit te voeren.

    >[!NOTE]
    >Dit probleem is opgelost in Agent-versie 1.1.0-28 of hoger
    >

* Als `omsconfig.log` het logboekbestand `PerformRequiredConfigurationChecks` niet aangeeft dat bewerkingen periodiek op het systeem worden uitgevoerd, kan er een probleem zijn met de cron-taak/-service. Zorg ervoor dat `/etc/cron.d/OMSConsistencyInvoker`cron job bestaat onder . Voer indien nodig de volgende opdrachten uit om de cron-taak te maken:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Zorg er ook voor dat de cron-service wordt uitgevoerd. U kunt `service cron status` met Debian, Ubuntu, `service crond status` SUSE of met RHEL, CentOS, Oracle Linux de status van deze service controleren. Als de service niet bestaat, u de binaire bestanden installeren en de service starten met het volgende:

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

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Probleem: Bij het configureren van verzameling van de portal voor Syslog- of Linux-prestatiemeteritems worden de instellingen niet toegepast

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* De Log Analytics-agent voor Linux heeft niet de nieuwste configuratie opgepikt
* De gewijzigde instellingen in de portal zijn niet toegepast

### <a name="resolution"></a>Oplossing
**Achtergrond:** `omsconfig` is de Log Analytics-agent voor Linux-configuratieagent die elke vijf minuten op zoek is naar nieuwe portal-side configuratie. Deze configuratie wordt vervolgens toegepast op de Log Analytics-agent voor Linux-configuratiebestanden op /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* In sommige gevallen kan de Log Analytics-agent voor Linux-configuratieagent mogelijk niet communiceren met de portalconfiguratieservice, waardoor de nieuwste configuratie niet is toegepast.
  1. Controleer of `omsconfig` de agent `dpkg --list omsconfig` is `rpm -qi omsconfig`geïnstalleerd door het uitvoeren of .  Als deze niet is geïnstalleerd, installeert u de nieuwste versie van de Log Analytics-agent voor Linux opnieuw.

  2. Controleer of `omsconfig` de agent kan communiceren met `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`Azure Monitor door de volgende opdracht uit te voeren . Met deze opdracht retourneert u de configuratie die agent van de service ontvangt, inclusief Syslog-instellingen, Linux-prestatiemeteritems en aangepaste logboeken. Als deze opdracht mislukt, `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`voert u de volgende opdracht uit . Met deze opdracht dwingt de omsconfig-agent om met Azure Monitor te praten en de nieuwste configuratie op te halen.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Probleem: U ziet geen aangepaste logboekgegevens 

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* Onboarding naar Azure Monitor is mislukt.
* De instelling **De volgende configuratie toepassen op mijn Linux-servers** is niet geselecteerd.
* omsconfig heeft niet de nieuwste aangepaste logboekconfiguratie van de service opgehaald.
* Log Analytics-agent `omsagent` voor Linux-gebruiker heeft geen toegang tot het aangepaste logboek vanwege machtigingen of niet worden gevonden.  Mogelijk ziet u de volgende fouten:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Bekend probleem met rasvoorwaarde opgelost in Log Analytics-agent voor Linux-versie 1.1.0-217

### <a name="resolution"></a>Oplossing
1. Controleer of het inwerken op Azure Monitor is `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`gelukt door te controleren of het volgende bestand bestaat: . Zo niet, dan:  

  1. Opnieuw aan boord met behulp van de [instructies](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)van de opdrachtregel omsadmin.sh .
  2. Controleer **onder Geavanceerde instellingen** in de Azure-portal of de instelling De volgende configuratie toepassen op mijn **Linux-servers** is ingeschakeld.  

2. Controleer of `omsconfig` de agent kan communiceren met `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`Azure Monitor door de volgende opdracht uit te voeren .  Met deze opdracht retourneert u de configuratie die agent van de service ontvangt, inclusief Syslog-instellingen, Linux-prestatiemeteritems en aangepaste logboeken. Als deze opdracht mislukt, `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`voert u de volgende opdracht uit . Met deze opdracht dwingt de omsconfig-agent om met Azure Monitor te praten en de nieuwste configuratie op te halen.

**Achtergrond:** In plaats van de Log Analytics-agent `root`voor Linux die `omsagent` als bevoorrechte gebruiker wordt uitgevoerd , wordt de agent uitgevoerd als de gebruiker. In de meeste gevallen moet deze gebruiker expliciete toestemming krijgen om bepaalde bestanden te kunnen lezen. Voer de `omsagent` volgende opdrachten uit om toestemming te verlenen aan de gebruiker:

1. De `omsagent` gebruiker toevoegen aan specifieke groep`sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Universele leestoegang verlenen tot het vereiste bestand`sudo chmod -R ugo+rx <FILE DIRECTORY>`

Er is een bekend probleem met een race conditie met de Log Analytics agent voor Linux versie eerder dan 1.1.0-217. Voer na het bijwerken naar de nieuwste agent de volgende `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`opdracht uit om de nieuwste versie van de uitvoerplug-in te krijgen.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Probleem: u probeert opnieuw aan boord te gaan naar een nieuwe werkruimte
Wanneer u een agent opnieuw aan een nieuwe werkruimte wilt koppelen, moet de configuratie van de Log Analytics-agent worden opgeschoond voordat u opnieuw aan boord gaat. Als u oude configuratie van de agent wilt opschonen, voert u de shell-bundel uit met`--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
of

```
sudo sh ./onboard_agent.sh --purge
```

U verder rijden `--purge` nadat u de optie hebt gebruikt

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Logboekanalyseagent-extensie in de Azure-portal is gemarkeerd met een mislukte status: inprovisioning is mislukt

### <a name="probable-causes"></a>Waarschijnlijke oorzaken
* Log Analytics-agent is uit het besturingssysteem verwijderd
* Log Analytics-agentservice is uitgeschakeld of niet geconfigureerd

### <a name="resolution"></a>Oplossing 
Voer de volgende stappen uit om het probleem te verhelpen.
1. Verwijder de extensie uit azure-portal.
2. Installeer de agent volgens de [instructies.](../../azure-monitor/learn/quick-collect-linux-computer.md)
3. Start de agent opnieuw door `sudo /opt/microsoft/omsagent/bin/service_control restart`de volgende opdracht uit te voeren: .
* Wacht enkele minuten en de wijzigingen in de inrichtingsstatus **in Provisioning zijn geslaagd.**


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Probleem: de upgrade van de Log Analytics-agent op aanvraag

### <a name="probable-causes"></a>Waarschijnlijke oorzaken

De log analytics-agentpakketten op de host zijn verouderd.

### <a name="resolution"></a>Oplossing 
Voer de volgende stappen uit om het probleem te verhelpen.

1. Controleer op de laatste release op [pagina](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Download installatiescript (1.4.2-124 als voorbeeldversie):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Upgradepakketten door `sudo sh ./omsagent-*.universal.x64.sh --upgrade`het uitvoeren van .
