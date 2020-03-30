---
title: Werken met de vorige versie van Azure Migreren
description: Beschrijft hoe u werken met de vorige versie van Azure Migreren.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c9cea52e04a991e6e3ac64426f0443939f8aaa3a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77914378"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Werken met de vorige versie van Azure Migreren

In dit artikel vindt u informatie over het werken met de vorige versie van Azure Migrate.


Er zijn twee versies van de Azure Migrate-service:

- **Huidige versie**: Gebruik deze versie om Azure Migrate-projecten te maken, on-premises machines te ontdekken en beoordelingen en migraties te orkestreren. [Meer informatie](whats-new.md) over wat er nieuw is in deze versie.
- **Vorige versie**: Als u de vorige versie van Azure Migrate gebruikt (alleen beoordeling van on-premises VMware VM's werd ondersteund), moet u nu de huidige versie gebruiken. Als u azure migrate-projecten die in de vorige versie zijn gemaakt, nog steeds moet gebruiken, u dit wel en niet doen:
    - U geen migratieprojecten meer maken.
    - We raden je aan geen nieuwe ontdekkingen te doen.
    - U hebt nog steeds toegang tot bestaande projecten.
    - U nog steeds beoordelingen uitvoeren.
    

## <a name="upgrade-between-versions"></a>Upgraden tussen versies

U projecten of onderdelen in de vorige versie niet upgraden naar de nieuwe versie. U moet [een nieuw Azure Migrate-project maken](how-to-add-tool-first-time.md)en er beoordelings- en migratiehulpprogramma's aan toevoegen.

## <a name="find-projects-from-previous-version"></a>Projecten uit vorige versie zoeken

Als volgt projecten uit de vorige versie zoeken:

1. Zoek in de Azure-portal > **Alle services**naar Azure Migreren en selecteer **deze.** 
2. Op het Azure Migrate-dashboard is er een melding en een koppeling om toegang te krijgen tot oude Azure Migrate-projecten.
3. Klik op de link om v1-projecten te openen.


## <a name="create-an-assessment"></a>Een evaluatie maken

Nadat virtuele machines in de portal zijn gedetecteerd, kunt u ze groeperen en een evaluatie maken.

- U direct als on-premises assessments maken nadat VM's in het portaal zijn ontdekt.
- Voor prestatiegebaseerde beoordelingen raden we u aan minstens een dag te wachten voordat u een prestatiegebaseerde beoordeling maakt, om betrouwbare aanbevelingen voor grootte te krijgen.

Maak als volgt een beoordeling:

1. Klik op de **overzichtspagina** van het project op **+Evaluatie maken**.
2. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.
3. Maak de groep en geef een groepsnaam op.
4. Selecteer de machines die u aan de groep wilt toevoegen.
5. Klik op **Evaluatie maken** om de groep en de evaluatie te maken.
6. Nadat de beoordeling is gemaakt, bekijkt u deze in **overzichtsdashboard** > **Dashboard**.
7. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.

Als u een bestaande evaluatie wilt bijwerken met de meest recente prestatiegegevens, kunt u de opdracht **Opnieuw berekenen** voor de evaluatie gebruiken om deze bij te werken.

## <a name="review-an-assessment"></a>Een beoordeling beoordelen 

Een beoordeling kent drie fasen:

- Een beoordeling begint met een geschiktheidsanalyse om erachter te komen of machines compatibel zijn in Azure.
- Grootteschattingen.
- Maandelijkse kostenschatting.

Een machine beweegt alleen mee naar een later stadium als deze de vorige passeert. Als een machine bijvoorbeeld niet voldoet aan de geschiktheidscontrole, wordt deze gemarkeerd als ongeschikt voor Azure en wordt de grootte en kosten niet uitgevoerd.


### <a name="review-azure-readiness"></a>Azure-gereedheid controleren

De weergave van de Azure-gereedheid in de evaluatie toont de gereedheidsstatus van alle VM's.

**Bereidheid** | **Staat** | **Details**
--- | --- | ---
Gereed voor Azure | Geen compatibiliteitsproblemen. De machine kan worden gemigreerd naar Azure en wordt opgestart in Azure met volledige Azure-ondersteuning. | Voor virtuele machines die gereed zijn, wordt door Azure Migrate een VM-grootte in Azure aanbevolen.
Voorwaardelijk gereed voor Azure | De machine wordt mogelijk opgestart in Azure, maar heeft mogelijk geen volledige Azure-ondersteuning. Bijvoorbeeld een machine met een oudere versie van Windows Server die niet wordt ondersteund in Azure. | Azure Migrate legt de gereedheidsproblemen uit en biedt herstelstappen.
Niet gereed voor Azure |  De VM wordt niet opgestart in Azure. Als een VM bijvoorbeeld een schijf van meer dan 4 TB heeft, kan deze niet worden gehost op Azure. | Azure Migrate legt de gereedheidsproblemen uit en biedt herstelstappen.
Gereedheid onbekend | Azure Migrate kan azure-gereedheid niet identificeren, meestal omdat er geen gegevens beschikbaar zijn.


#### <a name="azure-vm-properties"></a>Azure VM-eigenschappen
Gereedheid houdt rekening met een aantal VM-eigenschappen om te bepalen of de VM in Azure kan worden uitgevoerd.


**Eigenschap** | **Details** | **Bereidheid**
--- | --- | ---
**Opstarttype** | BIOS ondersteund. UEFI wordt niet ondersteund. | Voorwaardelijk klaar als boottype UEFI is.
**Kernen** | Machines core <= het maximum aantal cores (128) dat wordt ondersteund voor een Azure VM.<br/><br/> Als de prestatiegeschiedenis beschikbaar is, houdt Azure Migrate rekening met de gebruikte kernen.<br/>Als een comfortfactor wordt opgegeven in de beoordelingsinstellingen, wordt het aantal gebruikte kernen vermenigvuldigd met de comfortfactor.<br/><br/> Als er geen prestatiegeschiedenis is, gebruikt Azure Migrate de toegewezen kernen, zonder de comfortfactor toe te passen. | Klaar als minder dan of gelijk aan grenzen.
**Geheugen** | De grootte van het machinegeheugen <= het maximale&nbsp;geheugen (3892 GB op Azure M-reeks Standard_M128m<sup>2)</sup>voor een Azure VM. [Meer informatie](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Als de prestatiegeschiedenis beschikbaar is, houdt Azure Migrate rekening met het gebruikte geheugen.<br/><br/>Als een comfortfactor wordt opgegeven, wordt het gebruikte geheugen vermenigvuldigd met de comfortfactor.<br/><br/> Als er geen geschiedenis is, wordt het toegewezen geheugen gebruikt, zonder de comfortfactor toe te passen.<br/><br/> | Klaar als binnen de grenzen.
**Opslagschijf** | Toegewezen grootte van een schijf moet 4 TB (4096 GB) of minder.<br/><br/> Het aantal schijven dat aan de machine is gekoppeld, moet 65 of minder zijn, inclusief de os-schijf. | Klaar als binnen de grenzen.
**Networking** | Aan een machine moeten 32 of minder NIC's zijn bevestigd. | Klaar als binnen de grenzen.

#### <a name="guest-operating-system"></a>Gastbesturingssysteem

Samen met VM-eigenschappen kijkt Azure Migrate ook naar het gastbesturingssysteem van de on-premises VM om te bepalen of de VM in Azure kan worden uitgevoerd.

- Azure Migrate houdt rekening met het besturingssysteem dat is opgegeven in vCenter Server.
- Aangezien de detectie van Azure Migrate op basis van apparaten is gebaseerd op apparaten, is het geen manier om te controleren of het besturingssysteem dat in de VM wordt uitgevoerd, hetzelfde is als het besturingssysteem dat is opgegeven in vCenter Server.

De volgende logica wordt gebruikt.

**Besturingssysteem** | **Details** | **Bereidheid**
--- | --- | ---
Windows Server 2016 en alle SP's | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2012 R2 en alle SP's | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2012 en alle SP's | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2008 R2 en alle SP's | Azure biedt volledige ondersteuning.| Gereed voor Azure
Windows Server 2008 (32-bits en 64-bits) | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2003, 2003 R2 | Out-of-support en hebben een [Custom Support Agreement (CSA)](https://aka.ms/WSosstatement) nodig voor ondersteuning in Azure. | Overweeg voorwaardelijk klaar voor Azure om het besturingssysteem te upgraden voordat u migreert naar Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Out-of-support. De machine kan worden opgestart in Azure, maar er wordt geen OS-ondersteuning geleverd door Azure. | Voorwaardelijk klaar voor Azure, wordt aanbevolen om het besturingssysteem te upgraden voordat u migreert naar Azure.
Windows-client 7, 8 en 10 | Azure biedt alleen ondersteuning met [een Visual Studio-abonnement.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Voorwaardelijk gereed voor Azure
Windows 10 Pro-bureaublad | Azure biedt ondersteuning met [Multitenant Hosting Rights.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Voorwaardelijk gereed voor Azure
Windows Vista, XP Professional | Out-of-support. De machine kan worden opgestart in Azure, maar er wordt geen OS-ondersteuning geleverd door Azure. | Voorwaardelijk klaar voor Azure, wordt aanbevolen om het besturingssysteem te upgraden voordat u migreert naar Azure.
Linux | Azure onderschrijft deze [Linux-besturingssystemen.](../virtual-machines/linux/endorsed-distros.md) Andere Linux-besturingssystemen kunnen worden opgestart in Azure, maar we raden u aan het besturingssysteem te upgraden naar een goedgekeurde versie voordat u naar Azure migreert. | Klaar voor Azure als de versie wordt goedgekeurd.<br/><br/>Voorwaardelijk klaar als de versie niet wordt goedgekeurd.
Andere besturingssystemen<br/><br/> Bijvoorbeeld Oracle Solaris, Apple Mac OS etc., FreeBSD, enz. | Azure onderschrijft deze besturingssystemen niet. De machine kan worden opgestart in Azure, maar er wordt geen OS-ondersteuning geleverd door Azure. | Voorwaardelijk klaar voor Azure, wordt aanbevolen om een ondersteund besturingssysteem te installeren voordat u migreert naar Azure.  
BE opgegeven als **Ander** in vCenter Server | Azure Migrate kan het besturingssysteem in dit geval niet identificeren. | Onbekende bereidheid. Controleer of het besturingssysteem dat in de VM wordt uitgevoerd, wordt ondersteund in Azure.
32-bits besturingssystemen | De machine kan worden opgestart in Azure, maar Azure biedt mogelijk geen volledige ondersteuning. | Overweeg voorwaardelijk klaar voor Azure en overweeg het besturingssysteem van de machine te upgraden van 32-bits besturingssysteem naar 64-bits besturingssysteem voordat u naar Azure migreert.


### <a name="review-sizing"></a>Bekijk de grootte van de beoordeling

 De aanbeveling voor de grootte van Azure Migrate is afhankelijk van het groottecriterium dat is opgegeven in de beoordelingseigenschappen.

- Als de grootte op prestaties is gebaseerd, wordt in de grootteaanbeveling rekening gehouden met de prestatiegeschiedenis van de VM's (CPU en geheugen) en schijven (IOPS en doorvoer).
- Als het groottecriterium 'on-premises' is, is de grootteaanbeveling in Azure gebaseerd op de grootte van de vm on-premises. Schijfgrootte is gebaseerd op het opslagtype dat is opgegeven in de beoordelingseigenschappen (standaard is premiumschijven). Azure Migrate houdt geen rekening met de prestatiegegevens voor de VM en schijven.

### <a name="review-cost-estimates"></a>Beoordeling geraamde kosten

Kostenschattingen geven de totale reken- en opslagkosten weer van het uitvoeren van de VM's in Azure, samen met de details voor elke machine.

- Kostenramingen worden berekend aan de hand van de aanbeveling voor de grootte van een VM-machine en de schijven en de beoordelingseigenschappen.
- Geschatte maandelijkse kosten voor computing en opslag worden samengevoegd voor alle virtuele machines in de groep.
- De kostenschatting is voor het uitvoeren van de on-premises VM als Azure Infrastructure as a service (IaaS) VM's. Azure Migrate beschouwt Platform as a service (PaaS) of Software as a service (SaaS) niet.

### <a name="review-confidence-rating-performance-based-assessment"></a>Betrouwbaarheidsbeoordeling beoordelen (beoordeling op basis van prestaties)

Elke prestatiegebaseerde beoordeling is gekoppeld aan een betrouwbaarheidsbeoordeling.

- Een betrouwbaarheidsscore varieert van één-ster tot vijf-sterren (een-ster is de laagste en vijf-sterren de hoogste).
- De betrouwbaarheidsbeoordeling wordt toegewezen aan een beoordeling, gebaseerd op de beschikbaarheid van gegevenspunten die nodig zijn om de beoordeling te berekenen.
- De betrouwbaarheidsclassificatie van een evaluatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte die Azure Migrate geeft.
- Vertrouwensclassificatie is niet beschikbaar voor 'as-is' on-premises assessments.

Voor prestatiegebaseerde grootte heeft Azure Migrate het volgende nodig:
- Gebruiksgegevens voor CPU.
- VM-geheugengegevens.
- Voor elke schijf die aan de VM is gekoppeld, heeft deze de schijf-IOPS- en doorvoergegevens nodig.
- Voor elke netwerkadapter die aan een VM is gekoppeld, heeft Azure Migrate de netwerkinvoer/-uitvoer nodig.
- Als een van de bovenstaande niet beschikbaar zijn, zijn aanbevelingen voor grootte (en dus vertrouwensclassificaties) mogelijk niet betrouwbaar.


Afhankelijk van het percentage beschikbare gegevenspunten worden de mogelijke vertrouwensbeoordelingen samengevat in de tabel.

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Beoordelingsproblemen die van invloed zijn op vertrouwensbeoordelingen

Een beoordeling heeft mogelijk niet alle gegevenspunten beschikbaar om een aantal redenen:

- Je hebt je omgeving niet geprofileerd voor de duur van de beoordeling. Als u bijvoorbeeld de beoordeling maakt waarbij de prestatieduur is ingesteld op één dag, moet u ten minste een dag wachten nadat u de detectie hebt gestart of alle gegevenspunten die moeten worden verzameld.
- Sommige VM's werden stilgelegd in de periode waarvoor de beoordeling werd berekend. Als vm's voor een deel van de duur zijn uitgeschakeld, kan Azure Migrate geen prestatiegegevens voor die periode verzamelen.
- Tijdens de berekeningsperiode zijn tussendoor enkele VM's gemaakt. Als u bijvoorbeeld een beoordeling maakt met behulp van de prestatiegeschiedenis van de vorige maand, maar een week geleden een aantal VM's in de omgeving hebt gemaakt, is de prestatiegeschiedenis van de nieuwe VM's niet voor de gehele duur.

> [!NOTE]
> Als de betrouwbaarheidsbeoordeling van een beoordeling lager is dan vijf sterren, wacht u ten minste een dag tot het apparaat de omgeving heeft geprofileerd en berekent u de beoordeling opnieuw. Als u niet op prestaties gebaseerde grootte misschien niet betrouwbaar. Als u niet opnieuw wilt berekenen, raden we u aan over te schakelen naar on-premises grootte, door de beoordelingseigenschappen te wijzigen.



## <a name="create-groups-using-dependency-visualization"></a>Groepen maken met afhankelijkheidsvisualisatie

Naast het handmatig maken van groepen, u groepen maken met behulp van afhankelijkheidsvisualisatie.
- U gebruikt deze methode meestal wanneer u groepen met hogere vertrouwensniveaus wilt beoordelen door afhankelijkheden van machines te controleren voordat u een beoordeling uitvoert.
- Afhankelijkheidsvisualisatie kan u helpen uw migratie naar Azure effectief te plannen. Het helpt u ervoor te zorgen dat er niets achterblijft en dat er geen verrassingsonderbrekingen optreden wanneer u naar Azure migreert.
- U alle onderling afhankelijke systemen ontdekken die samen moeten migreren en bepalen of een lopend systeem gebruikers nog steeds bedient of een kandidaat is voor ontmanteling in plaats van migratie.
- Azure Migrate gebruikt de Service Map-oplossing in Azure Monitor om afhankelijkheidsvisualisatie in te schakelen.

> [!NOTE]
> Afhankelijkheidsvisualisatie is niet beschikbaar in Azure Government.

Als u afhankelijkheidsvisualisatie wilt instellen, koppelt u een Log Analytics-werkruimte aan een Azure Migrate-project, installeert u agents op machines waarvoor u afhankelijkheden wilt visualiseren en maakt u groepen met afhankelijkheidsgegevens. 



### <a name="associate-a-log-analytics-workspace"></a>Een logboekanalysewerkruimte koppelen

Als u afhankelijkheidsvisualisatie wilt gebruiken, koppelt u een werkruimte Log Analytics aan een migratieproject. U alleen een werkruimte maken of koppelen in hetzelfde abonnement waar het migratieproject wordt gemaakt.

1. Als u een werkruimte Log Analytics aan een project wilt koppelen, klikt u in **Overzicht**> **Essentials**op **Configuratie vereisen**.
2. U een nieuwe werkruimte maken of een bestaande werkruimte toevoegen:
  - Als u een nieuwe werkruimte wilt maken, geeft u een naam op. De werkruimte wordt gemaakt in een gebied in dezelfde [Azure-geografie](https://azure.microsoft.com/global-infrastructure/geographies/) als het migratieproject.
  - Wanneer u een bestaande werkruimte koppelt, u kiezen uit alle beschikbare werkruimten in hetzelfde abonnement als het migratieproject. Alleen die werkruimten worden weergegeven die zijn gemaakt in een [ondersteund servicemapgebied](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites). Als u een werkruimte wilt koppelen, moet u ervoor zorgen dat u 'Reader'-toegang hebt tot de werkruimte.

> [!NOTE]
> U de werkruimte die is gekoppeld aan een migratieproject niet wijzigen.

### <a name="download-and-install-vm-agents"></a>VM-agents downloaden en installeren

Nadat u een werkruimte hebt geconfigureerd, downloadt en installeert u agents op elke on-premises machine die u wilt evalueren. Bovendien, als u machines zonder internetverbinding hebt, moet u [de Log Analytics-gateway](../azure-monitor/platform/gateway.md) downloaden en installeren.

1. Klik in **Overzicht**op**Machines** **beheren** > en selecteer de vereiste machine.
2. Klik in de kolom **Afhankelijkheden** op **Agents installeren**.
3. Download en installeer op de pagina **Afhankelijkheden** de Microsoft Monitoring Agent (MMA) en de afhankelijkheidsagent van elke vm die u wilt beoordelen.
4. Kopieer de werkruimte-id en -sleutel. U hebt deze nodig wanneer u de MMA op de on-premises machine installeert.

> [!NOTE]
> Als u de installatie van agents wilt automatiseren, u een implementatietool gebruiken, zoals Configuration Manager of een partnertool zoals Een, [Intigua,](https://www.intigua.com/getting-started-intigua-for-azure-migration)die een oplossing voor agentimplementatie biedt voor Azure Migrate.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>De MMA-agent op een Windows-machine installeren

Ga als lid van het apparaat op een Windows-machine:

1. Dubbelklik op de gedownloade agent.
2. Klik op de pagina **Welkom** op **Volgende**. Klik op de pagina **Licentievoorwaarden** op **Ik ga akkoord om** de licentie te accepteren.
3. Bewaar of wijzig de standaardinstallatiemap > **Volgende**in **doelmap**.
4. Selecteer Azure Log **Analytics** > **Next**in **agentinstellingen.**
5. Klik **op Toevoegen** om een nieuwe werkruimte Log Analytics toe te voegen. Plak de werkruimte-id en de sleutel in die u van de portal hebt gekopieerd. Klik op **Volgende**.

U de agent installeren vanaf de opdrachtregel of met behulp van een geautomatiseerde methode zoals Configuratiebeheer. [Meer informatie](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) over het gebruik van deze methoden om de MMA-agent te installeren.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Installeer de MMA-agent op een Linux-machine

Ga als lid op een Linux-machine:

1. Breng de juiste bundel (x86 of x64) over naar uw Linux-computer met behulp van scp/sftp.
2. Installeer de bundel met behulp van het argument --installeren.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Meer informatie](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) over de lijst met Linux-besturingssystemen die door MMA worden ondersteund.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Installeer de MMA-agent op een machine die wordt bewaakt door Operations Manager

Voor computers die worden bewaakt met System Center Operations Manager 2012 R2 of hoger is het niet nodig om de MMA-agent te installeren. Service Map integreert met de Operations Manager MMA om de benodigde afhankelijkheidsgegevens te verzamelen. [Meer informatie](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). De afhankelijkheidsagent moet wel worden geïnstalleerd.

### <a name="install-the-dependency-agent"></a>De afhankelijkheidsagent installeren

1. Als u de afhankelijkheidsagent op een Windows-machine wilt installeren, dubbelklikt u op het installatiebestand en volgt u de wizard.
2. Als u de afhankelijkheidsagent op een Linux-machine wilt installeren, installeert u deze als hoofd met behulp van de volgende opdracht:

    ```sh InstallDependencyAgent-Linux64.bin```

- Meer informatie over de [afhankelijkheidsagentondersteuning](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) voor de Windows- en Linux-besturingssystemen.
- [Meer informatie](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) over hoe u scripts gebruiken om de afhankelijkheidsagent te installeren.

>[!NOTE]
> Het artikel azure-monitor voor VM's waarnaar wordt verwezen om een overzicht te geven van de systeemvereisten en -methoden voor het implementeren van de afhankelijkheidsagent, is ook van toepassing op de servicemapoplossing.

### <a name="create-a-group-with-dependency-mapping"></a>Een groep maken met afhankelijkheidstoewijzing

1. Nadat u de agents hebt geïnstalleerd, gaat u naar de portal en klikt u op**Machines** **beheren.** > 
2. Zoek naar de machine waar u de agenten hebt geïnstalleerd.
3. De kolom **Afhankelijkheden** voor de machine moet nu worden weergegeven als **Afhankelijkheidsweergave**. Klik op de kolom om de afhankelijkheden van de machine weer te geven.
4. De afhankelijkheidskaart voor de machine toont de volgende details:
    - Binnenkomende (clients) en uitgaande (Servers) TCP-verbindingen van/naar de machine
        - De afhankelijke machines die de MMA- en afhankelijkheidsagent niet hebben geïnstalleerd, worden gegroepeerd op poortnummers.
        - De afhankelijke machines waarop de MMA en de afhankelijkheidsagent zijn geïnstalleerd, worden als afzonderlijke vakken weergegeven.
    - Processen die in de machine worden uitgevoerd, u elke machinebox uitbreiden om de processen te bekijken
    - Machine-eigenschappen, waaronder het FQDN- en besturingssysteem, MAC-adres, worden weergegeven. U op elk machinevak klikken om details weer te geven.

4. U afhankelijkheden voor verschillende tijdsduur weergeven door te klikken op de tijdsduur in het tijdsbereiklabel. Standaard is het bereik een uur. U het tijdsbereik wijzigen of begin- en einddatums en duur opgeven.

   > [!NOTE]
   >    Een tijdsbereik van maximaal een uur wordt ondersteund. Gebruik Azure Monitor-logboeken om [afhankelijkheidsgegevens](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) over een langere duur op te vragen.

5. Nadat u afhankelijke machines hebt geïdentificeerd die u samen wilt groeperen, gebruikt u Ctrl+Klik om meerdere machines op de kaart te selecteren en klikt u op **Machines groeperen**.
6. Geef een groepsnaam op. Controleer of de afhankelijke machines worden gedetecteerd door Azure Migrate.

    > [!NOTE]
    > Als een afhankelijke machine niet wordt ontdekt door Azure Migrate, u deze niet aan de groep toevoegen. Als u dergelijke machines aan de groep wilt toevoegen, moet u het detectieproces opnieuw uitvoeren met het juiste bereik in vCenter Server en ervoor zorgen dat de machine wordt gedetecteerd door Azure Migrate.  

7. Als u een beoordeling voor deze groep wilt maken, schakelt u het selectievakje in om een nieuwe beoordeling voor de groep te maken.
8. Klik op **OK** om de groep op te slaan.

Zodra de groep is gemaakt, wordt aanbevolen om agents op alle machines van de groep te installeren en de groep te verfijnen door de afhankelijkheid van de hele groep te visualiseren.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Afhankelijkheidsgegevens van query's uit Azure Monitor-logboeken

Afhankelijkheidsgegevens die door Servicemap zijn vastgelegd, zijn beschikbaar voor query's in de werkruimte Log Analytics die is gekoppeld aan uw Azure Migrate-project. [Meer informatie](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) over de gegevenstabellen servicetoewijzing die u wilt opvragen in Azure Monitor-logboeken. 

Ga als u de Kusto-query's uitvoeren:

1. Nadat u de agents hebt geïnstalleerd, gaat u naar de portal en klikt u op **Overzicht**.
2. Ga in **overzicht**naar het gedeelte **Essentials** van het project en klik op de naam van de werkruimte naast **OMS Workspace**.
3. Klik op de werkruimtepagina Log Analytics op **Algemene** > **logboeken**.
4. Schrijf uw query om afhankelijkheidsgegevens te verzamelen met Azure Monitor-logboeken. Voorbeeldquery's zoeken in de volgende sectie.
5. Voer uw query uit door op Uitvoeren te klikken. 

[Meer informatie](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) over het schrijven van Kusto-query's. 

### <a name="sample-azure-monitor-logs-queries"></a>Voorbeeld van Azure Monitor logboeken query's

Hieronder volgen voorbeeldquery's die u gebruiken om afhankelijkheidsgegevens te extraheren. U de query's wijzigen om de gewenste gegevenspunten te extraheren. Een uitputtende lijst van de velden in afhankelijkheidsgegevensrecords is [hier](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)beschikbaar. Meer voorbeeldquery's [vindt u hier.](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Binnenkomende verbindingen op een set machines samenvatten

De records in de tabel voor verbindingsstatistieken, VMConnection, vertegenwoordigen geen afzonderlijke fysieke netwerkverbindingen. Meerdere fysieke netwerkverbindingen worden gegroepeerd in een logische verbinding. [Meer informatie](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) over hoe fysieke netwerkverbindingsgegevens worden samengevoegd tot één logische record in VMConnection. 

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

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Volume van de verzonden en ontvangen gegevens samenvatten op binnenkomende verbindingen tussen een set machines

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
[Meer informatie over](migrate-services-overview.md) de nieuwste versie van Azure Migrate.
