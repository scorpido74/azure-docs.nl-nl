---
title: Afhankelijkheidsanalyse op basis van agent instellen in Azure Migrate Server Assessment
description: In dit artikel wordt beschreven hoe u afhankelijkheidsanalyse op basis van agentinstelt in Azure Migrate Server Assessment.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: e61b7b4e6c3e566aa67d2bd585d2049ae885083b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453612"
---
# <a name="set-up-dependency-visualization"></a>Afhankelijkheidsvisualisatie instellen

In dit artikel wordt beschreven hoe u afhankelijkheidsanalyse op basis van agentinstelt in Azure Migrate:Server Assessment. [Afhankelijkheidsanalyse](concepts-dependency-visualization.md) helpt u bij het identificeren en begrijpen van afhankelijkheden tussen machines die u wilt beoordelen en migreren naar Azure.

## <a name="before-you-start"></a>Voordat u begint

- [Meer informatie over](concepts-dependency-visualization.md#agent-based-analysis) afhankelijkheidsanalyse op basis van agenten.
- Bekijk de vereisten en ondersteuningsvereisten voor het instellen van op agent gebaseerde [afhankelijkheidsvisualisatie voor VMware VM's,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements) [fysieke servers](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)en [Hyper-V VM's](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- Zorg ervoor dat u een Azure Migrate-project hebt [gemaakt.](how-to-add-tool-first-time.md)
- Als u al een project hebt gemaakt, controleert u of u het hulpprogramma Azure Migreren:Serverbeoordeling hebt [toegevoegd.](how-to-assess.md)
- Zorg ervoor dat u een [Azure Migrate-toestel](migrate-appliance.md) hebt ingesteld om uw on-premises machines te ontdekken. Meer informatie over het instellen van een toestel voor [VMware,](how-to-set-up-appliance-vmware.md) [Hyper-V](how-to-set-up-appliance-hyper-v.md)of [fysieke servers](how-to-set-up-appliance-physical.md). Het toestel detecteert on-premises machines en stuurt prestatiegegevens naar Azure Migrate:Server Assessment.
- Als u afhankelijkheidsvisualisatie wilt gebruiken, koppelt u een [werkruimte Logboekanalyse](../azure-monitor/platform/manage-access.md) aan een Azure Migrate-project:
    - U een werkruimte alleen koppelen nadat u het Azure Migrate-toestel hebt ingesteld en machines in het Azure Migrate-project hebt ontdekt.
    - Zorg ervoor dat u een werkruimte hebt in het abonnement dat het Azure Migrate-project bevat.
    - De werkruimte moet zich in de regio's Oost-VS, Zuidoost-Azië of West-Europa bevinden. Werkruimten in andere regio's kunnen niet aan een project worden gekoppeld.
    - De werkruimte moet zich in een gebied bevinden waarin [servicekaart wordt ondersteund.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)
    - U een nieuwe of bestaande Log Analytics-werkruimte koppelen aan een Azure Migrate-project.
    - U koppelt de werkruimte de eerste keer dat u afhankelijkheidsvisualisatie voor een machine instelt. De werkruimte voor een Azure Migrate-project kan niet worden gewijzigd nadat deze is toegevoegd.
    - In Log Analytics wordt de werkruimte die is gekoppeld aan Azure Migrate getagd met de toets Migratieproject en de projectnaam.

## <a name="associate-a-workspace"></a>Een werkruimte koppelen

1. Nadat u machines hebt ontdekt voor beoordeling, klikt u in **Servers** > **Azure Migrate: Server Assessment**op **Overzicht**.  
2. Klik in **Azure Migrate: Serverbeoordeling**op **Essentials**.
3. Klik in **OMS Workspace**op **Configuratie vereisen**.

     ![Werkruimte Logboekanalyse configureren](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. Geef in **de WERKRUIMTE OMS configureren**op of u een nieuwe werkruimte wilt maken of een bestaande werkruimte wilt gebruiken.
    - U een bestaande werkruimte selecteren uit alle werkruimten in het projectabonnement migreren.
    - U hebt Reader-toegang tot de werkruimte nodig om deze te koppelen.
5. Als u een nieuwe werkruimte maakt, selecteert u er een locatie voor.

    ![Een nieuwe werkruimte toevoegen](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>De VM-agents downloaden en installeren

Op elke machine die u wilt analyseren, installeert u de agents.

> [!NOTE]
> Voor machines die worden gecontroleerd door System Center Operations Manager 2012 R2 of hoger, hoeft u de MMA-agent niet te installeren. Service Map integreert met Operations Manager. [Volg](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) de integratiebegeleiding.

1. Klik in **Azure Migrate: Serverbeoordeling**op **Gedetecteerde servers**.
2. Voor elke machine die u wilt analyseren met afhankelijkheidsvisualisatie, klikt u in de kolom **Afhankelijkheden** op **Installatie van agent vereist**.
3. Download op de pagina **Afhankelijkheden** de MMA- en Afhankelijkheidsagent voor Windows of Linux.
4. Kopieer onder **MMA-agent configureren**de werkruimte-id en -sleutel. Deze heb je nodig bij het installeren van de MMA-agent.

    ![De agents installeren](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>De MMA installeren

Installeer de MMA op elke Windows- of Linux-machine die u wilt analyseren.

### <a name="install-mma-on-a-windows-machine"></a>MMA installeren op een Windows-machine

Ga als lid van het apparaat op een Windows-machine:

1. Dubbelklik op de gedownloade agent.
2. Klik op de pagina **Welkom** op **Volgende**. Klik op de pagina **Licentievoorwaarden** op **Ik ga akkoord om** de licentie te accepteren.
3. Bewaar of wijzig de standaardinstallatiemap > **Volgende**in **doelmap**.
4. Selecteer Azure Log **Analytics** > **Next**in **agentinstellingen.**
5. Klik **op Toevoegen** om een nieuwe werkruimte Log Analytics toe te voegen. Plak de werkruimte-id en de sleutel in die u van de portal hebt gekopieerd. Klik op **Volgende**.

U de agent installeren vanaf de opdrachtregel of een geautomatiseerde methode gebruiken, zoals Configuration Manager of [Intigua.](https://go.microsoft.com/fwlink/?linkid=2104196)
- [Meer informatie](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) over het gebruik van deze methoden om de MMA-agent te installeren.
- De MMA-agent kan ook worden geïnstalleerd met behulp van dit [script](https://go.microsoft.com/fwlink/?linkid=2104394).
- [Meer informatie](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) over de Windows-besturingssystemen die door MMA worden ondersteund.

### <a name="install-mma-on-a-linux-machine"></a>MMA installeren op een Linux-machine

Ga als lid van het OPA op een Linux-machine:

1. Breng de juiste bundel (x86 of x64) over naar uw Linux-computer met behulp van scp/sftp.
2. Installeer de bundel met behulp van het argument --installeren.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Meer informatie](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) over de lijst met Linux-besturingssystemen die door MMA worden ondersteund. 

## <a name="install-the-dependency-agent"></a>De afhankelijkheidsagent installeren

1. Als u de afhankelijkheidsagent op een Windows-machine wilt installeren, dubbelklikt u op het installatiebestand en volgt u de wizard.
2. Als u de afhankelijkheidsagent op een Linux-machine wilt installeren, installeert u deze als hoofd met behulp van de volgende opdracht:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Meer informatie](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) over hoe u scripts gebruiken om de afhankelijkheidsagent te installeren.
- [Meer informatie](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) over de besturingssystemen die worden ondersteund door de afhankelijkheidsagent.


## <a name="create-a-group-using-dependency-visualization"></a>Een groep maken met afhankelijkheidsvisualisatie

Maak nu een groep voor beoordeling. 


> [!NOTE]
> Groepen waarvoor u afhankelijkheden wilt visualiseren, mogen niet meer dan 10 machines bevatten. Als u meer dan 10 machines hebt, splitst u ze op in kleinere groepen.

1. Klik in **Azure Migrate: Serverbeoordeling**op **Gedetecteerde servers**.
2. Klik in de kolom **Afhankelijkheden** op **Afhankelijkheden weergeven** voor elke machine die u wilt controleren.
3. Op de afhankelijkheidskaart ziet u het volgende:
    - Binnenkomende (clients) en uitgaande (servers) TCP-verbindingen, van en naar de machine.
    - Afhankelijke machines die de afhankelijkheidsagents niet hebben geïnstalleerd, worden gegroepeerd op poortnummers.
    - Afhankelijke machines met geïnstalleerde afhankelijkheidsagents worden als afzonderlijke vakken weergegeven.
    - Processen die in de machine worden uitgevoerd. Vouw elke machinebox uit om de processen te bekijken.
    - Machine-eigenschappen (inclusief FQDN, besturingssysteem, MAC-adres). Klik op elk machinevak om de details te bekijken.

4. U afhankelijkheden voor verschillende tijdsduur bekijken door te klikken op de tijdsduur in het tijdsbereiklabel.
    - Standaard is het bereik een uur. 
    - U het tijdsbereik wijzigen of begin- en einddatums en duur opgeven.
    - Het tijdsbereik kan oplopen tot een uur. Als u een groter bereik nodig hebt, gebruikt u Azure Monitor om afhankelijke gegevens voor een langere periode op te vragen.

5. Nadat u de afhankelijke machines hebt geïdentificeerd die u samen wilt groeperen, gebruikt u Ctrl+Klik om meerdere machines op de kaart te selecteren en klikt u op **Machines groeperen**.
6. Geef een groepsnaam op.
7. Controleer of de afhankelijke machines worden gedetecteerd door Azure Migrate.

    - Als een afhankelijke machine niet wordt ontdekt door Azure Migrate: Server Assessment, u deze niet aan de groep toevoegen.
    - Als u een machine wilt toevoegen, voert u de detectie opnieuw uit en controleert u of de machine wordt gedetecteerd.

8. Als u een beoordeling voor deze groep wilt maken, schakelt u het selectievakje in om een nieuwe beoordeling voor de groep te maken.
8. Klik op **OK** om de groep op te slaan.

Nadat u de groep hebt gemaakt, raden we u aan agents op alle machines in de groep te installeren en vervolgens afhankelijkheden voor de hele groep te visualiseren.

## <a name="query-dependency-data-in-azure-monitor"></a>Afhankelijkheidsgegevens voor query's in Azure Monitor

U afhankelijkheidsgegevens opvragen die zijn vastgelegd in Servicemap in de werkruimte Log Analytics die is gekoppeld aan het Azure Migrate-project. Log Analytics wordt gebruikt voor het schrijven en uitvoeren van Azure Monitor-logboekquery's.

- [Meer informatie over het](../azure-monitor/insights/service-map.md#log-analytics-records) zoeken naar servicekaartgegevens in Log Analytics.
- [Een overzicht](../azure-monitor/log-query/get-started-queries.md) van het schrijven van logquery's in [Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Voer een query voor afhankelijkheidsgegevens als volgt uit:

1. Nadat u de agents hebt geïnstalleerd, gaat u naar de portal en klikt u op **Overzicht**.
2. Klik in **Azure Migrate: Serverbeoordeling**op **Overzicht**. Klik op de pijl-omlaag om **Essentials**uit te vouwen.
3. Klik in **OMS Workspace**op de naam van de werkruimte.
3. Klik op de werkruimtepagina logboekanalyse > **Algemeen**op **Logboeken**.
4. Schrijf uw query en klik op **Uitvoeren**.

### <a name="sample-queries"></a>Voorbeeldquery's

Hier volgen een paar voorbeeldquery's die u gebruiken om afhankelijkheidsgegevens te extraheren.

- U de query's wijzigen om de gewenste gegevenspunten te extraheren.
- [Bekijk](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) een volledige lijst met afhankelijkheidsgegevensrecords.
- [Bekijk](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) aanvullende voorbeeldquery's.

#### <a name="sample-review-inbound-connections"></a>Voorbeeld: Binnenkomende verbindingen controleren

Controleer binnenkomende verbindingen voor een set VM's.

- De records in de tabel voor verbindingsstatistieken (VMConnection) vertegenwoordigen geen afzonderlijke fysieke netwerkverbindingen.
- Meerdere fysieke netwerkverbindingen worden gegroepeerd in een logische verbinding.
- [Meer informatie](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) over hoe fysieke netwerkverbindingsgegevens worden samengevoegd in VMConnection.

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

#### <a name="sample-summarize-sent-and-received-data"></a>Voorbeeld: verzonden en ontvangen gegevens samenvatten

In dit voorbeeld wordt een overzicht van de hoeveelheid gegevens die is verzonden en ontvangen op binnenkomende verbindingen tussen een set machines.

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

[Maak een beoordeling](how-to-create-assessment.md) voor een groep.


