---
title: Werken met de vorige versie van Azure Migrate
description: Hierin wordt beschreven hoe u kunt werken met de vorige versie van Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 52fe4af87d1f5ed6684896aebf404926691ccb07
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186544"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Werken met de vorige versie van Azure Migrate

Dit artikel bevat informatie over het werken met de vorige versie van Azure Migrate.


Er zijn twee versies van de Azure Migrate-service:

- **Huidige versie**: gebruik deze versie om Azure migrate projecten te maken, on-premises machines te detecteren en beoordelingen en migraties te organiseren. Meer [informatie](whats-new.md) over wat er nieuw is in deze versie.
- **Vorige versie**: als u de vorige versie van Azure migrate gebruikt (alleen de evaluatie van on-premises virtuele VMware-machines wordt ondersteund), moet u nu de huidige versie gebruiken. Als u nog steeds Azure Migrate projecten moet gebruiken die in de vorige versie zijn gemaakt, kunt u dit op de volgende manieren doen:
    - U kunt geen migratie projecten meer maken.
    - U wordt aangeraden geen nieuwe detecties uit te voeren.
    - U kunt nog steeds toegang krijgen tot bestaande projecten.
    - U kunt nog steeds beoordelingen uitvoeren.
    

## <a name="upgrade-between-versions"></a>Upgrade tussen versies

U kunt geen upgrade uitvoeren van projecten of onderdelen in de vorige versie naar de nieuwe versie. U moet [een nieuw Azure migrate-project maken](how-to-add-tool-first-time.md)en er hulpprogram ma's voor de evaluatie en migratie aan toevoegen.

## <a name="find-projects-from-previous-version"></a>Projecten van de vorige versie zoeken

Ga als volgt te weten over de vorige versies van projecten:

1. In de Azure Portal > **alle services**, zoekt en selecteert u **Azure migrate**. 
2. Op het dash board van Azure Migrate ziet u een melding en een koppeling voor toegang tot oude Azure Migrate projecten.
3. Klik op de koppeling om v1-projecten te openen.


## <a name="create-an-assessment"></a>Een evaluatie maken

Nadat virtuele machines in de portal zijn gedetecteerd, kunt u ze groeperen en een evaluatie maken.

- U kunt onmiddellijk een on-premises evaluatie maken zodra er Vm's zijn gedetecteerd in de portal.
- Voor evaluaties op basis van prestaties raden we u aan minstens een dag te wachten voordat u een evaluatie op basis van prestaties maakt, zodat u kunt beschikken over betrouw bare aanbevelingen voor de grootte.

Maak als volgt een evaluatie:

1. Klik op de **overzichtspagina** van het project op **+Evaluatie maken**.
2. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.
3. Maak de groep en geef een groepsnaam op.
4. Selecteer de machines die u aan de groep wilt toevoegen.
5. Klik op **Evaluatie maken** om de groep en de evaluatie te maken.
6. Nadat de evaluatie is gemaakt, kunt u deze bekijken in **Overzicht** > **Dashboard**.
7. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.

Als u een bestaande evaluatie wilt bijwerken met de meest recente prestatiegegevens, kunt u de opdracht **Opnieuw berekenen** voor de evaluatie gebruiken om deze bij te werken.

## <a name="review-an-assessment"></a>Een evaluatie controleren 

Een evaluatie heeft drie fasen:

- Een evaluatie begint met een geschiktheids analyse om erachter te komen of computers compatibel zijn in Azure.
- Schattingen van de grootte.
- Schatting van maandelijkse kosten.

Een machine wordt alleen verplaatst naar een latere fase als deze de voor gaande wordt door gegeven. Als een computer bijvoorbeeld de geschiktheids controle niet kan uitvoeren, wordt deze gemarkeerd als niet geschikt voor Azure en wordt de grootte en de kosten voor de berekening niet in rekening gebracht.


### <a name="review-azure-readiness"></a>Azure-gereedheid controleren

