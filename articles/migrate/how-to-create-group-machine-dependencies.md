---
title: Machines groeperen met machine afhankelijkheden met Azure Migrate | Microsoft Docs
description: Hierin wordt beschreven hoe u een evaluatie maakt met behulp van computer afhankelijkheden met de Azure Migrate-service.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/01/2019
ms.author: hamusa
ms.openlocfilehash: bf6798d557cb1d27030565e4706864e945de6f04
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472091"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>Afhankelijkheids visualisatie instellen voor evaluatie

In dit artikel wordt beschreven hoe u de afhankelijkheids toewijzing instelt in Azure Migrate: Server Assessment.

Met afhankelijkheids toewijzing kunt u afhankelijkheden visualiseren tussen computers die u wilt beoordelen en migreren.

- In Azure Migrate: Server evaluatie verzamelt u computers samen voor evaluatie. Doorgaans machines die u samen wilt migreren.
- U gebruikt meestal afhankelijkheids toewijzing als u groepen met een hoger vertrouwens niveau wilt beoordelen.
- Met afhankelijkheids toewijzing kunt u computer afhankelijkheden van elkaar controleren voordat u een evaluatie en migratie uitvoert.
- Het toewijzen en visualiseren van afhankelijkheden helpt bij het effectief plannen van de migratie naar Azure. Het helpt ervoor te zorgen dat er niets achterblijft, waardoor onverwachte storingen worden voor komen tijdens de migratie.
- Met toewijzing kunt u afhankelijke systemen detecteren die samen moeten worden gemigreerd. U kunt ook bepalen of een actief systeem nog steeds gebruikers is of een kandidaat is voor buiten gebruik stellen in plaats van migratie.

