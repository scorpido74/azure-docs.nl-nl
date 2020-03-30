---
title: VMware-bewakingsoplossing in Azure-monitor | Microsoft Documenten
description: Meer informatie over hoe de VMware Monitoring-oplossing kan helpen bij het beheren van logboeken en het bewaken van ESXi-hosts.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: c1622ef16155206d779c6d703fc7da568d233e7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664776"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>VMware Monitoring (Afgeschaft) oplossing in Azure Monitor

![VMware-symbool](./media/vmware/vmware-symbol.png)

> [!NOTE]
> De VMware Monitoring oplossing is afgeschaft.  Klanten die de oplossing al hebben geïnstalleerd, kunnen deze blijven gebruiken, maar VMware Monitoring kan niet worden toegevoegd aan nieuwe werkruimten.

De VMware Monitoring-oplossing in Azure Monitor is een oplossing waarmee u een gecentraliseerde registratie- en bewakingsbenadering maken voor grote VMware-logboeken. In dit artikel wordt beschreven hoe u de ESXi-hosts op één locatie oplossen, vastleggen en beheren met behulp van de oplossing. Met de oplossing u gedetailleerde gegevens voor al uw ESXi-hosts op één locatie bekijken. U de belangrijkste gebeurtenistellingen, status en trends van VM- en ESXi-hosts bekijken die worden geleverd via de ESXi-hostlogboeken. U problemen oplossen door gecentraliseerde ESXi-hostlogboeken te bekijken en te doorzoeken. En u waarschuwingen maken op basis van query's in logboekzoekopdrachten.

De oplossing maakt gebruik van native syslog-functionaliteit van de ESXi-host om gegevens naar een doel-VM te pushen, die de Log Analytics-agent heeft. De oplossing schrijft echter geen bestanden in syslog binnen de doel-VM. De Log Analytics-agent opent poort 1514 en luistert hiernaar. Zodra de gegevens zijn ontvangen, pusht de log-analyse-agent de gegevens naar Azure Monitor.

## <a name="install-and-configure-the-solution"></a>De oplossing installeren en configureren
Gebruik de volgende informatie om de oplossing te installeren en configureren.

