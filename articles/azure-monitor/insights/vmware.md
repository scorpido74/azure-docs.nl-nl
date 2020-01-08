---
title: VMWare-bewaking oplossing in Azure Monitor | Microsoft Docs
description: Meer informatie over hoe u de oplossing VMware Monitoring kunt logboeken beheren en controleren van de ESXi-hosts.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: ac735c9131ebe7b7273d93a927cb4d4a8be24508
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75399194"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Oplossing VMWare-bewaking (afgeschaft) in Azure Monitor

![VMware-symbool](./media/vmware/vmware-symbol.png)

> [!NOTE]
> De oplossing VMware Monitoring is afgeschaft.  Klanten die de oplossing al hebt geïnstalleerd kunnen blijven gebruiken, maar de VMware-bewaking kan niet worden toegevoegd voor nieuwe werkruimten.

De VMWare-bewaking oplossing in Azure Monitor is een oplossing waarmee u een gecentraliseerde aanpak van logboek registratie en bewaking voor grote VMware-Logboeken kunt maken. Dit artikel wordt beschreven hoe u kunt oplossen, vastleggen en beheren van de ESXi-hosts op één locatie met behulp van de oplossing. Met de oplossing ziet u gedetailleerde gegevens voor uw ESXi-hosts op één locatie. U kunt zien aantal belangrijke gebeurtenissen, statussen en trends van VM- en ESXi-hosts via de logboeken van ESXi-host. U kunt oplossen door bekijken en gecentraliseerde logboeken van ESXi-host te zoeken. Daarnaast kunt u waarschuwingen op basis van log zoekquery's.

De oplossing maakt gebruik van systeemeigen syslog-functionaliteit van de ESXi-host om gegevens te pushen naar een doel-VM met de Log Analytics-agent. De oplossing schrijven niet echter bestanden naar syslog binnen de doel-VM. De Log Analytics-agent wordt poort 1514 geopend en luistert naar deze. Zodra de gegevens zijn ontvangen, worden de gegevens door de Log Analytics agent naar Azure Monitor gepusht.

## <a name="install-and-configure-the-solution"></a>Installeren en configureren van de oplossing
Gebruik de volgende informatie om de oplossing te installeren en configureren.

