---
title: Evaluaties in Azure Migrate
description: Meer informatie over evaluaties in Azure Migrate.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a8912263432bc0e9cd7172c4b6c9b118132863d3
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029115"
---
# <a name="about-assessments-in-azure-migrate"></a>Over evaluaties in Azure Migrate

In dit artikel wordt beschreven hoe beoordelingen worden berekend in [Azure migrate: Server evaluatie](migrate-services-overview.md#azure-migrate-server-assessment-tool). U voert evaluaties uit op groepen on-premises machines om te bepalen of ze gereed zijn voor migratie naar Azure Migrate.

## <a name="how-do-i-run-an-assessment"></a>Hoe kan ik een evaluatie uit te voeren?
U kunt een evaluatie uitvoeren met behulp van Azure Migrate: Server evaluatie of een ander hulp programma van Azure of derden. Nadat u een Azure Migrate project hebt gemaakt, voegt u het hulp programma toe dat u nodig hebt. [Meer informatie] (how-to-add-tool-first-time.md

### <a name="collect-compute-data"></a>Reken gegevens verzamelen

Prestatie gegevens voor reken instellingen worden als volgt verzameld:

1. Het [Azure migrate apparaat](migrate-appliance.md) verzamelt een real-time voorbeeld punt:

    - **VMware-vm's**: voor virtuele VMware-machines verzamelt het Azure migrate apparaat een realtime-steekproef punt bij elke 20-seconden interval.
    - **Virtuele Hyper-v-machines**: voor virtuele Hyper-v-machines wordt het realtime-voorbeeld punt verzameld bij elk interval van dertig seconden.
    - **Fysieke servers**: voor fysieke servers wordt het realtime-voorbeeld punt verzameld tijdens elke interval van vijf minuten. 
    
2. Het apparaat rolt de voorbeeld punten (20 seconden, 30 seconden, vijf minuten) samen om één gegevens punt om de 10 minuten te maken. Als u het ene gegevens punt wilt maken, selecteert het apparaat de piek waarde van alle voor beelden en verzendt deze vervolgens naar Azure.
3. In de server beoordeling worden de voorbeeld punten van 10 minuten voor de afgelopen maand opgeslagen.
4. Wanneer u een evaluatie maakt, identificeert server assessment het juiste gegevens punt dat moet worden gebruikt voor de juiste grootte, op basis van de percentiel waarden voor de *prestatie geschiedenis* en het *percentiel gebruik*.

    - Als de prestatie geschiedenis bijvoorbeeld is ingesteld op één week en het percentiel gebruik het 95e percentiel is, sorteert server beoordeling de 10-minuten steekproef punten voor de afgelopen week in oplopende volg orde en wordt de waarde van het 95e percentiel voor de juiste grootte gepickt. 
    - De 95e percentiel waarde zorgt ervoor dat u alle uitschieters negeert, die mogelijk worden opgenomen als u het 99e percentiel selecteert.
    - Als u het piek gebruik voor de periode wilt kiezen en u geen uitschieters wilt missen, selecteert u het 99e percentiel voor percentiel gebruik.

5. Deze waarde wordt vermenigvuldigd met de comfort factor om de effectief prestatie gebruiks gegevens te verkrijgen voor elke metriek (CPU-gebruik, geheugen gebruik, schijf-IOPS (lezen en schrijven), schijf doorvoer (lezen en schrijven) en netwerk doorvoer (in en uit) die de apparaat verzamelt.

Voor het uitvoeren van analyses in Server evaluatie, bereid u voor op on-premises en in azure, en stelt u het Azure Migrate apparaat in om voortdurend on-premises machines te detecteren. Nadat de computers zijn gedetecteerd, verzamelt u deze in groepen om ze te beoordelen. Voor gedetailleerde en hoge betrouw baarheid kunt u afhankelijkheden tussen computers visualiseren en toewijzen, om erachter te gaan hoe u ze migreert.

- Meer informatie over het uitvoeren van evaluaties voor [virtuele VMware-machines](tutorial-prepare-vmware.md), [virtuele Hyper-V-machines](tutorial-prepare-hyper-v.md)en [fysieke servers](tutorial-prepare-physical.md).
- Meer informatie over het beoordelen van servers [die zijn geïmporteerd met een CSV-bestand](tutorial-assess-import.md).
- Meer informatie over het instellen van de [visualisatie van afhankelijkheden](concepts-dependency-visualization.md).

## <a name="assessments-in-server-assessment"></a>Evaluaties in Server evaluatie 

Evaluaties die u maakt met Azure Migrate server-evaluatie zijn een tijdgebonden moment opname van gegevens. Het hulp programma Server evaluatie biedt twee soorten evaluaties.

**Beoordelings type** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Beoordelingen die aanbevelingen doen op basis van verzamelde prestatie gegevens | Aanbeveling voor VM-grootte is gebaseerd op gegevens van CPU en geheugen gebruik.<br/><br/> De aanbeveling van het schijf type (standaard HDD/SSD of Premium-Managed disks) is gebaseerd op de IOPS en door Voer van de on-premises schijven.
**As-is on-premises** | Evaluaties die geen gebruik maken van prestatie gegevens voor aanbevelingen. | Aanbeveling voor VM-grootte is gebaseerd op de on-premises VM-grootte<br/><br> Het aanbevolen schijf type is gebaseerd op het geselecteerde opslag type voor de evaluatie.

## <a name="collecting-performance-data"></a>Prestatie gegevens verzamelen

Prestatie gegevens worden als volgt verzameld:

1. Het [Azure migrate apparaat](migrate-appliance.md) verzamelt een real-time voorbeeld punt:

    - **VMware-vm's**: voor virtuele VMware-machines verzamelt het Azure migrate apparaat een realtime-steekproef punt bij elke 20-seconden interval.
    - **Virtuele Hyper-v-machines**: voor virtuele Hyper-v-machines wordt het realtime-voorbeeld punt verzameld bij elk interval van dertig seconden.
    - **Fysieke servers**: voor fysieke servers wordt het realtime-voorbeeld punt verzameld tijdens elke interval van vijf minuten. 
    
2. Het apparaat rolt de voorbeeld punten (20 seconden, 30 seconden, vijf minuten) samen om één gegevens punt om de 10 minuten te maken. Als u het ene gegevens punt wilt maken, selecteert het apparaat de piek waarde van alle voor beelden en verzendt deze vervolgens naar Azure.
3. In de server beoordeling worden de voorbeeld punten van 10 minuten voor de afgelopen maand opgeslagen.
4. Wanneer u een evaluatie maakt, identificeert server assessment het juiste gegevens punt dat moet worden gebruikt voor de juiste grootte, op basis van de percentiel waarden voor de *prestatie geschiedenis* en het *percentiel gebruik*.

    - Als de prestatie geschiedenis bijvoorbeeld is ingesteld op één week en het percentiel gebruik het 95e percentiel is, sorteert server beoordeling de 10-minuten steekproef punten voor de afgelopen week in oplopende volg orde en wordt de waarde van het 95e percentiel voor de juiste grootte gepickt. 
    - De 95e percentiel waarde zorgt ervoor dat u alle uitschieters negeert, die mogelijk worden opgenomen als u het 99e percentiel selecteert.
    - Als u het piek gebruik voor de periode wilt kiezen en u geen uitschieters wilt missen, selecteert u het 99e percentiel voor percentiel gebruik.

5. Deze waarde wordt vermenigvuldigd met de comfort factor om de effectief prestatie gebruiks gegevens te verkrijgen voor elke metriek (CPU-gebruik, geheugen gebruik, schijf-IOPS (lezen en schrijven), schijf doorvoer (lezen en schrijven) en netwerk doorvoer (in en uit) die de apparaat verzamelt.
## <a name="whats-in-an-assessment"></a>Waaruit bestaat een evaluatie?

Hier volgt een beoordeling in Azure Migrate: Server Assessment.

**Eigenschap** | **Details**
--- | ---
**Doellocatie** | De locatie waarnaar u wilt migreren. Server analyse ondersteunt momenteel deze Azure-doel regio's:<br/><br/> Australië-oost, Australië-zuidoost, Brazilië-zuid, Canada-centraal, Canada-oost, Centraal-India, centraal VS, China-oost, China-noord, Azië-oost, VS-Oost, Oost-VS2, Duitsland-centraal, Duitsland-noordoost, Japan-Oost, Japan-West, Korea-centraal, Korea-zuid, Noord VS-centraal, Europa-noord, Zuid-Centraal VS, Zuidoost-Azië, India-zuid, UK-zuid, UK-west, US Gov-Arizona, US Gov-Texas, US Gov-Virginia, VS-West-Centraal, Europa-west, West-India, westelijke VS en West-VS2.
*Doel opslag schijf (in grootte)* * | Het type schijven dat moet worden gebruikt voor opslag in Azure. <br/><br/> Geef de doel opslag schijf op als Premium beheerd, Standard SSD beheerd of standaard HDD beheerd.
**Doel opslag schijf (op basis van de grootte van de prestaties)** | Geef het type doel opslag schijf op als automatische, Premium beheerde, standaard HDD beheerd of standaard SSD beheerd.<br/><br/> **Automatisch**: de aanbevolen schijf is gebaseerd op de prestatie gegevens van de schijven (de invoer/uitvoer-bewerkingen per seconde (IOPS) en door Voer).<br/><br/>**Premium/Standard**: de evaluatie beveelt een schijf-SKU aan binnen het geselecteerde opslag type.<br/><br/> Als u een VM-SLA met één exemplaar van 99,9% wilt maken, overweeg dan om Premium Managed disks te gebruiken. Dit zorgt ervoor dat alle schijven in de evaluatie worden aanbevolen als Premium-beheerde schijven.<br/><br/> Azure Migrate biedt voor migratiebeoordeling alleen ondersteuning voor beheerde schijven.
**Gereserveerde instanties (RIs)** | Geef [gereserveerde instanties](https://azure.microsoft.com/pricing/reserved-vm-instances/) op in azure, zodat de kosten ramingen in de evaluatie van de RI-kortingen in rekening worden gebracht.<br/><br/> RIs wordt momenteel alleen ondersteund voor aanbiedingen met betalen naar gebruik in Azure Migrate.
**Grootte criteria** | Wordt gebruikt om de grootte van de virtuele machine in azure naar rechts te wijzigen.<br/><br/> Gebruiken als de grootte van het formaat of op basis van de prestaties.
**Prestatiegeschiedenis** | Wordt gebruikt met een grootte op basis van prestaties. Geef de duur op die wordt gebruikt bij het evalueren van prestatie gegevens.
**Percentiel gebruik** | Wordt gebruikt met een grootte op basis van prestaties. Hiermee geeft u de percentiel waarde van het voor beeld van de prestaties op die moet worden gebruikt voor de juiste grootte. 
**VM-reeks** | Geef de Azure VM-serie op die u wilt overwegen voor een juiste grootte. Als u bijvoorbeeld geen productie omgeving hebt die virtuele machines van de A-serie nodig heeft in azure, kunt u een-serie uitsluiten van de lijst of reeks.
**Comfortfactor** | Buffer die wordt gebruikt tijdens de evaluatie. Wordt toegepast boven op de machine gebruiks gegevens voor Vm's (CPU, geheugen, schijf en netwerk). IT-accounts voor problemen zoals seizoen gebruik, korte prestatie geschiedenis en waarschijnlijke toename van toekomstig gebruik.<br/><br/> Zo resulteert een virtuele machine met 10 kern met 20% gebruik doorgaans in een virtuele machine met twee kernen. Met een comfort factor van 2,0 x is het resultaat een virtuele machine met vier kernen.
**Aanbieding** | Hier wordt de [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) weer gegeven waarin u bent Inge schreven. De server beoordeling schat de kosten dienovereenkomstig.
**Valuta** | De facturerings valuta voor uw account.
**Korting (%)** | Een lijst met alle abonnements kortingen die boven op de Azure-aanbieding worden weer gegeven. De standaardinstelling is 0%.
**VM tijd actief** | Als Azure-Vm's niet 24 uur per dag, 7 dagen per week worden uitgevoerd, kunt u de duur (dagen per maand en uur per dag) opgeven. deze worden uitgevoerd. Kosten ramingen worden dienovereenkomstig afgehandeld.<br/><br/> De standaardwaarde is 31 dagen per maand en 24 uur per dag.
**Azure Hybrid Benefit** | Hiermee geeft u op of u Software Assurance hebt en in aanmerking komt voor [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Als deze optie is ingesteld op Ja (de standaard instelling), worden niet-Windows Azure-prijzen in rekening gehouden voor Windows-Vm's.

[Bekijk de aanbevolen procedures voor het](best-practices-assessment.md) maken van evaluatie met server evaluatie.

## <a name="how-are-assessments-calculated"></a>Hoe worden evaluaties berekend? 

Evaluaties in Azure Migrate: de evaluatie van de server wordt berekend met behulp van de meta gegevens die zijn verzameld over de on-premises machines. Als u een evaluatie uitvoert op machines die worden geïmporteerd met behulp van een. CSV-bestand, geeft u de meta gegevens voor de berekening op. Berekeningen worden in drie fasen uitgevoerd:

1. **Azure-gereedheid berekenen**: Bepaal of computers geschikt zijn voor migratie naar Azure.
2. **Aanbevelingen voor de grootte berekenen**: schatting van de berekenings-, opslag-en netwerk grootte. 
2. **Bereken de maandelijkse kosten**: Bereken de geschatte maandelijkse reken-en opslag kosten voor het uitvoeren van de machines in azure na de migratie.

Berekeningen worden op volg orde gezet en een machine server wordt alleen naar een latere fase verplaatst als deze de voor gaande wordt door gegeven. Als een server bijvoorbeeld uitvalt op de Azure-gereedheid, wordt deze gemarkeerd als ongeschikt voor Azure, en wordt de grootte en de kosten voor de server niet meer in rekening gebracht.



## <a name="calculate-readiness"></a>Gereedheid berekenen

Niet alle machines zijn geschikt om te worden uitgevoerd in Azure. Server evaluatie evalueert elke on-premises computer en wijst deze toe aan een gereedheids categorie. 
- **Gereed voor Azure**: de machine kan zonder wijzigingen worden gemigreerd naar Azure. Het wordt gestart in azure met volledige ondersteuning voor Azure.
- **Voorwaardelijk gereed voor Azure**: de machine kan worden gestart in azure, maar heeft mogelijk geen volledige ondersteuning voor Azure. Bijvoorbeeld: een machine waarop een oudere versie van Windows Server wordt uitgevoerd, wordt niet ondersteund in Azure. U moet voorzichtig zijn voordat u deze machines naar Azure migreert. Volg de richt lijnen voor herstel in de evaluatie om de gereedheids problemen op te lossen.
- **Niet gereed voor Azure**: de computer start niet in Azure. Als een on-premises machine schijf bijvoorbeeld meer is dan 64-TBs, kan deze niet worden gehost in Azure. Volg de richt lijnen voor herstel om het probleem op te lossen voordat u de migratie uitvoert. 
- **Gereedheid onbekend**: Azure migrate kan de gereedheid van een machine niet bepalen vanwege onvoldoende meta gegevens.

Voor de berekening van de gereedheid controleert server assessment de computer eigenschappen en de instellingen van het besturings systeem in de volgende tabellen. 

### <a name="machine-properties"></a>Computer eigenschappen

Server beoordeling controleert de volgende eigenschappen van de on-premises virtuele machine om te bepalen of deze kan worden uitgevoerd op Azure.

**Eigenschap** | **Details** | **Status van Azure-gereedheid**
--- | --- | ---
**Opstart type** | Azure ondersteunt Vm's met een opstart type BIOS, niet voor UEFI. | Voorwaardelijk gereed als het opstart type UEFI is.
**Kernen** | Het aantal kernen op de computers moet gelijk zijn aan of kleiner zijn dan het maximum aantal kernen (128) dat wordt ondersteund voor een virtuele machine van Azure.<br/><br/> Als er een prestatie geschiedenis beschikbaar is, worden de gebruikte kernen Azure Migrate beschouwd als vergelijking. Als er een comfort factor is opgegeven in de evaluatie-instellingen, wordt het aantal gebruikte kern geheugens vermenigvuldigd met de comfort factor.<br/><br/> Als er geen prestatie geschiedenis is, gebruikt Azure Migrate de toegewezen kernen zonder de comfort factor toe te passen. | Gereed als deze kleiner dan of gelijk aan de limieten zijn.
**Geheugen** | De grootte van het computer geheugen moet gelijk zijn aan of kleiner zijn dan het maximum geheugen (3892 gigabytes [GB] op de Azure M-serie Standard_M128m&nbsp;<sup>2</sup>) dat is toegestaan voor een Azure-VM. [Meer informatie](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Als er een prestatie geschiedenis beschikbaar is, wordt het gebruikte geheugen in Azure Migrate beschouwd als vergelijking. Als er een comfort factor is opgegeven, wordt het gebruikte geheugen vermenigvuldigd met de comfort factor.<br/><br/> Als er geen geschiedenis is, wordt het toegewezen geheugen gebruikt zonder de comfort factor toe te passen.<br/><br/> | Gereed indien binnen de limieten.
**Opslag schijf** | De toegewezen grootte van een schijf moet 32 TB of minder zijn. Azure biedt ondersteuning voor 64-TB schijven met Ultra-SSD schijven, Azure Migrate: door server evaluatie wordt momenteel gecontroleerd op 32 TB als de grootte van de schijf wordt beperkt, omdat deze nog geen Ultra-SSD ondersteunt. <br/><br/> Het aantal schijven dat is gekoppeld aan de computer, moet 65 of minder zijn, inclusief de besturingssysteem schijf. | Gereed indien binnen de limieten.
**Netwerken** | Aan een machine moeten 32 of minder netwerk interfaces (Nic's) zijn gekoppeld. | Gereed indien binnen de limieten.

### <a name="guest-operating-system"></a>Gastbesturingssysteem
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
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Deze besturings systemen hebben hun end-of-support-datum door gegeven. De machine kan worden gestart in azure, maar Azure biedt geen ondersteuning voor het besturings systeem. | Voorwaardelijk gereed voor Azure. Het is raadzaam om het besturings systeem bij te werken voordat u naar Azure migreert.
Windows-client 7, 8 en 10 | Azure biedt alleen ondersteuning voor een [abonnement op Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Voorwaardelijk gereed voor Azure
Windows 10 Pro Desktop | Azure biedt ondersteuning voor [multi tenant-hosting rechten.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Voorwaardelijk gereed voor Azure
Windows Vista, XP Professional | Deze besturings systemen hebben hun end-of-support-datum door gegeven. De machine kan worden gestart in azure, maar Azure biedt geen ondersteuning voor het besturings systeem. | Voorwaardelijk gereed voor Azure. Het is raadzaam om het besturings systeem bij te werken voordat u naar Azure migreert.
Linux | Azure bevestigt deze [Linux-besturings systemen](../virtual-machines/linux/endorsed-distros.md). Andere Linux-besturings systemen kunnen worden gestart in azure, maar we raden u aan het besturings systeem bij te werken naar een officiële versie voordat u naar Azure migreert. | Gereed voor Azure als de versie wordt goedgekeurd.<br/><br/>Voorwaardelijk gereed als de versie niet wordt goedgekeurd.
Andere besturings systemen<br/><br/> Bijvoorbeeld Oracle Solaris, Apple Mac OS etc., FreeBSD, enzovoort. | Deze besturings systemen worden niet door Azure goedgekeurd. De machine kan worden gestart in azure, maar Azure biedt geen ondersteuning voor het besturings systeem. | Voorwaardelijk gereed voor Azure. U wordt aangeraden een ondersteund besturings systeem te installeren voordat u naar Azure migreert.  
Besturings systeem opgegeven als een **andere** in vCenter Server | Azure Migrate kan het besturings systeem in dit geval niet identificeren. | Onbekende gereedheid. Zorg ervoor dat het besturings systeem dat wordt uitgevoerd in de virtuele machine wordt ondersteund in Azure.
32-bits besturings systemen | De machine kan worden gestart in azure, maar Azure biedt mogelijk geen volledige ondersteuning. | Voorwaardelijk gereed voor Azure. Overweeg het besturings systeem van de machine te upgraden van 32-bits besturings systeem naar 64-bits besturings systeem voordat u naar Azure migreert.

## <a name="calculate-sizing-as-is-on-premises"></a>Grootte berekenen (as-on-premises)

Nadat de computer is gemarkeerd als gereed voor Azure, maakt de server evaluatie aanbevelingen voor het identificeren van de Azure VM en schijf-SKU. Als u de on-premises grootte op locatie gebruikt, wordt de prestatie geschiedenis van de Vm's en schijven niet door de server bepaling beschouwd.

**Berekenings grootte**: er wordt een Azure VM-SKU toegewezen op basis van de grootte die on-premises is toegekend.
**Grootte van opslag/schijf**: Server evaluatie bekijkt het opslag type dat is opgegeven in de eigenschappen van de evaluatie (standaard HDD/SSD/Premium) en beveelt het schijf type aan. Het standaard opslag type is Premium-schijven.
**Netwerk grootte**: Server evaluatie beschouwt de netwerk adapter op de on-premises computer.


## <a name="calculate-sizing-performance-based"></a>Grootte berekenen (op basis van prestaties)

Als u een computer hebt gemarkeerd als gereed voor Azure, kunt u als volgt de opties voor het aanpassen van de grootte van de server evalueren:

- De evaluatie van de server houdt rekening met de prestatie geschiedenis van de machine om de VM-grootte en het schijf type in azure te identificeren.
- Als servers zijn geïmporteerd met behulp van een CSV-bestand, worden de waarden gebruikt die u opgeeft. Deze methode is vooral nuttig als u de on-premises machine hebt toegewezen, het gebruik in werkelijkheid laag is en u de grootte van de virtuele machine in azure op de juiste manier wilt besparen. 
- Als u de prestatie gegevens niet wilt gebruiken, stelt u de grootte van de insluitings criteria in op on-premises, zoals beschreven in de vorige sectie.

### <a name="calculate-storage-sizing"></a>Opslag grootte berekenen

Voor opslag grootte Azure Migrate elke schijf die aan de computer is gekoppeld, toewijzen aan een schijf in Azure en werkt als volgt:

1. De server analyse voegt de IOPS voor lezen en schrijven van een schijf toe om het totale aantal IOPS dat is vereist te verkrijgen. Op dezelfde manier worden de doorvoer-en schrijf waarden toegevoegd om de totale door Voer van elke schijf te verkrijgen.
2. Als u opslag type hebt opgegeven als automatisch, op basis van de ingangs waarden voor IOPS en door Voer, bepaalt de server bepaling of de schijf moet worden toegewezen aan een standaard HDD, standaard SSD of een Premium-schijf in Azure. Als het opslag type is ingesteld op Standard-HDD/SSD/Premium, probeert de server evaluatie een schijf-SKU te vinden binnen het geselecteerde opslag type (Standard-HDD/SSD/Premium-schijven).
3. Schijven worden als volgt geselecteerd:
    - Als met de server bepaling geen schijf met de vereiste IOPS en door Voer wordt gevonden, wordt de machine als niet geschikt voor Azure gemarkeerd.
    - Als server evaluatie een set geschikte schijven vindt, worden de schijven geselecteerd die ondersteuning bieden voor de locatie die is opgegeven in de instellingen voor evaluatie.
    - Als er meerdere in aanmerking komende schijven zijn, selecteert de server bepaling de schijf met de laagste kosten.
    - Als de prestatie gegevens voor schijven niet beschikbaar zijn, worden de configuratie gegevens van de schijf (schijf grootte) gebruikt om een standaard SSD-schijf in azure te vinden.

### <a name="calculate-network-sizing"></a>Netwerk grootte berekenen

De server beoordeling probeert een virtuele machine van Azure te vinden die het aantal netwerk adapters kan ondersteunen dat is gekoppeld aan de on-premises machine en de prestaties die zijn vereist voor deze netwerk adapters.
- Om de effectief netwerk prestaties van de on-premises virtuele machine te verkrijgen, worden de gegevens die per seconde (MBps) van de computer (netwerk uitgaan) door de server bepaling geaggregeerd op alle netwerk adapters en wordt de comfort factor toegepast. Dit nummer wordt gebruikt om een virtuele machine van Azure te zoeken die de vereiste netwerk prestaties kan ondersteunen.
- Naast de netwerk prestaties is server evaluatie ook van oordeel of de virtuele machine van Azure het vereiste aantal netwerk adapters kan ondersteunen.
- Als er geen netwerk prestatie gegevens beschikbaar zijn, is voor de server evaluatie alleen het aantal netwerk adapters voor de VM-grootte in rekening gebracht.


### <a name="calculate-compute-sizing"></a>Reken grootte berekenen

Nadat de opslag-en netwerk vereisten zijn berekend, beschouwt de server evaluatie aan de vereisten voor de CPU en het geheugen om een geschikte VM-grootte in azure te vinden.
- Azure Migrate zoekt naar de effectief gebruikte kernen en het geheugen om een geschikte VM-grootte in azure te vinden.
- Als er geen geschikte grootte wordt gevonden, wordt de computer gemarkeerd als niet geschikt voor Azure.
- Als er een geschikte grootte wordt gevonden, past Azure Migrate de opslag-en netwerk berekeningen toe. Vervolgens worden de instellingen voor locatie en prijs categorie toegepast voor de laatste aanbeveling van de VM-grootte.
- Als er meer Azure VM-grootten in aanmerking komen, wordt de grootte met de laagste kosten aanbevolen.


### <a name="calculate-confidence-ratings"></a>Betrouwbaarheids classificaties berekenen

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

#### <a name="low-confidence-ratings"></a>Classificaties met lage betrouw baarheid

Hier volgen enkele redenen waarom een evaluatie een lage betrouwbaarheids classificatie kan krijgen:

- U hebt uw omgeving niet in het profiel voor de duur waarvoor u de evaluatie maakt. Als u bijvoorbeeld de beoordeling met de prestatie duur hebt ingesteld op één dag, moet u wachten tot minstens een dag nadat u de detectie hebt gestart voor alle gegevens punten die u wilt verzamelen.
- Sommige Vm's zijn afgesloten tijdens de periode waarvoor de evaluatie is berekend. Als een virtuele machine gedurende enige tijd is uitgeschakeld, kan de server bepaling de prestatie gegevens voor die periode niet verzamelen.
- Sommige Vm's zijn gemaakt tijdens de periode waarvoor de evaluatie is berekend. Als u bijvoorbeeld een evaluatie hebt gemaakt voor de prestatie geschiedenis van de afgelopen maand, maar sommige virtuele machines in de omgeving slechts een week geleden zijn gemaakt, bestaat de prestatie geschiedenis van de nieuwe Vm's niet voor de volledige duur.

> [!NOTE]
> Als de betrouwbaarheids classificatie van een evaluatie van minder dan vijf sterren is, raden we u aan om ten minste een dag te wachten op het apparaat om de omgeving in te stellen en de evaluatie vervolgens opnieuw te berekenen. Als dat niet het geval is, is de grootte van de prestaties mogelijk niet betrouwbaar. In dat geval raden wij u aan de evaluatie over te scha kelen naar de on-premises grootte.

## <a name="calculate-monthly-costs"></a>Maandelijkse kosten berekenen

Nadat de aanbevelingen voor de grootte zijn voltooid, berekent Azure Migrate reken-en opslag kosten voor na de migratie.

- **Reken kosten**: met de aanbevolen Azure VM-grootte maakt Azure migrate gebruik van de facturerings-API voor het berekenen van de maandelijkse kosten voor de virtuele machine.
    - Met de berekening worden het besturings systeem, Software Assurance, gereserveerde instanties, VM-uptime, locatie en valuta-instellingen in rekening gebracht.
    - Hiermee worden de kosten op alle machines geaggregeerd om de totale maandelijkse reken kosten te berekenen.
- **Opslag kosten**: de maandelijkse opslag kosten voor een machine worden berekend door het samen voegen van de maandelijkse kosten van alle schijven die aan de machine zijn gekoppeld, als volgt:
    - Server beoordeling berekent de totale maandelijkse opslag kosten door het samen voegen van de opslag kosten van alle machines.
    - Op dit moment worden voor de berekening geen aanbiedingen in de evaluatie-instellingen beschouwd.

De kosten worden weer gegeven in de valuta die is opgegeven in de instellingen voor evaluatie.


## <a name="next-steps"></a>Volgende stappen

[Bekijk](best-practices-assessment.md) de aanbevolen procedures voor het maken van evaluaties. 