Meer [informatie](concepts-dependency-visualization.md#how-does-it-work) over de visualisatie van afhankelijkheden.

## <a name="before-you-start"></a>Voordat u begint

- Zorg ervoor dat u een Azure Migrate project hebt [gemaakt](how-to-add-tool-first-time.md) .
- Als u al een project hebt gemaakt, moet u ervoor zorgen dat u het Azure Migrate: Server Assessment Tool hebt [toegevoegd](how-to-assess.md) .
- Zorg ervoor dat u uw computers hebt gedetecteerd in Azure Migrate. u kunt dit doen door een Azure Migrate apparaat in te stellen voor [VMware](how-to-set-up-appliance-vmware.md) of [Hyper-V](how-to-set-up-appliance-hyper-v.md). Het apparaat detecteert on-premises machines en verstuurt meta gegevens en prestatie gegevens naar Azure Migrate: Server evaluatie. [Meer informatie](migrate-appliance.md).


**Functies** | **Opmerking**
--- | ---
Beschikbaarheid | Visualisatie van afhankelijkheid is niet beschikbaar in Azure Government.
Servicetoewijzing | Bij een afhankelijkheids visualisatie wordt Servicetoewijzing oplossing in Azure Monitor. [Servicetoewijzing](../azure-monitor/insights/service-map.md) detecteert en toont automatisch verbindingen tussen servers.
Agents | Als u afhankelijkheids visualisatie wilt gebruiken, installeert u de volgende agents op de computers die u wilt toewijzen:<br/> - [log Analytics agent](../azure-monitor/platform/log-analytics-agent.md) agent (voorheen micro soft Monitoring Agent (MMA) genoemd.<br/> - [servicetoewijzing-afhankelijkheids agent](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent).<br/><br/> Als u de installatie van de agent wilt automatiseren, kunt u een implementatie hulpprogramma, zoals Configuration Manager, gebruiken dat een agent implementatie oplossing voor Azure Migrate heeft.
Agent voor afhankelijkheden | Controleer de ondersteuning voor de [afhankelijkheids agent](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent) voor Windows en Linux.<br/><br/> Meer [informatie](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) over het gebruik van scripts voor het installeren van de afhankelijkheids agent.
Log Analytics-agent (MMA) | Meer [informatie](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) over MMA-installatie methoden.<br/><br/> Voor computers die worden bewaakt door System Center Operations Manager 2012 R2 of hoger, hoeft u de MMA-agent niet te installeren. Servicetoewijzing integreert met Operations Manager. U kunt de integratie [hier](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)inschakelen met behulp van de richt lijnen. Houd er echter rekening mee dat de afhankelijkheids agent moet worden geïnstalleerd op deze computers.<br/><br/> [Controleer](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) de Linux-besturings systemen die worden ondersteund door de log Analytics-agent.
Beoordelings groepen | Groepen waarvoor u afhankelijkheden wilt visualiseren, mogen niet meer dan 10 machines bevatten. Als u meer dan 10 computers hebt, splitst u deze in kleinere groepen om afhankelijkheden te visualiseren.

## <a name="associate-a-log-analytics-workspace"></a>Een Log Analytics werkruimte koppelen

Als u afhankelijkheids visualisatie wilt gebruiken, moet u een [log Analytics-werk ruimte](../azure-monitor/platform/manage-access.md) koppelen aan een Azure migrate-project.

- U kunt een werk ruimte alleen in het Azure Migrate project abonnement toevoegen.
- U kunt een bestaande werk ruimte koppelen of een nieuwe maken.
- U koppelt de werk ruimte de eerste keer dat u een afhankelijkheids visualisatie instelt voor een machine.
- U kunt een werk ruimte pas toevoegen nadat u de computers in het Azure Migrate-project hebt gedetecteerd. U kunt dit doen door een Azure Migrate apparaat in te stellen voor [VMware](how-to-set-up-appliance-vmware.md) of [Hyper-V](how-to-set-up-appliance-hyper-v.md). Het apparaat detecteert on-premises machines en verstuurt meta gegevens en prestatie gegevens naar Azure Migrate: Server evaluatie. [Meer informatie](migrate-appliance.md).

Voeg als volgt een werk ruimte toe:

1. Klik in **Azure migrate: Server evaluatie**op **overzicht**. Als u het hulp programma voor Server evaluatie nog niet hebt toegevoegd, [doet u dat eerst](how-to-assess.md).
2. Klik in het **overzicht**op de pijl-omlaag om **essentiële**items uit te vouwen.
3. Klik in de **OMS-werk ruimte**op **configuratie vereist**.
4. In **werk ruimte configureren**geeft u op of u een nieuwe werk ruimte wilt maken of een bestaande wilt gebruiken:

    ![Werk ruimte toevoegen](./media/how-to-create-group-machine-dependencies/workspace.png)

    - Nadat u een naam voor een nieuwe werk ruimte hebt opgegeven, kunt u de [regio](https://azure.microsoft.com/global-infrastructure/regions/) kiezen waarin de werk ruimte wordt gemaakt.
    - Wanneer u een bestaande werk ruimte koppelt, kunt u kiezen uit alle beschik bare werk ruimten in hetzelfde abonnement als het migratie project.
    - U hebt toegang tot de lezer nodig om de werk ruimte te kunnen koppelen.
    - U kunt de werk ruimte die is gekoppeld aan een project niet wijzigen nadat deze is gekoppeld.

## <a name="download-and-install-the-vm-agents"></a>De VM-agents downloaden en installeren

Down load en installeer de agents op elke on-premises computer die u wilt visualiseren met afhankelijkheids toewijzing.

1. Klik in **Azure migrate: Server evaluatie**op **gedetecteerde servers**.
2. Voor elke machine waarvoor u de visualisatie van afhankelijkheden wilt gebruiken, klikt u op **Agent installatie vereist**.
3. Op de pagina **afhankelijkheden** van een machine > het **downloaden en installeren van MMA**, de juiste agent downloaden en installeren zoals hieronder wordt beschreven.
4. Down load en installeer de juiste agent in de **afhankelijkheids agent downloaden en**installeren, zoals hieronder wordt beschreven.
5. Onder **MMA-agent configureren kopieert u**de werk ruimte-ID en-sleutel. U hebt deze nodig wanneer u de MMA-Agent installeert.

### <a name="install-the-mma"></a>De MMA installeren

#### <a name="install-the-agent-on-a-windows-machine"></a>De agent installeren op een Windows-computer

De agent installeren op een Windows-computer:

1. Dubbelklik op de gedownloade agent.
2. Klik op de pagina **Welkom** op **Volgende**. Klik op de pagina **Licentievoorwaarden** op **Akkoord** om de licentie te accepteren.
3. Bewaar of wijzig in **doelmap**de standaardinstallatiemap > **volgende**.
4. Selecteer in **installatie opties voor agent**de optie **Azure log Analytics** > **volgende**.
5. Klik op **toevoegen** om een nieuwe log Analytics-werk ruimte toe te voegen. Plak de werk ruimte-ID en-sleutel die u hebt gekopieerd uit de portal. Klik op **Volgende**.

U kunt de agent installeren vanaf de opdracht regel of met behulp van een geautomatiseerde methode als Configuration Manager of [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196). Meer [informatie](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) over het gebruik van deze methoden om de MMA-agent te installeren. De MMA-agent kan ook worden geïnstalleerd met behulp van dit [script](https://go.microsoft.com/fwlink/?linkid=2104394).

Meer [informatie](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) over de Windows-besturings systemen die worden ondersteund door MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>De agent installeren op een Linux-computer

De agent installeren op een Linux-computer:

1. De juiste bundel (x86 of x64) overdragen aan uw Linux-computer met SCP/SFTP.
2. Installeer de bundel met behulp van het argument--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

Meer [informatie](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) over de lijst met ondersteunde Linux-besturings systemen door MMA. 

### <a name="install-the-dependency-agent"></a>De afhankelijkheidsagent installeren
1. Als u de afhankelijkheids agent op een Windows-computer wilt installeren, dubbelklikt u op het installatie bestand en volgt u de wizard.
2. Als u de afhankelijkheids agent op een Linux-computer wilt installeren, installeert u als root met de volgende opdracht:

    ```sh InstallDependencyAgent-Linux64.bin```

Meer [informatie](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) over hoe u scripts kunt gebruiken om de afhankelijkheids agent te installeren.

Meer [informatie](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) over de besturings systemen die worden ondersteund door de afhankelijkheids agent.


## <a name="create-a-group-using-dependency-visualization"></a>Een groep maken met behulp van afhankelijkheids visualisatie

1. Klik in **Azure migrate: Server evaluatie**op **gedetecteerde servers**.
2. Klik in de kolom **afhankelijkheden** op **afhankelijkheden weer geven** voor elke computer die u wilt controleren.
3. Op de afhankelijkheids kaart ziet u het volgende:
    - Inkomende (clients) en uitgaande (servers) TCP-verbindingen van en naar de computer.
    - Afhankelijke computers waarop geen afhankelijkheids agenten zijn geïnstalleerd, worden gegroepeerd op poort nummers.
    - Afhankelijke computers waarop afhankelijkheids agenten zijn geïnstalleerd, worden weer gegeven als afzonderlijke vakken.
    - Processen die worden uitgevoerd op de computer. Vouw elke machine uit om de processen weer te geven.
    - Computer eigenschappen (inclusief FQDN, besturings systeem, MAC-adres). Klik op elke computer vak om de details weer te geven.

4. U kunt afhankelijkheden voor verschillende tijds duren bekijken door te klikken op de tijds duur in het label tijds bereik. Het bereik is standaard een uur. U kunt het tijds bereik wijzigen of begin-en eind datums en duur opgeven.

    > [!NOTE]
    > Het tijds bereik kan Maxi maal een uur duren. Als u een langere periode nodig hebt, gebruikt u Azure Monitor voor het opvragen van afhankelijke gegevens gedurende langere tijd.

5. Nadat u de afhankelijke computers die u wilt groeperen, hebt geïdentificeerd, gebruikt u CTRL + klikken om meerdere computers op de kaart te selecteren en klikt u op **machines groeperen**.
6. Geef een groeps naam op.
7. Controleer of de afhankelijke computers worden gedetecteerd door Azure Migrate.

    - Als een afhankelijke machine niet wordt gedetecteerd door Azure Migrate: Server evaluatie, kunt u deze niet toevoegen aan de groep.
    - Als u een machine wilt toevoegen, voert u de detectie opnieuw uit en controleert u of de computer is gedetecteerd.

8. Als u een evaluatie voor deze groep wilt maken, schakelt u het selectie vakje in om een nieuwe evaluatie voor de groep te maken.
8. Klik op **OK** om de groep op te slaan.

Nadat u de groep hebt gemaakt, wordt u aangeraden agents op alle computers in de groep te installeren en vervolgens afhankelijkheden voor de hele groep te visualiseren.

## <a name="query-dependency-data-in-azure-monitor"></a>Query's uitvoeren op afhankelijkheids gegevens in Azure Monitor

U kunt een query uitvoeren op afhankelijkheids gegevens die zijn vastgelegd door Servicetoewijzing in de werk ruimte Log Analytics die aan uw Azure Migrate-project is gekoppeld. Log Analytics wordt gebruikt om Azure Monitor-logboek query's te schrijven en uit te voeren.

- [Meer informatie over het](../azure-monitor/insights/service-map.md#log-analytics-records) zoeken naar servicetoewijzing gegevens in log Analytics.
- [Bekijk een overzicht van het](../azure-monitor/log-query/get-started-queries.md) schrijven van logboek query's in [log Analytics](../azure-monitor/log-query/get-started-portal.md).

Voer een query uit voor afhankelijkheids gegevens als volgt:

1. Nadat u de agents hebt geïnstalleerd, gaat u naar de portal en klikt u op **overzicht**.
2. Klik in **Azure migrate: Server evaluatie**op **overzicht**. Klik op de pijl-omlaag om de **kernen**uit te vouwen.
3. Klik in de **OMS-werk ruimte**op de naam van de werk ruimte.
3. Klik op de pagina Log Analytics werk ruimte > **Algemeen**op **Logboeken**.
4. Schrijf uw query en klik op **uitvoeren**.

### <a name="sample-queries"></a>Voorbeeldquery's

We bieden een aantal voorbeeld query's die u kunt gebruiken om afhankelijkheids gegevens te extra heren.

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