* Voeg de VMware Monitoring-oplossing toe aan uw abonnement met behulp van het proces dat wordt beschreven in [Een bewakingsoplossing installeren.](../insights/solutions.md#install-a-monitoring-solution)

#### <a name="supported-vmware-esxi-hosts"></a>Ondersteunde VMware ESXi-hosts
vSphere ESXi Host 5.5, 6.0 en 6.5

#### <a name="prepare-a-linux-server"></a>Een Linux-server voorbereiden
Maak een Linux-besturingssysteem VM om alle syslog gegevens van de ESXi hosts te ontvangen. De [Log Analytics Linux-agent](../learn/quick-collect-linux-computer.md) is het verzamelpunt voor alle ESXi-hostsysloggegevens. U meerdere ESXi-hosts gebruiken om logboeken door te sturen naar één Linux-server, zoals in het volgende voorbeeld.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![syslogstroom](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Syslog-verzameling configureren
1. Syslog forwarding instellen voor VSphere. Zie [Syslog configureren op ESXi 5.0 en hoger (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322)voor gedetailleerde informatie om het doorsturen van syslog te helpen. Ga naar **ESXi Host Configuration** > **Software** > **Advanced Settings** > **Syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. Voeg in het veld *Syslog.global.logHost* uw Linux-server en het poortnummer *1514*toe. Bijvoorbeeld `tcp://hostname:1514` of `tcp://123.456.789.101:1514`
1. Open de ESXi-hostfirewall voor syslog. **ESXi Host Configuration** > **Software** > **Security Profile** > **Firewall** en open **Eigenschappen**.  

    ![vspherefw vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Controleer de vSphere-console om te controleren of syslog correct is ingesteld. Bevestig op de ESXI-host dat poort **1514** is geconfigureerd.
1. Download en installeer de Log Analytics-agent voor Linux op de Linux-server. Zie de agent [Documentation for Log Analytics voor Linux voor](https://github.com/Microsoft/OMS-Agent-for-Linux)meer informatie.
1. Nadat de Log Analytics-agent voor Linux is geïnstalleerd, gaat u naar de /etc/opt/microsoft/omsagent/sysconf/omsagent.d directory en kopieert u het vmware_esxi.conf-bestand naar het /etc/opt/microsoft/omsagent/conf/omsagent.d directory en de wijziging van de eigenaar/groep en machtigingen van het bestand. Bijvoorbeeld:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Start de loganalytics-agent `sudo /opt/microsoft/omsagent/bin/service_control restart`voor Linux opnieuw door het uitvoeren van .
1. Test de connectiviteit tussen de Linux-server en `nc` de ESXi-host met behulp van de opdracht op de ESXi-host. Bijvoorbeeld:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. Voer in de Azure-portal `VMware_CL`een logboekquery uit voor . Wanneer Azure Monitor de sysloggegevens verzamelt, behoudt het de syslog-indeling. In de portal worden enkele specifieke velden vastgelegd, zoals *Hostname* en *ProcessName*.  

    ![type](./media/vmware/type.png)  

    Als de zoekresultaten van uw weergavelogboek vergelijkbaar zijn met de afbeelding hierboven, u het dashboard van de VMware-oplossing gebruiken.  

## <a name="vmware-data-collection-details"></a>Details vMware-gegevensverzameling
De VMware Monitoring-oplossing verzamelt verschillende prestatiestatistieken en loggegevens van ESXi-hosts met behulp van de Log Analytics-agents voor Linux die u hebt ingeschakeld.

In de volgende tabel worden methoden voor het verzamelen van gegevens en andere details weergegeven over de manier waarop gegevens worden verzameld.

| 
    platform
   | Log Analytics-agent voor Linux | SCOM-agent | Azure Storage | SCOM vereist? | SCOM-agentgegevens verzonden via managementgroep | verzamelfrequentie |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |elke 3 minuten |

In de volgende tabel worden voorbeelden weergegeven van gegevensvelden die zijn verzameld door de Oplossing VMware-monitoring:

| veldnaam | description |
| --- | --- |
| Device_s |VMware-opslagapparaten |
| ESXIFailure_s |fouttypen |
| EventTime_t |tijd waarop gebeurtenis heeft plaatsgevonden |
| HostName_s |ESXi-hostnaam |
| Operation_s |VM maken of VM verwijderen |
| ProcessName_s |gebeurtenisnaam |
| ResourceId_s |naam van de VMware-host |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |VMware SCSI-status |
| SyslogMessage_s |Syslog-gegevens |
| UserName_s |gebruiker die VM heeft gemaakt of verwijderd |
| VMName_s |VM-naam |
| Computer |hostcomputer |
| TimeGenerated |tijd dat de gegevens zijn gegenereerd |
| DataCenter_s |VMware datacenter |
| StorageLatency_s |storagelatentie (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Overzicht van VMware Monitoring-oplossing
De tegel VMware wordt weergegeven in de werkruimte Log Analytics. Het biedt een overzicht op hoog niveau van eventuele storingen. Wanneer u op de tegel klikt, gaat u naar een dashboardweergave.

![tegelzetter](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navigeren in de dashboardweergave
In de **vmware-dashboardweergave** worden blades georganiseerd door:

* Aantal foutstatussen
* Hoogste gastheer per evenement telt
* Hoogste gebeurtenistellingen
* Activiteiten voor virtuele machines
* ESXi Host Disk-evenementen

![oplossing1](./media/vmware/solutionview1-1.png)

![oplossing2](./media/vmware/solutionview1-2.png)

Klik op een blade om het zoekvenster Log Analytics te openen met gedetailleerde informatie die specifiek is voor het blad.

Vanaf hier u de logboekquery bewerken om deze te wijzigen voor iets specifieks. Zie [Gegevens zoeken met logboekquery's in Azure Monitor](../log-query/log-query-overview.md)voor meer informatie over het maken van logboekquery's.

#### <a name="find-esxi-host-events"></a>Esxi-hostevenementen zoeken
Eén ESXi-host genereert meerdere logboeken op basis van hun processen. De VMware Monitoring-oplossing centraliseert ze en vat de gebeurtenistellingen samen. Deze gecentraliseerde weergave helpt u te begrijpen welke ESXi-host een groot aantal gebeurtenissen heeft en welke gebeurtenissen het vaakst voorkomen in uw omgeving.

![Gebeurtenis](./media/vmware/events.png)

U verder boren door op een ESXi-host of een gebeurtenistype te klikken.

Wanneer u op een ESXi-hostnaam klikt, bekijkt u informatie van die ESXi-host. Als u de resultaten wilt verkleinen `“ProcessName_s=EVENT TYPE”` met het gebeurtenistype, voegt u uw zoekopdracht toe. U **ProcessName** selecteren in het zoekfilter. Dat verkleint de informatie voor jou.

![Boor](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Hoge VM-activiteiten zoeken
Een virtuele machine kan worden gemaakt en verwijderd op elke ESXi-host. Het is handig voor een beheerder om te bepalen hoeveel VM's een ESXi-host maakt. Dat op zijn beurt, helpt om de prestaties en capaciteitsplanning te begrijpen. Het bijhouden van VM-activiteiten gebeurtenissen is van cruciaal belang bij het beheren van uw omgeving.

![Boor](./media/vmware/vmactivities1.png)

Als u aanvullende ESXi-hostvm-creatiegegevens wilt zien, klikt u op een ESXi-hostnaam.

![Boor](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Veelvoorkomende logboekquery's
De oplossing bevat andere nuttige query's waarmee u uw ESXi-hosts beheren, zoals hoge opslagruimte, opslaglatentie en padfout.

![Query 's](./media/vmware/queries.png)


#### <a name="save-queries"></a>Query's opslaan
Het opslaan van logboekquery's is een standaardfunctie in Azure Monitor en kan u helpen alle query's die u nuttig hebt gevonden, te bewaren. Nadat u een query hebt gemaakt die u nuttig vindt, slaat u deze op door op de **favorieten te**klikken. Met een opgeslagen query u deze later eenvoudig opnieuw gebruiken vanaf de pagina [Mijn dashboard,](../learn/tutorial-logs-dashboards.md) waar u uw eigen aangepaste dashboards maken.

![DockerDashboardView](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Waarschuwingen maken op query's
Nadat u uw query's hebt gemaakt, u de query's gebruiken om u te waarschuwen wanneer specifieke gebeurtenissen plaatsvinden. Zie [Waarschuwingen in Logboekanalyse](../platform/alerts-overview.md) voor informatie over het maken van waarschuwingen. Zie de [Monitor VMware met het](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) blogbericht Log Analytics voor voorbeelden van waarschuwingsquery's en andere queryvoorbeelden.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Wat moet ik doen op de ESXi-hostinstelling? Welke impact zal het hebben op mijn huidige omgeving?
De oplossing maakt gebruik van het native ESXi Host Syslog forwarding mechanism. U hebt geen extra Microsoft-software op de ESXi-host nodig om de logboeken vast te leggen. Het moet een lage impact hebben op uw bestaande omgeving. U moet echter wel syslog forwarding instellen, wat ESXI-functionaliteit is.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Moet ik mijn ESXi-host opnieuw starten?
Nee. Dit proces vereist geen herstart. Soms werkt vSphere de syslog niet goed bij. Log in zo'n geval in bij de ESXi-host en herlaad de syslog. Nogmaals, u hoeft de host niet opnieuw op te starten, dus dit proces is niet storend voor uw omgeving.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Kan ik het volume van logboekgegevens dat naar Log Analytics wordt verzonden, vergroten of verkleinen?
Ja, dat kan je wel. U de instellingen van het ESXi-hostlogboekniveau in vSphere gebruiken. Logboekverzameling is gebaseerd op het *infoniveau.* Dus als u het maken of verwijderen van vm's wilt controleren, moet u het *infoniveau* op Hostd behouden. Zie voor meer informatie de [VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Waarom verstrekt Hostd geen gegevens aan Log Analytics? Mijn loginstelling is ingesteld op info.
Er was een ESXi host bug voor de syslog timestamp. Zie voor meer informatie de [VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Nadat u de tijdelijke oplossing hebt toegepast, moet Hostd normaal functioneren.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Kan ik meerdere ESXi-hosts syslog-gegevens laten doorsturen naar één VM met omsagent?
Ja. U meerdere ESXi-hosts naar één VM met omsagent laten doorsturen.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Waarom zie ik geen gegevens stromen in Log Analytics?
Er kunnen meerdere redenen zijn:

* De ESXi-host pusht gegevens niet correct naar de VM met omsagent. Voer de volgende stappen uit om te testen:

  1. Meld u aan bij de ESXi-host met ssh en voer de volgende opdracht uit:`nc -z ipaddressofVM 1514`

      Als dit niet lukt, zijn de vSphere-instellingen in de geavanceerde configuratie waarschijnlijk niet correct. Zie [Syslog-verzameling configureren](#configure-syslog-collection) voor informatie over het instellen van de ESXi-host voor syslog forwarding.
  1. Als de syslog-poortconnectiviteit succesvol is, maar u nog steeds geen gegevens ziet, herlaadt u de syslog op de ESXi-host met behulp van ssh om de volgende opdracht uit te voeren:`esxcli system syslog reload`
* De VM met Log Analytics-agent is niet correct ingesteld. Voer de volgende stappen uit om dit te testen:

  1. Log Analytics luistert naar de poort 1514. Voer de volgende opdracht uit om te controleren of deze is geopend:`netstat -a | grep 1514`
  1. Je zou `1514/tcp` de poort open moeten zien. Als u dit niet doet, controleert u of de omsagent correct is geïnstalleerd. Als u de poortinformatie niet ziet, is de syslogpoort niet geopend op de VM.

    a. Controleer of de loganalytics-agent `ps -ef | grep oms`wordt uitgevoerd met behulp van . Als het proces niet wordt uitgevoerd, start u het proces door de opdracht uit te voeren`sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Open het `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`-bestand.

     c. Controleer of de juiste gebruikers- en groepsinstelling geldig is, vergelijkbaar met:`-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Als het bestand niet bestaat of als de instelling van de gebruiker en groep verkeerd is, neemt u corrigerende maatregelen door [een Linux-server voor te bereiden.](#prepare-a-linux-server)

## <a name="next-steps"></a>Volgende stappen
* Gebruik [logboekquery's](../log-query/log-query-overview.md) in Log Analytics om gedetailleerde VMware-hostgegevens weer te geven.
* [Maak uw eigen dashboards](../learn/tutorial-logs-dashboards.md) met VMware-hostgegevens.
* [Maak waarschuwingen](../platform/alerts-overview.md) wanneer specifieke VMware-hostgebeurtenissen plaatsvinden.