De weergave van de Azure-gereedheid in de evaluatie toont de gereedheidsstatus van alle VM's.

**Gereedheid** | **Overheids** | **Details**
--- | --- | ---
Gereed voor Azure | Geen compatibiliteits problemen. De machine kan worden gemigreerd naar Azure en wordt opgestart in azure met volledige ondersteuning voor Azure. | Voor virtuele machines die gereed zijn, wordt door Azure Migrate een VM-grootte in Azure aanbevolen.
Voorwaardelijk gereed voor Azure | De computer kan worden opgestart in azure, maar heeft mogelijk geen volledige ondersteuning voor Azure. Bijvoorbeeld een machine met een oudere versie van Windows Server die niet wordt ondersteund in Azure. | In Azure Migrate worden de gereedheids problemen beschreven en worden er herstels tappen geboden.
Niet gereed voor Azure |  De virtuele machine wordt niet opgestart in Azure. Als een virtuele machine bijvoorbeeld een schijf heeft die groter is dan 4 TB, kan deze niet worden gehost op Azure. | In Azure Migrate worden de gereedheids problemen beschreven en worden er herstels tappen geboden.
Gereedheid onbekend | Azure Migrate kunt de gereedheid van Azure niet identificeren, meestal omdat er geen gegevens beschikbaar zijn.


#### <a name="azure-vm-properties"></a>Eigenschappen van Azure VM
Gereedheid houdt rekening met een aantal VM-eigenschappen om te bepalen of de virtuele machine kan worden uitgevoerd in Azure.


