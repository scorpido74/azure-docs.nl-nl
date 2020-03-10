---
title: Afhankelijkheids visualisatie instellen in Azure Migrate
description: In dit artikel wordt beschreven hoe u afhankelijkheids visualisatie instelt in Azure Migrate server-evaluatie.
ms.topic: article
ms.date: 2/24/2020
ms.openlocfilehash: 2b75a38a376558946841d08ab7a9dbf730232e51
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78940968"
---
# <a name="set-up-dependency-visualization"></a>Visualisatie van afhankelijkheid instellen

In dit artikel wordt beschreven hoe u afhankelijkheids visualisatie instelt in Azure Migrate: Server Assessment. Met de [visualisatie van afhankelijkheden](concepts-dependency-visualization.md#what-is-dependency-visualization) kunt u afhankelijkheden identificeren en begrijpen tussen computers die u wilt beoordelen en migreren naar Azure.

## <a name="before-you-start"></a>Voordat u begint

- [Bekijk](concepts-dependency-visualization.md) de vereisten en kosten die zijn gekoppeld aan de visualisatie van afhankelijkheden.
- Zorg ervoor dat u een Azure Migrate project hebt [gemaakt](how-to-add-tool-first-time.md) .
- Als u al een project hebt gemaakt, moet u ervoor zorgen dat u het Azure Migrate: Server Assessment Tool hebt [toegevoegd](how-to-assess.md) .
- Zorg ervoor dat u een [Azure migrate apparaat](migrate-appliance.md) hebt ingesteld om uw on-premises machines te detecteren. Meer informatie over het instellen van een apparaat voor [VMware](how-to-set-up-appliance-vmware.md) of [Hyper-V](how-to-set-up-appliance-hyper-v.md). Het apparaat detecteert on-premises machines en verstuurt meta gegevens en prestatie gegevens naar Azure Migrate: Server evaluatie.
- Als u afhankelijkheids visualisatie wilt gebruiken, koppelt u een [log Analytics-werk ruimte](../azure-monitor/platform/manage-access.md) aan een Azure migrate project:
    - Zorg ervoor dat u een werk ruimte hebt in het abonnement met het Azure Migrate-project.
    - De werk ruimte moet zich bevinden in de regio's VS-Oost, Zuidoost-Azië of Europa-west. Werk ruimten in andere regio's kunnen niet worden gekoppeld aan een project.
    - De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
    - U kunt een nieuwe of bestaande Log Analytics-werk ruimte koppelen aan een Azure Migrate-project.
    - U koppelt de werk ruimte de eerste keer dat u een afhankelijkheids visualisatie instelt voor een machine. De werk ruimte voor een Azure Migrate project kan niet worden gewijzigd nadat deze is toegevoegd.
    - In Log Analytics wordt de werk ruimte die is gekoppeld aan Azure Migrate gelabeld met de sleutel van het migratie project en de project naam.

- U kunt een werk ruimte pas toevoegen nadat u de computers in het Azure Migrate-project hebt gedetecteerd. U kunt dit doen door een Azure Migrate apparaat in te stellen voor [VMware](how-to-set-up-appliance-vmware.md) of [Hyper-V](how-to-set-up-appliance-hyper-v.md). Het apparaat detecteert on-premises machines en verstuurt meta gegevens en prestatie gegevens naar Azure Migrate: Server evaluatie. [Meer informatie](migrate-appliance.md).

## <a name="associate-a-workspace"></a>Een werk ruimte koppelen

1. Nadat u computers voor beoordeling hebt gedetecteerd, klikt u in **Servers** > **Azure migrate: Server evaluatie**op **overzicht**.  
2. Klik in **Azure migrate: Server evaluatie**op **essentiële**elementen.
3. Klik in de **OMS-werk ruimte**op **configuratie vereist**.

     ![Log Analytics werkruimte configureren](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. In de **werk ruimte OMS configureren**geeft u op of u een nieuwe werk ruimte wilt maken of een bestaande wilt gebruiken.
    - U kunt een bestaande werk ruimte selecteren uit alle werk ruimten in het project abonnement migreren.
    - U hebt toegang tot de lezer nodig om de werk ruimte te koppelen.
5. Als u een nieuwe werk ruimte maakt, selecteert u een locatie.

    ![Een nieuwe werk ruimte toevoegen](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>De VM-agents downloaden en installeren

Installeer de agents op elke computer die u wilt analyseren.

> [!NOTE]
    > Voor computers die worden bewaakt door System Center Operations Manager 2012 R2 of hoger, hoeft u de MMA-agent niet te installeren. Servicetoewijzing integreert met Operations Manager. [Volg deze](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) richt lijnen voor integratie.

1. Klik in **Azure migrate: Server evaluatie**op **gedetecteerde servers**.
2. Voor elke computer die u wilt analyseren met afhankelijkheids visualisatie, klikt u in de kolom **afhankelijkheden** op **Agent installatie vereist**.
3. Down load de MMA-en Dependency-agent voor Windows of Linux op de pagina **afhankelijkheden** .
4. Onder **MMA-agent configureren kopieert u**de werk ruimte-ID en-sleutel. U hebt deze nodig wanneer u de MMA-Agent installeert.

    ![De agents installeren](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>De MMA installeren

Installeer de MMA op elke Windows-of Linux-computer die u wilt analyseren.

### <a name="install-mma-on-a-windows-machine"></a>MMA installeren op een Windows-computer

De agent installeren op een Windows-computer:

1. Dubbelklik op de gedownloade agent.
2. Klik op de pagina **Welkom** op **Volgende**. Klik op de pagina **Licentievoorwaarden** op **Akkoord** om de licentie te accepteren.
3. Bewaar of wijzig in **doelmap**de standaardinstallatiemap > **volgende**.
4. Selecteer in **installatie opties voor agent**de optie **Azure log Analytics** > **volgende**.
5. Klik op **toevoegen** om een nieuwe log Analytics-werk ruimte toe te voegen. Plak de werk ruimte-ID en-sleutel die u hebt gekopieerd uit de portal. Klik op **Volgende**.

U kunt de agent installeren vanaf de opdracht regel of met behulp van een geautomatiseerde methode als Configuration Manager of [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196).
- Meer [informatie](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) over het gebruik van deze methoden om de MMA-agent te installeren.
- De MMA-agent kan ook worden geïnstalleerd met behulp van dit [script](https://go.microsoft.com/fwlink/?linkid=2104394).
- Meer [informatie](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) over de Windows-besturings systemen die worden ondersteund door MMA.

### <a name="install-mma-on-a-linux-machine"></a>MMA installeren op een Linux-computer

De MMA op een Linux-computer installeren:

1. De juiste bundel (x86 of x64) overdragen aan uw Linux-computer met SCP/SFTP.
2. Installeer de bundel met behulp van het argument--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

Meer [informatie](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) over de lijst met ondersteunde Linux-besturings systemen door MMA. 

## <a name="install-the-dependency-agent"></a>De afhankelijkheidsagent installeren

1. Als u de afhankelijkheids agent op een Windows-computer wilt installeren, dubbelklikt u op het installatie bestand en volgt u de wizard.
2. Als u de afhankelijkheids agent op een Linux-computer wilt installeren, installeert u als root met de volgende opdracht:

    ```sh InstallDependencyAgent-Linux64.bin```

- Meer [informatie](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) over hoe u scripts kunt gebruiken om de afhankelijkheids agent te installeren.
- Meer [informatie](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) over de besturings systemen die worden ondersteund door de afhankelijkheids agent.


## <a name="create-a-group-using-dependency-visualization"></a>Een groep maken met behulp van afhankelijkheids visualisatie

Maak nu een groep voor evaluatie. 


> [!NOTE]
> Groepen waarvoor u afhankelijkheden wilt visualiseren, mogen niet meer dan 10 machines bevatten. Als u meer dan 10 computers hebt, splitst u deze in kleinere groepen.

1. Klik in **Azure migrate: Server evaluatie**op **gedetecteerde servers**.
2. Klik in de kolom **afhankelijkheden** op **afhankelijkheden weer geven** voor elke computer die u wilt controleren.
3. Op de afhankelijkheids kaart ziet u het volgende:
    - Inkomende (clients) en uitgaande (servers) TCP-verbindingen van en naar de computer.
    - Afhankelijke computers waarop geen afhankelijkheids agenten zijn geïnstalleerd, worden gegroepeerd op poort nummers.
    - Afhankelijke computers waarop afhankelijkheids agenten zijn geïnstalleerd, worden weer gegeven als afzonderlijke vakken.
    - Processen die worden uitgevoerd op de computer. Vouw elke machine uit om de processen weer te geven.
    - Computer eigenschappen (inclusief FQDN, besturings systeem, MAC-adres). Klik op elke computer vak om de details weer te geven.

4. U kunt afhankelijkheden voor verschillende tijds duren bekijken door te klikken op de tijds duur in het label tijds bereik.
    - Het bereik is standaard een uur. 
    - U kunt het tijds bereik wijzigen of begin-en eind datums en duur opgeven.
    - Het tijds bereik kan Maxi maal een uur duren. Als u een langere periode nodig hebt, gebruikt u Azure Monitor voor het opvragen van afhankelijke gegevens gedurende langere tijd.

5. Nadat u de afhankelijke computers die u wilt groeperen, hebt geïdentificeerd, gebruikt u CTRL + klikken om meerdere computers op de kaart te selecteren en klikt u op **machines groeperen**.
6. Geef een groeps naam op.
7. Controleer of de afhankelijke computers worden gedetecteerd door Azure Migrate.

    - Als een afhankelijke machine niet wordt gedetecteerd door Azure Migrate: Server evaluatie, kunt u deze niet toevoegen aan de groep.
    - Als u een machine wilt toevoegen, voert u de detectie opnieuw uit en controleert u of de computer is gedetecteerd.

8. Als u een evaluatie voor deze groep wilt maken, schakelt u het selectie vakje in om een nieuwe evaluatie voor de groep te maken.
8. Klik op **OK** om de groep op te slaan.

Nadat u de groep hebt gemaakt, wordt u aangeraden agents op alle computers in de groep te installeren en vervolgens afhankelijkheden voor de hele groep te visualiseren.

## <a name="query-dependency-data-in-azure-monitor"></a>Query's uitvoeren op afhankelijkheids gegevens in Azure Monitor

U kunt een query uitvoeren op afhankelijkheids gegevens die zijn vastgelegd door Servicetoewijzing in de werk ruimte Log Analytics die aan het Azure Migrate project is gekoppeld. Log Analytics wordt gebruikt om Azure Monitor-logboek query's te schrijven en uit te voeren.

- [Meer informatie over het](../azure-monitor/insights/service-map.md#log-analytics-records) zoeken naar servicetoewijzing gegevens in log Analytics.
- [Bekijk een overzicht van het](../azure-monitor/log-query/get-started-queries.md) schrijven van logboek query's in [log Analytics](../azure-monitor/log-query/get-started-portal.md).

Voer een query uit voor afhankelijkheids gegevens als volgt:

1. Nadat u de agents hebt geïnstalleerd, gaat u naar de portal en klikt u op **overzicht**.
2. Klik in **Azure migrate: Server evaluatie**op **overzicht**. Klik op de pijl-omlaag om de **kernen**uit te vouwen.
3. Klik in de **OMS-werk ruimte**op de naam van de werk ruimte.
3. Klik op de pagina Log Analytics werk ruimte > **Algemeen**op **Logboeken**.
4. Schrijf uw query en klik op **uitvoeren**.

### <a name="sample-queries"></a>Voorbeeldquery's

Hier volgen enkele voor beelden van query's die u kunt gebruiken om afhankelijkheids gegevens te extra heren.

- U kunt de query's aanpassen om uw voorkeurs gegevens punten uit te pakken.
- [Bekijk](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) een volledige lijst met afhankelijkheids gegevens records.
- [Bekijk](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) aanvullende voorbeeld query's.

#### <a name="sample-review-inbound-connections"></a>Voor beeld: binnenkomende verbindingen controleren

Controleer binnenkomende verbindingen voor een set Vm's.

- De records in de tabel voor de metrische gegevens van de verbinding (VMConnection) vertegenwoordigen geen afzonderlijke fysieke netwerk verbindingen.
- Meerdere fysieke netwerk verbindingen worden gegroepeerd in een logische verbinding.
- Meer [informatie](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) over hoe fysieke netwerk verbindings gegevens worden geaggregeerd in VMConnection.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Voor beeld: verzonden en ontvangen gegevens samenvatten

In dit voor beeld wordt een overzicht gegeven van het volume van de gegevens die worden verzonden en ontvangen op binnenkomende verbindingen tussen een set machines.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Volgende stappen

[Een evaluatie](how-to-create-assessment.md) voor een groep maken.


