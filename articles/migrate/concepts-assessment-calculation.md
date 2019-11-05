---
title: Evaluatie berekeningen in Azure Migrate | Microsoft Docs
description: Hierin wordt een overzicht gegeven van de evaluatie berekeningen in de Azure Migrate-service.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: hamusa
ms.openlocfilehash: d72e5a6dea8b411b6214e7749b8993f9f5a6e7a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466916"
---
# <a name="assessment-calculations-in-azure-migrate"></a>Evaluatie berekeningen in Azure Migrate

[Azure migrate](migrate-services-overview.md) biedt een centrale hub voor het bijhouden van de detectie, beoordeling en migratie van uw on-premises apps en workloads. Ook worden uw persoonlijke en open bare Cloud exemplaren in azure bijgehouden. De hub biedt Azure Migrate-hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke ISV-aanbiedingen (Independent Software Vendor) van derden.

Server evaluatie is een hulp programma in Azure Migrate dat on-premises servers evalueert voor migratie naar Azure. Dit artikel bevat informatie over hoe beoordelingen worden berekend.

## <a name="whats-in-an-assessment"></a>Waaruit bestaat een evaluatie?

**Eigenschap** | **Details**
--- | ---
**Doellocatie** | Hiermee geeft u de Azure-locatie op waarnaar u wilt migreren.<br/><br/>Server evaluatie ondersteunt momenteel deze doel regio's: Australië-oost, Australië-zuidoost, Brazilië-zuid, Canada-centraal, Canada-oost, Centraal-India, centraal VS, China-oost, China-noord, Azië-oost, VS-Oost, Oost-VS2, Duitsland-centraal, Duitsland-noordoost, Japan-Oost, Japan-West, Korea-centraal, Korea-zuid, Noord-Centraal VS, Europa-noord, Zuid-Centraal VS, Zuidoost-Azië, India-zuid, UK-zuid, UK-west, US Gov-Arizona, US Gov-Texas, US Gov-Virginia, VS-West-Centraal, Europa-west, West-India, West-VS en West-VS2.
**Opslagtype** | Hiermee geeft u het type schijven op dat u wilt gebruiken voor opslag in Azure. <br/><br/> Voor on-premises grootte kunt u het type van de doel opslag schijf opgeven als door Premium beheerd, Standard-SSD beheerd of Standard-HDD beheerd. Voor een schaal op basis van prestaties kunt u het type van de doel opslag schijf opgeven als automatische, door een Standard-HDD beheerd of Standard-SSD beheerd. Wanneer u het opslag type opgeeft als automatisch, wordt de aanbevolen schijf gebaseerd op de prestatie gegevens van de schijven: de invoer/uitvoer-bewerkingen per seconde (IOPS) en door voer. <br/><br/>Als u het opslag type opgeeft als Premium of standaard, wordt een schijf-SKU aanbevolen binnen het geselecteerde opslag type. Als u een VM-SLA met één exemplaar van 99,9% wilt uitvoeren, kunt u het opslag type opgeven als Premium-beheerde schijven. Dit zorgt ervoor dat alle schijven in de evaluatie worden aanbevolen als Premium-beheerde schijven. Houd er rekening mee dat Azure Migrate voor migratiebeoordeling alleen ondersteuning voor beheerde schijven biedt.
**Gereserveerde instanties (RIs)** | Met deze eigenschap kunt u [gereserveerde instanties](https://azure.microsoft.com/pricing/reserved-vm-instances/) in azure opgeven. Kosten ramingen in de beoordeling nemen vervolgens RI-kortingen in rekening. RIs wordt momenteel alleen ondersteund voor aanbiedingen met betalen naar gebruik in Azure Migrate.
**Grootte criteria** | Hiermee stelt u de criteria in die moeten worden gebruikt voor de *juiste grootte* van Vm's voor Azure. U kunt ervoor kiezen om op *prestaties gebaseerde* grootte te wijzigen of de virtuele machines *als on-premises* te schalen zonder dat u rekening houdt met de prestatie geschiedenis.
**Prestatiegeschiedenis** | Hiermee stelt u de duur in waarmee rekening moet worden gehouden bij het evalueren van de prestatie gegevens van machines. Deze eigenschap is alleen van toepassing wanneer de grootte criteria *op basis van prestaties*zijn.
**Percentiel gebruik** | Hiermee geeft u de percentiel waarde van de voorbereidings Voorbeeldset op die in aanmerking komt voor de juiste grootte. Deze eigenschap is alleen van toepassing wanneer de grootte van het formaat op basis van prestaties is gebaseerd.
**VM-reeks** | Hiermee kunt u de VM-reeks opgeven die u wilt overwegen voor de juiste grootte. Als u bijvoorbeeld een productie omgeving hebt die u niet van plan bent om te migreren naar A-serie Vm's in azure, kunt u een-serie uitsluiten van de lijst of reeks en wordt de grootte van de rechter kant alleen in de geselecteerde reeks uitgevoerd.
**Comfortfactor** | Bij het evalueren van Azure Migrate-server wordt een buffer (comfort factor) in rekening gehouden tijdens de evaluatie. Deze buffer wordt toegepast boven op de gegevens over machinegebruik voor VM's (CPU, geheugen, schijf en netwerk). De comfortfactor houdt rekening met factoren zoals seizoensgebonden gebruik, een korte prestatiegeschiedenis en een mogelijke gebruikstoename in de toekomst.<br/><br/> Een VM met 10 kernen en een gebruik van 20% komt bijvoorbeeld gewoonlijk overeen met een VM met 2 kernen. Met een comfortfactor van 2,0x is het resultaat echter een VM met 4 kernen.
**Aanbieding** | Hier wordt de [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) weer gegeven die u hebt Inge schreven. Azure Migrate maakt dienovereenkomstig een schatting van de kosten.
**Valuta** | Hier wordt de facturerings valuta voor uw account weer gegeven.
**Korting (%)** | Een lijst met alle abonnements kortingen die boven op de Azure-aanbieding worden weer gegeven. De standaardinstelling is 0%.
**VM tijd actief** | Als uw Vm's niet 24 uur per dag, 7 dagen per week in Azure worden uitgevoerd, kunt u de duur (het aantal dagen per maand en het aantal uren van de dag) opgeven waarvoor ze worden uitgevoerd, en worden de kosten ramingen dienovereenkomstig afgehandeld. De standaardwaarde is 31 dagen per maand en 24 uur per dag.
**Azure Hybrid Benefit** | Hiermee geeft u op of u Software Assurance hebt en in aanmerking komt voor [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Als deze optie is ingesteld op Ja, worden niet-Windows Azure-prijzen voor Windows-VM's gehanteerd. De standaard instelling is Ja.

## <a name="how-are-assessments-calculated"></a>Hoe worden evaluaties berekend?

Een evaluatie van Azure Migrate server beoordeling wordt berekend met behulp van de meta gegevens die zijn verzameld over de on-premises servers. Als de detectie bron een import is met behulp van een. CSV-bestand, de evaluatie wordt berekend met behulp van de meta gegevens die door de gebruiker zijn verschaft over de servers. De evaluatie berekening wordt in drie fasen verwerkt. Voor elke server begint de evaluatie berekening met een Azure-geschiktheids analyse, gevolgd door de grootte, en ten slotte, een schatting van de maandelijkse kosten. Een server wordt in een latere fase alleen verplaatst als deze de voor gaande wordt door gegeven. Als een server bijvoorbeeld de Azure-geschiktheids controle uitschakelt, wordt deze gemarkeerd als niet geschikt voor Azure, en wordt de grootte en de kosten voor de server niet aangepast.

## <a name="azure-suitability-analysis"></a>Azure-geschiktheids analyse

Niet alle machines zijn geschikt om te worden uitgevoerd in Azure. Server evaluatie evalueert elke on-premises machine voor de geschiktheid voor Azure-migratie. Er wordt ook elke geraamde machine toegewezen aan een van de volgende geschiktheids Categorieën:
- **Gereed voor Azure**: de machine kan zonder wijzigingen worden gemigreerd naar Azure. Het wordt gestart in azure met volledige ondersteuning voor Azure.
- **Voorwaardelijk gereed voor Azure**: de machine wordt mogelijk gestart in azure, maar heeft mogelijk geen volledige ondersteuning voor Azure. Bijvoorbeeld: een machine waarop een oudere versie van Windows Server wordt uitgevoerd, wordt niet ondersteund in Azure. U moet voorzichtig zijn voordat u deze machines naar Azure migreert en de richt lijnen voor herstel in de beoordeling volgen om de gereedheids problemen op te lossen.
- **Niet gereed voor Azure**: de computer kan niet worden gestart in Azure. Als een on-premises computer bijvoorbeeld een schijf bevat van meer dan 64 terabytes (TB) die eraan is gekoppeld, kan deze niet worden gehost op Azure. U moet de richt lijnen voor herstel in de evaluatie volgen om het gereedheids probleem op te lossen voordat u de machine naar Azure migreert. Voor computers die zijn gemarkeerd als niet gereed voor Azure, wordt er geen schatting van de juiste grootte en kosten in rekening gebracht.
- **Gereedheid onbekend**: Azure migrate kan de gereedheid van de machine niet bepalen vanwege onvoldoende meta gegevens die zijn verzameld uit de on-premises omgeving.

Server evaluatie controleert de computer eigenschappen en het gast besturingssysteem om de Azure-gereedheid van de on-premises machine te bepalen.

### <a name="machine-properties"></a>Computer eigenschappen

Server beoordeling controleert de volgende eigenschappen van de on-premises virtuele machine om te bepalen of deze kan worden uitgevoerd op Azure.

**Eigenschap** | **Details** | **Status van Azure-gereedheid**
--- | --- | ---
**Opstart type** | Azure ondersteunt Vm's met een opstart type BIOS, niet voor UEFI. | Voorwaardelijk gereed als het opstart type UEFI is.
**Kernen** | Het aantal kernen op de computers moet gelijk zijn aan of kleiner zijn dan het maximum aantal kernen (128) dat wordt ondersteund voor een virtuele machine van Azure.<br/><br/> Als er een prestatie geschiedenis beschikbaar is, worden de gebruikte kernen Azure Migrate beschouwd als vergelijking. Als er een comfort factor is opgegeven in de evaluatie-instellingen, wordt het aantal gebruikte kern geheugens vermenigvuldigd met de comfort factor.<br/><br/> Als er geen prestatie geschiedenis is, gebruikt Azure Migrate de toegewezen kernen zonder de comfort factor toe te passen. | Gereed als deze kleiner dan of gelijk aan de limieten zijn.
**Geheugenmetabase** | De grootte van het computer geheugen moet gelijk zijn aan of kleiner zijn dan het maximum geheugen (3892 gigabytes [GB] op Azure M-serie Standard_M128m&nbsp;<sup>2</sup>) dat is toegestaan voor een Azure-VM. [Meer informatie](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Als er een prestatie geschiedenis beschikbaar is, wordt het gebruikte geheugen in Azure Migrate beschouwd als vergelijking. Als er een comfort factor is opgegeven, wordt het gebruikte geheugen vermenigvuldigd met de comfort factor.<br/><br/> Als er geen geschiedenis is, wordt het toegewezen geheugen gebruikt zonder de comfort factor toe te passen.<br/><br/> | Gereed indien binnen de limieten.
**Opslag schijf** | De toegewezen grootte van een schijf moet 32 TB of minder zijn. Hoewel Azure ondersteuning biedt voor 64 TB schijven met Ultra-SSD schijven, Azure Migrate: met server evaluatie wordt momenteel gecontroleerd op 32 TB als de schijf grootte limieten, omdat deze nog niet Ultra-SSD worden ondersteund. <br/><br/> Het aantal schijven dat is gekoppeld aan de computer, moet 65 of minder zijn, inclusief de besturingssysteem schijf. | Gereed indien binnen de limieten.
**Netwerken** | Aan een machine moeten 32 of minder netwerk interfaces (Nic's) zijn gekoppeld. | Gereed indien binnen de limieten.

### <a name="guest-operating-system"></a>Gast besturingssysteem
Naast de VM-eigenschappen controleert de server evaluatie op het gast besturingssysteem van de computers om te bepalen of het op Azure kan worden uitgevoerd.

> [!NOTE]
> Voor virtuele VMware-machines gebruikt server assessment het besturings systeem dat is opgegeven voor de virtuele machine in vCenter Server voor het afhandelen van de analyse van het gast besturingssysteem. Voor Linux-Vm's die worden uitgevoerd op VMware, wordt momenteel niet de exacte kernel-versie van het gast besturingssysteem geïdentificeerd.

De volgende logica wordt gebruikt door server evaluatie om de Azure-gereedheid op basis van het besturings systeem te identificeren.

**Besturingssysteem** | **Details** | **Status van Azure-gereedheid**
--- | --- | ---
Windows Server 2016 & alle SPs | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2012 R2 & alle SPs | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2012 & alle SPs | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2008 R2 met alle SPs | Azure biedt volledige ondersteuning.| Gereed voor Azure
Windows Server 2008 (32-bits en 64 bits) | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2003, 2003 R2 | Deze besturings systemen hebben hun end-of-support-datum door lopen en hebben een [aangepaste ondersteunings overeenkomst (CSA)](https://aka.ms/WSosstatement) nodig voor ondersteuning in Azure. | Voorwaardelijk gereed voor Azure. Overweeg het besturings systeem te upgraden voordat u naar Azure migreert.
Windows 2000, 98, 95, NT, 3,1, MS-DOS | Deze besturings systemen hebben hun end-of-support-datum door gegeven. De machine kan worden gestart in azure, maar Azure biedt geen ondersteuning voor het besturings systeem. | Voorwaardelijk gereed voor Azure. Het is raadzaam om het besturings systeem bij te werken voordat u naar Azure migreert.
Windows-client 7, 8 en 10 | Azure biedt alleen ondersteuning voor een [abonnement op Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Voorwaardelijk gereed voor Azure
Windows 10 Pro Desktop | Azure biedt ondersteuning voor [multi tenant-hosting rechten.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Voorwaardelijk gereed voor Azure
Windows Vista, XP Professional | Deze besturings systemen hebben hun end-of-support-datum door gegeven. De machine kan worden gestart in azure, maar Azure biedt geen ondersteuning voor het besturings systeem. | Voorwaardelijk gereed voor Azure. Het is raadzaam om het besturings systeem bij te werken voordat u naar Azure migreert.
Linux | Azure bevestigt deze [Linux-besturings systemen](../virtual-machines/linux/endorsed-distros.md). Andere Linux-besturings systemen kunnen worden gestart in azure, maar we raden u aan het besturings systeem bij te werken naar een officiële versie voordat u naar Azure migreert. | Gereed voor Azure als de versie wordt goedgekeurd.<br/><br/>Voorwaardelijk gereed als de versie niet wordt goedgekeurd.
Andere besturings systemen<br/><br/> Bijvoorbeeld Oracle Solaris, Apple Mac OS etc., FreeBSD, enzovoort. | Deze besturings systemen worden niet door Azure goedgekeurd. De machine kan worden gestart in azure, maar Azure biedt geen ondersteuning voor het besturings systeem. | Voorwaardelijk gereed voor Azure. U wordt aangeraden een ondersteund besturings systeem te installeren voordat u naar Azure migreert.  
Besturings systeem opgegeven als een **andere** in vCenter Server | Azure Migrate kan het besturings systeem in dit geval niet identificeren. | Onbekende gereedheid. Zorg ervoor dat het besturings systeem dat wordt uitgevoerd in de virtuele machine wordt ondersteund in Azure.
32-bits besturings systemen | De machine kan worden gestart in azure, maar Azure biedt mogelijk geen volledige ondersteuning. | Voorwaardelijk gereed voor Azure. Overweeg het besturings systeem van de machine te upgraden van 32-bits besturings systeem naar 64-bits besturings systeem voordat u naar Azure migreert.

## <a name="sizing"></a>Grootte aanpassen

Nadat een computer is gemarkeerd als gereed voor Azure, maakt de server evaluatie aanbeveling aan het bepalen van de juiste Azure VM en schijf SKU voor de on-premises VM. Deze aanbevelingen variëren, afhankelijk van de opgegeven evaluatie-eigenschappen.

- Als de evaluatie gebruikmaakt van *grootte op basis van prestaties*, beschouwt Azure migrate de prestatie geschiedenis van de machine om de VM-grootte en het schijf type in azure te identificeren. In het geval van servers met detectie bron als import, worden de waarden voor het prestatie gebruik opgegeven door de gebruiker beschouwd. Deze methode is vooral nuttig als u de on-premises VM hebt toegewezen, maar het gebruik is laag en u de grootte van de virtuele machine in azure op de juiste manier wilt besparen. Deze methode helpt u bij het optimaliseren van de grootten tijdens de migratie.
- Als u de prestatie gegevens voor de VM-grootte niet wilt overwegen en de on-premises machines wilt uitvoeren als-is naar Azure, kunt u de grootte criteria instellen op *on-premises*. Vervolgens wordt de grootte van de virtuele machines gebaseerd op de on-premises configuratie zonder rekening te houden met de gebruiks gegevens. In dit geval zijn de activiteiten voor schijf grootte gebaseerd op het opslag type dat u opgeeft in de eigenschappen van de beoordeling (Standard-HDD, Standard-SSD of Premium-schijven).

### <a name="performance-based-sizing"></a>Grootte op basis van prestaties

Voor een hoge grootte op basis van prestaties begint de server analyse met de schijven die zijn gekoppeld aan de virtuele machine, gevolgd door netwerk adapters. Vervolgens wordt een Azure VM-SKU toegewezen op basis van de reken vereisten van de on-premises VM. De Azure Migrate apparaat profielen de on-premises omgeving voor het verzamelen van prestatie gegevens voor CPU, geheugen, schijven en netwerk adapters.

**Stappen voor het verzamelen van prestatie gegevens:**

1. Voor virtuele VMware-machines verzamelt het Azure Migrate-apparaat een realtime-steekproef punt bij elke 20-seconden interval. Voor virtuele Hyper-V-machines wordt het realtime-voorbeeld punt verzameld bij elk interval van dertig seconden. Voor fysieke servers wordt het realtime-voorbeeld punt verzameld bij elke periode van vijf minuten. 
2. Het apparaat rolt de sample punten die elke 10 minuten worden verzameld samen en verzendt de maximum waarde voor de laatste tien minuten naar server evaluatie. 
3. In de server beoordeling worden de voorbeeld punten van 10 minuten voor de afgelopen maand opgeslagen. Afhankelijk van de evaluatie-eigenschappen die zijn opgegeven voor de *prestatie geschiedenis* en het *percentiel gebruik*, wordt het juiste gegevens punt aangegeven dat moet worden gebruikt voor de juiste grootte. Als de prestatie geschiedenis bijvoorbeeld is ingesteld op 1 dag en het percentiel gebruik het 95e percentiel is, gebruikt server assessment de 10-minuten steek proeven voor de afgelopen dag, worden deze in oplopende volg orde gesorteerd en wordt de waarde van het 95e percentiel voor rechts formaat gekozen. 
4. Deze waarde wordt vermenigvuldigd met de comfort factor om de effectief prestatie gebruiks gegevens te verkrijgen voor elke metriek (CPU-gebruik, geheugen gebruik, schijf-IOPS (lezen en schrijven), schijf doorvoer (lezen en schrijven) en netwerk doorvoer (in en uit) die de apparaat verzamelt.

Nadat de waarde voor effectief gebruik is vastgesteld, worden de opslag, het netwerk en de computer grootte als volgt afgehandeld.

> [!NOTE]
> Voor servers die zijn toegevoegd via importeren, wordt de prestatie gegevens die door de gebruiker worden verschaft, rechtstreeks gebruikt voor de juiste grootte.

**Opslag grootte**: Azure migrate probeert elke schijf die aan de computer is gekoppeld, toe te wijzen aan een schijf in Azure.

> [!NOTE]
> Azure Migrate server Assessment ondersteunt alleen beheerde schijven voor evaluatie.

  - De server analyse voegt de IOPS voor lezen en schrijven van een schijf toe om het totale aantal IOPS dat is vereist te verkrijgen. Op dezelfde manier worden de doorvoer-en schrijf waarden toegevoegd om de totale door Voer van elke schijf te verkrijgen.
  - Als u opslag type hebt opgegeven als automatisch, op basis van de ingangs waarden voor IOPS en door Voer, bepaalt de server bepaling of de schijf moet worden toegewezen aan een standaard HDD, een standaard SSD of een Premium-schijf in Azure. Als het opslag type is ingesteld op Standard-HDD/SSD/Premium, probeert de server evaluatie een schijf-SKU te vinden binnen het geselecteerde opslag type (Standard-HDD/SSD/Premium-schijven).
  - Als met de server bepaling geen schijf met de vereiste IOPS en door Voer wordt gevonden, wordt de machine als niet geschikt voor Azure gemarkeerd.
  - Als server evaluatie een set geschikte schijven vindt, worden de schijven geselecteerd die ondersteuning bieden voor de locatie die is opgegeven in de instellingen voor evaluatie.
  - Als er meerdere in aanmerking komende schijven zijn, selecteert de server bepaling de schijf met de laagste kosten.
  - Als de prestatie gegevens voor schijven niet beschikbaar zijn, worden de configuratie gegevens van de schijf (schijf grootte) gebruikt om een standaard SSD-schijf in azure te vinden.

**Netwerk grootte**: Server beoordeling probeert een virtuele machine van Azure te vinden die het aantal netwerk adapters kan ondersteunen dat is gekoppeld aan de on-premises machine en de prestaties die zijn vereist voor deze netwerk adapters.
- Om de effectief netwerk prestaties van de on-premises virtuele machine te verkrijgen, worden de gegevens die per seconde (MBps) van de computer (netwerk uitgaan) door de server bepaling geaggregeerd op alle netwerk adapters en wordt de comfort factor toegepast. Dit nummer wordt gebruikt om een virtuele machine van Azure te zoeken die de vereiste netwerk prestaties kan ondersteunen.
- Naast de netwerk prestaties is server evaluatie ook van oordeel of de virtuele machine van Azure het vereiste aantal netwerk adapters kan ondersteunen.
- Als er geen netwerk prestatie gegevens beschikbaar zijn, is voor de server evaluatie alleen het aantal netwerk adapters voor de VM-grootte in rekening gebracht.

> [!NOTE]
> Het opgeven van het aantal netwerk adapters wordt momenteel niet ondersteund voor geïmporteerde servers

**Berekenings grootte**: nadat de opslag-en netwerk vereisten zijn berekend, worden de vereisten voor de CPU en het geheugen van de server beoordeeld beschouwd voor een geschikte VM-grootte in Azure.
- Azure Migrate zoekt naar de effectief gebruikte kernen en het geheugen om een geschikte VM-grootte in azure te vinden.
- Als er geen geschikte grootte wordt gevonden, wordt de computer gemarkeerd als niet geschikt voor Azure.
- Als er een geschikte grootte wordt gevonden, past Azure Migrate de opslag-en netwerk berekeningen toe. Vervolgens worden de instellingen voor locatie en prijs categorie toegepast voor de laatste aanbeveling van de VM-grootte.
- Als er meer Azure VM-grootten in aanmerking komen, wordt de grootte met de laagste kosten aanbevolen.

### <a name="as-on-premises-sizing"></a>Als een on-premises grootte

Als u gebruikmaakt *van on-premises grootte*, wordt de prestatie geschiedenis van de vm's en schijven niet door de server bepaling beschouwd. In plaats daarvan wordt er een VM-SKU in azure toegewezen op basis van de grootte van de on-premises lokale. Net als bij schijf grootte controleert server assessment het opslag type dat is opgegeven in de eigenschappen van de beoordeling (Standard-HDD/SSD/Premium) en wordt het type van de schijf dienovereenkomstig aanbevolen. Het standaard opslag type is Premium-schijven.

## <a name="confidence-ratings"></a>Betrouwbaarheids classificaties
Elke evaluatie op basis van prestaties in Azure Migrate is gekoppeld aan een betrouwbaarheids classificatie die van één (laagste) tot vijf sterren (hoogst) ligt.
- De betrouwbaarheidsclassificatie wordt aan een evaluatie toegewezen op basis van de beschikbaarheid van de gegevenspunten die nodig zijn om de evaluatie te berekenen.
- De betrouwbaarheidsclassificatie van een evaluatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte die Azure Migrate geeft.
- Betrouwbaarheids classificaties zijn niet van toepassing *op de on-premises* evaluaties.
- Voor een hoge grootte op basis van prestaties moet de server beoordeling:
    - De gebruiks gegevens voor de CPU en het geheugen van de virtuele machine.
    - De schijf-IOPS en doorvoer gegevens voor elke schijf die aan de VM is gekoppeld.
    - De netwerk-I/O voor het afhandelen van de grootte van de prestaties voor elke netwerk adapter die is gekoppeld aan een virtuele machine.

   Als een van deze gebruiks nummers niet beschikbaar is in vCenter Server, is de aanbeveling voor de grootte mogelijk niet betrouwbaar.

Afhankelijk van het percentage beschik bare gegevens punten, gaat de betrouwbaarheids classificatie voor de evaluatie als volgt.

   **Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
   --- | ---
   0-20% | 1 ster
   21-40% | 2 sterren
   41-60% | 3 sterren
   61-80% | 4 sterren
   81-100% | 5 sterren

> [!NOTE]
> Vertrouwens classificaties worden niet toegewezen aan beoordelingen van servers die worden geïmporteerd met. CSV-bestand in Azure Migrate. 

### <a name="low-confidence-ratings"></a>Classificaties met lage betrouw baarheid

Hier volgen enkele redenen waarom een evaluatie een lage betrouwbaarheids classificatie kan krijgen:

- U hebt uw omgeving niet in het profiel voor de duur waarvoor u de evaluatie maakt. Als u bijvoorbeeld de beoordeling met de prestatie duur hebt ingesteld op één dag, moet u wachten tot minstens een dag nadat u de detectie hebt gestart voor alle gegevens punten die u wilt verzamelen.
- Sommige Vm's zijn afgesloten tijdens de periode waarvoor de evaluatie is berekend. Als een virtuele machine gedurende enige tijd is uitgeschakeld, kan de server bepaling de prestatie gegevens voor die periode niet verzamelen.
- Sommige Vm's zijn gemaakt tijdens de periode waarvoor de evaluatie is berekend. Als u bijvoorbeeld een evaluatie hebt gemaakt voor de prestatie geschiedenis van de afgelopen maand, maar sommige virtuele machines in de omgeving slechts een week geleden zijn gemaakt, bestaat de prestatie geschiedenis van de nieuwe Vm's niet voor de volledige duur.

> [!NOTE]
> Als de betrouwbaarheids classificatie van een evaluatie van minder dan vijf sterren is, raden we u aan om ten minste een dag te wachten op het apparaat om de omgeving in te stellen en de evaluatie vervolgens opnieuw te berekenen. Als dat niet het geval is, is de grootte van de prestaties mogelijk niet betrouwbaar. In dat geval raden wij u aan de evaluatie over te scha kelen naar de on-premises grootte.

## <a name="monthly-cost-estimation"></a>Schatting maandelijkse kosten

Nadat de aanbevelingen voor de grootte zijn voltooid, berekent Azure Migrate reken-en opslag kosten voor na de migratie.

- **Reken kosten**: met de aanbevolen Azure VM-grootte maakt Azure migrate gebruik van de facturerings-API voor het berekenen van de maandelijkse kosten voor de virtuele machine.
    - Met de berekening worden het besturings systeem, Software Assurance, gereserveerde instanties, VM-uptime, locatie en valuta-instellingen in rekening gebracht.
    - Hiermee worden de kosten op alle machines geaggregeerd om de totale maandelijkse reken kosten te berekenen.
- **Opslag kosten**: de maandelijkse opslag kosten voor een machine worden berekend door het samen voegen van de maandelijkse kosten van alle schijven die aan de machine zijn gekoppeld, als volgt:
    - Server beoordeling berekent de totale maandelijkse opslag kosten door het samen voegen van de opslag kosten van alle machines.
    - Op dit moment worden voor de berekening geen aanbiedingen in de evaluatie-instellingen beschouwd.

De kosten worden weer gegeven in de valuta die is opgegeven in de instellingen voor evaluatie.


## <a name="next-steps"></a>Volgende stappen

Een evaluatie maken voor virtuele [VMware-machines](tutorial-assess-vmware.md) of [virtuele Hyper-V-machines](tutorial-assess-hyper-v.md).