**Eigenschap** | **Details** | **Gereedheid**
--- | --- | ---
**Opstart type** | BIOS ondersteund. UEFI wordt niet ondersteund. | Voorwaardelijk gereed als het opstart type UEFI is.
**Kernen** | Kern < van machines = het maximum aantal kern geheugens (128) dat wordt ondersteund voor een virtuele machine van Azure.<br/><br/> Als er een prestatie geschiedenis beschikbaar is, wordt Azure Migrate beschouwd als de gebruikte kernen.<br/>Als er een comfort factor is opgegeven in de evaluatie-instellingen, wordt het aantal gebruikte kern geheugens vermenigvuldigd met de comfort factor.<br/><br/> Als er geen prestatie geschiedenis is, gebruikt Azure Migrate de toegewezen kernen zonder de comfort factor toe te passen. | Gereed als deze kleiner dan of gelijk aan de limieten zijn.
**Geheugenmetabase** | De grootte van het computer geheugen < = de maximale hoeveelheid geheugen (3892 GB op de Azure M-serie Standard_M128m&nbsp;<sup>2</sup>) voor een virtuele machine van Azure. [Meer informatie](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Als de prestatie geschiedenis beschikbaar is, Azure Migrate beschouwt het gebruikte geheugen.<br/><br/>Als er een comfort factor is opgegeven, wordt het gebruikte geheugen vermenigvuldigd met de comfort factor.<br/><br/> Als er geen geschiedenis is, wordt het toegewezen geheugen gebruikt, zonder dat u de comfort factor hoeft toe te passen.<br/><br/> | Gereed indien binnen de limieten.
**Opslag schijf** | De toegewezen grootte van een schijf moet 4 TB (4096 GB) of minder zijn.<br/><br/> Het aantal schijven dat is gekoppeld aan de computer, moet 65 of minder zijn, inclusief de besturingssysteem schijf. | Gereed indien binnen de limieten.
**Netwerken** | Aan een machine moet 32 of minder Nic's zijn gekoppeld. | Gereed indien binnen de limieten.

#### <a name="guest-operating-system"></a>Gast besturingssysteem

Naast de VM-eigenschappen controleert Azure Migrate ook op het gast besturingssysteem van de on-premises VM om te bepalen of de virtuele machine kan worden uitgevoerd in Azure.

- Azure Migrate beschouwt het besturings systeem dat is opgegeven in vCenter Server.
- Omdat de detectie door Azure Migrate is gebaseerd op het apparaat, is er geen manier om te controleren of het besturings systeem dat in de virtuele machine wordt uitgevoerd, hetzelfde is als de versie die is opgegeven in vCenter Server.

De volgende logica wordt gebruikt.

**Besturingssysteem** | **Details** | **Gereedheid**
--- | --- | ---
Windows Server 2016 en alle SPs | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2012 R2 en alle SPs | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2012 en alle SPs | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2008 R2 en alle SPs | Azure biedt volledige ondersteuning.| Gereed voor Azure
Windows Server 2008 (32-bits en 64 bits) | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2003, 2003 R2 | Out-of-support en er is een [aangepaste ondersteunings overeenkomst (CSA)](https://aka.ms/WSosstatement) vereist voor ondersteuning in Azure. | Voor bereid voor Azure kunt u het besturings systeem upgraden voordat u naar Azure migreert.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Out-of-support. De computer kan worden opgestart in azure, maar er is geen ondersteuning voor het besturings systeem van Azure. | Voor bereid voor Azure is het raadzaam om het besturings systeem bij te werken voordat u naar Azure migreert.
Windows-client 7, 8 en 10 | Azure biedt alleen ondersteuning voor een [abonnement op Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Voorwaardelijk gereed voor Azure
Windows 10 Pro Desktop | Azure biedt ondersteuning voor [multi tenant-hosting rechten.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Voorwaardelijk gereed voor Azure
Windows Vista, XP Professional | Out-of-support. De computer kan worden opgestart in azure, maar er is geen ondersteuning voor het besturings systeem van Azure. | Voor bereid voor Azure is het raadzaam om het besturings systeem bij te werken voordat u naar Azure migreert.
Linux | Azure bevestigt deze [Linux-besturings systemen](../virtual-machines/linux/endorsed-distros.md). Andere Linux-besturings systemen kunnen worden opgestart in azure, maar het is raadzaam om het besturings systeem te upgraden naar een officiële versie voordat u naar Azure migreert. | Gereed voor Azure als de versie wordt goedgekeurd.<br/><br/>Voorwaardelijk gereed als de versie niet wordt goedgekeurd.
Andere besturings systemen<br/><br/> Bijvoorbeeld Oracle Solaris, Apple Mac OS etc., FreeBSD, enzovoort. | Deze besturings systemen worden niet door Azure goedgekeurd. De computer kan worden opgestart in azure, maar er is geen ondersteuning voor het besturings systeem van Azure. | Voor bereid voor Azure is het raadzaam een ondersteund besturings systeem te installeren voordat u naar Azure migreert.  
Besturings systeem opgegeven als een **andere** in vCenter Server | Azure Migrate kan het besturings systeem in dit geval niet identificeren. | Onbekende gereedheid. Zorg ervoor dat het besturings systeem dat wordt uitgevoerd in de virtuele machine wordt ondersteund in Azure.
32-bits besturings systemen | De computer kan worden opgestart in azure, maar Azure biedt mogelijk geen volledige ondersteuning. | Voor bereid voor Azure kunt u overwegen om het besturings systeem van de machine te upgraden van 32-bits besturings systeem naar 64-bits besturings systeem voordat u naar Azure migreert.


### <a name="review-sizing"></a>Grootte controleren

 De aanbeveling van Azure Migrate formaat is afhankelijk van het criterium voor de grootte die is opgegeven in de eigenschappen van de beoordeling.

- Als de grootte van het prestatie niveau is gebaseerd op prestaties, wordt de prestatie geschiedenis van de virtuele machines (CPU en geheugen) en schijven (IOPS en door Voer) in acht neemt.
- Als het criterium voor het wijzigen van de grootte ' as on-premises ' is, is de aanbeveling van de omvang in azure gebaseerd op de grootte van de virtuele machine on-premises. De schijf grootte is gebaseerd op het opslag type dat is opgegeven in de eigenschappen van de beoordeling (standaard is Premium-schijven). Azure Migrate houdt geen rekening met de prestatie gegevens voor de virtuele machine en schijven.

### <a name="review-cost-estimates"></a>Geschatte kosten evalueren

Met kosten ramingen worden de totale reken-en opslag kosten voor het uitvoeren van de virtuele machines in azure weer gegeven, samen met de details van elke machine.

- Kosten ramingen worden berekend op basis van de aanbevolen grootte voor een VM-machine, de schijven en de evaluatie-eigenschappen.
- Geschatte maandelijkse kosten voor computing en opslag worden samengevoegd voor alle virtuele machines in de groep.
- De schatting van de kosten is voor het uitvoeren van de on-premises VM als Azure Infrastructure as a Service (IaaS) Vm's. Azure Migrate beschouwt geen PaaS (platform as a Service) of SaaS-kosten (software als een service).

### <a name="review-confidence-rating-performance-based-assessment"></a>Beoordeling van betrouw baarheid beoordelen (op basis van prestaties)

Elke evaluatie op basis van prestaties is gekoppeld aan een betrouwbaarheids classificatie.

- Een betrouwbaarheids classificatie varieert van één ster tot vijf sterren (één ster is de laagste en vijf sterren de hoogste).
- De vertrouwens classificatie wordt toegewezen aan een evaluatie op basis van de beschik baarheid van gegevens punten die nodig zijn om de evaluatie te berekenen.
- De betrouwbaarheidsclassificatie van een evaluatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte die Azure Migrate geeft.
- De vertrouwens classificatie is niet beschikbaar voor ' as-is ' on-premises Beoordelingen.

Azure Migrate heeft het volgende nodig voor het aanpassen van de prestaties:
- Gebruiks gegevens voor CPU.
- Geheugen gegevens van de virtuele machine.
- Voor elke schijf die aan de VM is gekoppeld, zijn de schijf-IOPS en doorvoer gegevens nodig.
- Voor elke netwerk adapter die aan een virtuele machine is gekoppeld, moet Azure Migrate de netwerk invoer/-uitvoer hebben.
- Als een van de bovenstaande voor keuren niet beschikbaar is, zijn de aanbevelingen voor de grootte mogelijk niet betrouwbaar.


Afhankelijk van het percentage beschik bare gegevens punten, worden de mogelijke vertrouwens classificaties in de tabel samenvatten.

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Beoordelings problemen die invloed hebben op vertrouwens classificaties

Een evaluatie heeft mogelijk niet alle gegevens punten beschikbaar vanwege een aantal redenen:

- U hebt uw omgeving niet voor de duur van de evaluatie. Als u bijvoorbeeld de beoordeling met de prestatie duur hebt ingesteld op één dag, moet u wachten tot minstens een dag nadat u de detectie hebt gestart, of alle gegevens punten die moeten worden verzameld.
- Sommige Vm's zijn afgesloten tijdens de periode waarvoor de evaluatie is berekend. Als een virtuele machine voor een deel van de duur is uitgeschakeld, kan Azure Migrate geen prestatie gegevens voor die periode verzamelen.
- Tijdens de evaluatie berekenings periode zijn enkele Vm's gemaakt. Als u bijvoorbeeld een evaluatie maakt met behulp van de prestatie geschiedenis van de afgelopen maand, maar een aantal Vm's in de omgeving per week geleden maakt, is de prestatie geschiedenis van de nieuwe Vm's niet voor de hele duur.

> [!NOTE]
> Als de betrouwbaarheids classificatie van een evaluatie onder vijf sterren is, wacht u ten minste een dag voor het apparaat om de omgeving in te dienen, en berekent u de evaluatie vervolgens opnieuw. Als u geen hoge grootte op basis van prestaties hebt, is het mogelijk niet betrouwbaar. Als u niet opnieuw wilt berekenen, kunt u het beste overschakelen naar een on-premises grootte door de evaluatie-eigenschappen te wijzigen.



## <a name="create-groups-using-dependency-visualization"></a>Groepen maken met behulp van afhankelijkheids visualisatie

Naast het hand matig maken van groepen kunt u groepen maken met behulp van afhankelijkheids visualisatie.
- Normaal gesp roken gebruikt u deze methode als u groepen met een grotere mate van vertrouwen wilt beoordelen door afhankelijkheden van machines te controleren voordat u een evaluatie uitvoert.
- Met behulp van afhankelijkheids visualisatie kunt u uw migratie naar Azure effectief plannen. Zo kunt u ervoor zorgen dat er niets achter komt en dat onverwachte storingen optreden wanneer u naar Azure migreert.
- U kunt alle afhankelijke systemen detecteren die samen moeten worden gemigreerd en aangeven of een actief systeem nog steeds gebruikers is of een kandidaat is voor buiten gebruik stellen in plaats van migratie.
- Azure Migrate maakt gebruik van de Servicetoewijzing oplossing in Azure Monitor om de visualisatie van afhankelijkheden in te scha kelen.

> [!NOTE]
> Visualisatie van afhankelijkheid is niet beschikbaar in Azure Government.

Als u afhankelijkheids visualisatie wilt instellen, koppelt u een Log Analytics-werk ruimte aan een Azure Migrate-project, installeert u agents op computers waarvoor u afhankelijkheden wilt visualiseren en maakt u vervolgens groepen met afhankelijkheids informatie. 



### <a name="associate-a-log-analytics-workspace"></a>Een Log Analytics werkruimte koppelen

Als u afhankelijkheids visualisatie wilt gebruiken, koppelt u een Log Analytics-werk ruimte aan een migratie project. U kunt alleen een werk ruimte maken of koppelen in hetzelfde abonnement waar het migratie project wordt gemaakt.

1. Als u een Log Analytics werk ruimte aan een project wilt koppelen, klikt u in **overzicht**> **Essentials**op **configuratie vereist**.
2. U kunt een nieuwe werk ruimte maken of een bestaande toevoegen:
  - Geef een naam op om een nieuwe werk ruimte te maken. De werk ruimte wordt gemaakt in een regio in dezelfde [Azure-geografie](https://azure.microsoft.com/global-infrastructure/geographies/) als het migratie project.
  - Wanneer u een bestaande werk ruimte koppelt, kunt u kiezen uit alle beschik bare werk ruimten in hetzelfde abonnement als het migratie project. Er worden alleen werk ruimten weer gegeven die zijn gemaakt in een [ondersteunde servicetoewijzing regio](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites). Als u een werk ruimte wilt koppelen, moet u ervoor zorgen dat u toegang hebt tot de werk ruimte.

> [!NOTE]
> U kunt de werk ruimte die is gekoppeld aan een migratie project niet wijzigen.

### <a name="download-and-install-vm-agents"></a>VM-agents downloaden en installeren

Nadat u een werk ruimte hebt geconfigureerd, downloadt en installeert u agents op elke on-premises computer die u wilt evalueren. Als u bovendien computers hebt zonder Internet verbinding, moet u de [log Analytics-gateway](../azure-monitor/platform/gateway.md) hierop downloaden en installeren.

1. Klik in **overzicht**op > **machines** **beheren** en selecteer de gewenste computer.
2. Klik in de kolom **afhankelijkheden** op **agents installeren**.
3. Down load en installeer micro soft Monitoring Agent (MMA) en de afhankelijkheids agent op elke virtuele machine die u wilt beoordelen op de pagina **afhankelijkheden** .
4. Kopieer de werkruimte-id en -sleutel. U hebt deze nodig wanneer u de MMA installeert op de on-premises machine.

> [!NOTE]
> Als u de installatie van agents wilt automatiseren, kunt u een implementatie hulpprogramma, zoals System Center Configuration Manager of een partner programma, zoals [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), gebruiken dat een oplossing voor agent implementatie biedt voor Azure Migrate.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>De MMA-agent op een Windows-computer installeren

De agent installeren op een Windows-computer:

1. Dubbelklik op de gedownloade agent.
2. Klik op de pagina **Welkom** op **Volgende**. Klik op de pagina **Licentievoorwaarden** op **Akkoord** om de licentie te accepteren.
3. Bewaar of wijzig in **doelmap**de standaardinstallatiemap > **volgende**.
4. Selecteer in **installatie opties voor agent**de optie **Azure log Analytics** > **volgende**.
5. Klik op **toevoegen** om een nieuwe log Analytics-werk ruimte toe te voegen. Plak de werk ruimte-ID en-sleutel die u hebt gekopieerd uit de portal. Klik op **Volgende**.

U kunt de agent installeren vanaf de opdracht regel of met behulp van een geautomatiseerde methode zoals System Center Configuration Manager. Meer [informatie](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) over het gebruik van deze methoden om de MMA-agent te installeren.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>De MMA-agent op een Linux-computer installeren

De agent installeren op een Linux-computer:

1. De juiste bundel (x86 of x64) overdragen aan uw Linux-computer met SCP/SFTP.
2. Installeer de bundel met behulp van het argument--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

Meer [informatie](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) over de lijst met ondersteunde Linux-besturings systemen door MMA.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>De MMA-agent installeren op een computer die wordt bewaakt door Operations Manager

Voor computers die worden bewaakt met System Center Operations Manager 2012 R2 of hoger is het niet nodig om de MMA-agent te installeren. Servicetoewijzing integreert met de Operations Manager MMA om de benodigde afhankelijkheids gegevens te verzamelen. [Meer informatie](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). De afhankelijkheids agent moet worden geïnstalleerd.

### <a name="install-the-dependency-agent"></a>De afhankelijkheidsagent installeren

1. Als u de afhankelijkheids agent op een Windows-computer wilt installeren, dubbelklikt u op het installatie bestand en volgt u de wizard.
2. Als u de afhankelijkheids agent op een Linux-computer wilt installeren, installeert u als root met de volgende opdracht:

    ```sh InstallDependencyAgent-Linux64.bin```

- Meer informatie over de [ondersteuning van de afhankelijkheids agent](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) voor de Windows-en Linux-besturings systemen.
- Meer [informatie](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) over hoe u scripts kunt gebruiken om de afhankelijkheids agent te installeren.

>[!NOTE]
> Het Azure Monitor voor VM's artikel waarnaar wordt verwezen om een overzicht te geven van de systeem vereisten en-methoden voor het implementeren van de afhankelijkheids agent, zijn ook van toepassing op de Servicetoewijzing oplossing.

### <a name="create-a-group-with-dependency-mapping"></a>Een groep met afhankelijkheids toewijzing maken

1. Nadat u de agents hebt geïnstalleerd, gaat u naar de portal en klikt u op > **machines** **beheren** .
2. Zoek naar de computer waarop u de agents hebt geïnstalleerd.
3. De kolom **afhankelijkheden** voor de machine moet nu als **weergave afhankelijkheden**worden weer gegeven. Klik op de kolom om de afhankelijkheden van de machine weer te geven.
4. De afhankelijkheids toewijzing voor de machine bevat de volgende details:
    - Inkomende (clients) en uitgaande (servers) TCP-verbindingen van de computer
        - De afhankelijke computers waarop geen MMA en een afhankelijkheids agent is geïnstalleerd, worden gegroepeerd op poort nummers.
        - De afhankelijke computers waarop de MMA en de afhankelijkheids agent zijn geïnstalleerd, worden weer gegeven als afzonderlijke vakken.
    - Processen die worden uitgevoerd op de computer, kunt u elke computer vak uitvouwen om de processen weer te geven
    - Computer eigenschappen, met inbegrip van de FQDN, het besturings systeem, het MAC-adres worden weer gegeven. U kunt op elke machine klikken om details weer te geven.

4. U kunt afhankelijkheden voor verschillende tijds duren weer geven door te klikken op de tijds duur in het label tijds bereik. Het bereik is standaard een uur. U kunt het tijds bereik wijzigen of begin-en eind datums en duur opgeven.

   > [!NOTE]
   >    Een tijds bereik van Maxi maal een uur wordt ondersteund. Gebruik Azure Monitor Logboeken om de [afhankelijkheids gegevens](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) over een langere periode te vragen.

5. Nadat u de afhankelijke machines die u wilt groeperen, hebt geïdentificeerd, gebruikt u CTRL + klikken om meerdere computers op de kaart te selecteren en klikt u op **machines groeperen**.
6. Geef een groeps naam op. Controleer of de afhankelijke computers worden gedetecteerd door Azure Migrate.

    > [!NOTE]
    > Als een afhankelijke computer niet wordt gedetecteerd door Azure Migrate, kunt u deze niet toevoegen aan de groep. Als u dergelijke machines aan de groep wilt toevoegen, moet u het detectie proces opnieuw uitvoeren met het juiste bereik in vCenter Server en ervoor zorgen dat de computer wordt gedetecteerd door Azure Migrate.  

7. Als u een evaluatie voor deze groep wilt maken, schakelt u het selectie vakje in om een nieuwe evaluatie voor de groep te maken.
8. Klik op **OK** om de groep op te slaan.

Zodra de groep is gemaakt, wordt aanbevolen agents op alle machines van de groep te installeren en de groep te verfijnen door de afhankelijkheid van de hele groep te visualiseren.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Afhankelijkheids gegevens uit Azure Monitor logboeken opvragen

Afhankelijkheids gegevens die door Servicetoewijzing zijn vastgelegd, zijn beschikbaar voor het uitvoeren van query's in de werk ruimte Log Analytics die aan uw Azure Migrate-project is gekoppeld. Meer [informatie](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) over de servicetoewijzing gegevens tabellen om in azure monitor-logboeken te zoeken. 

De Kusto-query's uitvoeren:

1. Nadat u de agents hebt geïnstalleerd, gaat u naar de portal en klikt u op **overzicht**.
2. Ga in **overzicht**naar het gedeelte **essentiële** elementen van het project en klik op de werkruimte naam die wordt vermeld naast de **OMS-werk ruimte**.
3. Klik op de pagina Log Analytics werk ruimte op **algemene** > **Logboeken**.
4. Schrijf uw query om afhankelijkheids gegevens te verzamelen met behulp van Azure Monitor Logboeken. Zoek voorbeeld query's in de volgende sectie.
5. Voer de query uit door te klikken op uitvoeren. 

Meer [informatie](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) over het schrijven van Kusto-query's. 

### <a name="sample-azure-monitor-logs-queries"></a>Voorbeeld Azure Monitor logboeken query's

Hieronder vindt u voorbeeld query's die u kunt gebruiken om afhankelijkheids gegevens te extra heren. U kunt de query's aanpassen om uw voorkeurs gegevens punten uit te pakken. [Hier](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)vindt u een volledige lijst met de velden in afhankelijkheids gegevens records. Zoek [hier](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)meer voor beelden van query's.

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Binnenkomende verbindingen op een set machines samenvatten

De records in de tabel voor metrische verbindings gegevens, VMConnection, vertegenwoordigen geen afzonderlijke fysieke netwerk verbindingen. Meerdere fysieke netwerk verbindingen worden gegroepeerd in een logische verbinding. Meer [informatie](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) over hoe fysieke netwerk verbindings gegevens worden geaggregeerd in één logische record in VMConnection. 

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

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Een samen vatting van de hoeveelheid gegevens die wordt verzonden en ontvangen op binnenkomende verbindingen tussen een set machines

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
