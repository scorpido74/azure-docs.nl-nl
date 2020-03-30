---
title: Beoordelingen in Azure Migrate Server Assessment
description: Meer informatie over beoordelingen in Azure Migrate Server Assessment
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: ae55686f0152d9c2b170ae1b34d7493ed7ac8d94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127781"
---
# <a name="assessments-in-azure-migrateserver-assessment"></a>Beoordelingen in Azure Migreren:Serverbeoordeling

In dit artikel vindt u een overzicht van beoordelingen in het hulpprogramma [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Het hulpprogramma Serverbeoordeling kan on-premises Vm's vMware, Hyper-V VM's en fysieke servers beoordelen op migratie naar Azure.

## <a name="whats-an-assessment"></a>Wat is een beoordeling?

Een beoordeling met het hulpprogramma Serverbeoordeling meet de gereedheid en schat de impact van het migreren van on-premises servers naar Azure.

## <a name="types-of-assessments"></a>Soorten beoordelingen

Beoordelingen die u maakt met ServerAssessment zijn een point-in-time momentopname van gegevens. Server Assessment biedt twee soorten beoordelingen.

**Beoordelingstype** | **Details** | **Gegevens**
--- | --- | ---
**Op prestaties gebaseerd** | Beoordelingen die aanbevelingen doen op basis van verzamelde prestatiegegevens | Vm-grootte aanbeveling is gebaseerd op CPU en geheugen gebruik gegevens.<br/><br/> Schijftypeaanbeveling (standaard HDD/SSD of premium-managed schijven) is gebaseerd op het IOPS en de doorvoer van de on-premises schijven.
**As-is on-premises** | Beoordelingen die geen prestatiegegevens gebruiken om aanbevelingen te doen. | Vm-grootteaanbeveling is gebaseerd op de on-premises VM-grootte<br/><br> Het aanbevolen schijftype is gebaseerd op het geselecteerde opslagtype voor de beoordeling.

## <a name="how-do-i-run-an-assessment"></a>Hoe voer ik een assessment uit?

Er zijn een paar manieren om een beoordeling uit te voeren:

- Beoordeel machines met behulp van servermetadata die zijn verzameld door een lichtgewicht Azure Migrate-toestel. Het toestel detecteert on-premises machines en stuurt machinemetagegevens/prestatiegegevens naar Azure Migrate.
- Beoordeel machines met behulp van servermetagegevens die worden geïmporteerd in een CSV-indeling (comma-separated values).

## <a name="how-do-i-assess-with-the-appliance"></a>Hoe beoordeel ik met het apparaat?

Als u een Azure Migrate-toestel implementeert om on-premises servers te detecteren, gaat u als volgt te werk:

1. U stelt Azure en uw on-premises omgeving in om te werken met serverbeoordeling.
2. Voor uw eerste beoordeling maakt u een Azure-project en voegt u het hulpprogramma serverbeoordeling toe.
3. U implementeert een lichtgewicht Azure Migrate-toestel. Het toestel detecteert continu on-premises machines en stuurt machinemetadata en prestatiegegevens naar Azure Migrate. Het apparaat wordt geïmplementeerd als een VM of een fysieke machine. Het is niet nodig om iets te installeren op machines die u wilt beoordelen.
4. Nadat het apparaat met machinedetectie is begonnen, u machines verzamelen die u in een groep wilt beoordelen en een beoordeling voor de groep uitvoeren.

U onze tutorials voor [VMware,](tutorial-prepare-vmware.md) [Hyper-V](tutorial-prepare-hyper-v.md)of [fysieke servers](tutorial-prepare-physical.md) volgen om deze stappen uit te proberen.

## <a name="how-do-i-assess-with-imported-data"></a>Hoe beoordeel ik met geïmporteerde gegevens?

Als u servers beoordeelt met een CSV-bestand, hebt u geen toestel nodig. In plaats daarvan gaat u als volgt te werk:

1. U stelt Azure in voor het werken met serverbeoordeling.
2. Voor uw eerste beoordeling maakt u een Azure-project en voegt u het hulpprogramma serverbeoordeling toe.
3. U downloadt een CSV-sjabloon en voegt er servergegevens aan toe.
4. U importeert de sjabloon in Serverbeoordeling.
5. U ontdekt servers die bij de import zijn toegevoegd, verzamelt vervolgens in een groep en voert een beoordeling uit voor de groep.

## <a name="what-data-does-the-appliance-collect"></a>Welke gegevens verzamelt het toestel?

Als u het Azure Migrate-toestel gebruikt voor beoordeling, leest u meer over de metagegevens en prestatiegegevens die zijn verzameld voor [VMware](migrate-appliance.md#collected-data---vmware) en [Hyper-V.](migrate-appliance.md#collected-data---hyper-v)

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hoe berekent het toestel prestatiegegevens?

Als u het toestel voor detectie gebruikt, worden prestatiegegevens voor rekeninstellingen als volgt verzameld:

1. Het apparaat verzamelt een real-time monsterpunt:

    - **VMware VM's**: Het apparaat verzamelt een real-time monsterpunt bij elk interval van 20 seconden.
    - **Hyper-V VM's**: Het real-time monsterpunt wordt verzameld bij elk interval van 30 seconden.
    - **Fysieke servers**: Het real-time monsterpunt wordt verzameld bij elk interval van vijf minuten. 
    
2. Het apparaat rolt de monsterpunten op (20 seconden, 30 seconden, vijf minuten) om elke 10 minuten één gegevenspunt te creëren. Als u het ene punt wilt maken, selecteert het toestel de piekwaarde uit alle voorbeelden en verzendt het vervolgens naar Azure.
3. Server assessment slaat alle voorbeeldpunten van 10 minuten van de afgelopen maand op.
4. Wanneer u een beoordeling maakt, identificeert Serverassessment het juiste gegevenspunt dat moet worden gebruikt voor de juiste grootte, op basis van de percentielwaarden voor *prestatiegeschiedenis* *en percentielgebruik.*

    - Als de prestatiegeschiedenis bijvoorbeeld is ingesteld op één week en het percentielgebruik het 95e percentiel is, sorteert Serverassessment de 10-minutenmonsterpunten voor de laatste week in oplopende volgorde en kiest u de 95e percentielwaarde voor de juiste grootte. 
    - De 95e percentielwaarde zorgt ervoor dat u uitschieters negeert, die kunnen worden opgenomen als u het 99e percentiel kiest.
    - Als u het piekgebruik voor de periode wilt kiezen en geen uitschieters wilt missen, moet u het 99e percentiel voor percentielgebruik selecteren.

5. Deze waarde wordt vermenigvuldigd met de comfortfactor om de effectieve prestatiegebruiksgegevens voor elke statistiek (CPU-gebruik, geheugengebruik, schijf-IOPS (lezen en schrijven), schijfdoorvoer (lezen en schrijven) en netwerkdoorvoer (in en uit) te krijgen apparaat verzamelt.



## <a name="how-are-assessments-calculated"></a>Hoe worden beoordelingen berekend? 

Beoordelingen in serverbeoordeling worden berekend met behulp van metagegevens/prestatiegegevens voor de on-premises machines. Als u het Azure Migrate-toestel implementeert, wordt de beoordeling uitgevoerd met behulp van de gegevens die door het toestel zijn verzameld. Als u een beoordeling uitvoert voor machines die zijn geïmporteerd met een . CSV-bestand, geeft u de metagegevens voor de berekening. Berekeningen vinden plaats in drie fasen:

1. **Azure-gereedheid berekenen:** beoordeel of machines geschikt zijn voor migratie naar Azure.
2. **Grootteaanbevelingen berekenen:** schat rekenkracht, opslag en netwerkgrootte. 
2. **Maandelijkse kosten berekenen:** bereken de geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de machines in Azure na migratie.

Berekeningen zijn in orde, en een machine server beweegt mee naar een later stadium alleen als het de vorige passeert. Als een server bijvoorbeeld de Azure-gereedheid niet haalt, is deze gemarkeerd als ongeschikt voor Azure en wordt de grootte en kosten niet voor die server uitgevoerd.


## <a name="whats-in-an-assessment"></a>Waaruit bestaat een evaluatie?

Dit is wat er in een beoordeling is opgenomen in Serverassessment.

**Eigenschap** | **Details**
--- | ---
**Doellocatie** | De locatie waarnaar u wilt migreren. Serverassessment ondersteunt momenteel deze doelazure-regio's:<br/><br/> Australië Oost, Australië Zuidoost, Brazilië Zuid, Canada Centraal, Canada Oost, Centraal-India, Centraal-VS, China Oost, China Noord, Oost-Azië, Oost-VS, Oost-VS2, Duitsland Centraal, Duitsland Noordoost, Japan Oost, Japan West, Korea Centraal, Korea Zuid, Noord-Noord Centraal VS, Noord-Europa, Zuid-Centraal VS, Zuidoost-Azië, Zuid-India, Uk South, UK West, US Gov Arizona, US Gov Texas, US Gov Virginia, West Central US, West-Europa, West-India, West US en West US2.
*Doelopslagschijf (as-is-sizing)** | Het type schijven dat moet worden gebruikt voor opslag in Azure. <br/><br/> Geef de doelopslagschijf op als premium managed, standard SSD managed of standard HDD managed.
**Doelopslagschijf (prestatiegebaseerde grootte)** | Geef het type doelopslagschijf op als automatisch, premium beheerd, standaard HDD beheerd of standaard SSD beheerd.<br/><br/> **Automatisch**: De schijfaanbeveling is gebaseerd op de prestatiegegevens van de schijven (de invoer-/uitvoerbewerkingen per seconde (IOPS) en doorvoer).<br/><br/>**Premium/standaard**: De beoordeling beveelt een schijf SKU aan binnen het geselecteerde opslagtype.<br/><br/> Als u één VM-SLA van 99,9% in één instantie wilt bereiken, overweegt u premium beheerde schijven te gebruiken. Dit zorgt ervoor dat alle schijven in de beoordeling worden aanbevolen als schijven met een premium beheerde schijf.<br/><br/> Azure Migrate biedt voor migratiebeoordeling alleen ondersteuning voor beheerde schijven.
**Gereserveerde instanties (RI's)** | Geef [gereserveerde exemplaren](https://azure.microsoft.com/pricing/reserved-vm-instances/) op in Azure, zodat kostenschattingen in de beoordeling rekening houden met RI-kortingen.<br/><br/> RI's worden momenteel alleen ondersteund voor Pay-As-You-Go-aanbiedingen in Azure Migrate.
**Groottecriteria** | Wordt gebruikt om de vm op de juiste grootte in Azure te vergroten.<br/><br/> Gebruik as-is-maatvoering of prestatiegebaseerde grootte.
**Prestatiegeschiedenis** | Gebruikt met prestatiegebaseerde maatvoering. Geef de duur op die wordt gebruikt bij het evalueren van prestatiegegevens.
**Percentiel gebruik** | Gebruikt met prestatiegebaseerde maatvoering. Hiermee geeft u de percentielwaarde op van het prestatievoorbeeld dat moet worden gebruikt voor de juiste grootte. 
**VM-reeks** | Geef de Azure VM-reeks op die u wilt overwegen voor de juiste grootte. Als u bijvoorbeeld geen productieomgeving hebt waarvoor VM's uit de A-serie in Azure nodig zijn, u A-reeksen uitsluiten van de lijst of reeksen.
**Comfortfactor** | Buffer gebruikt tijdens de beoordeling. Toegepast bovenop machinegebruiksgegevens voor VM's (CPU, geheugen, schijf en netwerk). Het is goed voor problemen zoals seizoensgebonden gebruik, korte prestatiegeschiedenis en waarschijnlijke toename van toekomstig gebruik.<br/><br/> Een 10-core VM met 20% gebruik resulteert bijvoorbeeld normaal gesproken in een tweecore VM. Met een comfortfactor van 2,0x is het resultaat een vier-core VM in plaats daarvan.
**Bieden** | Hiermee geeft u de [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) weer waarin u bent ingeschreven. Server assessment schat de kosten dienovereenkomstig.
**Valuta** | Factureringsvaluta voor uw account.
**Korting (%)** | Geeft een overzicht van alle abonnementsspecifieke kortingen die u ontvangt bovenop de Azure-aanbieding. De standaardinstelling is 0%.
**VM tijd actief** | Als Azure VM's niet 24 uur per dag, 7 dagen per week worden uitgevoerd, u de duur opgeven (dagen per maand en uren per dag) die ze uitvoeren. Kostenramingen worden dienovereenkomstig behandeld.<br/><br/> De standaardwaarde is 31 dagen per maand en 24 uur per dag.
**Azure Hybrid Benefit** | Hiermee geeft u op of u softwarezekerheid hebt en in aanmerking komt voor [Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-use-benefit/) Als u bent ingesteld op Ja (de standaardinstelling), worden niet-Windows Azure-prijzen in aanmerking genomen voor Windows VM's.

[Bekijk de aanbevolen procedures](best-practices-assessment.md) voor het maken van beoordeling met serverbeoordeling.


## <a name="calculate-readiness"></a>Gereedheid berekenen

Niet alle machines zijn geschikt om in Azure te draaien. Server Assessment beoordeelt elke on-premises machine en wijst deze een gereedheidscategorie toe. 
- **Klaar voor Azure**: de machine kan worden gemigreerd naar Azure zonder wijzigingen. Het begint in Azure met volledige Azure-ondersteuning.
- **Voorwaardelijk klaar voor Azure:** de machine start mogelijk in Azure, maar heeft mogelijk geen volledige Azure-ondersteuning. Een machine waarop een oudere versie van Windows Server wordt uitgevoerd, wordt bijvoorbeeld niet ondersteund in Azure. U moet voorzichtig zijn voordat u deze machines migreert naar Azure. Volg de herstelrichtlijnen die in de beoordeling worden voorgesteld om de gereedheidsproblemen op te lossen.
- **Niet klaar voor Azure**: De machine start niet in Azure. Als een on-premises machineschijf bijvoorbeeld meer dan 64-tv's is, kan deze niet worden gehost in Azure. Volg de herstelrichtlijnen om het probleem op te lossen voordat u de migratie aangaat. 
- **Gereedheid onbekend**: Azure Migrate kon de gereedheid van een machine niet bepalen, vanwege onvoldoende metagegevens.

Om de gereedheid te berekenen, controleert Server Assessment de machine-eigenschappen en de instellingen van het besturingssysteem die zijn samengevat in de volgende tabellen. 

### <a name="machine-properties"></a>Machine-eigenschappen

Serverassessment controleert de volgende eigenschappen van de on-premises VM om te bepalen of deze op Azure kan worden uitgevoerd.

**Eigenschap** | **Details** | **Azure-gereedheidsstatus**
--- | --- | ---
**Opstarttype** | Azure ondersteunt VM's met een opstarttype BIOS, niet Met UEFI. | Voorwaardelijk klaar als het opstarttype UEFI is.
**Kernen** | Het aantal cores in de machines moet gelijk zijn aan of kleiner zijn dan het maximum aantal cores (128) dat wordt ondersteund voor een Azure VM.<br/><br/> Als de prestatiegeschiedenis beschikbaar is, houdt Azure Migrate rekening met de gebruikte kernen ter vergelijking. Als een comfortfactor wordt opgegeven in de beoordelingsinstellingen, wordt het aantal gebruikte kernen vermenigvuldigd met de comfortfactor.<br/><br/> Als er geen prestatiegeschiedenis is, gebruikt Azure Migrate de toegewezen kernen zonder de comfortfactor toe te passen. | Klaar als minder dan of gelijk aan grenzen.
**Geheugen** | De grootte van het machinegeheugen moet gelijk zijn aan of kleiner zijn dan het&nbsp;maximale geheugen (3892 gigabyte [GB] op azure m-serie Standard_M128m<sup>2)</sup>die is toegestaan voor een Azure VM. [Meer informatie](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Als prestatiegeschiedenis beschikbaar is, houdt Azure Migrate rekening met het gebruikte geheugen ter vergelijking. Als een comfortfactor wordt opgegeven, wordt het gebruikte geheugen vermenigvuldigd met de comfortfactor.<br/><br/> Als er geen geschiedenis is, wordt het toegewezen geheugen gebruikt zonder de comfortfactor toe te passen.<br/><br/> | Klaar als binnen de grenzen.
**Opslagschijf** | Toegewezen grootte van een schijf moet 32 TB of minder. Hoewel Azure 64-TB schijven met Ultra SSD-schijven ondersteunt, controleert Azure Migrate: Server Assessment momenteel op 32 TB als de schijfgroottelimieten omdat het ultra SSD nog niet ondersteunt. <br/><br/> Het aantal schijven dat aan de machine is gekoppeld, moet 65 of minder zijn, inclusief de os-schijf. | Klaar als binnen de grenzen.
**Networking** | Aan een machine moeten 32 of minder netwerkinterfaces (NIC's) zijn gekoppeld. | Klaar als binnen de grenzen.

### <a name="guest-operating-system"></a>Gastbesturingssysteem
Samen met VM-eigenschappen kijkt Server Assessment naar het gastbesturingssysteem van de machines om te bepalen of het op Azure kan worden uitgevoerd.

> [!NOTE]
> Voor VMware VM's gebruikt Server Assessment het besturingssysteem dat is opgegeven voor de VM in vCenter Server om de analyse van het gastbesturingssysteem af te handelen. Voor Linux VM's die op VMware draaien, identificeert het momenteel niet de exacte kernelversie van het gastbesturingssysteem.

De volgende logica wordt door Server Assessment gebruikt om azure-gereedheid te identificeren op basis van het besturingssysteem.

**Besturingssysteem** | **Details** | **Azure-gereedheidsstatus**
--- | --- | ---
Windows Server 2016 & alle SP's | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2012 R2 & alle SP's | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2012 & alle SP's | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2008 R2 met alle SP's | Azure biedt volledige ondersteuning.| Gereed voor Azure
Windows Server 2008 (32-bits en 64-bits) | Azure biedt volledige ondersteuning. | Gereed voor Azure
Windows Server 2003, 2003 R2 | Deze besturingssystemen hebben hun einddatum van ondersteuning overschreden en hebben een [Aangepaste Ondersteuningsovereenkomst (CSA)](https://aka.ms/WSosstatement) nodig voor ondersteuning in Azure. | Voorwaardelijk klaar voor Azure. Overweeg het besturingssysteem te upgraden voordat u migreert naar Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Deze besturingssystemen hebben hun einddatum van de ondersteuning verstreken. De machine kan starten in Azure, maar Azure biedt geen OS-ondersteuning. | Voorwaardelijk klaar voor Azure. We raden u aan het besturingssysteem te upgraden voordat u migreert naar Azure.
Windows-client 7, 8 en 10 | Azure biedt alleen ondersteuning met [een Visual Studio-abonnement.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Voorwaardelijk gereed voor Azure
Windows 10 Pro-bureaublad | Azure biedt ondersteuning met [Multitenant Hosting Rights.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Voorwaardelijk gereed voor Azure
Windows Vista, XP Professional | Deze besturingssystemen hebben hun einddatum van de ondersteuning verstreken. De machine kan starten in Azure, maar Azure biedt geen OS-ondersteuning. | Voorwaardelijk klaar voor Azure. We raden u aan het besturingssysteem te upgraden voordat u migreert naar Azure.
Linux | Azure onderschrijft deze [Linux-besturingssystemen.](../virtual-machines/linux/endorsed-distros.md) Andere Linux-besturingssystemen starten mogelijk in Azure, maar we raden u aan het besturingssysteem te upgraden naar een goedgekeurde versie voordat u naar Azure migreert. | Klaar voor Azure als de versie wordt goedgekeurd.<br/><br/>Voorwaardelijk klaar als de versie niet wordt goedgekeurd.
Andere besturingssystemen<br/><br/> Bijvoorbeeld Oracle Solaris, Apple macOS etc., FreeBSD, enz. | Azure onderschrijft deze besturingssystemen niet. De machine kan starten in Azure, maar Azure biedt geen OS-ondersteuning. | Voorwaardelijk klaar voor Azure. We raden u aan een ondersteund besturingssysteem te installeren voordat u naar Azure migreert.  
BE opgegeven als **Ander** in vCenter Server | Azure Migrate kan het besturingssysteem in dit geval niet identificeren. | Onbekende bereidheid. Controleer of het besturingssysteem dat in de VM wordt uitgevoerd, wordt ondersteund in Azure.
32-bits besturingssystemen | De machine start mogelijk in Azure, maar Azure biedt mogelijk geen volledige ondersteuning. | Voorwaardelijk klaar voor Azure. Overweeg het besturingssysteem van de machine te upgraden van 32-bits besturingssysteem naar 64-bits besturingssysteem voordat u overstapt naar Azure.

## <a name="calculating-sizing"></a>Het berekenen van de grootte


Nadat de machine is gemarkeerd als klaar voor Azure, doet Server Assessment maataanbevelingen om de Azure VM en disk SKU te identificeren. Grootteberekeningen zijn afhankelijk van de vraag of u as-is on-premises grootte gebruikt of op prestaties gebaseerde grootte.

### <a name="calculate-sizing-as-is-on-premises"></a>Grootte berekenen (on-premises)

 Als u on-premises grootte sizing gebruikt, houdt Server Assessment geen rekening met de prestatiegeschiedenis van de VM's en schijven.

- **Compute-grootte:** het wijst een Azure VM SKU toe op basis van de toegewezen grootte on-premises.
- **Opslag/schijfgrootte**: Serverassessment kijkt naar het opslagtype dat is opgegeven in beoordelingseigenschappen (standaard HDD/SSD/premium) en raadt het schijftype dienovereenkomstig aan. Het standaardopslagtype is premium schijven.
- **Netwerkgrootte**: Serverassessment houdt rekening met de netwerkadapter op de on-premises machine.


### <a name="calculate-sizing-performance-based"></a>Grootte berekenen (op basis van prestaties)

Als u prestatiegebaseerde maatvoering gebruikt, doet Server Assessment als volgt maatadviezen:

- Serverbeoordeling houdt rekening met de prestatiegeschiedenis van de machine om de VM-grootte en het schijftype in Azure te identificeren.
- Als servers zijn geïmporteerd met een CSV-bestand, worden de opgegeven waarden gebruikt. Deze methode is vooral handig als u de on-premises machine te veel hebt toegewezen, het gebruik eigenlijk laag is en u de VM in Azure naar de juiste grootte wilt maken om kosten te besparen. 
- Als u de prestatiegegevens niet wilt gebruiken, stelt u de groottecriteria opnieuw in op de on-premises as-is, zoals beschreven in de vorige sectie.

#### <a name="calculate-storage-sizing"></a>Opslaggrootte berekenen

Voor het formaat van de opslag probeert Azure Migrate elke schijf die aan de machine is gekoppeld, in te richten op een schijf in Azure en werkt het als volgt:

1. Server Assessment voegt de lees- en schrijf-IOPS van een schijf toe om de totale Vereiste IOPS te krijgen. Op dezelfde manier worden de lees- en schrijfdoorvoerwaarden toegevoegd om de totale doorvoer van elke schijf te krijgen.
2. Als u opslagtype als automatisch hebt opgegeven, op basis van de effectieve IOPS- en doorvoerwaarden, bepaalt serverbeoordeling of de schijf moet worden toegewezen aan een standaard HDD, standaard SSD of een premium schijf in Azure. Als het opslagtype is ingesteld op Standaard HDD/SSD/Premium, probeert Server Assessment een schijf-SKU te vinden binnen het geselecteerde opslagtype (Standard HDD/SSD/Premium-schijven).
3. Schijven worden als volgt geselecteerd:
    - Als Server Assessment geen schijf met de vereiste IOPS en doorvoer kan vinden, wordt de machine als ongeschikt voor Azure beschouwd.
    - Als serverbeoordeling een set geschikte schijven vindt, selecteert u de schijven die de locatie ondersteunen die is opgegeven in de beoordelingsinstellingen.
    - Als er meerdere in aanmerking komende schijven zijn, selecteert Serverassessment de schijf met de laagste kosten.
    - Als prestatiegegevens voor een schijf niet beschikbaar zijn, worden de configuratiegegevens van de schijf (schijfgrootte) gebruikt om een standaard SSD-schijf in Azure te vinden.

#### <a name="calculate-network-sizing"></a>Netwerkgrootte berekenen

Server assessment probeert een Azure VM te vinden die het aantal netwerkadapters kan ondersteunen dat is gekoppeld aan de on-premises machine en de prestaties die door deze netwerkadapters worden vereist.
- Om de effectieve netwerkprestaties van de on-premises VM te krijgen, verzamelt Server Assessment de gegevens die per seconde (MBps) worden verzonden vanuit de machine (netwerk uit), over alle netwerkadapters en past de comfortfactor toe. Het gebruikt dit nummer om een Azure VM te vinden die de vereiste netwerkprestaties kan ondersteunen.
- Naast de netwerkprestaties wordt bij Server Assessment ook gekeken of de Azure VM het vereiste aantal netwerkadapters kan ondersteunen.
- Als er geen netwerkprestatiegegevens beschikbaar zijn, houdt Server Assessment rekening met alleen het aantal netwerkadapters voor vm-grootte.


#### <a name="calculate-compute-sizing"></a>Compute-grootte berekenen

Nadat de opslag- en netwerkvereisten zijn berekend, houdt Server Assessment rekening met CPU- en geheugenvereisten om een geschikte VM-grootte in Azure te vinden.
- Azure Migrate kijkt naar de effectieve gebruikte kernen en geheugen om een geschikte VM-grootte in Azure te vinden.
- Als er geen geschikte grootte wordt gevonden, wordt de machine gemarkeerd als ongeschikt voor Azure.
- Als er een geschikte grootte wordt gevonden, past Azure Migrate de opslag- en netwerkberekeningen toe. Vervolgens worden locatie- en prijsniveauinstellingen voor de uiteindelijke aanbeveling voor vm-grootte van toepassing.
- Als er meer Azure VM-grootten in aanmerking komen, wordt de grootte met de laagste kosten aanbevolen.


## <a name="confidence-ratings-performance-based"></a>Vertrouwensbeoordelingen (op basis van prestaties)

Elke prestatiegebaseerde beoordeling in Azure Migrate is gekoppeld aan een betrouwbaarheidsclassificatie die varieert van één (laagste) tot vijf sterren (hoogste). Met de betrouwbaarheidsclassificatie u de betrouwbaarheid schatten van de grootteaanbevelingen van Azure Migrate.

- De betrouwbaarheidsclassificatie wordt aan een evaluatie toegewezen op basis van de beschikbaarheid van de gegevenspunten die nodig zijn om de evaluatie te berekenen.
- Voor prestatiegebaseerde grootte, serverbeoordeling behoeften:
    - De gebruiksgegevens voor CPU- en VM-geheugen.
    - De schijf-IOPS- en doorvoergegevens voor elke schijf die aan de VM is gekoppeld.
    - De netwerk-I/O voor het verwerken van prestatiegebaseerde grootte voor elke netwerkadapter die is gekoppeld aan een vm.
    - Als een van deze gebruiksnummers niet beschikbaar is, zijn de aanbevelingen voor grootte mogelijk niet betrouwbaar.

> [!NOTE]
> Vertrouwensclassificaties worden niet toegewezen voor servers die zijn beoordeeld met een geïmporteerd . CSV-bestand. Beoordelingen zijn ook niet van toepassing op on-premises beoordeling.
   
### <a name="ratings"></a>Waarderingen

Afhankelijk van het percentage beschikbare gegevenspunten gaat de betrouwbaarheidsbeoordeling voor de beoordeling als volgt.

   **Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
   --- | ---
   0-20% | 1 ster
   21-40% | 2 sterren
   41-60% | 3 sterren
   61-80% | 4 sterren
   81-100% | 5 sterren

### <a name="low-confidence-ratings"></a>Lage vertrouwensbeoordelingen

Hier zijn een paar redenen waarom een beoordeling een lage betrouwbaarheidsscore zou kunnen krijgen:

- Je hebt je omgeving niet geprofileerd voor de duur waarvoor je de beoordeling maakt. Als u bijvoorbeeld de beoordeling maakt waarbij de prestatieduur is ingesteld op één dag, moet u ten minste een dag wachten nadat u de detectie hebt gestart voordat alle gegevenspunten worden verzameld.
- Sommige VM's werden stilgelegd in de periode waarvoor de beoordeling werd berekend. Als vm's voor enige tijd zijn uitgeschakeld, kan Server assessment de prestatiegegevens voor die periode niet verzamelen.
- Sommige VM's werden gemaakt tijdens de periode waarvoor de beoordeling werd berekend. Als u bijvoorbeeld een beoordeling hebt gemaakt voor de prestatiegeschiedenis van de afgelopen maand, maar sommige VM's pas een week geleden in de omgeving zijn gemaakt, bestaat de prestatiegeschiedenis van de nieuwe VM's niet voor de volledige duur.

> [!NOTE]
> Als de betrouwbaarheidsbeoordeling van een beoordeling minder dan vijf sterren bedraagt, raden we u aan ten minste een dag te wachten voordat het apparaat de omgeving heeft geprofileerd en vervolgens de beoordeling opnieuw te berekenen. Als u dat niet doet, is prestatiegebaseerde maatvoering mogelijk niet betrouwbaar. In dat geval raden we u aan de beoordeling over te schakelen op on-premises dimensionering.

## <a name="calculate-monthly-costs"></a>Maandelijkse kosten berekenen

Nadat de aanbevelingen voor het dimensioneren zijn voltooid, berekent Azure Migrate reken- en opslagkosten voor na migratie.

- **Rekenkosten:** Azure Migrate gebruikt de facturerings-API om de maandelijkse kosten voor de VM te berekenen met behulp van de aanbevolen Azure VM-grootte.
    - Bij de berekening wordt rekening gehouden met het besturingssysteem, de softwarezekerheid, gereserveerde exemplaren, de uptime van de VM, de locatie en de valuta-instellingen.
    - Het verzamelt de kosten voor alle machines om de totale maandelijkse rekenkosten te berekenen.
- **Opslagkosten**: De maandelijkse opslagkosten voor een machine worden berekend door de maandelijkse kosten van alle schijven die aan de machine zijn gekoppeld, als volgt te aggregeren:
    - Server assessment berekent de totale maandelijkse opslagkosten door de opslagkosten van alle machines te aggregeren.
    - Momenteel houdt de berekening geen rekening met aanbiedingen die zijn opgegeven in de beoordelingsinstellingen.

De kosten worden weergegeven in de valuta die is opgegeven in de beoordelingsinstellingen.


## <a name="next-steps"></a>Volgende stappen

[Bekijk](best-practices-assessment.md) de aanbevolen procedures voor het maken van beoordelingen. 


- Meer informatie over het uitvoeren van beoordelingen voor [Vm's van VMware,](tutorial-prepare-vmware.md) [Hyper-V VM's](tutorial-prepare-hyper-v.md)en [fysieke servers](tutorial-prepare-physical.md).
- Meer informatie over het beoordelen van servers [die zijn geïmporteerd met een CSV-bestand](tutorial-assess-import.md).
- Meer informatie over het instellen van [afhankelijkheidsvisualisatie](concepts-dependency-visualization.md).