* Voeg de VMWare-bewaking oplossing toe aan uw abonnement met behulp van het proces dat wordt beschreven in [een bewakings oplossing installeren](../insights/solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Ondersteunde VMware ESXi-hosts
vSphere ESXi-Host 5.5, 6.0 of 6.5

#### <a name="prepare-a-linux-server"></a>Een Linux-server voorbereiden
Maak een Linux-besturingssysteem VM voor het ontvangen van alle syslog-gegevens van de ESXi-hosts. De [Log Analytics Linux-agent](../learn/quick-collect-linux-computer.md) is het punt verzameling voor alle ESXi-host syslog-gegevens. U kunt meerdere ESXi-hosts kunt gebruiken om logboeken naar een enkel Linux-server, zoals in het volgende voorbeeld te sturen.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![Syslog-stroom](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Syslog-verzameling configureren
1. Instellen van syslog doorsturen voor VSphere. Zie voor gedetailleerde informatie over het instellen van syslog doorsturen, [syslog op ESXi 5.0 en hoger (2003322) configureren](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Ga naar **ESXi-Host configuratie** > **Software** > **geavanceerde instellingen** > **Syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. In de *Syslog.global.logHost* veld, voegt u uw Linux-server en het poortnummer *1514*. Bijvoorbeeld, `tcp://hostname:1514` of `tcp://123.456.789.101:1514`
1. Open de firewall van de ESXi-host voor syslog. **De configuratie van de ESXi-Host** > **Software** > **beveiligingsprofiel** > **Firewall** en open **Eigenschappen**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Controleer de vSphere-Console om te controleren dat die syslog correct is ingesteld. Bevestig op de ESXI-host die poort **1514** is geconfigureerd.
1. Download en installeer de Log Analytics-agent voor Linux op de Linux-server. Zie voor meer informatie de [documentatie voor Log Analytics-agent voor Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Nadat de Log Analytics-agent voor Linux is geïnstalleerd, gaat u naar de directory /etc/opt/microsoft/omsagent/sysconf/omsagent.d en kopieer de vmware_esxi.conf van het bestand in de map /etc/opt/microsoft/omsagent/conf/omsagent.d en de wijziging van de eigenaar of groep en de machtigingen van het bestand. Bijvoorbeeld:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. De Log Analytics-agent voor Linux opnieuw starten met `sudo /opt/microsoft/omsagent/bin/service_control restart`.
1. Test de connectiviteit tussen de Linux-server en de ESXi-host met behulp van de `nc` opdracht op de ESXi-Host. Bijvoorbeeld:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. Voer in de Azure Portal een logboek query uit voor `VMware_CL`. Wanneer Azure Monitor de syslog-gegevens verzamelt, behoudt deze de indeling syslog. In de portal, enkele specifieke velden worden vastgelegd, zoals *hostnaam* en *procesnaam*.  

    ![type](./media/vmware/type.png)  

    Als de resultaten van de logboekzoekopdracht weergave vergelijkbaar met de afbeelding hierboven zijn, bent u ingesteld op het dashboard van de oplossing VMware Monitoring.  

## <a name="vmware-data-collection-details"></a>Details van VMware gegevens verzamelen
De oplossing VMware Monitoring verzamelt verschillende metrische gegevens en logboekbestanden prestatiegegevens van ESXi-hosts met behulp van de Log Analytics-agents voor Linux die u hebt ingeschakeld.

De volgende tabel bevat de methoden voor het verzamelen van gegevens en andere informatie over hoe gegevens worden verzameld.

| Platform | Log Analytics-agent voor Linux | SCOM-agents | Azure Storage | SCOM vereist? | SCOM-agent gegevens verzonden via de beheergroep | Verzamelingsfrequentie |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |om de 3 minuten |

De volgende tabel ziet u voorbeelden van velden die worden verzameld door de oplossing VMware Monitoring:

| veldnaam | description |
| --- | --- |
| Device_s |VMware-opslagapparaten |
| ESXIFailure_s |Fout-typen |
| EventTime_t |tijd waarop de gebeurtenis heeft plaatsgevonden |
| HostName_s |De naam van de ESXi-host |
| Operation_s |maken van virtuele machine of virtuele machine verwijderen |
| ProcessName_s |De naam van gebeurtenis |
| ResourceId_s |naam van de VMware-host |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |VMware SCSC-status |
| SyslogMessage_s |Syslog-gegevens |
| UserName_s |gebruikers die zijn gemaakt of verwijderd van virtuele machine |
| VMName_s |VM-naam |
| Computer |host-computer |
| TimeGenerated |Wanneer die de gegevens is gegenereerd |
| DataCenter_s |VMware-datacenter |
| StorageLatency_s |opslaglatentie (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Overzicht van de oplossing VMware Monitoring
De VMware-tegel wordt weergegeven in uw Log Analytics-werkruimte. Het biedt een weergave op hoog niveau van storingen. Als u op de tegel klikt, gaat u naar de weergave van een dashboard.

![tegelzetter](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>De dashboardweergave gaan
In de **VMware** dashboardweergave, blades zijn gerangschikt op:

* Aantal van de Status mislukt
* Bovenste Host door het aantal gebeurtenissen
* Aantal belangrijke gebeurtenissen
* Activiteiten van de virtuele Machine
* Schijfgebeurtenissen voor ESXi-Host

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Klik op een blade om Log Analytics search deelvenster waarin gedetailleerde informatie voor de blade te openen.

Hier kunt u de logboek query bewerken om deze te wijzigen voor iets specifiek. Zie [gegevens zoeken met behulp van logboek query's in azure monitor](../log-query/log-query-overview.md)voor meer informatie over het maken van logboek query's.

#### <a name="find-esxi-host-events"></a>ESXi-host evenementen zoeken
Één ESXi-host genereert meerdere logboeken, op basis van hun processen. De oplossing VMware Monitoring zijn ze gecentraliseerd en bevat een overzicht van het aantal gebeurtenissen. Deze gecentraliseerde weergave helpt u begrijpen welke ESXi-host heeft een groot aantal gebeurtenissen en welke gebeurtenissen treden het vaakst in uw omgeving.

![gebeurtenis](./media/vmware/events.png)

U kunt inzoomen verder door te klikken op een ESXi-host of een gebeurtenistype.

Wanneer u op de naam van een ESXi-host, kunt u gegevens uit die ESXi-host weergeven. Als u beperken resultaten met het gebeurtenistype wilt, voegt u toe `“ProcessName_s=EVENT TYPE”` in uw zoekopdracht. U kunt selecteren **procesnaam** in het zoekvak. Zo beperkt de gegevens voor u.

![Inzoomen](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Hoge VM activiteiten zoeken
Een virtuele machine worden gemaakt en verwijderd op een ESXi-host. Het is handig voor een beheerder om te bepalen hoeveel virtuele machines maakt u een ESXi-host. Deze beurt, kunt u inzicht in prestaties en het plannen van capaciteit. Het bijhouden van gebeurtenissen van de virtuele machine-activiteit is van cruciaal belang bij het beheren van uw omgeving.

![Inzoomen](./media/vmware/vmactivities1.png)

Als u zien van aanvullende ESXi-host virtuele machine maken van gegevens wilt, klikt u op de naam van een ESXi-host.

![Inzoomen](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Veelvoorkomende logboek query's
De oplossing bevat andere handige query's die u kunnen helpen uw ESXi-hosts, zoals hoge opslagruimte, opslaglatentie en padfout beheren.

![query's](./media/vmware/queries.png)


#### <a name="save-queries"></a>Query's opslaan
Het opslaan van logboek query's is een standaard functie in Azure Monitor en kan u helpen bij het bewaren van query's die u nuttig hebt gevonden. Nadat u een query die u hebt gemaakt, sla deze door te klikken op de **Favorieten**. Een opgeslagen query kunt u eenvoudig opnieuw kunt gebruiken vanaf de [mijn Dashboard](../learn/tutorial-logs-dashboards.md) pagina waar u uw eigen aangepaste dashboards kunt maken.

![DockerDashboardView](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Waarschuwingen van query's maken
Nadat u uw query's hebt gemaakt, wilt u mogelijk de query's gebruiken om u te waarschuwen wanneer specifieke gebeurtenissen plaatsvinden. Zie [waarschuwingen in Log Analytics](../platform/alerts-overview.md) voor informatie over het maken van waarschuwingen. Zie voor meer voorbeelden van query's en voorbeelden van andere waarschuwingen, de [Monitor VMware met behulp van Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) blogbericht.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Wat moet ik doen op de ESXi host instellen? Wat zijn de gevolgen heeft dit op mijn huidige omgeving?
De oplossing maakt gebruik van de systeemeigen ESXi-Host Syslog doorsturen mechanisme. U hoeft geen aanvullende Microsoft-software op de ESXi-Host om vast te leggen van de logboeken niet. Er moet een weinig impact op uw bestaande omgeving. U hoeft echter syslog doorsturen van berichten, ESXI-functionaliteit is ingesteld.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Moet ik mijn ESXi-host opnieuw opstarten?
Nee. Dit proces vereist niet opnieuw worden opgestart. Soms vSphere niet correct bijgewerkt de syslog. In dat geval, meld u aan bij de ESXi-host en laad de syslog opnieuw. U hebt geen opnieuw start opnieuw op de host, zodat dit proces wordt niet verstorend voor uw omgeving.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Kan ik vergroten of verkleinen van de hoeveelheid logboekgegevens die zijn verzonden naar Log Analytics?
U kunt Ja. U kunt de instellingen van de ESXi-Host logboek-niveau in vSphere gebruiken. Logboekverzameling is gebaseerd op de *info* niveau. Dus als u controleren van de virtuele machine worden gemaakt of verwijderd wilt, moet u houden de *info* op Hostd. Zie voor meer informatie de [VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Waarom is Hostd niet het leveren van gegevens naar Log Analytics? De instelling voor mijn log is ingesteld op info.
Er is een fout van de ESXi-host voor de syslog-tijdstempel. Zie voor meer informatie de [VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Nadat u de oplossing hebt toegepast, moet Hostd normaal functioneren.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Kan ik meerdere ESXi-hosts syslog-gegevens worden doorgestuurd naar een enkele virtuele machine met omsagent hebben?
Ja. U kunt meerdere ESXi-hosts worden doorgestuurd naar een enkele virtuele machine met omsagent hebben.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Waarom zie ik geen gegevens die binnenkomen in Log Analytics?
Kunnen er meerdere redenen zijn:

* De ESXi-host is niet correct pushen van gegevens naar de virtuele machine omsagent uitgevoerd. Als u wilt testen, moet u de volgende stappen uitvoeren:

  1. Om te bevestigen, meld u aan bij de ESXi-host met behulp van ssh en voer de volgende opdracht uit: `nc -z ipaddressofVM 1514`

      Als dit niet lukt, vSphere-instellingen in de geavanceerde configuratie waarschijnlijk niet corrigeren. Zie [syslog-verzameling configureren](#configure-syslog-collection) voor meer informatie over het instellen van de ESXi-host voor syslog doorsturen.
  1. Als syslog-poort verbinding geslaagd is, maar u nog steeds geen gegevens ziet, moet u vervolgens de syslog op de ESXi-host laden met behulp van ssh de volgende opdracht uit te voeren: `esxcli system syslog reload`
* De virtuele machine met Log Analytics-agent is niet correct ingesteld. U kunt dit testen, moet u de volgende stappen uitvoeren:

  1. Log Analytics luistert naar de 1514-poort. Om te controleren dat geopend is, moet u de volgende opdracht uitvoeren: `netstat -a | grep 1514`
  1. U ziet nu poort `1514/tcp` openen. Als u dit niet doet, moet u controleren of de omsagent correct is geïnstalleerd. Als u de informatie over de poort niet ziet, klikt u vervolgens is de syslog-poort niet geopend op de virtuele machine.

    a. Controleren of de Log Analytics-agent wordt uitgevoerd met behulp van `ps -ef | grep oms`. Als deze niet wordt uitgevoerd, het proces starten met de opdracht `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Open het `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`-bestand.

     c. Controleer of de juiste gebruiker en de instelling voor het groepsbeleid is geldig, vergelijkbaar met: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Als het bestand bestaat niet of de gebruiker en de instelling voor het groepsbeleid onjuist is, neemt u corrigerende maatregelen door [een Linux-server voorbereiden](#prepare-a-linux-server).

## <a name="next-steps"></a>Volgende stappen
* Gebruik [logboek query's](../log-query/log-query-overview.md) in log Analytics om gedetailleerde VMware-hostgegevens weer te geven.
* [Maak uw eigen dashboards](../learn/tutorial-logs-dashboards.md) VMware hostgegevens weergeven.
* [Waarschuwingen maken](../platform/alerts-overview.md) wanneer specifieke VMware host gebeurtenissen plaatsvinden.
